---
title: Installera MongoDB på en virtuell Linux-dator med Azure CLI | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar MongoDB på en Linux VM ianvända Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: zarhoads
ms.openlocfilehash: 138435664222ff268069465aa98a53f0902a9f99
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412707"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Hur du installerar och konfigurerar MongoDB på en Linux VM

[MongoDB](http://www.mongodb.org) är en populära öppen källkod, högpresterande NoSQL-databas. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en Linux VM med Azure CLI. Exempel visas den information om hur du:

* [Installera och konfigurera en grundläggande MongoDB-instans manuellt](#manually-install-and-configure-mongodb-on-a-vm)
* [Skapa en grundläggande MongoDB-instans med en Resource Manager-mall](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Skapa en komplex MongoDB delat kluster med replik anger med hjälp av en Resource Manager-mall](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installera och konfigurera MongoDB på en virtuell dator manuellt
MongoDB [ger installationsanvisningar](https://docs.mongodb.com/manual/administration/install-on-linux/) för Linux-distributioner, inklusive Red Hat / CentOS, SUSE, Ubuntu eller Debian. I följande exempel skapas en *CentOS* VM. Om du vill skapa den här miljön, du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM* med en användare med namnet *azureuser* med SSH-autentisering för offentlig nyckel

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med ditt eget användarnamn och `publicIpAddress` visas i utdata från föregående steg:

```bash
ssh azureuser@<publicIpAddress>
```

Om du vill lägga till installationskällor för MongoDB, skapa en **yum** databasfil på följande sätt:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Öppna filen MongoDB-lagringsplatsen för att redigera, till exempel med `vi` eller `nano`. Lägg till följande rader:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installera MongoDB med **yum** på följande sätt:

```bash
sudo yum install -y mongodb-org
```

Som standard aktiveras SELinux på CentOS-avbildningar som hindrar dig från att komma åt MongoDB. Installera hanteringsverktyg för gruppolicy och konfigurera SELinux för att tillåta MongoDB att arbeta med dess TCP-standardporten 27017 på följande sätt:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Starta tjänsten MongoDB på följande sätt:

```bash
sudo service mongod start
```

Kontrollera MongoDB-installationen genom att ansluta med hjälp av lokalt `mongo` klienten:

```bash
mongo
```

Testa nu MongoDB-instans genom att lägga till vissa data och sedan söka:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Om du vill kan du konfigurera MongoDB för att starta automatiskt under en omstart av systemet:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Skapa grundläggande MongoDB-instans på CentOS med en mall
Du kan skapa en grundläggande MongoDB-instans på en enda virtuell CentOS-dator med hjälp av följande Azure quickstart-mallen från GitHub. Den här mallen använder tillägget för anpassat skript för Linux för att lägga till en **yum** lagringsplatsen till din nyligen skapade virtuella CentOS-datorn och sedan installera MongoDB.

* [Grundläggande MongoDB-instans på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Om du vill skapa den här miljön, du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index). Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med MongoDB [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create). När du uppmanas, anger du ditt eget unika värden för *newStorageAccountName*, *dnsNameForPublicIP*, och administratörens användarnamn och lösenord:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Logga in på den virtuella datorn med den offentliga DNS-adressen för den virtuella datorn. Du kan visa den offentliga DNS-adressen med [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH till den virtuella datorn med ditt eget användarnamn och en offentlig DNS-adress:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Kontrollera MongoDB-installationen genom att ansluta med hjälp av lokalt `mongo` klienten på följande sätt:

```bash
mongo
```

Nu testar du instansen genom att lägga till vissa data och söka på följande sätt:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Skapa ett komplext Shardade MongoDB-kluster på CentOS med en mall
Du kan skapa komplexa shardade MongoDB-kluster med hjälp av följande Azure quickstart-mallen från GitHub. Den här mallen följer den [MongoDB delat kluster metodtips](https://docs.mongodb.com/manual/core/sharded-cluster-components/) att ge redundans och hög tillgänglighet. Mallen skapar två shards, med tre noder i varje replikuppsättningen. En replik för config server med tre noder skapas också, samt två **mongos** router-servrar som tillhandahåller konsekvens till program från över shards.

* [MongoDB horisontell partitionering kluster på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Distribuera den här komplexa shardade MongoDB-kluster kräver fler än 20 kärnor, vilket vanligtvis är standardvärdet för antal kärnor per region för en prenumeration. Öppna en supportförfrågan för Azure att öka din antal kärnor.

Om du vill skapa den här miljön, du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index). Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med MongoDB [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create). Definiera din egen resurs namn och storlekar där det behövs, till exempel som för *mongoAdminUsername*, *sizeOfDataDiskInGB*, och *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Den här distributionen kan ta över en timme att distribuera och konfigurera alla VM-instanser. Den `--no-wait` används i slutet av det föregående kommandot för att komma tillbaka till Kommandotolken när malldistributionen har godkänts av Azure-plattformen. Du kan sedan se Distributionsstatus med [az group deployment show](/cli/azure/group/deployment#az_group_deployment_show). Följande exempel visar statusen för den *myMongoDBCluster* distribution i den *myResourceGroup* resursgrupp:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Nästa steg
I det här ansluter du till MongoDB-instans lokalt från den virtuella datorn. Om du vill ansluta till MongoDB-instans från en annan virtuell dator eller nätverk, se till att rätt [nätverkssäkerhetsgruppsregler skapas](nsg-quickstart.md).

De här exemplen distribuera core MongoDB-miljö för utveckling. Tillämpa den begärda konfigurationsalternativen för din miljö. Mer information finns i den [MongoDB security docs](https://docs.mongodb.com/manual/security/).

Mer information om hur du skapar med hjälp av mallar finns i den [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Azure Resource Manager-mallar använder tillägget för anpassat skript för att hämta och köra skript på dina virtuella datorer. Mer information finns i [med hjälp av tillägget för anpassat skript för Azure med Linux Virtual Machines](extensions-customscript.md).

