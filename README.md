# Dépôt point recapitulatif Kubernetes Datascientest
```shell
kubectl apply -f .
```

### install etcdctl https://github.com/etcd-io/etcd/releases
kubectl -n kube-system get pod/etcd-k8s-master -o=jsonpath='{$.spec.containers[:1].image}'

#debu install etcd
ETCD_VER=v3.5.10

### choose either URL
GOOGLE_URL=https://storage.googleapis.com/etcd
GITHUB_URL=https://github.com/etcd-io/etcd/releases/download
DOWNLOAD_URL=${GOOGLE_URL}

rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
rm -rf /tmp/etcd-download-test && mkdir -p /tmp/etcd-download-test

curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
tar xzvf /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /tmp/etcd-download-test --strip-components=1
rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz

/tmp/etcd-download-test/etcd --version
/tmp/etcd-download-test/etcdctl version

ln -s /tmp/etcd-download-test/etcdctl /usr/bin/etcdctl
etcdctl version
#end install etcd




cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep etcd
rm /tmp/backup.db



ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /tmp/backup.db

ETCDCTL_API=3 etcdctl --write-out=table snapshot status /tmp/backup.db

kubectl delete -f pod-red.yml
sss
mv /etc/kubernetes/manifests/kube-apiserver.yaml ./
mv /etc/kubernetes/manifests/etcd.yaml ./
rm -rf /var/lib/etcd

ETCDCTL_API=3 etcdctl --data-dir="/var/lib/etcd" snapshot restore /tmp/backup.db

mv kube-apiserver.yaml /etc/kubernetes/manifests/
mv etcd.yaml /etc/kubernetes/manifests/

kubectl get po

ctr -n k8s.io containers list

journalctl -u kubelet

kubectl delete -f .


