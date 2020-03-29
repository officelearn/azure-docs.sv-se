---
title: Installera MongoDB på en virtuell Linux-dator med Azure CLI
description: Lär dig hur du installerar och konfigurerar MongoDB på en virtuell Linux-dator som iusing av Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: e1bc7c8a6f97d6dc6bb1d6cb54825425244b2158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944876"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Så här installerar och konfigurerar du MongoDB på en virtuell Linux-dator

[MongoDB](https://www.mongodb.org) är en populär nosql-databas med öppen källkod. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en virtuell Linux-dator med Azure CLI. Exempel visas som beskriver hur du:

* [Installera och konfigurera en grundläggande MongoDB-instans manuellt](#manually-install-and-configure-mongodb-on-a-vm)
* [Skapa en grundläggande MongoDB-instans med hjälp av en Resource Manager-mall](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Skapa ett komplext MongoDB-fragmenterat kluster med replikuppsättningar med hjälp av en Resource Manager-mall](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installera och konfigurera MongoDB manuellt på en virtuell dator
MongoDB [tillhandahåller installationsinstruktioner](https://docs.mongodb.com/manual/administration/install-on-linux/) för Linux-distributioner, inklusive Red Hat / CentOS, SUSE, Ubuntu och Debian. I följande exempel skapas en *Virtuell CentOS-dator.* För att skapa den här miljön behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [az login](/cli/azure/reference-index).

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM* med en användare med namnet *azureuser* med SSH-autentisering av offentliga nyckel

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn `publicIpAddress` med ditt eget användarnamn och listan i utdata från föregående steg:

```bash
ssh azureuser@<publicIpAddress>
```

Om du vill lägga till installationskällorna för MongoDB skapar du en **yum-databasfil** enligt följande:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Öppna filen MongoDB för redigering, till `vi` `nano`exempel med eller . Lägg till följande rader:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installera MongoDB med **yum** enligt följande:

```bash
sudo yum install -y mongodb-org
```

Som standard tillämpas SELinux på CentOS-avbildningar som hindrar dig från att komma åt MongoDB. Installera principhanteringsverktyg och konfigurera SELinux så att MongoDB kan fungera på standard-TCP-port 27017 enligt följande:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Starta MongoDB-tjänsten enligt följande:

```bash
sudo service mongod start
```

Verifiera MongoDB-installationen genom att `mongo` ansluta med den lokala klienten:

```bash
mongo
```

Testa nu MongoDB-instansen genom att lägga till data och sedan söka:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Om så önskas konfigurerar du MongoDB så att den startar automatiskt under en omstart av systemet:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Skapa grundläggande MongoDB-instans på CentOS med hjälp av en mall
Du kan skapa en grundläggande MongoDB-instans på en enda CentOS-virtuell dator med hjälp av följande Azure-snabbstartsmall från GitHub. Den här mallen använder tillägget Anpassat **yum** skript för Linux för att lägga till en yum-lagringsplats i din nyskapade Virtuella CentOS-fil och sedan installera MongoDB.

* [Grundläggande MongoDB-instans på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

För att skapa den här miljön behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [az login](/cli/azure/reference-index). Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera sedan MongoDB-mallen med [az-gruppdistribution skapa](/cli/azure/group/deployment). Ange dina egna unika värden för *newStorageAccountName*, *dnsNameForPublicIP*och administratörsanvändarnamn och lösenord:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Logga in på den virtuella datorn med den offentliga DNS-adressen för den virtuella datorn. Du kan visa den offentliga DNS-adressen med [az vm show:](/cli/azure/vm)

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH till din virtuella dator med ditt eget användarnamn och offentliga DNS-adress:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verifiera MongoDB-installationen genom att `mongo` ansluta med den lokala klienten enligt följande:

```bash
mongo
```

Testa nu instansen genom att lägga till data och söka på följande sätt:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Skapa ett komplext MongoDB Sharded Cluster på CentOS med hjälp av en mall
Du kan skapa ett komplext MongoDB-fragmenterat kluster med hjälp av följande Azure-snabbstartsmall från GitHub. Den här mallen följer metodtipsen för [MongoDB-fragmenterade kluster för](https://docs.mongodb.com/manual/core/sharded-cluster-components/) att ge redundans och hög tillgänglighet. Mallen skapar två shards, med tre noder i varje replikuppsättning. En konfigurationsserverreplikuppsättning med tre noder **skapas** också, plus två mongosroutrservrar för att ge konsekvens till program från hela shards.

* [MongoDB Sharding Cluster på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Distribuera denna komplexa MongoDB fragmenterade kluster kräver mer än 20 kärnor, vilket vanligtvis är standard kärnantalet per region för en prenumeration. Öppna en Azure-supportbegäran för att öka antalet kärnor.

För att skapa den här miljön behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [az login](/cli/azure/reference-index). Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera sedan MongoDB-mallen med [az-gruppdistribution skapa](/cli/azure/group/deployment). Definiera dina egna resursnamn och storlekar där det behövs, till exempel för *mongoAdminUsername,* *sizeOfDataDiskInGB*och *configNodeVmSize:*

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

Den här distributionen kan ta över en timme att distribuera och konfigurera alla VM-instanser. Flaggan `--no-wait` används i slutet av föregående kommando för att returnera kontrollen till kommandotolken när malldistributionen har accepterats av Azure-plattformen. Du kan sedan visa distributionsstatus med [az-gruppdistributionsshow](/cli/azure/group/deployment). I följande exempel visar statusen för *myMongoDBCluster-distributionen* i resursgruppen *myResourceGroup:*

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Nästa steg
I de här exemplen ansluter du till MongoDB-instansen lokalt från den virtuella datorn. Om du vill ansluta till MongoDB-instansen från en annan virtuell dator eller nätverk kontrollerar du att lämpliga [regler för nätverkssäkerhetsgrupp skapas](nsg-quickstart.md).

Dessa exempel distribuerar den centrala MongoDB-miljön för utvecklingsändamål. Använd de nödvändiga säkerhetskonfigurationsalternativen för din miljö. Mer information finns i [MongoDB-säkerhetsdokumenten](https://docs.mongodb.com/manual/security/).

Mer information om hur du skapar mallar finns i [översikten över Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Azure Resource Manager-mallarna använder det anpassade skripttillägget för att hämta och köra skript på dina virtuella datorer. Mer information finns i [Använda Azure Custom Script Extension med virtuella Linux-datorer](extensions-customscript.md).

