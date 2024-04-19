# Deployment for testing proxy and custom certs using curl command.

What we need before we start:
- proxy server url, if present
- One file containing all CA certs(trust chain) concatinated as ca.crt
- Create a configMap from ca.crt: ```kubectl create cm ca-certs --from-file ca.crt```
- List of proxy and no-proxy targets in the customer environment

The procedure for testing proxy and/or custom CAs is as follows:
1. Edit and create ssd-proxy-certs configMAp in the default namespace. ```kubectl apply -f ssd-proxy-certs.yaml```
2. Create deployment: ```kubectl apply -f proxy-test-dep.yaml```
3. Wait for the pod to come up: ```kubectl get po | grep proxy-test```
4. Exec into the pod: ```kubectl exec -it <POD-NAME> sh```
5. execute curl commands to the expected destinations: ```curl -vvv https://MY-SERVER_HOSTNAME```


If all goes well, i.e. all responses are 200/404, etc. we can confirm that the connection is working. If there are any errors, update the ssd-proxy-certs.yaml and restart the pod using:
```kubectl rollout restart deploy proxy-test```

Ensure that we are testing one proxy and one no-proxy target at a minimum. Ideally we should check out ALL targets. Depending on how the cluster is configured, services within the cluster/namespace may or
may not require proxy configuration. Ensure that no-proxy is configured for all the ISD/SSD services.

SAMPLE for ISD/Spinnaker Services JAVA_OPTS is available **here**
