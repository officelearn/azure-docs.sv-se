---
title: Begränsa dataexfiltration till Azure Storage - Azure CLI
description: I den här artikeln får du lära dig hur du begränsar och begränsar virtuell nätverksdataexfiltration till Azure Storage-resurser med slutpunktsprinciper för virtuella nätverkstjänster med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271190"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Hantera dataexfiltration till Azure Storage-konton med slutpunktsprinciper för virtuella nätverkstjänster med Hjälp av Azure CLI

Med slutpunktsprinciper för virtuella nätverkstjänster kan du tillämpa åtkomstkontroll på Azure Storage-konton inifrån ett virtuellt nätverk över tjänstslutpunkter. Detta är en nyckel till att skydda dina arbetsbelastningar, hantera vilka lagringskonton som är tillåtna och var dataexfiltration är tillåtet.
I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk och lägg till ett undernät.
* Aktivera tjänstslutpunkt för Azure Storage.
* Skapa två Azure Storage-konton och tillåt nätverksåtkomst till det från undernätet som skapats ovan.
* Skapa en princip för tjänstslutpunkt för att endast tillåta åtkomst till ett av lagringskontona.
* Distribuera en virtuell dator (VM) till undernätet.
* Bekräfta åtkomst till det tillåtna lagringskontot från undernätet.
* Bekräfta åtkomst nekas till det icke tillåtna lagringskontot från undernätet.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt 

I det här exemplet skapas en tjänstslutpunkt för *Microsoft.Storage* för undernätet *Privat:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Begränsa nätverksåtkomsten till Azure Storage-konton

I det här avsnittet visas steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto från det angivna undernätet i ett virtuellt nätverk via tjänstslutpunkten.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa två Azure-lagringskonton med [az storage-konto skapa](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

När lagringskontona har skapats hämtar du anslutningssträngen för lagringskontona till en variabel med [az storage-account show-connection-string](/cli/azure/storage/account). Anslutningssträngen används för att skapa en filresurs i ett senare steg.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visa innehållet i variabeln och notera värdet för **AccountKey** som returneras i utdata, eftersom det används i ett senare steg.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

Skapa en filresurs i lagringskontot med [az storage share create](/cli/azure/storage/share). I ett senare steg är den här filresursen monterad för att bekräfta nätverksåtkomst till den.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Neka all nätverksåtkomst till lagringskontot

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standardåtgärden till *Neka* med [uppdatering av az-lagringskonto](/cli/azure/storage/account). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Aktivera nätverksåtkomst från virtuellt nätverksundernät

Tillåt nätverksåtkomst till lagringskontot från det *privata* undernätet med [ett az-lagringskontonätverksregeltillägg](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Tillämpa principen för att tillåta åtkomst till giltigt lagringskonto

Azure Service Slutpunktsprinciper är endast tillgängliga för Azure Storage. Vi aktiverar därför tjänstslutpunkten för *Microsoft.Storage* i det här undernätet för den här exempelkonfigurationen.

Tjänstslutpunktsprinciper tillämpas över tjänstslutpunkter. Vi börjar med att skapa en princip för tjänstslutpunkt. Vi skapar sedan principdefinitionerna under den här principen för Att Azure Storage-konton ska vitlistas för det här undernätet

Skapa en princip för tjänstslutpunkt

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Spara resurs-URI för det tillåtna lagringskontot i en variabel. Innan du kör kommandot nedan ersätter du * \<ditt prenumerations-ID>* med det faktiska värdet av ditt prenumerations-ID.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Skapa & lägga till en principdefinition för att tillåta ovanstående Azure Storage-konto i tjänstslutpunktsprincipen

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Och uppdatera det virtuella nätverksundernätet för att associera den tjänstslutpunktsprincipen som skapades i föregående steg

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validera åtkomstbegränsning till Azure Storage-konton

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Om du vill testa nätverksåtkomst till ett lagringskonto distribuerar du en virtuell dator till undernätet.

Skapa en virtuell dator i det *privata* undernätet med [az vm create](/cli/azure/vm). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

SSH i *myVmPrivate* VM. Ersätt * \<publicIpAddress>* med den offentliga IP-adressen för din *virtuella myVmPrivate-dator.*

```bash 
ssh <publicIpAddress>
```

Skapa en mapp för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Montera Azure-filresursen till den katalog som du skapade. Innan du kör kommandot nedan ersätter du * \<lagrings-konto-nyckel>* med värdet *för AccountKey* från **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Du får `user@myVmPrivate:~$` uppmaningen. Filresursen i Azure har monterats på */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

Från samma virtuella dator *myVmPrivate*, skapa en katalog för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Försök att montera Azure-filresursen från *lagringskontot som inte tillåtsstoracc* till den katalog som du skapade. Den här artikeln förutsätter att du har distribuerat den senaste versionen av Ubuntu. Om du använder tidigare versioner av Ubuntu läser du [Montera på Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för ytterligare instruktioner om montering av filresurser. 

Innan du kör kommandot nedan ersätter du * \<lagrings-konto-nyckel>* med värdet *för AccountKey* från **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Åtkomst nekas och du `mount error(13): Permission denied` får ett felmeddelande eftersom det här lagringskontot inte finns i listan över tjänstslutpunktsprincipen som vi tillämpade på undernätet. 

Avsluta SSH-sessionen till *myVmPublic-virtuella* datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs använder du [az-gruppborttagning](/cli/azure) för att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tillämpat en princip för tjänstslutpunkt över en Azure-slutpunkt för virtuella nätverkstjänsten på Azure Storage. Du skapade Azure Storage-konton och begränsad nätverksåtkomst till endast vissa lagringskonton (och därmed nekat andra) från ett virtuellt nätverksundernät. Mer information om tjänstslutpunktsprinciper finns i Översikt över principer för [tjänstslutpunkter](virtual-network-service-endpoint-policies-overview.md).
