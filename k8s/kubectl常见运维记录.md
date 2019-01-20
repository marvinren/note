# 关于节点的操作

## 新增一个节点
token一般是24小时过期，所以使用kubeadm新增节点，需要生成token
```
kubeadm token create
```

然后通过如下命令新增节点，注意node上必须按照一些基本东西，这个可以参照k8s安装
```
kubeadm join 10.12.1.20:6443 --token y88mej.3hbrfwqysw1y73cf --discovery-token-ca-cert-hash sha256:a1ed105cc6712b2742c1d8228f047fb140c2b0a8f8671595fe330af2d9f18d79
```