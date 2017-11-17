---
title: "Installera MongoDB på en Linux-VM som använder Azure CLI 1.0 | Microsoft Docs"
description: "Lär dig hur du installerar och konfigurerar MongoDB på en Linux-dator i Azure med hjälp av Resource Manager-distributionsmodellen."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: ff9e23de41245ea21ba6e9c3efe13ca13b0b0ae1
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Installera och konfigurera MongoDB på en Linux VM som använder Azure CLI 1.0
[MongoDB](http://www.mongodb.org) är en populär öppen källkod, högpresterande NoSQL-databas. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en Linux-VM i Azure med hjälp av Resource Manager-distributionsmodellen. Exempel visas hur detaljer till:

* [Installera och konfigurera en grundläggande MongoDB-instansen manuellt](#manually-install-and-configure-mongodb-on-a-vm)
* [Skapa en grundläggande MongoDB-instans med en Resource Manager-mall](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Skapa en komplex MongoDB delat kluster med replik anger med hjälp av en Resource Manager-mall](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- Azure CLI 1.0 – vårt CLI för den klassiska distributionsmodellen och Resource Manager-distributionsmodellen (den här artikeln)
- [Azure CLI 2.0](create-cli-complete-nodejs.md) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installera och konfigurera MongoDB på en virtuell dator manuellt
MongoDB [ange installationsinstruktioner](https://docs.mongodb.com/manual/administration/install-on-linux/) för Linux-distributioner inklusive Red Hat / CentOS, SUSE, Ubuntu och Debian. I följande exempel skapas en *CentOS* VM som använder en SSH-nyckel som lagras på *~/.ssh/id_rsa.pub*. Besvara anvisningarna för lagringskontonamn, DNS-namn och autentiseringsuppgifter som administratör:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Logga in på den virtuella datorn med hjälp av den offentliga IP-adressen som visas i slutet av det föregående steget för virtuell dator skapas:

```bash
ssh azureuser@40.78.23.145
```

För att lägga till installationskällor för MongoDB, skapa en **yum** fil i databasen på följande sätt:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Öppna filen MongoDB lagringsplatsen för redigering. Lägg till följande rader:

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Installera MongoDB med **yum** på följande sätt:

```bash
sudo yum install -y mongodb-org
```

Som standard aktiveras SELinux på CentOS bilder som hindrar dig från att komma åt MongoDB. Installera hanteringsverktyg och konfigurera SELinux för att tillåta MongoDB att använda dess TCP-standardporten 27017 på följande sätt. 

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
Du kan skapa en grundläggande MongoDB-instans på en enda CentOS VM som använder följande Azure quickstart-mall från GitHub. Den här mallen använder tillägget för anpassat skript för Linux för att lägga till en `yum` databasen till den nyligen skapade CentOS VM och sedan installera MongoDB.

* [Grundläggande MongoDB-instansen på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i den `eastus` region. Ange egna värden enligt följande:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI tillbaka till en fråga inom några sekunder för att skapa distributionen men installationen och konfigurationen tar några minuter att slutföra. Kontrollera statusen för distributionen med `azure group deployment show myResourceGroup`, ange namnet på resursgruppen därefter. Vänta tills den **ProvisioningState** visar *lyckades* innan du försöker SSH till den virtuella datorn.

När installationen är klar, SSH till den virtuella datorn. Hämta IP-adressen för din virtuella datorn med hjälp av den `azure vm show` kommandot som i följande exempel:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

I slutet av utdata visas den offentliga IP-adressen. SSH till den virtuella datorn med IP-adressen för den virtuella datorn:

```bash
ssh azureuser@138.91.149.74
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
> Distribuera det här komplexa delat MongoDB-kluster kräver mer än 20 vCPUs som vanligtvis är standard vCPU antal per region för en prenumeration. Öppna ett Azure supportbegäran för att öka din vCPU antal.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* region. Ange egna värden enligt följande:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI tillbaka till en fråga inom några sekunder för att skapa distributionen men installationen och konfigurationen kan ta över en timme att slutföra. Kontrollera statusen för distributionen med `azure group deployment show myResourceGroup`, justeras efter namnet på resursgruppen. Vänta tills den **ProvisioningState** visar *lyckades* innan du ansluter till de virtuella datorerna.


## <a name="next-steps"></a>Nästa steg
I dessa fall måste ansluta du till MongoDB-instansen lokalt från den virtuella datorn. Om du vill ansluta till MongoDB-instansen från en annan virtuell dator eller nätverk, se till att rätt [Nätverkssäkerhetsgruppen regler skapas](nsg-quickstart.md).

Mer information om hur du skapar med hjälp av mallar finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Azure Resource Manager-mallar använda tillägget för anpassat skript för att hämta och köra skript på din virtuella dator. Mer information finns i [med hjälp av tillägget för anpassat skript på Azure med Linux-datorer](extensions-customscript.md).

