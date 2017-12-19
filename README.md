=== Installing K8S in Vagrant ===

==== Links ====
https://www.stratoscale.com/blog/kubernetes/installing-kubernetes-bare-metal/
https://github.com/Stratoscale/installing_k8s_on_bare_metal/tree/master/worker

1. git clone https://github.com/Stratoscale/installing_k8s_on_bare_metal/tree/master/worker
2. vagrant plugin install vagrant-hostmanager
3. cd master
4. vagrant up
5. vagrant ssh
6. sudo kubeadm init --apiserver-advertise-address=192.168.77.10 --pod-network-cidr=10.244.0.0/16
7. mkdir -p ~/.kube
8. sudo cp /etc/kubernetes/admin.conf ~/.kube/config
9. sudo chown $(id -u):$(id -g) $HOME/.kube/config
10. export KUBECONFIG=~/.kube/config
11. kubectl apply -f https://git.io/weave-kube-1.6 #networking
12. ----
13. cd worker
14. vagrant up
15. copy `kubeadm join` from master printout
16. copy kubectl config from master to worker
17. git clone https://github.com/scicatproject/localdeploy
18. cd localdeploy
19. remove minikube reference in `start.sh`
20. wget https://raw.githubusercontent.com/mclarkson/JSONPath.sh/master/JSONPath.sh && chmod +x JSONPath.sh && sudo mv JSONPath.sh /usr/local/bin/JSONPath.sh
21. `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > scripts/get_helm.sh`
22. `chmod +x get_helm.sh && get_helm.sh`
23. `kubectl create sa tiller`
24. `kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller`
25. `helm init --service-account tiller --upgrade`
26. clustip=$(kubectl get svc -o json | JSONPath.sh '$.items[?(@.metadata.name=kubernetes)]..clusterIP' -b)
node=$(kubectl get endpoints -o json | JSONPath.sh '$.items[?(@.metadata.name=kubernetes)]..ip' -b)
sudo ip ro add $clustip/32 via $node
27. `./run.sh -d dacat`

