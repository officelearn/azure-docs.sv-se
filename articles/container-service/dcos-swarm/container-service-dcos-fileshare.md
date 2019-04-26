---
title: (INAKTUELL) Filresurs för Azure DC/OS-kluster
description: Skapa och montera en filresurs till ett DC/OS-kluster i Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480392"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(INAKTUELL) Skapa och montera en filresurs till ett DC/OS-kluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Den här självstudien förklarar hur du skapar en filresurs i Azure och monterar den på varje agent och original av DC/OS-klustret. Om du konfigurerar en filresurs så blir det enklare att dela filer på ditt kluster, till exempel konfiguration, åtkomst, loggar och mycket mer. I den här självstudien slutför du följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure-lagringskonto
> * Skapa en filresurs
> * Montera filresursen i DC/OS-klustret

Du behöver ett ACS DC/OS-klustret när du ska utföra stegen i den här självstudien. Om det behövs kan du skapa ett sådant med [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Skapa en filresurs på Microsoft Azure

Innan du använder en Azure-filresurs med ett ACS DC/OS-kluster så måste du skapa lagringskontot och filresursen. Kör följande skript för att skapa lagrings- och filresursen. Uppdatera parametrarna med de från din miljö.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montera resursen i ditt kluster

Därefter behöver filresursen monteras på varje virtuell dator i ditt kluster. Den här åtgärden har slutförts med verktyget/protokollet cifs. Monteringsåtgärden kan utföras manuellt på varje nod i klustret eller genom att köra ett skript mot varje nod i klustret.

I det här exemplet körs två skript, en för att montera Azure-filresursen och ett andra för att köra det skriptet på varje nod i DC/OS-klustret.

Först krävs Azure lagringskontonamn och åtkomstnyckel. Kör följande kommandon för att få den här informationen. Skriv ner vart och ett av de här värdena som används i ett senare steg.

Lagringskontots namn:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Lagringskontots åtkomstnyckel:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Därefter hämtar du FQDN för DC/OS-originalet och lagrar det i en variabel.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Kopiera din privata nyckel till huvudnoden. Den här nyckeln behövs för att skapa en ssh-anslutning med alla noder i klustret. Uppdatera användarnamnet om du använde något annat än standardvärdet när du skapade klustret. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Skapa en SSH-anslutning med huvudservern (eller den första huvudservern) i ditt DC/OS-baserade kluster. Uppdatera användarnamnet om du använde något annat än standardvärdet när du skapade klustret.

```azurecli-interactive
ssh azureuser@$FQDN
```

Skapa en fil med namnet **nginx-public.json** och kopiera följande innehåll till den. 

Det här skriptet används för att montera Azure-filresursen. Uppdatera variablerna `STORAGE_ACCT_NAME` och `ACCESS_KEY` med informationen som samlats in tidigare.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Skapa en andra fil med namnet **getNodesRunScript.sh** och kopiera följande innehåll till filen. 

Det här skriptet identifierar alla klusternoder och kör sedan skriptet **cifsMount.sh** för att montera filresursen på var och en.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Kör skriptet för att montera Azure-filresursen på alla noder i klustret.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Filresursen är nu tillgänglig på `/mnt/share/dcosshare` för varje nod i klustret.

## <a name="next-steps"></a>Nästa steg

I den här självstudien gjordes en Azure-filresurs tillgänglig till ett DC/OS-kluster med hjälp av stegen:

> [!div class="checklist"]
> * Skapa ett Azure-lagringskonto
> * Skapa en filresurs
> * Montera filresursen i DC/OS-klustret

Gå vidare till nästa självstudie för att lära dig om hur du integrerar en Azure Container Registry med DC/OS i Azure.  

> [!div class="nextstepaction"]
> [Belastningsutjämna program](container-service-dcos-acr.md)
