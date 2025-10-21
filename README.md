# Kubernetes Cluster Upgrade Project

## Executive Summary
Successfully upgraded a 3-node production-style Kubernetes cluster from version 1.29.x to 1.30.14 with zero downtime, following official Kubernetes documentation and best practices.

**Project Date:** October 21, 2025  
**Total Duration:** ~90 minutes  
**Methodology:** Rolling upgrade (control plane → workers)  
**Documentation Lines:** 735 lines of terminal capture

---

## Cluster Architecture

### Infrastructure
- **Cloud Provider:** AWS EC2
- **Topology:** Multi-AZ (us-east-1a, us-east-1b)
- **Control Plane:** 1 node
- **Worker Nodes:** 2 nodes
- **CNI:** Calico
- **Applications:** nginx, evershop e-commerce platform, PostgreSQL

### Network Configuration
- **VPC:** vpc-0bc53c73a3d679762
- **Security Groups:** Properly configured for cross-AZ communication
- **Special Challenge:** Secondary IP routing (documented separately)

---

## Upgrade Process

### Phase 1: Control Plane
```
Version: 1.29.15 → 1.30.14
Duration: ~30 minutes
Status: ✅ Success
```

**Steps Completed:**
1. Added v1.30 Kubernetes repository
2. Upgraded kubeadm to v1.30.14
3. Reviewed upgrade plan
4. Applied control plane upgrade
5. Upgraded kubelet and kubectl
6. Verified all control plane components

### Phase 2: Worker Nodes
```
Worker 1 (ip-172-31-28-145): 1.29.6 → 1.30.14 ✅
Worker 2 (ip-172-31-25-165): 1.29.6 → 1.30.14 ✅
Duration: ~30 minutes each
```

**Steps per Worker:**
1. Drained node (workloads migrated to other nodes)
2. Added v1.30 repository
3. Upgraded kubeadm, kubelet, kubectl
4. Ran node upgrade procedure
5. Uncordoned node
6. Verified successful upgrade

---

## Technical Skills Demonstrated

### Kubernetes Administration
- ✅ Cluster upgrades using kubeadm
- ✅ Control plane management
- ✅ Node lifecycle operations (cordon/drain/uncordon)
- ✅ Pod migration and rescheduling
- ✅ ConfigMap management for state tracking

### High Availability Practices
- ✅ Zero-downtime upgrades
- ✅ Rolling deployment strategies
- ✅ Workload distribution across nodes
- ✅ Health checks and verification

### AWS Cloud Integration
- ✅ Multi-AZ deployment
- ✅ VPC networking
- ✅ Security group configuration
- ✅ EC2 instance management
- ✅ ENI secondary IP configuration

### DevOps Best Practices
- ✅ Following official documentation
- ✅ Comprehensive logging (735 lines)
- ✅ State tracking at each milestone
- ✅ Systematic troubleshooting
- ✅ Documentation as code (ConfigMaps)

---

## Challenges Overcome

### Challenge: Workers Unable to Join Cluster Initially
**Problem:** Workers in different availability zone couldn't reach master's Kubernetes API

**Root Cause:** Secondary IP address (172.31.89.68) not registered with AWS ENI

**Solution:** Used AWS CLI to properly assign secondary IP to network interface
```bash
aws ec2 assign-private-ip-addresses \
  --network-interface-id eni-054cab4e32bf4c5fc \
  --private-ip-addresses 172.31.89.68
```

**Key Learning:** Cloud provider networking requires proper registration through APIs, not just OS-level configuration

[Full troubleshooting article →](link-to-article)

---

## Verification Results

### Final Node Status
```
NAME               STATUS   ROLES           VERSION
master             Ready    control-plane   v1.30.14
ip-172-31-28-145   Ready    <none>          v1.30.14
ip-172-31-25-165   Ready    <none>          v1.30.14
```

### All Workloads Healthy
- ✅ System pods: All running
- ✅ Application pods: All running
- ✅ No failed containers
- ✅ No pod restarts (except expected during migration)

### Metrics
| Metric | Value |
|--------|-------|
| Total Upgrade Time | ~90 minutes |
| Application Downtime | 0 seconds |
| Pods Migrated | 6 |
| Failed Upgrades | 0 |
| Rollbacks Required | 0 |

---

## Documentation Artifacts

### Generated Files
1. **Complete Terminal Log** (735 lines)
   - Every command executed
   - All output captured
   - Timestamps included

2. **Cluster State ConfigMaps**
   - Pre-upgrade state
   - Post-master upgrade
   - Post-worker upgrades
   - Final state

3. **Resource Exports**
   - All cluster resources (YAML)
   - Node configurations
   - Pod manifests

### Stored in Cluster
```bash
# View all documentation
kubectl get configmap -n upgrade-docs

# ConfigMaps created:
# - master-upgraded-to-v1.30.14
# - post-upgrade-complete
# - final-upgrade-state
```

---

## Key Takeaways

1. **Planning is Critical**: Understanding the upgrade path and prerequisites prevents issues

2. **Documentation is Essential**: Real-time logging captures valuable troubleshooting information

3. **Cloud Networking Differs from Traditional**: AWS requires proper resource registration

4. **Official Documentation Works**: Following Kubernetes docs ensures reliable upgrades

5. **Zero-Downtime is Achievable**: Proper use of cordon/drain enables seamless upgrades

---

## Repository Structure

```
kubernetes-upgrade-project/
├── README.md                           (This file)
├── upgrade-session-20251021-083000.log (Complete terminal capture)
├── upgrade-configmaps.yaml             (All cluster state saves)
├── troubleshooting-article.md          (AWS networking challenge)
└── screenshots/
    ├── pre-upgrade-nodes.png
    ├── upgrade-in-progress.png
    └── post-upgrade-complete.png
```

---

## Technologies Used

- Kubernetes 1.29 → 1.30.14
- kubeadm
- AWS EC2
- Calico CNI
- Ubuntu 22.04
- kubectl
- Docker/containerd

---

## Links

- **GitHub Repository:** [Your repo link]
- **LinkedIn Article:** [Your article link]
- **Related Projects:** [Other K8s work]

---

## Contact

**[Your Name]**  
[Email] | [LinkedIn] | [GitHub]

*This project demonstrates production-ready Kubernetes administration skills and cloud infrastructure management capabilities.*

---

**Completion Date:** Tue Oct 21 09:04:49 UTC 2025  
**Total Project Time:** ~4 hours (including troubleshooting and documentation)
