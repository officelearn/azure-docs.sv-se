---
title: Distribuera ett Azure Container Service-kluster | Microsoft Docs
description: Distribuera ett Azure Container Service-kluster via Azure Portal, Azure CLI eller PowerShell.
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: a7d957fd4be4c823077b1220dfb8ed91070a0e97
ms.openlocfilehash: d056b9489eba1f97e8fb87f231b03d104c4cab66


---
# <a name="deploy-an-azure-container-service-cluster"></a>Distribuera ett Azure Container Service-kluster
Azure Container Service ger snabb distribution av populär behållarklustring med öppen källkod och orchestration-lösningar. Med Azure Container Service kan du distribuera DC/OS-, Kubernetes- och Docker Swarm-kluster med Azure Resource Manager-mallar eller Azure Portal. Du kan distribuera de här klusten med Azure Virtual Machine-skalningsuppsättningar, och klustren drar nytta av Azures nätverks- och -lagringserbjudanden. Du behöver en Azure-prenumeration för att kunna använda Azure Container Service. Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Det här dokumentet innehåller anvisningar för att distribuera ett Azure Container Service-kluster med hjälp av [Azure Portal](#creating-a-service-using-the-azure-portal), [Azure-kommandoradsgränssnittet (CLI)](#creating-a-service-using-the-azure-cli) och [Azure PowerShell-modulen](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Skapa en tjänst med Azure Portal
Logga in på Azure Portal, välj **Ny** och sök på Azure Marketplace efter **Azure Container Service**.

![Skapa distribution 1](media/acs-portal1.png)  <br />

Välj **Azure Container Service** och klicka på **Skapa**.

![Skapa distribution 2](media/acs-portal2.png)  <br />

Ange följande information:

* **Användarnamn**: Det här är det användarnamn som ska sättas på ett konto på var och en av de virtuella datorerna och skalningsuppsättningarna för virtuella datorer i Azure Container Service-klustret.
* **Prenumeration**: Välj en Azure-prenumeration.
* **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny.
* **Plats**: Välj en Azure-region för Azure Container Service-distributionen.
* **Offentlig SSH-nyckel**: Lägg till den offentliga nyckel som ska användas för autentisering mot Azure Container Service Virtual Machines. Det är viktigt att den här nyckeln inte innehåller några radbrytningar och att den innehåller prefixet ”ssh-rsa” och postfixet 'username@domain'. Det bör likna följande: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. Anvisningar om hur du skapar Secure Shell-nycklar finns i [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/)- och [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/)-artiklarna.

Klicka på **OK** när du är redo att gå vidare.

![Skapa distribution 3](media/acs-portal3.png)  <br />

Välj en Orchestration-typ. Alternativen är:

* **DC/OS**: distribuerar ett DC/OS-kluster.
* **Swarm**: distribuerar ett Docker Swarm-kluster.
* **Kubernetes**: Distribuerar ett Kubernetes-kluster.

Klicka på **OK** när du är redo att gå vidare.

![Skapa distribution 4](media/acs-portal4-new.png)  <br />

Om **Kubernetes** har valts i listrutan måste du ange klient-ID:t för tjänstobjektet och klienthemligheten för tjänstobjektet.
Läs mer om hur du skapar ett tjänstobjekt på [den här sidan](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md) 

![Skapa distribution 4.5](media/acs-portal10.PNG)  <br />

Ange följande information:

* **Antal huvudservrar**: antal huvudservrar i klustret. Om ”Kubernetes” har valts anges antalet huvudservrar till standardvärdet 1
* **Antal agenter**: För Docker Swarm och Kubernetes är det här det inledande antalet agenter i agentskalningsuppsättningen. När det gäller DC/OS utgör det här det inledande antalet agenter i en privat skalningsuppsättning. Dessutom skapas en offentlig skalningsuppsättning som innehåller ett förinställt antal agenter. Antalet agenter i den här offentliga skalningsuppsättningen avgörs av hur många huvudservrar som har skapats i klustret, 1 offentlig agent för 1 huvudserver och 2 offentliga agenter för 3 eller 5 huvudservrar.
* **Storlek på agentens virtuella dator**: storleken på agentens virtuella datorer.
* **DNS-prefix**: ett helt unikt namn som ska användas som prefix i viktiga delar i de fullständigt kvalificerade domännamnen för tjänsten.

Klicka på **OK** när du är redo att gå vidare.

![Skapa distribution 5](media/acs-portal5.png)  <br />

Klicka på **OK** när tjänsteverifieringen är klar.

![Skapa distribution 6](media/acs-portal6.png)  <br />

Klicka på **Skapa** för att starta distributionsprocessen.

![Skapa distribution 7](media/acs-portal7.png)  <br />

Om du har valt att fästa distributionen på Azure Portal, visas distributionsstatusen.

![Skapa distribution 8](media/acs-portal8.png)  <br />

När distributionen är klar kan Azure Container Service-klustret användas.

## <a name="create-a-service-by-using-the-azure-cli"></a>Skapa en tjänst med Azure CLI
Du måste ha en Azure-prenumeration för att kunna skapa en instans av Azure Container Service med kommandoraden. Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Du måste också ha [installerat](../xplat-cli-install.md) och [konfigurerat](../xplat-cli-connect.md) Azure CLI.

Om du vill distribuera ett DC/OS-, Docker Swarm- eller Kubernetes-kluster väljer du någon av nedanstående mallar från GitHub. 

* [DC/OS-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Swarm-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
* [Kubernetes-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

Kontrollera därefter att Azure CLI har anslutits till en Azure-prenumeration. Det kan du göra med hjälp av följande kommando:

```bash
azure account show
```
Om ett Azure-konto inte returneras kan du använda följande kommando för att logga in CLI på Azure.

```bash
azure login -u user@domain.com
```

Konfigurera därefter Azure CLI-verktygen för att använda Azure Resource Manager.

```bash
azure config mode arm
```

Skapa en Azure-resursgrupp och ett Container Service-kluster med följande kommando, där:

* **RESOURCE_GROUP** är namnet på resursgruppen som du vill använda för den här tjänsten.
* **LOCATION** är Azure-regionen där resursgruppen och Azure Container Service-distributionen skapas.
* **TEMPLATE_URI** är platsen för distributionsfilen. Observera att det här måste vara Raw-filen, inte en pekare till GitHub-gränssnittet. Du kan hitta den här URL:en genom att markera filen azuredeploy.json i GitHub och klicka på **Raw**-knappen.

> [!NOTE]
> När du kör det här kommandot uppmanar gränssnittet dig att ange parametervärden för distributionen.
> 
> 

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Ange mallparametrar
Med den här versionen av kommandot måste du interaktivt definiera parametrar. Om du vill ange parametrar, t.ex. en JSON-formaterad sträng, kan du göra det med växeln `-p`. Exempel:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Du kan även ange en JSON-formaterad parameterfil genom att använda växeln `-e`:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Om du vill se ett exempel på en parameterfil som heter `azuredeploy.parameters.json` kan du leta efter den med Azure Container Service-mallarna i GitHub.

## <a name="create-a-service-by-using-powershell"></a>Skapa en tjänst med PowerShell
Du kan även distribuera ett Azure Container Service-kluster med PowerShell. Det här dokumentet utgår från version 1.0 av [Azure PowerShell-modulen](https://azure.microsoft.com/blog/azps-1-0/).

Om du vill distribuera ett DC/OS-, Docker Swarm- eller Kubernetes-kluster väljer du någon av nedanstående mallar. Observera att båda de här mallarna är likadana, med undantag för standardvalet av orchestrator.

* [DC/OS-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Swarm-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
* [Kubernetes-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

Innan du skapar ett kluster i din Azure-prenumeration måste du kontrollera att PowerShell-sessionen har loggats in på Azure. Det kan du göra med kommandot `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Om du behöver logga in på Azure kan du använda kommandot `Login-AzureRMAccount`:

```powershell
Login-AzureRmAccount
```

Om du distribuerar till en ny resursgrupp måste du först skapa resursgruppen. Skapa en ny resursgrupp genom att använda kommandot `New-AzureRmResourceGroup` och ange ett resursgruppnamn och en målregion:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

När du har skapat en resursgrupp kan du skapa klustret med nedanstående kommando. URI:n för den önskade mallen anges för parametern `-TemplateUri`. När du kör det här kommandot uppmanar PowerShell dig att ange parametervärden för distributionen.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Ange mallparametrar
Om du är bekant med PowerShell vet du att du kan gå igenom tillgängliga parametrar för en cmdlet genom att skriva ett minustecken (-) och sedan trycka på TABB-tangenten. Den här metoden går även att använda med parametrar som du anger i mallen. När du anger mallnamnet hämtar cmdleten mallen, parsar parametrarna och lägger dynamiskt till mallparametrarna i kommandot. På så sätt är det mycket enkelt att ange parametervärden för mallen. Och om du glömmer ett nödvändigt parametervärde efterfrågar PowerShell värdet.

Nedan visas det fullständiga kommandot inklusive parametrar. Du kan ange egna värden för resursnamnen.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Nästa steg
Nu när du har ett fungerande kluster kan du visa dessa dokument för anslutnings- och hanteringsinformation:

* [Ansluta till ett Azure Container Service-kluster](container-service-connect.md)
* [Arbeta med Azure Container Service och DC/OS](container-service-mesos-marathon-rest.md)
* [Arbeta med Azure Container Service och Docker Swarm](container-service-docker-swarm.md)
* [Arbeta med Azure Container Service och Kubernetes](container-service-kubernetes-walkthrough.md)




<!--HONumber=Nov16_HO5-->


