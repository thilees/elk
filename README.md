# ELK stack on a Kubernetes cluster: A hard way

## What is this about?
These are Kubernetes manifests, drafted without using any readymade scripts, in order to create an ELK stack with Elasticsearch, Logstash and Kibana services. 

Please note, however, that these manifests should not be used for production workloads since this was created for educational purpose. 

And also note that no shipper, such as Filebeat, has not been included in these manifests in order to ship logs to Logstash. Nevertheless, we would be able to test the cluster by pointing Logstash to read the logs from as described in the section at the bottom.

Is there any prerequisite?
An access to a Kubernetes cluster on Google Cloud Platform (GLP) with the version v1.10 or later.

## How to use the manifests to create the stack?
1. Download the scripts or clone the repo

2. Pass the manifests to kubectl in the order. i.e.
   ```
   kubectl  create -f  1.namespace.yaml
   kubectl  create -f  2.rbac.yaml
   kubectl  create -f  3.es.yaml
   kubectl  create -f  4.kibana.yaml
   kubectl  create -f  5.logstash.yaml
   ```

3. Make sure that all the services are up and running 
   ```
   Kubectl get pods -n elk
   ```

## How to verify the the stack?
1. Get the loadbalancer IP address for Kibana services and copy it
   '''
   kubectl get services kibana -o wide
   '''

2. Render the Kibana GUI by pasting the IP on your browser 
 
3. Generate some logs in Logstash to export the logs to Elasticsearch
   - Get a terminal to Kibana pod: 
     ```
     kubectl exec -it <<kibana-pod-name->> -n elk sh
     ```
   - Navigate to: /usr/share/logstash
   - Create a file sample.log and write some log data on it.

4. Go and create index in elasticsearch via Kibana web interface to retrieve the log you have written on /usr/share/logstash/sample.log

