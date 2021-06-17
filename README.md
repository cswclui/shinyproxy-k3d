Adapted from: https://github.com/openanalytics/shinyproxy-config-examples/tree/master/03-containerized-kubernetes

# install k3d
`curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`

# create k3d cluster

## create k3s cluster with 1 master node
` k3d cluster create mycluster`

## create worker nodes
 
` k3d node create worker1 -c mycluster`

` k3d node create worker2 -c mycluster`


# install kubectl
 `curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"`
 
 `sudo cp kubectl /usr/bin/`
 
 `sudo chmod +x /usr/bin/kubectl


# check the nodes in the k3s cluster

`kubectl get nodes`

```
NAME			 STATUS   ROLES 		 AGE	 VERSION
k3d-mycluster-server-0	 Ready	  control-plane,master	 6m33s	 v1.20.6+k3s1
k3d-worker2-0		 Ready	  <none>		 11s	 v1.20.6+k3s1
k3d-worker1-0		 Ready	  <none>		 16s	 v1.20.6+k3s1
```

# deploy shiny proxy in k3s
`git clone https://github.com/cswclui/shinyproxy-k3d`

`cd shinyproxy-k3d`

` kubectl apply -f sp-deployment.yaml`

` kubectl apply -f sp-service.yaml`

` kubectl  create -f sp-authorization.yaml`


` kubectl get all`

```
NAME				  READY   STATUS    RESTARTS   AGE
pod/shinyproxy-85c5784c6b-vpgvf   1/1	  Running   0	       13s

NAME		     TYPE	 CLUSTER-IP	 EXTERNAL-IP   PORT(S)		AGE
service/kubernetes   ClusterIP	 10.43.0.1	 <none>        443/TCP		16m
service/shinyproxy   NodePort	 10.43.233.218	 <none>        8080:32094/TCP	8m2s

NAME			     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/shinyproxy   1/1     1		  1	      2m43s

NAME					DESIRED   CURRENT   READY   AGE
replicaset.apps/shinyproxy-85c5784c6b	1	  1	    1	    13s
replicaset.apps/shinyproxy-76cbc4475f	0	  0	    0	    2m43s
```

# port forward localhost:9000 to service/shinyproxy at port 8080

` kubectl port-forward service/shinyproxy 9000:8080`

```
Forwarding from 127.0.0.1:9000 -> 8080
Forwarding from [::1]:9000 -> 8080
```

# visit localhost:9000 in browser

# Other notes

Kubeconfig is located at `/.kube/config` and owned by root.

To allow non-root to use kubectl, copy the .kube under user's home directly and change owner to the user.

`sudo chown user ~/.kube/*`

