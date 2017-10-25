Skapa en App Service-plan i Cloud Shell med kommandot [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan-linux.md)]

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` i prisnivån **Standard** och i en Linux-behållare:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```