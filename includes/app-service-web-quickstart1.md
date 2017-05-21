## <a name="create-a-resource-group"></a>Skapa en resursgrupp

 En [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) är en logisk behållare i vilken resurser som webbappar och databaser hanteras. Du kan distribuera, uppdatera och ta bort resurser i en resursgrupp.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Om du vill se tillgängliga platser använder du kommandot `az appservice list-locations`. Du skapar vanligtvis resurser i en region nära dig.

## <a name="create-an-azure-app-service-plan"></a>Skapa en Azure App Service-plan

Skapa en kostnadsfri [App Service-plan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) med kommandot [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

Följande kommando skapar en App Service-plan med namnet `quickStartPlan` och prisnivån **Kostnadsfri**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Skapa en webbapp