# kubectl-commands

**mostly based on kuberneets documentations**

### To find all pods on a specific node:

```kubectl get pods --field-selector=spec.nodeName=k3d-k3drancher-server-0 -A```

### To set alias for  ```kubectl```
```alias k=kubectl```

```k config view```

### To print a list of pods sorted by name, you run:
```kubectl get pods --sort-by=.metadata.name```

### Create the objects that are defined in any .yaml, .yml, or .json file within the <directory> directory.
```kubectl apply -f <directory>```

### List all pods running on node server01
```kubectl get pods --field-selector=spec.nodeName=server01```

### Get output from running 'date' from pod <pod-name>. By default, output is from the first container.
```kubectl exec <pod-name> -- date```

### Get an interactive TTY and run /bin/bash from pod <pod-name>. By default, output is from the first container.
```kubectl exec -ti <pod-name> -- /bin/bash```

```kubectl get --watch --output-watch-events configmap```

# Viewing and finding resources

### Get all running pods in the namespace
```kubectl get pods --field-selector=status.phase=Running```


### Get ExternalIPs of all nodes
```kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'```

### Show labels for all pods (or any other Kubernetes object that supports labelling)
```kubectl get pods --show-labels```

### List all Secrets currently in use by a pod
```kubectl get pods -o json | jq '.items[].spec.containers[].env[]?.valueFrom.secretKeyRef.name'| grep -v null | sort | uniq```

### List Events sorted by timestamp
```kubectl get events --sort-by=.metadata.creationTimestamp```

# Updating resources

### Rolling update "www" containers of "frontend" deployment, updating the image
```kubectl set image deployment/frontend www=image:v2```

### Check the history of deployments including the revision
```kubectl rollout history deployment/frontend```

### Rollback to a specific revision
```kubectl rollout undo deployment/frontend --to-revision=2```


### to redeploy/restart a resource
```kubectl rollout restart RESOURCE [options]```  

-> It works by injecting an arbitrary timestamp into the Pod template metadata annotations. This causes the Pod spec to be different from what it was before, which causes a regular, rolling update of the Pods.


### Add a Label
```kubectl label pods my-pod new-label=awesome```                      

### Remove a label
```kubectl label pods my-pod new-label-``` 
                           

# Patching resources 


### Partially update a node
```kubectl patch node k8s-node-1 -p '{"spec":{"unschedulable":true}}'```

### Update a container's image; spec.containers[*].name is required because it's a merge key
```kubectl patch pod valid-pod -p '{"spec":{"containers":[{"name":"kubernetes-serve-hostname","image":"new image"}]}}'```

# Scaling resources

### Scale a replicaset named 'foo' to 3
```kubectl scale --replicas=3 rs/foo```                                 

### Scale a resource specified in "foo.yaml" to 3
```kubectl scale --replicas=3 -f foo.yaml```                            

### If the deployment named mysql's current size is 2, scale mysql to 3
```kubectl scale --current-replicas=2 --replicas=3 deployment/mysql```  

### Scale multiple replication controllers
```kubectl scale --replicas=5 rc/foo rc/bar rc/baz``` 
                  


# Deleting resources 


### Delete a pod using the type and name specified in pod.json
```kubectl delete -f ./pod.json```                                      

### Delete a pod with no grace period
```kubectl delete pod unwanted --now```                                 

Or:

```kubectl delete pods pod_name --grace-period=0```

### Delete pods and services with same names "baz" and "foo"
```kubectl delete pod,service baz foo```                                

### Delete pods and services with label name=myLabel
```kubectl delete pods,services -l name=myLabel```                      

### Delete all pods and services in namespace my-ns,
```kubectl -n my-ns delete pod,svc --all```                             

### Delete all pods matching the awk pattern1 or pattern2
```kubectl get pods  -n mynamespace --no-headers=true| awk '/pattern1|pattern2/{print $1}'| xargs  kubectl delete -n mynamespace pod```


# Interacting with running Pods 


### dump pod logs, with label name=myLabel (stdout)
```kubectl logs -l name=myLabel```                         

### dump pod logs (stdout) for a previous instantiation of a container
```kubectl logs my-pod --previous```                      

### stream pod logs (stdout)
```kubectl logs -f my-pod``` 

### Start a single instance of nginx pod in the namespace of mynamespace
```kubectl run nginx --image=nginx -n mynamespace```      

### Generate spec for running pod nginx and write it into a file called pod.yaml
```kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml```

### Attach to Running Container
```kubectl attach my-pod -i```                            

### Run command in existing pod (1 container case)
```kubectl exec my-pod -- ls /```                         

# Interactive shell access to a running pod (1 container case)
```kubectl exec --stdin --tty my-pod -- /bin/sh```        

### Run command in existing pod (multi-container case)
```kubectl exec my-pod -c my-container -- ls /```  
       


# Copying files and directories to and from containers 

Note: kubectl cp requires that the 'tar' binary is present in your container image. If 'tar' is not present, kubectl cp will fail

### Copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
```kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir```            

### Copy /tmp/foo local file to /tmp/bar in a remote pod in a specific container
```kubectl cp /tmp/foo my-pod:/tmp/bar -c my-container```    

### Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
```kubectl cp /tmp/foo my-namespace/my-pod:/tmp/bar```       

### Copy /tmp/foo from a remote pod to /tmp/bar locally
```kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar```  


# Interacting with Deployments and Services 

### dump Pod logs for a Deployment (single-container case)
```kubectl logs deploy/my-deployment```                         

### dump Pod logs for a Deployment (multi-container case)
```kubectl logs deploy/my-deployment -c my-container```   
      

# Interacting with Nodes and cluster 

### Mark my-node as unschedulable
```kubectl cordon my-node```                                               

### Drain my-node in preparation for maintenance
```kubectl drain my-node``` 

### Mark my-node as schedulable                                                
```kubectl uncordon my-node```   

### Show metrics for a given node
```kubectl top node my-node```                                                                                        

### Display addresses of the master and services
```kubectl cluster-info```   
                                               



# Kubectl output verbosity and debugging 

### Verbosity	Description
- ```--v=0```	Generally useful for this to always be visible to a cluster operator.
- ```--v=1```	A reasonable default log level if you don't want verbosity.
- ```--v=2```	Useful steady state information about the service and important log messages that may correlate to significant changes in the system. This is the recommended default log level for most systems.
- ```--v=3```	Extended information about changes.
- ```--v=4```	Debug level verbosity.
- ```--v=5```	Trace level verbosity.
- ```--v=6```	Display requested resources.
- ```--v=7```	Display HTTP request headers.
- ```--v=8```	Display HTTP request contents.
- ```--v=9```	Display HTTP request contents without truncation of contents.

