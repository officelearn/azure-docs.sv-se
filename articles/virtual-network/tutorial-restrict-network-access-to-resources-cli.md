---
title: Begränsa nätverksåtkomsten till PaaS-resurser – Azure CLI
description: I den här artikeln får du lära dig hur du begränsar och begränsar nätverksåtkomst till Azure-resurser, till exempel Azure Storage och Azure SQL Database, med slutpunkter för virtuella nätverkstjänster med Hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f2dcc714bc9052dd51f114e24f0b9bd74b87480c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186406"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Begränsa nätverksåtkomsten till PaaS-resurser med slutpunkter för virtuella nätverkstjänster med Hjälp av Azure CLI

Med tjänstslutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till vissa Azure-tjänsters resurser till ett undernät för virtuella datorer. Du kan också ta bort resursernas internetåtkomst. Tjänstslutpunkterna möjliggör direktanslutning från ditt virtuella nätverk till Azure-tjänster som stöds, så att du kan använda det privata adressutrymmet i det virtuella nätverket för åtkomst till Azure-tjänsterna. Trafik till Azure-resurser genom tjänstslutpunkterna finns alltid kvar i Microsoft Azure-stamnätverket. I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk med ett undernät
* Lägga till ett undernät och aktivera en tjänstslutpunkt
* Skapa en Azure-resurs och tillåt nätverksåtkomst till den från enbart ett undernät
* Distribuera en virtuell dator (VM) till varje undernät
* Bekräfta åtkomst till en resurs från ett undernät
* Bekräfta att åtkomst nekas för en resurs från ett undernät och internet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.28 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa ett virtuellt nätverk med ett undernät med [az nätverk vnet skapa](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt 

Du kan bara aktivera tjänstslutpunkter för tjänster som stöder tjänstslutpunkter. Visa tjänstslutpunktsaktiverade tjänster som är tillgängliga på en Azure-plats med [az network vnet list-endpoint-services](/cli/azure/network/vnet). I följande exempel returneras en lista över tjänstslutpunktsaktiverade tjänster som är tillgängliga i *regionen eastus.* Listan över returnerade tjänster kommer att växa med tiden, eftersom fler Azure-tjänster blir tjänstslutpunkt aktiverad.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Skapa ytterligare ett undernät i det virtuella nätverket med [az-nätverk vnet undernät skapa](/cli/azure/network/vnet/subnet). I det här exemplet skapas en tjänstslutpunkt för *Microsoft.Storage* för undernätet: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomst för ett undernät

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associera nätverkssäkerhetsgruppen till det *privata* undernätet med [uppdatering av az-nätverksnätsnät](/cli/azure/network/vnet/subnet). I följande exempel associeras säkerhetsgruppen *myNsgPrivate-nätverk* till det *privata* undernätet:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Skapa säkerhetsregler med [az network nsg-regel skapa](/cli/azure/network/nsg/rule). Regeln som följer tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats Azure Storage-tjänsten: 

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
```

Varje nätverkssäkerhetsgrupp innehåller flera [standardsäkerhetsregler](security-overview.md#default-security-rules). Regeln som följer åsidosätter en standardsäkerhetsregel som tillåter utgående åtkomst till alla offentliga IP-adresser. Alternativet `destination-address-prefix "Internet"` nekar utgående åtkomst till alla offentliga IP-adresser. Den tidigare regeln åsidosätter den här regeln på grund av dess högre prioritet, vilket ger åtkomst till de offentliga IP-adresserna för Azure Storage.

```azurecli-interactive
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
```

Följande regel tillåter SSH-trafik som kommer in till undernätet var som helst. Regeln åsidosätter en standardsäkerhetsregel som nekar all inkommande trafik från internet. SSH tillåts till undernätet så att anslutningen kan testas i ett senare steg.

```azurecli-interactive
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

De steg som behövs för att begränsa nätverksåtkomsten till resurser som har skapats via Azure-tjänster som är aktiverade för tjänstslutpunkter varierar från tjänst till tjänst. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som ett exempel.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett Azure-lagringskonto med [az storage-konto skapa](/cli/azure/storage/account). Ersätt `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt på alla Azure-platser, mellan 3-24 tecken i längd, med endast siffror och gemener.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

När lagringskontot har skapats hämtar du anslutningssträngen för lagringskontot till en variabel med [az storage-account show-connection-string](/cli/azure/storage/account). Anslutningssträngen används för att skapa en filresurs i ett senare steg.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visa innehållet i variabeln och notera värdet för **AccountKey** som returneras i utdata, eftersom det används i ett senare steg.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

Skapa en filresurs i lagringskontot med [az storage share create](/cli/azure/storage/share). I ett senare steg är den här filresursen monterad för att bekräfta nätverksåtkomst till den.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Neka all nätverksåtkomst till ett lagringskonto

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standardåtgärden till *Neka* med [uppdatering av az-lagringskonto](/cli/azure/storage/account). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Tillåt nätverksåtkomst till lagringskontot från det *privata* undernätet med [ett az-lagringskontonätverksregeltillägg](/cli/azure/storage/account/network-rule).

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

Skapa en virtuell dator i det *offentliga* undernätet med [az vm create](/cli/azure/vm). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information som liknar följande exempel: 

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

Ta del av **publicIpAddress** i den returnerade utdata. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

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

Det tar några minuter att skapa den virtuella datorn. När du har skapat det, ta del av **publicIpAddress** i utdata som returneras. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

SSH i *myVmPrivate* VM. Ersätt * \<publicIpAddress>* med den offentliga IP-adressen för din *virtuella myVmPrivate-dator.*

```bash 
ssh <publicIpAddress>
```

Skapa en mapp för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Montera Azure-filresursen till den katalog som du skapade. Innan du kör följande `<storage-account-name>` kommando ersätter `<storage-account-key>` du med kontonamnet och med nyckeln som du hämtade i [Skapa ett lagringskonto](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Du får `user@myVmPrivate:~$` uppmaningen. Filresursen i Azure har monterats på */mnt/MyAzureFileShare*.

Bekräfta att den virtuella datorn inte har någon utgående anslutning till andra offentliga IP-adresser:

```bash
ping bing.com -c 4
```

Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplad till det *privata* undernätet inte tillåter utgående åtkomst till offentliga IP-adresser förutom de adresser som är kopplade till Azure Storage-tjänsten.

Avsluta SSH-sessionen till *den virtuella datorn myVmPrivate.*

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

Använd följande kommando för att skapa en SSH-session med *den virtuella datorn myVmPublic.* Ersätt `<publicIpAddress>` med den offentliga IP-adressen för din *myVmPublic* VM: 

```bash 
ssh <publicIpAddress>
```

Skapa en katalog för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Försök att montera Azure-filresursen i katalogen som du skapade. Den här artikeln förutsätter att du har distribuerat den senaste versionen av Ubuntu. Om du använder tidigare versioner av Ubuntu läser du [Montera på Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för ytterligare instruktioner om montering av filresurser. Innan du kör följande `<storage-account-name>` kommando ersätter `<storage-account-key>` du med kontonamnet och med nyckeln du hämtade i [Skapa ett lagringskonto:](#create-a-storage-account)

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Åtkomst nekas och du `mount error(13): Permission denied` får ett felmeddelande eftersom *myVmPublic-virtuella* datorn distribueras i det *offentliga* undernätet. Det *offentliga* undernätet har ingen tjänstslutpunkt aktiverad för Azure Storage, och lagringskontot tillåter endast nätverksåtkomst från det *privata* undernätet, inte det *offentliga*.

Avsluta SSH-sessionen till *myVmPublic-virtuella* datorn.

Från datorn försöker du visa resurserna i ditt lagringskonto med [az storage share list](/cli/azure/storage/share?view=azure-cli-latest). Ersätt `<account-name>` `<account-key>` och med lagringskontots namn och nyckel från [Skapa ett lagringskonto:](#create-a-storage-account)

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Åtkomst nekas och du får en *Den här begäran har inte behörighet att utföra det här åtgärdsfelet,* eftersom datorn inte finns i det *privata* undernätet i det virtuella nätverket *MyVirtualNetwork.*

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs använder du [az-gruppborttagning](/cli/azure) för att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat en tjänstslutpunkt för ett virtuellt nätverksundernät. Du har lärt dig att tjänstslutpunkter kan aktiveras för resurser som distribueras med flera Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsat nätverksåtkomst för lagringskontot till enbart resurser inom ett undernät för ett virtuellt nätverk. Om du vill veta mer om tjänstslutpunkter går du till [Översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md) och [Hantera undernät](virtual-network-manage-subnet.md).

Om du har flera virtuella nätverk i ditt konto kanske du vill ansluta två virtuella nätverk så att resurserna i vart och ett kan kommunicera med varandra. Mer information finns i [Ansluta virtuella nätverk](tutorial-connect-virtual-networks-cli.md).
