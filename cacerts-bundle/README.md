## To create oes-cacerts secret, we start with an existing secret and import additional certs into it.
- This requires "keytool" command that comes with JDK (not JRE)
- Each cert loaded should have a differnent "alias" while importing and it should conflict with existing ones

```
Create the oes-certs secret
   kubectl apply -f oes-cacerts-secret.yaml
Get existing oes-cacert into a file
   kubectl get secrets oes-cacerts -o jsonpath='{.data.cacerts}' | base64 -d > cacerts
Import custom-ca.crt into cacerts
   keytool -import -trustcacerts -keystore cacerts -storepass changeit -noprompt -alias custom-self-signed -file custom-ca.crt
Re-create oes-cacerts
   kubectl delete secret oes-cacerts
   kubectl create secret generic oes-cacerts --from-file cacerts
```
