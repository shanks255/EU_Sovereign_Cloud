[🏠 Home](../index.md) > [🚀 Deployment](DEPLOYMENT_GUIDE.md)

---

# Deployment & Operations Guide

## Overview
This guide provides comprehensive instructions for deploying, operating, and maintaining the EU Sovereign Cloud infrastructure. It covers initial deployment, day-to-day operations, monitoring, and troubleshooting.

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│              Deployment Pipeline Architecture                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  1. Infrastructure as Code (IaC)                          │  │
│  │     Terraform • Ansible • Pulumi                          │  │
│  └──────────────────┬───────────────────────────────────────┘  │
│                      │                                           │
│  ┌──────────────────▼───────────────────────────────────────┐  │
│  │  2. CI/CD Pipeline                                        │  │
│  │     GitLab CI • Jenkins • ArgoCD                          │  │
│  └──────────────────┬───────────────────────────────────────┘  │
│                      │                                           │
│  ┌──────────────────▼───────────────────────────────────────┐  │
│  │  3. Configuration Management                              │  │
│  │     Ansible • Puppet • Chef                               │  │
│  └──────────────────┬───────────────────────────────────────┘  │
│                      │                                           │
│  ┌──────────────────▼───────────────────────────────────────┐  │
│  │  4. Deployment Validation                                 │  │
│  │     Testing • Security Scanning • Compliance Checks       │  │
│  └──────────────────┬───────────────────────────────────────┘  │
│                      │                                           │
│  ┌──────────────────▼───────────────────────────────────────┐  │
│  │  5. Production Deployment                                 │  │
│  │     Blue-Green • Canary • Rolling Updates                 │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

## 1. Prerequisites

### 1.1 Infrastructure Requirements
**Data Center:**
- Tier III or IV certified facility
- Located within EU borders
- Redundant power and cooling
- Physical security measures

**Hardware:**
- Compute nodes: 10+ servers (256GB RAM, 2x Intel Xeon/AMD EPYC)
- Storage nodes: 5+ servers (high-density storage)
- Network equipment: 100Gbps switches, firewalls
- Management servers: 3+ servers for control plane

**Network:**
- Public IP address range
- BGP peering (optional)
- VPN connectivity
- DNS delegation

### 1.2 Software Requirements
**Operating Systems:**
- Ubuntu 22.04 LTS or Rocky Linux 9
- Kernel 5.15+ with security patches
- SELinux/AppArmor enabled

**Tools:**
- Terraform >= 1.5
- Ansible >= 2.15
- kubectl >= 1.28
- Helm >= 3.12
- Git >= 2.40

### 1.3 Access Requirements
- Root/sudo access to all servers
- SSH key-based authentication
- VPN access to management network
- Access to container registries

## 2. Initial Deployment

### 2.1 Phase 1: Foundation Setup (Week 1-2)

#### Step 1: Prepare Infrastructure
```bash
# Clone deployment repository
git clone https://github.com/eu-sovereign-cloud/deployment.git
cd deployment

# Configure environment
cp config/example.tfvars config/production.tfvars
# Edit production.tfvars with your settings

# Initialize Terraform
terraform init

# Plan deployment
terraform plan -var-file=config/production.tfvars

# Apply infrastructure
terraform apply -var-file=config/production.tfvars
```

#### Step 2: Bootstrap Management Cluster
```bash
# Install management Kubernetes cluster
ansible-playbook -i inventory/production playbooks/bootstrap-mgmt-cluster.yml

# Verify cluster
kubectl get nodes
kubectl get pods --all-namespaces

# Install core components
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

#### Step 3: Deploy Identity & Access Management
```bash
# Deploy Keycloak
helm install keycloak codecentric/keycloak \
  --namespace identity \
  --create-namespace \
  --values config/keycloak-values.yaml

# Configure realms and clients
kubectl apply -f config/keycloak-realms.yaml

# Set up admin users
kubectl exec -it keycloak-0 -n identity -- /opt/keycloak/bin/kcadm.sh create users \
  -r master -s username=admin -s enabled=true
```

### 2.2 Phase 2: Core Services (Week 3-4)

#### Step 1: Deploy Storage Layer
```bash
# Deploy Ceph cluster
ansible-playbook -i inventory/production playbooks/deploy-ceph.yml

# Verify Ceph health
ceph -s
ceph osd tree

# Create storage pools
ceph osd pool create rbd 128
ceph osd pool create rgw 64
```

#### Step 2: Deploy Network Services
```bash
# Configure SDN (Calico/Cilium)
kubectl apply -f manifests/network/calico.yaml

# Deploy load balancers
kubectl apply -f manifests/network/metallb.yaml

# Configure DNS
kubectl apply -f manifests/network/coredns-config.yaml
```

#### Step 3: Deploy Monitoring Stack
```bash
# Deploy Prometheus
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --values config/prometheus-values.yaml

# Deploy Grafana dashboards
kubectl apply -f manifests/monitoring/dashboards/

# Deploy ELK Stack
helm install elasticsearch elastic/elasticsearch \
  --namespace logging \
  --create-namespace \
  --values config/elasticsearch-values.yaml
```

### 2.3 Phase 3: Platform Services (Week 5-6)

#### Step 1: Deploy Database Services
```bash
# Deploy PostgreSQL operator
kubectl apply -f https://raw.githubusercontent.com/zalando/postgres-operator/master/manifests/postgres-operator.yaml

# Create database clusters
kubectl apply -f manifests/databases/postgres-cluster.yaml

# Deploy Redis
helm install redis bitnami/redis \
  --namespace databases \
  --values config/redis-values.yaml
```

#### Step 2: Deploy Container Platform
```bash
# Deploy additional Kubernetes clusters for workloads
ansible-playbook -i inventory/production playbooks/deploy-workload-clusters.yml

# Configure cluster federation
kubectl apply -f manifests/federation/kubefed.yaml

# Set up multi-cluster ingress
kubectl apply -f manifests/federation/multi-cluster-ingress.yaml
```

#### Step 3: Deploy CI/CD Pipeline
```bash
# Deploy GitLab
helm install gitlab gitlab/gitlab \
  --namespace cicd \
  --create-namespace \
  --values config/gitlab-values.yaml

# Configure runners
kubectl apply -f manifests/cicd/gitlab-runner.yaml

# Deploy ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2.4 Phase 4: Security & Compliance (Week 7-8)

#### Step 1: Deploy Security Services
```bash
# Deploy Vault for secrets management
helm install vault hashicorp/vault \
  --namespace security \
  --create-namespace \
  --values config/vault-values.yaml

# Initialize and unseal Vault
kubectl exec -it vault-0 -n security -- vault operator init
kubectl exec -it vault-0 -n security -- vault operator unseal

# Deploy Falco for runtime security
helm install falco falcosecurity/falco \
  --namespace security \
  --values config/falco-values.yaml
```

#### Step 2: Configure Encryption
```bash
# Enable encryption at rest
kubectl apply -f manifests/security/encryption-config.yaml

# Configure TLS certificates
kubectl apply -f manifests/security/certificates.yaml

# Set up key rotation
kubectl apply -f manifests/security/key-rotation-cronjob.yaml
```

#### Step 3: Deploy Compliance Tools
```bash
# Deploy compliance scanner
kubectl apply -f manifests/compliance/compliance-scanner.yaml

# Configure audit logging
kubectl apply -f manifests/compliance/audit-policy.yaml

# Set up compliance dashboards
kubectl apply -f manifests/compliance/dashboards.yaml
```

## 3. Operations

### 3.1 Day-to-Day Operations

#### Monitoring & Alerting
```bash
# Check cluster health
kubectl get nodes
kubectl top nodes
kubectl get pods --all-namespaces | grep -v Running

# View metrics
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090
# Access Grafana
kubectl port-forward -n monitoring svc/prometheus-grafana 3000:80

# Check alerts
kubectl get prometheusrules -n monitoring
```

#### Log Management
```bash
# View logs
kubectl logs -f <pod-name> -n <namespace>

# Search logs in Elasticsearch
kubectl port-forward -n logging svc/elasticsearch 9200:9200
curl -X GET "localhost:9200/_search?q=error"

# Access Kibana
kubectl port-forward -n logging svc/kibana 5601:5601
```

#### Backup Operations
```bash
# Backup etcd
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-snapshot-$(date +%Y%m%d).db

# Backup persistent volumes
velero backup create daily-backup --include-namespaces=production

# Verify backups
velero backup describe daily-backup
```

### 3.2 Scaling Operations

#### Horizontal Scaling
```bash
# Scale deployment
kubectl scale deployment <deployment-name> --replicas=5 -n <namespace>

# Auto-scaling
kubectl autoscale deployment <deployment-name> --min=2 --max=10 --cpu-percent=80

# Scale node pool
terraform apply -var="worker_node_count=20"
```

#### Vertical Scaling
```bash
# Update resource limits
kubectl set resources deployment <deployment-name> \
  --limits=cpu=2,memory=4Gi \
  --requests=cpu=1,memory=2Gi

# Resize persistent volume
kubectl patch pvc <pvc-name> -p '{"spec":{"resources":{"requests":{"storage":"100Gi"}}}}'
```

### 3.3 Update & Patch Management

#### Kubernetes Updates
```bash
# Update control plane
kubeadm upgrade plan
kubeadm upgrade apply v1.28.0

# Update worker nodes (one at a time)
kubectl drain <node-name> --ignore-daemonsets
kubeadm upgrade node
kubectl uncordon <node-name>
```

#### Application Updates
```bash
# Rolling update
kubectl set image deployment/<deployment-name> <container-name>=<new-image>

# Canary deployment
kubectl apply -f manifests/canary-deployment.yaml

# Blue-green deployment
kubectl apply -f manifests/blue-deployment.yaml
# Switch traffic
kubectl patch service <service-name> -p '{"spec":{"selector":{"version":"green"}}}'
```

#### Security Patches
```bash
# Update base images
docker pull ubuntu:22.04
docker tag ubuntu:22.04 registry.eu-cloud.local/ubuntu:22.04
docker push registry.eu-cloud.local/ubuntu:22.04

# Scan for vulnerabilities
trivy image <image-name>

# Apply security patches
ansible-playbook -i inventory/production playbooks/security-patches.yml
```

### 3.4 Disaster Recovery

#### Backup Procedures
```bash
# Full cluster backup
velero backup create full-backup --include-cluster-resources=true

# Database backup
kubectl exec -it postgres-0 -n databases -- pg_dump -U postgres > backup.sql

# Configuration backup
kubectl get all --all-namespaces -o yaml > cluster-config-backup.yaml
```

#### Recovery Procedures
```bash
# Restore from backup
velero restore create --from-backup full-backup

# Restore database
kubectl exec -it postgres-0 -n databases -- psql -U postgres < backup.sql

# Restore etcd
ETCDCTL_API=3 etcdctl snapshot restore /backup/etcd-snapshot.db
```

### 3.5 Troubleshooting

#### Common Issues

**Pod Not Starting:**
```bash
# Check pod status
kubectl describe pod <pod-name> -n <namespace>

# Check logs
kubectl logs <pod-name> -n <namespace> --previous

# Check events
kubectl get events -n <namespace> --sort-by='.lastTimestamp'
```

**Network Issues:**
```bash
# Test connectivity
kubectl run -it --rm debug --image=nicolaka/netshoot --restart=Never -- /bin/bash

# Check DNS
kubectl run -it --rm debug --image=busybox --restart=Never -- nslookup kubernetes.default

# Check network policies
kubectl get networkpolicies -n <namespace>
```

**Storage Issues:**
```bash
# Check PVC status
kubectl get pvc -n <namespace>

# Check storage class
kubectl get storageclass

# Check Ceph health
ceph health detail
```

## 4. Maintenance Windows

### 4.1 Planned Maintenance
```
Schedule: Every Sunday 02:00-06:00 UTC
Notification: 7 days advance notice
Duration: Maximum 4 hours
Impact: Minimal (rolling updates)
```

### 4.2 Maintenance Procedures
1. Notify users 7 days in advance
2. Create maintenance window in monitoring
3. Perform pre-maintenance checks
4. Execute maintenance tasks
5. Verify system health
6. Close maintenance window
7. Send completion notification

## 5. Performance Optimization

### 5.1 Resource Optimization
```bash
# Identify resource usage
kubectl top pods --all-namespaces --sort-by=memory
kubectl top nodes

# Right-size resources
kubectl set resources deployment <name> --requests=cpu=500m,memory=1Gi

# Enable resource quotas
kubectl apply -f manifests/resource-quotas.yaml
```

### 5.2 Cost Optimization
- Use spot instances for non-critical workloads
- Implement auto-scaling
- Schedule non-production environments
- Optimize storage tiers
- Review and remove unused resources

## 6. Security Operations

### 6.1 Security Monitoring
```bash
# Check security alerts
kubectl get alerts -n security

# Review audit logs
kubectl logs -n kube-system kube-apiserver-* | grep audit

# Scan for vulnerabilities
trivy k8s --report summary cluster
```

### 6.2 Incident Response
1. Detect and alert
2. Assess severity
3. Contain threat
4. Investigate root cause
5. Remediate
6. Document and review

## 7. Compliance Operations

### 7.1 Compliance Checks
```bash
# Run compliance scan
kubectl apply -f manifests/compliance/compliance-scan.yaml

# Generate compliance report
kubectl exec -it compliance-scanner -n compliance -- generate-report

# Review findings
kubectl logs compliance-scanner -n compliance
```

### 7.2 Audit Procedures
- Monthly security audits
- Quarterly compliance reviews
- Annual third-party assessments
- Continuous automated scanning

## 8. Documentation

### 8.1 Required Documentation
- Architecture diagrams
- Network topology
- Runbooks for common tasks
- Incident response procedures
- Disaster recovery plans
- Change management logs

### 8.2 Knowledge Base
- Troubleshooting guides
- Best practices
- Configuration examples
- FAQ
- Training materials

## Related Documents
- [Architecture Overview](../architecture/00_OVERVIEW.md)
- [Security Framework](../security/SECURITY_FRAMEWORK.md)
- [Incident Response Plan](../security/INCIDENT_RESPONSE.md)
- [Runbooks](RUNBOOKS.md)