# Kubernetes Cluster Upgrade: v1.29 → v1.30.14

[![Kubernetes](https://img.shields.io/badge/kubernetes-v1.30.14-326CE5?logo=kubernetes)](https://kubernetes.io/)
[![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?logo=amazon-aws)](https://aws.amazon.com/ec2/)
[![Status](https://img.shields.io/badge/upgrade-success-brightgreen)](/)
[![Downtime](https://img.shields.io/badge/downtime-0%20seconds-blue)](/)

> A production-style Kubernetes cluster upgrade demonstrating zero-downtime operations, systematic troubleshooting, and comprehensive documentation practices.

## 🎯 Project Overview

Successfully upgraded a 3-node Kubernetes cluster from version 1.29 to 1.30.14 with zero downtime, following official Kubernetes documentation and enterprise best practices.

**Key Achievements:**
- ✅ Zero-downtime rolling upgrade
- ✅ Multi-AZ AWS deployment
- ✅ Comprehensive documentation (735 lines of terminal capture)
- ✅ Automated state tracking using ConfigMaps
- ✅ Resolved complex AWS networking challenge

## 🏗️ Architecture
```
┌─────────────────────────────────────────────┐
│  AWS VPC (vpc-0bc53c73a3d679762)           │
│                                             │
│  ┌──────────────────┐  ┌─────────────────┐ │
│  │  AZ: us-east-1a  │  │  AZ: us-east-1b │ │
│  │  ┌────────────┐  │  │  ┌────────────┐ │ │
│  │  │ Master     │  │  │  │ Worker 1   │ │ │
│  │  │ v1.30.14   │◄─┼──┼─►│ v1.30.14   │ │ │
│  │  └────────────┘  │  │  └────────────┘ │ │
│  │                  │  │  ┌────────────┐ │ │
│  │                  │  │  │ Worker 2   │ │ │
│  │                  │  │  │ v1.30.14   │ │ │
│  │                  │  │  └────────────┘ │ │
│  └──────────────────┘  └─────────────────┘ │
└─────────────────────────────────────────────┘
```

### Infrastructure Details
- **Platform:** Self-managed Kubernetes on AWS EC2
- **Control Plane:** 1 master node
- **Workers:** 2 nodes across different availability zones
- **CNI:** Calico
- **Applications:** nginx, evershop e-commerce, PostgreSQL

## 📋 Upgrade Process

### Phase 1: Control Plane (Master Node)
```bash
Version: 1.29.15 → 1.30.14
Duration: ~30 minutes
Status: ✅ Success
```

1. Added Kubernetes v1.30 repository
2. Upgraded kubeadm to v1.30.14
3. Reviewed upgrade plan (`kubeadm upgrade plan`)
4. Applied control plane upgrade
5. Upgraded kubelet and kubectl
6. Verified all control plane components

### Phase 2: Worker Nodes
```bash
Worker 1: 1.29.6 → 1.30.14 ✅
Worker 2: 1.29.6 → 1.30.14 ✅
Duration: ~30 minutes each
```

Each worker upgraded using rolling deployment strategy:
- Drained node (workloads migrated automatically)
- Upgraded components
- Uncordoned node
- Verified health

## 🔧 Technical Skills Demonstrated

### Kubernetes Administration
- Cluster upgrades using kubeadm
- Control plane component management
- Node lifecycle operations (cordon/drain/uncordon)
- Pod migration and rescheduling
- ConfigMap-based state management

### Cloud Infrastructure (AWS)
- Multi-AZ deployment architecture
- VPC networking configuration
- Security group management
- ENI secondary IP addressing
- Cross-AZ communication troubleshooting

### DevOps Practices
- Zero-downtime deployment strategies
- Comprehensive documentation
- Systematic troubleshooting methodology
- Following official documentation
- Automated state tracking

## 🐛 Challenges & Solutions

### Challenge: Workers Unable to Join Cluster

**Problem:** Worker nodes in different availability zone couldn't reach master's Kubernetes API server.

**Investigation:**
```bash
# Workers could reach primary IP
ping 172.31.82.189  # ✅ Success

# But not the secondary IP used by K8s API
ping 172.31.89.68   # ❌ Failed
```

**Root Cause:** Secondary IP address was added at OS level but not registered with AWS ENI.

**Solution:**
```bash
aws ec2 assign-private-ip-addresses \
  --network-interface-id eni-054cab4e32bf4c5fc \
  --private-ip-addresses 172.31.89.68
```

**Key Learning:** Cloud provider networking requires proper API registration, not just OS-level configuration.

## 📊 Results & Metrics

| Metric | Value |
|--------|-------|
| **Total Upgrade Time** | ~90 minutes |
| **Application Downtime** | 0 seconds |
| **Pods Migrated** | 6 |
| **Failed Attempts** | 0 |
| **Rollbacks Required** | 0 |
| **Documentation Lines** | 735 |

### Final Verification
```
NAME               STATUS   ROLES           VERSION
master             Ready    control-plane   v1.30.14
ip-172-31-28-145   Ready    <none>          v1.30.14
ip-172-31-25-165   Ready    <none>          v1.30.14
```

## 📁 Repository Structure
```
.
├── README.md                      # This file
├── logs/
│   └── upgrade-session.log        # Complete terminal capture (735 lines)
├── cluster-states/
│   ├── master-upgrade.yaml        # Post-master upgrade state
│   ├── final-state.yaml           # Final cluster state
│   └── complete-cluster-snapshot.yaml
├── docs/
│   ├── troubleshooting-aws-networking.md
│   └── upgrade-procedure.md
├── screenshots/
│   ├── pre-upgrade.png
│   └── post-upgrade.png
└── QUICK_START.md                 # Quick reference guide
```

## 🚀 Quick Start

### View Complete Upgrade Log
```bash
cat logs/upgrade-session-20251021-083000.log
```

### Examine Cluster States
```bash
# Pre-upgrade state
cat cluster-states/pre-upgrade.yaml

# Final state
cat cluster-states/final-state.yaml
```

### Apply ConfigMaps to Your Cluster
```bash
kubectl apply -f cluster-states/
```

## 🛠️ Technologies Used

- **Kubernetes** 1.29 → 1.30.14
- **kubeadm** - Cluster lifecycle management
- **AWS EC2** - Compute infrastructure
- **Calico** - Container networking (CNI)
- **Ubuntu** 22.04 LTS
- **kubectl** - Kubernetes CLI
- **containerd** - Container runtime

## 📚 References & Resources

- [Official Kubernetes Upgrade Documentation](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)
- [Kubernetes 1.30 Release Notes](https://kubernetes.io/blog/2024/04/17/kubernetes-v1-30-release/)
- [AWS VPC Networking Guide](https://docs.aws.amazon.com/vpc/)

## 💡 Key Takeaways

1. **Planning Prevents Issues** - Understanding upgrade paths and prerequisites is crucial
2. **Documentation is Essential** - Real-time logging captures invaluable troubleshooting information
3. **Cloud ≠ Traditional Infrastructure** - Cloud providers require proper resource registration through APIs
4. **Official Docs Work** - Following Kubernetes documentation ensures reliable upgrades
5. **Zero-Downtime is Achievable** - Proper use of cordon/drain enables seamless production upgrades

## 🤝 Connect

**Ibrahim Cisse**  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://www.linkedin.com/in/ibraheemcisse)
[![Email](https://img.shields.io/badge/Email-Contact-red?logo=gmail)](ibrahimcisse@ioc-labs.com)

---

⭐ **Star this repository if you found it helpful!**

📝 **Questions or suggestions?** Open an issue or reach out!

*This project demonstrates production-ready Kubernetes administration and cloud infrastructure management capabilities.*
