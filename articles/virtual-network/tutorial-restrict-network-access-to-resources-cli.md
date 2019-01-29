---
title: Begränsa nätverksåtkomst till PaaS-resurser – Azure CLI | Microsoft Docs
description: I den här artikeln lär du dig att begränsa nätverksåtkomst till Azure-resurser, till exempel Azure Storage och Azure SQL Database med virtuella nätverksslutpunkter med Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 80ca9df064239e9c7beb9d45acfabe963c532e4a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150556"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Begränsa nätverksåtkomst till PaaS-resurser med virtuella nätverksslutpunkter med Azure CLI

Med virtuella nätverksslutpunkter kan du begränsa nätverksåtkomsten till vissa Azure-tjänsters resurser till ett undernät för virtuella datorer. Du kan också ta bort resursernas internetåtkomst. Tjänstslutpunkterna möjliggör direktanslutning från ditt virtuella nätverk till Azure-tjänster som stöds, så att du kan använda det privata adressutrymmet i det virtuella nätverket för åtkomst till Azure-tjänsterna. Trafik till Azure-resurser genom tjänstslutpunkterna finns alltid kvar i Microsoft Azure-stamnätverket. I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk med ett undernät
* Lägga till ett undernät och aktivera en tjänstslutpunkt
* Skapa en Azure-resurs och tillåt nätverksåtkomst till den från enbart ett undernät
* Distribuera en virtuell dator (VM) till varje undernät
* Bekräfta åtkomst till en resurs från ett undernät
* Bekräfta att åtkomst nekas för en resurs från ett undernät och internet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.28 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Du måste skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapats i den här artikeln innan du skapar ett virtuellt nätverk. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa ett virtuellt nätverk med ett undernät med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt 

Du kan aktivera Tjänsteslutpunkter bara för tjänster som stöder Tjänsteslutpunkter. Visa tjänstens slutpunkt-aktiverade tjänster som är tillgängliga i en Azure-plats med [az network vnet list-endpoint-services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). I följande exempel returneras en lista över service-slutpunkt-aktiverade tjänster som är tillgängliga i den *eastus* region. I listan över tjänster som returneras kommer att växa med tiden när flera Azure-tjänster blir tjänstslutpunkt aktiverad.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Skapa ett ytterligare undernät i det virtuella nätverket med [az network vnet-undernät skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). I det här exemplet, en tjänstslutpunkt för *Microsoft.Storage* har skapats för undernätet: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomst för ett undernät

Skapa en nätverkssäkerhetsgrupp med [az network nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associera nätverkssäkerhetsgruppen till den *privata* undernätet med [az network vnet-undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). I följande exempel kopplar den *myNsgPrivate* nätverkssäkerhetsgrupp till det *privata* undernät:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Skapa säkerhetsregler med [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Regel som tillåter utgående åtkomst till de offentliga IP-adresser tilldelade till Azure Storage-tjänsten: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomst till en resurs

De steg som behövs för att begränsa nätverksåtkomsten till resurser som har skapats via Azure-tjänster som är aktiverade för tjänstslutpunkter varierar från tjänst till tjänst. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som exempel.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett Azure storage-konto med [az storage-konto skapar](/cli/azure/storage/account#az_storage_account_create). Ersätt `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt i alla Azure-platser, mellan 3 och 24 tecken långt, med hjälp av endast siffror och gemener.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

När lagringskontot har skapats kan du hämta anslutningssträngen för lagringskontot i en variabel med [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string). Strängen som används för att skapa en filresurs i ett senare steg.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visa innehållet i variabeln och anteckna värdet för **AccountKey** returneras i resultatet, eftersom den används i ett senare steg.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

Skapa en filresurs i storage-konto med [az-lagringsresursen skapa](/cli/azure/storage/share). Den här filresursen är monterad för att bekräfta nätverksåtkomst till den i ett senare steg.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Neka alla åtkomst till ett lagringskonto

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. För att begränsa åtkomsten till valda nätverk, ändrar du åtgärden du *neka* med [az storage-konto uppdatering](/cli/azure/storage/account#az_storage_account_update). Lagringskontot är inte tillgänglig från alla nätverk när nätverksåtkomst nekas.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Tillåt nätverksåtkomst till storage-konto från den *privata* undernätet med [az storage-konto-regel för Lägg till](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Om du vill testa nätverksåtkomsten till ett lagringskonto distribuerar du en virtuell dator till varje undernät.

### <a name="create-the-first-virtual-machine"></a>Skapa din första virtuella dator

Skapa en virtuell dator i den *offentliga* undernätet med [az vm skapa](/cli/azure/vm#az_vm_create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information liknande följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna den **publicIpAddress** i returnerade utdata. Den här adressen används för att få åtkomst till den virtuella datorn från internet i ett senare steg.

### <a name="create-the-second-virtual-machine"></a>Skapa den andra virtuella datorn

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat, anteckna den **publicIpAddress** i utdata som returneras. Den här adressen används för att få åtkomst till den virtuella datorn från internet i ett senare steg.

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

SSH till den *myVmPrivate* VM. Ersätt *<publicIpAddress>* med den offentliga IP-adressen för din *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Skapa en mapp för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Montera Azure-filresursen till den katalog som du skapade. Innan du kör följande kommando ersätter `<storage-account-name>` med kontonamn och `<storage-account-key>` med nyckeln som du hämtade i [skapa ett lagringskonto](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Du får den `user@myVmPrivate:~$` prompten. Azure-filresursen monterade till */mnt/MyAzureFileShare*.

Bekräfta att den virtuella datorn inte har någon utgående anslutning till alla andra offentliga IP-adresser:

```bash
ping bing.com -c 4
```

Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplad till det *privata* undernätet inte tillåter utgående åtkomst till offentliga IP-adresser förutom de adresser som är kopplade till Azure Storage-tjänsten.

Avsluta SSH-sessionen till den *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

Använd följande kommando för att skapa en SSH-session med den *myVmPublic* VM. Ersätt `<publicIpAddress>` med den offentliga IP-adressen för din *myVmPublic* VM: 

```bash 
ssh <publicIpAddress>
```

Skapa en katalog för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Försök att montera Azure-filresursen till den katalog som du skapade. Den här artikeln förutsätter att du har distribuerat den senaste versionen av Ubuntu. Om du använder tidigare versioner av Ubuntu [montera i Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för ytterligare information om hur du monterar filresurser. Innan du kör följande kommando ersätter `<storage-account-name>` med kontonamn och `<storage-account-key>` med nyckeln som du hämtade i [skapa ett lagringskonto](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Åtkomst nekas och du får en `mount error(13): Permission denied` fel, eftersom den *myVmPublic* virtuella datorn har distribuerats inom den *offentliga* undernät. Det *offentliga* undernätet har ingen tjänstslutpunkt aktiverad för Azure Storage, och lagringskontot tillåter endast nätverksåtkomst från det *privata* undernätet, inte det *offentliga*.

Avsluta SSH-sessionen till den *myVmPublic* VM.

Försök att visa resurserna i ditt storage-konto med från datorn, [az storage share lista](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Ersätt `<account-name>` och `<account-key>` med lagringskontonamnet och nyckeln från [skapa ett lagringskonto](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Åtkomst nekas och du får en *denna begäran har inte behörighet att utföra den här åtgärden* fel, eftersom datorn inte är i den *privata* undernät för den *MyVirtualNetwork* virtuellt nätverk.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [az group delete](/cli/azure#az_group_delete) att ta bort resursgruppen och alla resurser den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har aktiverat du en tjänstslutpunkt för ett virtuellt nätverksundernät. Du har lärt dig att tjänstslutpunkter kan aktiveras för resurser som distribueras med flera Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsat nätverksåtkomst för lagringskontot till enbart resurser inom ett undernät för ett virtuellt nätverk. Om du vill veta mer om tjänstslutpunkter går du till [Översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md) och [Hantera undernät](virtual-network-manage-subnet.md).

Om du har flera virtuella nätverk i ditt konto kanske du vill ansluta två virtuella nätverk så att resurserna i vart och ett kan kommunicera med varandra. Läs hur genom att läsa [ansluta virtuella nätverk](tutorial-connect-virtual-networks-cli.md).
