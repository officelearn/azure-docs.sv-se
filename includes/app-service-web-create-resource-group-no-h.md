Skapa i Cloud Shell en resursgrupp med kommandot [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *Europa, västra*. Om du vill se alla platser som stöds för App Service kör du kommandot `az appservice list-locations`.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. 
