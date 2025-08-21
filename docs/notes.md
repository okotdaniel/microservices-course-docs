
volumes 

Kubernetes supports many types of volumes. A Pod can use any number of volume types simultaneously. 

Ephemeral volume types have a lifetime linked to a specific Pod, but persistent volumes exist beyond the lifetime of any individual pod. When a pod ceases to exist,

Kubernetes destroys ephemeral volumes; however, Kubernetes does not destroy persistent volumes. For any kind of volume in a given pod, data is preserved across container restarts.

A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV

A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany, ReadWriteMany, or ReadWriteOncePod, see AccessModes).


<!-- setting uo mongodb with the command line locally  -->
So lets assume that you want to use mongodb locally on your machine 
typically you would run the following command in the terminal

``` bash
docker run -d --name mongo \
    e MONGO_INITDB_ROOT_USERNAME = user1 \
    e MONGO_INITDB_ROOT_PASSWORD = password \
    p 1207 
```
With this option, you can only work with the terminal. in real world, you would need to write a script that takes care of this. and thats when compose files come into the picture. with all the 
commands written in the terminal, one can be able to replciate the same using a compose file
this is how the above command would transalate into a a compose file as shown bwllow.

so lets start by creating a docker compose file 
``` bash
    touch compose.yaml
```
In the compose.yaml file, we shall then make sure that we define our appropriate configuration to 
work with the mongodb docker image that we want 
``` bash
    apiVersion: v1
    kind: Deployment
    metadata:
        name: deployment
        label:
            app: mongo
    spec:
        replicas: 1
        selector:
            matchLabels:
                app: mongo
        template:
            metadata:
                name: mongo
                app: mongo
            spec:
                containers:
                  - name: mongo
                   image: mongo:latest
                   ports:
                    containerPort: 8000
                   env:
                    - name: MONGO_INITDB_ROOT_USER_NAME
                      valueFrom:
                        configMapRef:
                            name: configmap
                            key: MONGO_INITDB_ROOT_USER_NAME
                    - name: MONGO_INITDB_ROOT_PASSWORD
                      valueFrom:
                        configMapRef:
                            name: configmap
                            key: MONGO_INITDB_ROOT_PASSWORD
                volumeMounts:
                    - name: mongo-storage
                    mountPath: /data/db
      volumes:
      - name: mongo-storage
        persistentVolumeClaim:
          claimName: mongo-pvc

```
so let me explain whats going on here.

on the comman that we saw earlier when running a mongodb on our terminal, we came across environent
variables that we shall cover in the sections bellow down here in a bit. 

