## <a name="prerequisites"></a>Krav

Om du inte redan har gjort det så ska du hämta en [kostnadsfri utvärderingsversion av en prenumeration på Azure](https://azure.microsoft.com/pricing/free-trial/) och installerad [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>Skapa skalningsuppsättningen

Börja med att skapa en resursgrupp som du kan distribuera skalningsuppsättningen till:

```azurecli
az group create --location westus --name myResourceGroup
```

Skapa skalningsuppsättningen med hjälp av `az vmss create`-kommandot. I följande exempel skapas en Linux-skalningsuppsättning med namnet `myvmss` i resursgruppen med namnet `myrg`:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Följande exempel skapar en Windows-skalningsuppsättning med samma konfiguration:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Om du vill välja en annan operativsystemsavbildning kan du se tillgängliga avbildningar med kommandot `az vm image list` eller `az vm image list --all`. Om du vill se anslutningsinformationen för de virtuella datorerna i skalningsuppsättningen använder du kommandot `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```