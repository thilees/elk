# PV for ES
apiVersion: v1
kind: PersistentVolume
metadata:
 name: esvol
 namespace: elk
spec:
 storageClassName: manual
 capacity:
  storage: 10Gi 
 accessModes:
 - ReadWriteOnce 
 hostPath:
  path: "data"

---
# PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: esvol
  namespace: elk
spec:
 storageClassName: manual
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 3Gi

---
# StatefulSet for elasticservice service 
apiVersion: apps/v1
kind: StatefulSet
metadata:
 name: es
 namespace: elk
 labels:
  app: es
spec:
 serviceName: elasticsearch
 replicas: 3
 updateStrategy:
  type: RollingUpdate
 selector:
  matchLabels:
   app: es
 template:
  metadata:
   labels:
    app: es
  spec:
   serviceAccountName: es
   initContainers:
   - image: alpine:3.6
     name: es-init
     command: ["/sbin/sysctl", "-w", "vm.max_map_count=262144"]
     securityContext:
      privileged: true
   containers:
   - image: elasticsearch:6.8.6
     name: es
     ports:
     - name: backendport
       containerPort: 9200
       protocol: TCP
     - name: transportport
       containerPort: 9300
       protocol: TCP
     volumeMounts:
     - name: datavolume
       mountPath: /data
     env:
     - name: cluster.name
       value: "logstore"
     - name: discovery.zen.ping.unicast.hosts
       value: "es-0.elasticsearch,es-1.elasticsearch,es-2.elasticsearch"
     - name: discovery.zen.minimum_master_nodes
       value: "2"
     - name: "DISCOVERY_SERVICE"
       value: "elasticsearch"
   volumes:
   - name: datavolume
     persistentVolumeClaim:
      claimName: esvol
  
---
# Service for elasticsearch
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch
  namespace: elk
spec:
  selector:
   app: es
  clusterIP: None
  ports:
  - name: gui
    port: 9200
    targetPort: 9200
  - name: transport
    port: 9300
    targetPort: 9300
