Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. Om du vill se alla platser som stöds för Azure Web Apps kör du kommandot `az appservice list-locations`. 