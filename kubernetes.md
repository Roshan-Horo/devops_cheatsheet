
# Kubernetes Commands

- `kubectl <command>`

## basic

- `kubectl get pods`: get the running pods
- `kubectl cluster-info`: cluster info
- `kubectl get nodes`: get the nodes
- `kubectl describe pod <pod_name>`

# Pods

- `kubectl run nginx --image nginx`: run a container inside a pod ( automatically created)
- `kubectl get pods`: get the running pods
- `kubectl get pods -o wide`: more info in short line (ip, node)
-  `kubectl describe pod <pod_name>`: more info about pod
-  `kubectl delete pod <pod_name>`

## YAML in kubernetes

pod-definition.yml
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: myap-pod
	labels:
		app: myapp
		type: front-end

spec:
	containers:
		- name: nginx-container
		  image: nginx


```

- `apiversion and kind`:

| **kind**   | **version** |
|------------|-------------|
| Pod       | v1          |
| Service    | v1          |
| ReplicaSet | apps/v1     |
| Deployment | apps/v1     |

- `metadata ` : name and labels
- `spec`: defines the it's specification like name and image

- Create pod by running `kubectl create/apply -f pod-definition.yml`
   
## Replication Controller

rc-definition.yml

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
```

- `kubectl apply -f rc-definition.yml`
- `kubectl get replicationcontroller`
- `kubectl get pods`
## ReplicaSet

replicaset-definition.yml

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
spec:
  template:
    metadata:
      name: myapp-pod
      labels: 
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

- `kubectl apply -f replicaset-definition.yml`
- `kubectl get replicaset`
- `kubectl delete replicaset <replica_set_name>`
- `kubectl get pods`
- 

Scaling up/down replicas

- `kubectl replace -f replicaset-definition.yml`: after updating yaml file run this command ( replicas: 6)
- `kubectl scale --replicas 6 -f replicaset-definition.yml`: don't need to update yaml file
- `kubectl scale --replicas=6 replicaset myapp-replicaset(name)`

# Deployment

( same as replicaset, but different object)

deployment-definition.yml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp-depl
spec:
  template:
    metadata:
      name: myapp-pod
      labels: 
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  strategy: Recreate
  selector:
    matchLabels:
      type: front-end
```

- `kubectl get all`: for pods, replicaset, deployment
- `kubectl edit deployment <deployment_name> --record`: to edit the deployment

## Deployment - update and rollback

- `kubectl rollout status deployment/<deployment_name>`: deployment rollout
- `kubectl rollout history deployment/<deployment_name>`: history of rollout
- `kubectl rollout undo deployment/<deployment_name>`: undo the new changes

## Deployment Strategy

- `Recreate`: delete old pods then create new one ( application down during that time)
- `Rolling Update(default)` : delete each pod one by one and also create new one while deleting

## Deployment Update

- `kubectl apply -f deployment-definition.yaml`: update the yaml file and apply
- `kubectl set image deployment/<deployment_name> nginx-container=nginx:1.9.1`

# Services

Services Types :

- `NodePort`: able to access internal pod through nodeport
- `ClusterIp`: create virtual ip to comm between different services
- `LoadBalancer` : used to distribute load

### NodePort Service

- `TargetPort` : port inside pod
- `Port`: port inside service
- `NodePort`: port inside Node(range - 30000 - 32767)

service-definition.yaml
```yaml
apiVersion: v1
kind: Service
metadata: 
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: front-end
```

- `kubectl get services` 

### ClusterIP Service(by default)

service-definition.yaml
```yaml
apiVersion: v1
kind: Service
metadata: 
  name: back-end
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    type: back-end
```

### LoadBalancer service

service-definition.yaml
```yaml
apiVersion: v1
kind: Service
metadata: 
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
```
