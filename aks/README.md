### Prequisites

> Note: execute the following prequisite steps from VSCode for a better experience

1. provision [a regional hub and a spoke virtual networks](./secure-baseline/networking/network-deploy.azcli)
2. create [the BU 0001's app team secure AKS cluster (ID: A0008)](./secure-baseline/network-deploy.azcli)
3. query the BU 0001's Azure Application Gateway Public Ip FQDN
   ``` bash
   export APP_GATEWAY_PUBLIC_IP_FQDN=$(az group deployment show --resource-group rg-bu0001a0008 -n cluster-stamp --query properties.outputs.appGatewayPublicIpFqdn.value -o tsv)
   ```

### Deploy a basic workload

The following example creates the ASPNET Core Docker sample web app and an Ingress object to route to its service.

```bash
# Create application namespace
kubectl create ns a0008

# Apply the contents
kubectl apply -f https://github.com/mspnp/reference-architectures/tree/fcp/aks-baseline/aks/secure-baseline/workload/aspnetapp.yaml
```

Now the ASPNET Core webapp sample is all setup. Wait until is ready to process requests running:

```bash
kubectl wait --namespace a0008 \
  --for=condition=ready pod \
  --selector=app=aspnetapp \
  --timeout=90s
```

Test the web app

> open a browser and navigate to http://{APP_GATEWAY_PUBLIC_IP_FQDN}