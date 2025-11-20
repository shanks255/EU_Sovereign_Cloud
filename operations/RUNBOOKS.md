# EU Sovereign Cloud - Operations Runbooks

## Table of Contents
1. [Overview](#overview)
2. [Infrastructure Operations](#infrastructure-operations)
3. [Security Operations](#security-operations)
4. [Database Operations](#database-operations)
5. [Network Operations](#network-operations)
6. [Monitoring & Alerting](#monitoring--alerting)
7. [Backup & Recovery](#backup--recovery)
8. [Emergency Procedures](#emergency-procedures)

## Overview

This document contains standardized operational procedures (runbooks) for managing the EU Sovereign Cloud infrastructure. These runbooks ensure consistent, reliable operations and quick resolution of common issues.

### Runbook Structure
Each runbook follows this format:
- **Purpose**: What this procedure accomplishes
- **Prerequisites**: Required access, tools, or conditions
- **Steps**: Detailed step-by-step instructions
- **Verification**: How to confirm success
- **Rollback**: How to undo changes if needed
- **Escalation**: When and how to escalate

---

## Infrastructure Operations

### 1.1 Virtual Machine Deployment

**Purpose**: Deploy new virtual machines in compliance with EU sovereignty requirements

**Prerequisites**:
- Admin access to cloud management platform
- Approved change request
- Resource quota availability

**Steps**:
1. **Validate Request**
   ```bash
   # Check resource quotas
   openstack quota show --project <project-id>
   
   # Verify EU region selection
   openstack region list | grep -E "(eu-west|eu-central|eu-north)"
   ```

2. **Create VM Instance**
   ```bash
   # Deploy VM with EU-compliant settings
   openstack server create \
     --flavor <flavor-name> \
     --image <eu-approved-image> \
     --network <eu-network> \
     --security-group <security-group> \
     --availability-zone <eu-zone> \
     --key-name <ssh-key> \
     <vm-name>
   ```

3. **Configure Security**
   ```bash
   # Apply security hardening
   ansible-playbook -i inventory security-hardening.yml \
     --limit <vm-name>
   
   # Enable monitoring
   ansible-playbook -i inventory monitoring-setup.yml \
     --limit <vm-name>
   ```

**Verification**:
- VM status shows "ACTIVE"
- Security scan passes
- Monitoring agents reporting

**Rollback**:
```bash
openstack server delete <vm-name>
```

**Escalation**: Contact Infrastructure Team Lead if deployment fails after 3 attempts

### 1.2 Kubernetes Cluster Scaling

**Purpose**: Scale Kubernetes clusters to meet demand

**Prerequisites**:
- kubectl access to target cluster
- Cluster autoscaler permissions

**Steps**:
1. **Check Current Status**
   ```bash
   kubectl get nodes
   kubectl top nodes
   kubectl get pods --all-namespaces | grep Pending
   ```

2. **Scale Node Pool**
   ```bash
   # Manual scaling
   kubectl scale deployment cluster-autoscaler \
     --replicas=<new-count> -n kube-system
   
   # Or update node pool
   openstack coe cluster update <cluster-name> \
     --node-count <new-count>
   ```

3. **Verify Scaling**
   ```bash
   # Wait for nodes to be ready
   kubectl wait --for=condition=Ready node/<node-name> --timeout=300s
   
   # Check pod scheduling
   kubectl get pods --all-namespaces -o wide
   ```

**Verification**:
- All nodes show "Ready" status
- Pending pods are scheduled
- Resource utilization within targets

**Rollback**:
```bash
kubectl scale deployment cluster-autoscaler \
  --replicas=<original-count> -n kube-system
```

---

## Security Operations

### 2.1 Security Incident Response

**Purpose**: Respond to security alerts and incidents

**Prerequisites**:
- Access to SIEM platform
- Incident response tools
- Communication channels

**Steps**:
1. **Initial Assessment**
   ```bash
   # Check alert details in SIEM
   curl -X GET "https://siem.eu-cloud.local/api/alerts/<alert-id>" \
     -H "Authorization: Bearer <token>"
   
   # Gather system logs
   journalctl -u <service-name> --since "1 hour ago"
   ```

2. **Containment**
   ```bash
   # Isolate affected systems
   iptables -A INPUT -s <suspicious-ip> -j DROP
   
   # Disable compromised accounts
   openstack user set --disable <user-id>
   
   # Quarantine affected VMs
   openstack server set --property quarantine=true <server-id>
   ```

3. **Investigation**
   ```bash
   # Collect forensic data
   dd if=/dev/<disk> of=/forensics/<case-id>.img bs=4M
   
   # Analyze network traffic
   tcpdump -i <interface> -w /forensics/<case-id>.pcap
   ```

**Verification**:
- Threat contained
- Evidence preserved
- Stakeholders notified

**Escalation**: Immediately escalate to CISO for critical incidents

### 2.2 Certificate Renewal

**Purpose**: Renew SSL/TLS certificates before expiration

**Prerequisites**:
- Certificate authority access
- DNS management permissions

**Steps**:
1. **Check Certificate Status**
   ```bash
   # Check expiration dates
   openssl x509 -in /etc/ssl/certs/<cert-name>.crt -text -noout | grep "Not After"
   
   # List certificates expiring in 30 days
   find /etc/ssl/certs -name "*.crt" -exec openssl x509 -checkend 2592000 -noout -in {} \; -print
   ```

2. **Generate New Certificate**
   ```bash
   # Generate CSR
   openssl req -new -key /etc/ssl/private/<key-name>.key \
     -out /tmp/<cert-name>.csr
   
   # Submit to CA (automated with Let's Encrypt)
   certbot certonly --dns-route53 -d <domain-name>
   ```

3. **Deploy Certificate**
   ```bash
   # Update certificate files
   cp /etc/letsencrypt/live/<domain>/fullchain.pem /etc/ssl/certs/
   cp /etc/letsencrypt/live/<domain>/privkey.pem /etc/ssl/private/
   
   # Restart services
   systemctl reload nginx
   systemctl reload haproxy
   ```

**Verification**:
- Certificate validity confirmed
- Services restart successfully
- No SSL errors in monitoring

---

## Database Operations

### 3.1 Database Backup Verification

**Purpose**: Verify database backups are successful and recoverable

**Prerequisites**:
- Database admin access
- Backup storage access

**Steps**:
1. **Check Backup Status**
   ```bash
   # PostgreSQL backup verification
   pg_dump --version
   ls -la /backups/postgresql/$(date +%Y-%m-%d)/
   
   # MySQL backup verification
   mysqldump --version
   ls -la /backups/mysql/$(date +%Y-%m-%d)/
   ```

2. **Test Restore Process**
   ```bash
   # Create test database
   createdb test_restore_$(date +%Y%m%d)
   
   # Restore from backup
   pg_restore -d test_restore_$(date +%Y%m%d) \
     /backups/postgresql/latest/database.dump
   
   # Verify data integrity
   psql -d test_restore_$(date +%Y%m%d) -c "SELECT COUNT(*) FROM <critical_table>;"
   ```

3. **Cleanup Test Environment**
   ```bash
   dropdb test_restore_$(date +%Y%m%d)
   ```

**Verification**:
- Backup files exist and are not corrupted
- Restore process completes successfully
- Data integrity checks pass

### 3.2 Database Performance Tuning

**Purpose**: Optimize database performance based on monitoring metrics

**Prerequisites**:
- Database monitoring access
- Performance baseline data

**Steps**:
1. **Analyze Performance Metrics**
   ```sql
   -- PostgreSQL slow query analysis
   SELECT query, mean_time, calls, total_time
   FROM pg_stat_statements
   ORDER BY total_time DESC
   LIMIT 10;
   
   -- Check connection statistics
   SELECT * FROM pg_stat_activity WHERE state = 'active';
   ```

2. **Apply Optimizations**
   ```sql
   -- Update statistics
   ANALYZE;
   
   -- Rebuild indexes if needed
   REINDEX INDEX CONCURRENTLY <index_name>;
   
   -- Update configuration
   ALTER SYSTEM SET shared_buffers = '256MB';
   SELECT pg_reload_conf();
   ```

**Verification**:
- Query performance improves
- Resource utilization optimized
- No degradation in other metrics

---

## Network Operations

### 4.1 Network Connectivity Troubleshooting

**Purpose**: Diagnose and resolve network connectivity issues

**Prerequisites**:
- Network monitoring access
- Administrative privileges

**Steps**:
1. **Initial Diagnostics**
   ```bash
   # Check interface status
   ip addr show
   ip route show
   
   # Test connectivity
   ping -c 4 <target-ip>
   traceroute <target-ip>
   
   # Check DNS resolution
   nslookup <hostname>
   dig <hostname>
   ```

2. **Advanced Diagnostics**
   ```bash
   # Check network statistics
   netstat -i
   ss -tuln
   
   # Analyze traffic
   tcpdump -i <interface> -c 100
   
   # Check firewall rules
   iptables -L -n -v
   ```

3. **Resolution Steps**
   ```bash
   # Restart network service
   systemctl restart networking
   
   # Flush DNS cache
   systemctl restart systemd-resolved
   
   # Reset network interface
   ip link set <interface> down
   ip link set <interface> up
   ```

**Verification**:
- Connectivity restored
- Network metrics normal
- Applications functioning

### 4.2 Load Balancer Configuration

**Purpose**: Configure and maintain load balancers for high availability

**Prerequisites**:
- Load balancer admin access
- Backend server access

**Steps**:
1. **Check Current Configuration**
   ```bash
   # HAProxy status
   echo "show stat" | socat stdio /var/run/haproxy/admin.sock
   
   # Check backend health
   curl -I http://<backend-server>/health
   ```

2. **Update Configuration**
   ```bash
   # Edit configuration
   vim /etc/haproxy/haproxy.cfg
   
   # Validate configuration
   haproxy -f /etc/haproxy/haproxy.cfg -c
   
   # Reload configuration
   systemctl reload haproxy
   ```

3. **Add/Remove Backend Servers**
   ```bash
   # Add server
   echo "enable server <backend>/<server>" | socat stdio /var/run/haproxy/admin.sock
   
   # Remove server
   echo "disable server <backend>/<server>" | socat stdio /var/run/haproxy/admin.sock
   ```

**Verification**:
- Load balancer responds correctly
- Traffic distributed evenly
- Health checks passing

---

## Monitoring & Alerting

### 5.1 Alert Investigation and Resolution

**Purpose**: Investigate and resolve monitoring alerts

**Prerequisites**:
- Monitoring system access
- System administration privileges

**Steps**:
1. **Alert Analysis**
   ```bash
   # Check Prometheus metrics
   curl "http://prometheus:9090/api/v1/query?query=<metric_name>"
   
   # Review Grafana dashboards
   # Access: https://grafana.eu-cloud.local
   
   # Check system resources
   top
   df -h
   free -m
   ```

2. **Root Cause Analysis**
   ```bash
   # Check system logs
   journalctl -f -u <service-name>
   
   # Analyze application logs
   tail -f /var/log/<application>/<logfile>
   
   # Check process status
   systemctl status <service-name>
   ps aux | grep <process-name>
   ```

3. **Resolution Actions**
   ```bash
   # Restart services if needed
   systemctl restart <service-name>
   
   # Clear disk space
   find /var/log -name "*.log" -mtime +7 -delete
   
   # Scale resources
   kubectl scale deployment <deployment-name> --replicas=<count>
   ```

**Verification**:
- Alert condition resolved
- Metrics return to normal
- No secondary alerts triggered

---

## Backup & Recovery

### 6.1 System Backup Procedures

**Purpose**: Perform comprehensive system backups

**Prerequisites**:
- Backup storage access
- Administrative privileges

**Steps**:
1. **Pre-backup Checks**
   ```bash
   # Check available space
   df -h /backup
   
   # Verify backup schedule
   crontab -l | grep backup
   
   # Check previous backup status
   ls -la /backup/$(date -d "yesterday" +%Y-%m-%d)/
   ```

2. **Execute Backup**
   ```bash
   # System configuration backup
   tar -czf /backup/$(date +%Y-%m-%d)/system-config.tar.gz \
     /etc /var/lib /home
   
   # Database backup
   pg_dumpall > /backup/$(date +%Y-%m-%d)/databases.sql
   
   # Application data backup
   rsync -av /var/www/ /backup/$(date +%Y-%m-%d)/www/
   ```

3. **Verify Backup**
   ```bash
   # Check backup integrity
   tar -tzf /backup/$(date +%Y-%m-%d)/system-config.tar.gz > /dev/null
   
   # Verify database backup
   psql -f /backup/$(date +%Y-%m-%d)/databases.sql --dry-run
   ```

**Verification**:
- Backup files created successfully
- Integrity checks pass
- Backup size within expected range

### 6.2 Disaster Recovery Procedures

**Purpose**: Restore services after a disaster

**Prerequisites**:
- Disaster recovery site access
- Recent backup availability

**Steps**:
1. **Assessment**
   ```bash
   # Assess damage scope
   ping <primary-site-servers>
   
   # Check backup availability
   ls -la /backup/latest/
   
   # Verify DR site readiness
   systemctl status <critical-services>
   ```

2. **Service Restoration**
   ```bash
   # Restore databases
   pg_restore -d <database-name> /backup/latest/database.dump
   
   # Restore application files
   rsync -av /backup/latest/www/ /var/www/
   
   # Update DNS records
   nsupdate -k /etc/bind/keys/update.key <<EOF
   server <dns-server>
   update delete <service-name>.eu-cloud.local A
   update add <service-name>.eu-cloud.local 300 A <dr-site-ip>
   send
   EOF
   ```

3. **Service Validation**
   ```bash
   # Test application functionality
   curl -I https://<service-name>.eu-cloud.local/health
   
   # Verify database connectivity
   psql -h <db-server> -U <user> -d <database> -c "SELECT 1;"
   
   # Check monitoring
   systemctl status prometheus grafana
   ```

**Verification**:
- All critical services operational
- Data integrity confirmed
- Monitoring systems active

---

## Emergency Procedures

### 7.1 Emergency Shutdown Procedures

**Purpose**: Safely shutdown systems during emergencies

**Prerequisites**:
- Emergency authorization
- System access

**Steps**:
1. **Immediate Actions**
   ```bash
   # Stop new connections
   iptables -A INPUT -p tcp --dport 80 -j DROP
   iptables -A INPUT -p tcp --dport 443 -j DROP
   
   # Gracefully stop applications
   systemctl stop <application-services>
   
   # Stop databases safely
   systemctl stop postgresql mysql
   ```

2. **System Shutdown**
   ```bash
   # Sync filesystems
   sync
   
   # Shutdown VMs
   openstack server stop <server-list>
   
   # Shutdown physical hosts
   shutdown -h now
   ```

**Verification**:
- All services stopped gracefully
- Data committed to storage
- Systems powered down safely

### 7.2 Emergency Contact Procedures

**Purpose**: Escalate critical issues to appropriate personnel

**Contact Matrix**:
| Severity | Contact | Response Time | Method |
|----------|---------|---------------|---------|
| Critical | On-call Engineer | 15 minutes | Phone + SMS |
| High | Team Lead | 30 minutes | Phone + Email |
| Medium | Team Members | 2 hours | Email + Slack |
| Low | Next Business Day | 24 hours | Email |

**Escalation Steps**:
1. **Initial Contact**: On-call engineer via automated alerting
2. **15 minutes**: Manual call to on-call engineer
3. **30 minutes**: Escalate to team lead
4. **1 hour**: Escalate to management
5. **2 hours**: Escalate to executive team

**Communication Channels**:
- **Primary**: Phone calls for immediate response
- **Secondary**: SMS for critical alerts
- **Tertiary**: Email for documentation
- **Status Updates**: Slack #incidents channel

---

## Appendix

### A. Common Commands Reference

```bash
# System Information
uname -a                    # System information
lscpu                      # CPU information
free -h                    # Memory usage
df -h                      # Disk usage
lsblk                      # Block devices

# Process Management
ps aux                     # List all processes
top                        # Real-time process viewer
htop                       # Enhanced process viewer
kill -9 <pid>             # Force kill process
killall <process-name>     # Kill by name

# Network Commands
ip addr show               # Show IP addresses
ss -tuln                   # Show listening ports
netstat -rn               # Show routing table
iptables -L               # Show firewall rules
tcpdump -i eth0           # Capture network traffic

# File Operations
find /path -name "*.log"   # Find files
grep -r "pattern" /path    # Search in files
tar -czf archive.tar.gz /path  # Create archive
rsync -av source/ dest/    # Sync directories
```

### B. Emergency Contact Information

**24/7 Operations Center**: +33-1-XXXX-XXXX
**Security Incident Hotline**: +33-1-XXXX-XXXX
**Infrastructure Team**: infrastructure@eu-cloud.local
**Security Team**: security@eu-cloud.local
**Management**: management@eu-cloud.local

### C. Related Documentation

- [Incident Response Plan](../security/INCIDENT_RESPONSE.md)
- [Security Framework](../security/SECURITY_FRAMEWORK.md)
- [Deployment Guide](../deployment/DEPLOYMENT_GUIDE.md)
- [Compliance Mapping](../compliance/COMPLIANCE_MAPPING.md)

---

*Last Updated: November 2025*
*Document Version: 1.0*
*Next Review: February 2026*