---
title: "Filresurs för Azure DC/OS-klustret"
description: "Skapa och montera en filresurs på en DC/OS-klustret i Azure Container Service"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Skapa och montera en filresurs på en DC/OS-klustret

Den här självstudiekursen beskrivs hur du skapar en filresurs i Azure och montera på varje agent och huvudserver för DC/OS-klustret. Skapa en filresurs gör det enklare att dela filer på ditt kluster som konfiguration, access, loggar och mycket mer. Följande åtgärder har utförts i den här självstudiekursen:

> [!div class="checklist"]
> * Skapa ett Azure-lagringskonto
> * Skapa en filresurs
> * Montera filresursen i DC/OS-klustret

Du behöver en ACS DC/OS-klustret för att slutföra stegen i den här självstudiekursen. Om det behövs, [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) kan skapa en åt dig.

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Skapa en filresurs på Microsoft Azure

Storage-konto och filresursen måste skapas innan du använder en Azure-filresursen med en ACS DC/OS-klustret. Kör följande skript för att skapa lagring och filresursen. Uppdatera parametrar med thoes från din miljö.

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

## <a name="mount-the-share-in-your-cluster"></a>Montera filresursen i klustret

Därefter måste filresursen vara monterad på varje virtuell dator i klustret. Den här åtgärden har slutförts med verktyget cifs/protokollet. Monteringen kan utföras manuellt på varje nod i klustret eller genom att köra ett skript mot varje nod i klustret.

I det här exemplet två skript körs, en för att montera Azure-filresursen och en andra köra skriptet på varje nod i DC/OS-klustret.

Först krävs Azure lagringskontonamnet och åtkomstnyckeln. Kör följande kommandon för att hämta informationen. Notera vardera, dessa värden används i ett senare steg.

Lagringskontonamn:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Åtkomstnyckeln för lagringskontot:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Sedan hämta det fullständiga Domännamnet för DC/OS-hanteraren och lagrar den i en variabel.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Kopiera din privata nyckel till huvudnoden. Den här nyckeln behövs för att skapa en ssh-anslutning med alla noder i klustret. Uppdatera användarnamnet om ett standardvärde används när klustret skapas. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Skapa en SSH-anslutning med huvudservern (eller den första huvudservern) på ditt DC/OS-baserade kluster. Uppdatera användarnamnet om ett standardvärde används när klustret skapas.

```azurecli-interactive
ssh azureuser@$FQDN
```

Skapa en fil med namnet **cifsMount.sh**, och kopiera följande innehåll till den. 

Det här skriptet för att montera Azure-filresursen. Uppdatering av `STORAGE_ACCT_NAME` och `ACCESS_KEY` variabler med informationen som samlas in tidigare.

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
Skapa en andra fil som heter **getNodesRunScript.sh** och kopiera följande innehåll i filen. 

Det här skriptet identifierar alla noder i klustret och kör sedan den **cifsMount.sh** skript för att montera filresursen på varje.

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

Kör skriptet för att ansluta till Azure-filresursen på alla noder i klustret.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Filresursen är nu tillgänglig på `/mnt/share/dcosshare` på varje nod i klustret.

## <a name="next-steps"></a>Nästa steg

Filresursen har gjorts tillgängliga för ett DC/OS-kluster med hjälp av stegen i den här självstudiekursen en Azure:

> [!div class="checklist"]
> * Skapa ett Azure-lagringskonto
> * Skapa en filresurs
> * Montera filresursen i DC/OS-klustret

Gå vidare till nästa kurs att lära dig om att integrera en registret för Azure-behållare med DC/OS i Azure.  

> [!div class="nextstepaction"]
> [Belastningsutjämna program](container-service-dcos-acr.md)
