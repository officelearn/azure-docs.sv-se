---
title: "Installera MongoDB på en virtuell Linux-dator med Azure CLI | Microsoft Docs"
description: "Lär dig hur du installerar och konfigurerar MongoDB på en Linux virtuella iusing Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 5a9797e1fe3d03840e3a20589a50c90968ea5de0
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Installera och konfigurera MongoDB på en Linux-VM
[MongoDB](http://www.mongodb.org) är en populär öppen källkod, högpresterande NoSQL-databas. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en Linux-VM med Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](install-mongodb-nodejs.md). Exempel visas hur detaljer till:

* [Installera och konfigurera en grundläggande MongoDB-instansen manuellt](#manually-install-and-configure-mongodb-on-a-vm)
* [Skapa en grundläggande MongoDB-instans med en Resource Manager-mall](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Skapa en komplex MongoDB delat kluster med replik anger med hjälp av en Resource Manager-mall](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installera och konfigurera MongoDB på en virtuell dator manuellt
MongoDB [ange installationsinstruktioner](https://docs.mongodb.com/manual/administration/install-on-linux/) för Linux-distributioner inklusive Red Hat / CentOS, SUSE, Ubuntu och Debian. I följande exempel skapas en *CentOS* VM. Om du vill skapa den här miljön måste senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login).

Skapa en resursgrupp med [az group create](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#create). I följande exempel skapas en virtuell dator med namnet *myVM* med en användare med namnet *azureuser* med SSH-autentisering för offentlig nyckel

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med hjälp av egna användarnamn och `publicIpAddress` anges i resultatet från föregående steg:

```bash
ssh azureuser@<publicIpAddress>
```

För att lägga till installationskällor för MongoDB, skapa en **yum** fil i databasen på följande sätt:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Öppna filen MongoDB lagringsplatsen för redigering, t.ex med `vi` eller `nano`. Lägg till följande rader:

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

Som standard aktiveras SELinux på CentOS bilder som hindrar dig från att komma åt MongoDB. Installera hanteringsverktyg och konfigurera SELinux för att tillåta MongoDB att använda dess TCP-standardporten 27017 på följande sätt:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Starta tjänsten MongoDB på följande sätt:

```bash
sudo service mongod start
```

Kontrollera installationen av MongoDB genom att ansluta med hjälp av lokalt `mongo` klient:

```bash
mongo
```

Nu testa MongoDB-instansen genom att lägga till vissa data och sedan söka:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Om du vill konfigurera MongoDB för att starta automatiskt under en omstart av systemet:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Skapa en grundläggande MongoDB-instans på CentOS med en mall
Du kan skapa en grundläggande MongoDB-instans på en enda CentOS VM som använder följande Azure quickstart-mall från GitHub. Den här mallen använder tillägget för anpassat skript för Linux för att lägga till en **yum** databasen till den nyligen skapade CentOS VM och sedan installera MongoDB.

* [Grundläggande MongoDB-instansen på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Om du vill skapa den här miljön måste senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login). Börja med att skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med MongoDB [az distribution skapa](/cli/azure/group/deployment#create). När du uppmanas att ange dina egna unika värden för *newStorageAccountName*, *dnsNameForPublicIP*, och admin-användarnamn och lösenord:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Logga in på den virtuella datorn med hjälp av den offentliga DNS-adressen för den virtuella datorn. Du kan visa den offentliga DNS-adressen med [az vm visa](/cli/azure/vm#show):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH till den virtuella datorn med hjälp av användarnamn och offentliga DNS-adress:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Kontrollera installationen av MongoDB genom att ansluta med hjälp av lokalt `mongo` klienten på följande sätt:

```bash
mongo
```

Nu testa instansen genom att lägga till vissa data och söka på följande sätt:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Skapa ett komplext delat MongoDB-kluster på CentOS med en mall
Du kan skapa komplexa delat MongoDB-kluster med hjälp av följande Azure quickstart-mall från GitHub. Den här mallen följer den [MongoDB delat kluster metodtips](https://docs.mongodb.com/manual/core/sharded-cluster-components/) att ge redundans och hög tillgänglighet. Mallen skapar två delar med tre noder i varje replik. En replik för config server med tre noder skapas också, plus två **mongos** router-servrar som tillhandahåller konsekvenskontroll till program från över delar.

* [MongoDB horisontell partitionering kluster på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Distribuera det här komplexa delat MongoDB-kluster kräver mer än 20 kärnor, som vanligtvis är standardvärdet för antal kärnor per region för en prenumeration. Öppna ett Azure supportbegäran för att öka din antal kärnor.

Om du vill skapa den här miljön måste senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login). Börja med att skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats:

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med MongoDB [az distribution skapa](/cli/azure/group/deployment#create). Definiera en egen resurs namn och storlekar vid behov, till exempel som för *mongoAdminUsername*, *sizeOfDataDiskInGB*, och *configNodeVmSize*:

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

Den här distributionen kan ta över en timme att distribuera och konfigurera alla VM-instanser. Den `--no-wait` används i slutet av kommandot ovan för att komma tillbaka till Kommandotolken när mallen distribueras har godkänts av Azure-plattformen. Du kan sedan visa Distributionsstatus med [az grupp distribution visa](/cli/azure/group/deployment#show). I följande exempel visar statusen för den *myMongoDBCluster* distribution i den *myResourceGroup* resursgrupp:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Nästa steg
I dessa fall måste ansluta du till MongoDB-instansen lokalt från den virtuella datorn. Om du vill ansluta till MongoDB-instansen från en annan virtuell dator eller nätverk, se till att rätt [Nätverkssäkerhetsgruppen regler skapas](nsg-quickstart.md).

De här exemplen distribuera core MongoDB-miljö för utveckling. Tillämpa obligatoriska säkerhet konfigurationsalternativ för din miljö. Mer information finns i [MongoDB säkerhet docs](https://docs.mongodb.com/manual/security/).

Mer information om hur du skapar med hjälp av mallar finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Azure Resource Manager-mallar använda tillägget för anpassat skript för att hämta och köra skript på din virtuella dator. Mer information finns i [med hjälp av tillägget för anpassat skript på Azure med Linux-datorer](extensions-customscript.md).

