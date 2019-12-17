```
kubeadm init --skip-phases=addon/kube-proxy --pod-network-cidr=10.217.0.0/16,fc22::/48 --service-cidr=10.96.0.0/16,fc44::/112 --feature-gates IPv6DualStack=true --apiserver-advertise-address=192.168.34.11
```
