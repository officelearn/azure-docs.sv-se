---
title: Begränsa nätverksåtkomst till PaaS - resurser i Azure CLI | Microsoft Docs
description: I den här artikeln lär du dig att begränsa och begränsa nätverksåtkomst till Azure-resurser, till exempel Azure Storage och Azure SQL Database med virtuella nätverksslutpunkter med hjälp av Azure CLI.
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
ms.openlocfilehash: f357861a7a44b249e06f091a8693b7f2d8dd5178
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841987"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Begränsa nätverksåtkomst till PaaS-resurser med virtuella nätverksslutpunkter med hjälp av Azure CLI

Slutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till resurser i Azure-tjänsten som ett undernät för virtuellt nätverk. Du kan också ta bort internet-åtkomst till resurserna. Tjänsteslutpunkter ger direkt anslutning från det virtuella nätverket till stöds Azure-tjänster, så att du kan använda privata adressutrymmet för det virtuella nätverket för att komma åt Azure-tjänster. Trafik till Azure-resurser via Tjänsteslutpunkter alltid kvar på stamnät Microsoft Azure-nätverk. I den här artikeln får du lära dig hur du:

* Skapa ett virtuellt nätverk med ett undernät
* Lägg till ett undernät och aktivera en tjänstslutpunkt
* Skapa en Azure-resurs och ge åtkomst till nätverket till den från bara ett undernät
* Distribuera en virtuell dator (VM) till varje undernät
* Bekräfta åtkomst till en resurs från ett undernät
* Bekräfta att åtkomst nekas till en resurs från ett undernät och internet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt denna Snabbstart kräver att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

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

Du kan aktivera Tjänsteslutpunkter endast för tjänster som stöder slutpunkter. Visa tjänsten endpoint-aktiverade tjänster som är tillgängliga i en Azure-plats med [az nätverkstjänster vnet lista-slutpunkt-](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). I följande exempel returneras en lista över service-slutpunkt-aktiverade tjänster som är tillgängliga i den *eastus* region. Listan över tjänster som returneras kommer att växa med tiden när flera Azure-tjänster blir tjänstslutpunkten aktiverad.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Skapa en ytterligare undernät i det virtuella nätverket med [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). I det här exemplet, en tjänstslutpunkt för *Microsoft.Storage* skapas för undernätet: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomsten till ett undernät

Skapa en nätverkssäkerhetsgrupp med [az nätverket nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associera nätverkssäkerhetsgruppen till den *privata* undernät med [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). I följande exempel associerar den *myNsgPrivate* nätverkssäkerhetsgruppen till den *privata* undernät:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Skapa säkerhetsregler med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Regeln som följer tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats till tjänsten Azure Storage: 

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

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomsten till en resurs

Stegen för att begränsa nätverksåtkomsten till resurser som skapats via Azure-tjänster aktiverad för slutpunkter varierar tjänster. Finns i dokumentationen för enskilda tjänster för specifika steg för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som exempel.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett Azure storage-konto med [az storage-konto skapar](/cli/azure/storage/account#az_storage_account_create). Ersätt `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt för alla Azure platser mellan 3 till 24 tecken, med endast siffror och gemener.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

När lagringskontot har skapats kan du hämta anslutningssträngen för lagringskontot i en variabel med [az lagring konto visa anslutningssträng](/cli/azure/storage/account#az_storage_account_show_connection_string). Anslutningssträngen används för att skapa en filresurs i ett senare steg.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visa innehållet i variabeln och anteckna värdet för **AccountKey** returneras i utdata, eftersom den används i ett senare steg.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i storage-konto

Skapa en filresurs i lagringskontot med [az lagringsresurs skapa](/cli/azure/storage/share#az_storage_share_create). Filresursen är monterad för att bekräfta nätverksåtkomst till den i ett senare steg.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Neka alla nätverksåtkomst till ett lagringskonto

Storage-konton godkänna anslutningar från klienter i ett nätverk som standard. Om du vill begränsa åtkomsten till valda nätverken, ändra standardåtgärden att *neka* med [az lagring uppdatering](/cli/azure/storage/account#az_storage_account_update). Lagringskontot är inte tillgänglig från alla nätverk när nätverksåtkomst nekas.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Tillåt nätverksåtkomst till storage-konto från den *privata* undernät med [az lagring kontot network-regel lägga](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Distribuera en virtuell dator till varje undernät för att testa nätverksåtkomst till ett lagringskonto.

### <a name="create-the-first-virtual-machine"></a>Skapa den första virtuella datorn

Skapa en virtuell dator i den *offentliga* undernät med [az vm skapa](/cli/azure/vm#az_vm_create). Om SSH-nycklar inte redan finns en nyckel standardplatsen, skapar dem i kommandot. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn visar Azure CLI information liknar följande exempel: 

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

Anteckna den **publicIpAddress** i returnerade utdata. Den här adressen används för åtkomst till den virtuella datorn från internet i ett senare steg.

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

Det tar några minuter att skapa den virtuella datorn. När den har skapats, notera den **publicIpAddress** i utdatan. Den här adressen används för åtkomst till den virtuella datorn från internet i ett senare steg.

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

SSH till den *myVmPrivate* VM. Ersätt *<publicIpAddress>* med den offentliga IP-adressen för din *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Skapa en mapp för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Montera filresursen Azure till den katalog som du skapade. Innan du kör följande kommando ersätter `<storage-account-name>` med kontonamn och `<storage-account-key>` med den nyckel som du hämtade i [skapa ett lagringskonto](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Du får den `user@myVmPrivate:~$` prompt. Azure-filresursen har monterats till */mnt/MyAzureFileShare*.

Bekräfta att den virtuella datorn har ingen utgående anslutning till alla andra offentliga IP-adresser:

```bash
ping bing.com -c 4
```

Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplat till den *privata* undernät inte tillåter utgående åtkomst till den offentliga IP-adresser än de adresser som tilldelats till tjänsten Azure Storage.

Avsluta SSH-session till den *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst nekas till storage-konto

Använd följande kommando för att skapa en SSH-session med den *myVmPublic* VM. Ersätt `<publicIpAddress>` med den offentliga IP-adressen för din *myVmPublic* VM: 

```bash 
ssh <publicIpAddress>
```

Skapa en katalog för en monteringspunkt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Försök att montera filresursen Azure till den katalog som du skapade. Den här artikeln förutsätter att du har distribuerat den senaste versionen av Ubuntu. Om du använder tidigare versioner av Ubuntu [montera på Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för mer information om hur du monterar filresurser. Innan du kör följande kommando ersätter `<storage-account-name>` med kontonamn och `<storage-account-key>` med den nyckel som du hämtade i [skapa ett lagringskonto](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Åtkomst nekad, och du får en `mount error(13): Permission denied` fel, eftersom den *myVmPublic* VM distribueras inom den *offentliga* undernät. Den *offentliga* undernätet har inte en tjänstslutpunkt aktiverat för Azure Storage och lagringskontot kan bara åtkomst till nätverket från den *privata* undernät, inte den *offentliga*undernät.

Avsluta SSH-session till den *myVmPublic* VM.

Från datorn, försöker visa resurser i ditt lagringskonto med [az lagring share lista](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Ersätt `<account-name>` och `<account-key>` med lagringskontonamn och nyckel från [skapa ett lagringskonto](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Åtkomst nekas och du får en *denna begäran har inte behörighet att utföra den här åtgärden* fel, eftersom datorn inte är i den *privata* undernätet för den *MyVirtualNetwork* virtuellt nätverk.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [ta bort grupp az](/cli/azure#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

Du har aktiverat en tjänstslutpunkt för ett undernät för virtuellt nätverk i den här artikeln. Du har lärt dig att slutpunkter kan aktiveras för resurser som har distribuerats med Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsad nätverksåtkomst till lagringskontot endast resurser inom ett undernät för virtuellt nätverk. Läs mer om Tjänsteslutpunkter i [översikt över slutpunkter](virtual-network-service-endpoints-overview.md) och [hantera undernät](virtual-network-manage-subnet.md).

Om du har flera virtuella nätverk i ditt konto kan du vill ansluta två virtuella nätverk tillsammans så resurser inom varje virtuellt nätverk kan kommunicera med varandra. Mer information finns i avsnittet [ansluta virtuella nätverk](tutorial-connect-virtual-networks-cli.md).