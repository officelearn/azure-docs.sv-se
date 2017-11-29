## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#create). 

Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Kommandot anger också *azureuser* som ett administratörsanvändarnamn. Du kan använda det här namnet senare för att ansluta till den virtuella datorn. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAddress`. Den här adressen används för åtkomst till den virtuella datorn i senare steg.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Som standard tillåts endast SSH-anslutningar till Linux virtuella datorer som distribueras i Azure. Eftersom den här virtuella datorn kommer att vara en webbserver, måste du öppna port 80 från internet. Använd kommandot [az vm open-port](/cli/azure/vm#open-port) för att öppna önskad port.  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn


Om du inte redan vet offentliga IP-adressen för den virtuella datorn kan köra den [az offentliga ip-lista över](/cli/azure/network/public-ip#list) kommando. Du behöver den här IP-adressen för flera senare steg.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt rätt offentliga IP-adressen för den virtuella datorn. I det här exemplet IP-adressen är *40.68.254.142*. *azureuser* anges administratörsanvändarnamn när du skapade den virtuella datorn.

```bash
ssh azureuser@40.68.254.142
```

