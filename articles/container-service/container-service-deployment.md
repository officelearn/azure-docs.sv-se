---
title: "Distribuera ett Docker-behållarkluster i Azure | Microsoft Docs"
description: Distribuera ett Azure Container Service-kluster via Azure Portal eller Azure Resource Manager-mall.
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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Distribuera ett Azure Container Service-kluster



Azure Container Service ger snabb distribution av populär behållarklustring med öppen källkod och orchestration-lösningar. Det här dokumentet innehåller anvisningar för att distribuera ett Azure Container Service-kluster via Azure Portal eller en snabbstartsmall för Azure Resource Manager. 

> [!NOTE]
> Stöd för Kubernetes i Azure Container Service förhandsvisas just nu.

Du kan även distribuera ett Azure Container Service-kluster via [Azure CLI 2.0 (Förhandsversion)](container-service-create-acs-cluster-cli.md) eller Azure Container Service-API:er.



## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Offentlig SSH-nyckel**: När du distribuerar genom portalen eller en av Azures snabbstartmallar så måste du ange den offentliga nyckeln för autentisering gentemot Azure Container Service Virtual Machines. Se [OS X och Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)- och [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)-artiklarna för vägledning för att skapa Secure Shell-nycklar. 

* **Klient-ID och hemlighet för tjänstens huvudnamn** (endast Kubernetes): Mer information och vägledning om hur du skapar en tjänsts huvudnamn finns i [Om tjänstens huvudnamn för ett Kubernetes-kluster](container-service-kubernetes-service-principal.md)



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Skapa ett kluster med Azure Portal
1. Logga in på Azure Portal, välj **Ny** och sök på Azure Marketplace efter **Azure Container Service**.

    ![Azure Container Service i Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Välj **Azure Container Service** och klicka på **Skapa**.

    ![Skapa en behållartjänst](media/container-service-deployment/acs-portal2.png)  <br />

3. Ange följande information:

    * **Användarnamn**: Användarnamnet för ett konto på var och en av de virtuella datorerna och skalningsuppsättningarna för virtuella datorer i Azure Container Service-klustret.
    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny.
    * **Plats**: Välj en Azure-region för Azure Container Service-distributionen.
    * **Offentlig SSH-nyckel**: Lägg till den offentliga nyckel som ska användas för autentisering mot Azure Container Service Virtual Machines. Det är viktigt att den här nyckeln inte innehåller några radbrytningar och att den innehåller prefixet `ssh-rsa`. Postfixen `username@domain` är valfri. Nyckeln bör vara lik följande: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Klicka på **OK** när du är redo att gå vidare.

    ![Grundläggande inställningar](media/container-service-deployment/acs-portal3.png)  <br />

5. Välj en Orchestration-typ. Alternativen är:

  * **DC/OS**: distribuerar ett DC/OS-kluster.
  * **Swarm**: distribuerar ett Docker Swarm-kluster.
  * **Kubernetes**: Distribuerar ett Kubernetes-kluster


6. Klicka på **OK** när du är redo att gå vidare.

    ![Välj en orchestrator](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Om **Kubernetes** har valts i listrutan måste du ange ett klient-ID för tjänstobjektet och klienthemligheten för tjänstobjektet. Mer information finns i [Om tjänstens huvudnamn för ett Kubernetes-kluster](container-service-kubernetes-service-principal.md).

    ![Ange tjänstens huvudnamn för Kubernetes](media/container-service-deployment/acs-portal10.png)  <br />

7. I inställningsbladet i **Azure Container Service** anger du följande information:

    * **Antal huvudservrar**: antal huvudservrar i klustret. Om Kubernetes har valts anges antalet huvudservrar till standardvärdet 1.
    * **Antal agenter**: För Docker Swarm och Kubernetes är det här värdet det inledande antalet agenter i agentskalningsuppsättningen. När det gäller DC/OS utgör det här det inledande antalet agenter i en privat skalningsuppsättning. Dessutom skapas en offentlig skalningsuppsättning för DC/OS som innehåller ett förinställt antal agenter. Antalet agenter i den här offentliga skalningsuppsättningen bestäms av hur många huvudservrar som har skapats i klustret. En offentlig agent för en huvudserver och två offentliga agenter för tre eller fem huvudservrar.
    * **Storlek på agentens virtuella dator**: storleken på agentens virtuella datorer.
    * **DNS-prefix**: Ett helt unikt namn som används som prefix i viktiga delar i de fullständigt kvalificerade domännamnen för tjänsten.
    * **VM-diagnostik**: För vissa orchestrator som du väljer kan du aktivera VM-diagnostik.

8. Klicka på **OK** när du är redo att gå vidare.

    ![Inställningar för Container Service](media/container-service-deployment/acs-portal5.png)  <br />

9. Klicka på **OK** när tjänsteverifieringen är klar.

    ![Validering](media/container-service-deployment/acs-portal6.png)  <br />

10. Granska villkoren. Klicka på **Köp** för att starta distributionsprocessen.

    ![Köp](media/container-service-deployment/acs-portal7.png)  <br />

    Om du har valt att fästa distributionen på Azure Portal, visas distributionsstatusen.

    ![Status för distribution](media/container-service-deployment/acs-portal8.png)  <br />

Distributionen tar normalt flera minuter för att slutföras. Sedan kan Azure Container Service-klustret användas.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Skapa ett kluster med en snabbstartsmall
Azure-snabbstartsmallar är tillgängliga för att distribuera ett kluster i Azure Container Service. De tillhandahållna snabbstartsmallarna kan ändras så att de inkluderar ytterligare eller avancerad Azure-konfiguration. Du måste ha en Azure-prenumeration för att kunna skapa ett Azure Container Service-kluster med en Azure-snabbstartsmall. Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Följ stegen nedan för att distribuera ett kluster med en mall och Azure CLI 2.0 (Förhandsversion) (se [anvisningar för installation och konfiguration](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> Du kan använda liknande steg för att distribuera en mall med Azure PowerShell om du är på ett Windows-system. Se anvisningarna senare i det här avsnittet. Du kan även distribuera en mall genom [Portalen](../azure-resource-manager/resource-group-template-deploy-portal.md) eller genom andra metoder.

1. Om du vill distribuera ett DC/OS-, Docker Swarm- eller Kubernetes-kluster väljer du någon av nedanstående mallar från GitHub. Observera att DC/OS- och Swarm-mallarna är likadana, med undantag för standardvalet av orchestrator.

    * [DC/OS-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Logga in på ditt Azure-konto (`az login`) och kontrollera att Azure CLI är anslutet till din Azure-prenumeration. Du kan se standard-prenumerationen med hjälp av följande kommando:

    ```azurecli
    az account show
    ```
    
    Om du har fler än en prenumeration och måste ange en annan standard-prenumeration kör du `az account set --subscription` och anger prenumerationens ID eller namn.

3. Du bör använda en ny resursgrupp för distributionen. Skapa en resursgrupp genom att använda kommandot `az group create` och ange ett resursgruppnamn och en plats: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Skapa en JSON-fil som innehåller nödvändiga mallparametrarna. Hämta parameterfilen som heter `azuredeploy.parameters.json` som kommer med Azure Container Service-mallarna `azuredeploy.json` i GitHub. Ange de parametervärden som krävs för klustret. 

    För att till exempel använda [DC/OS-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) anger du parametervärden för `dnsNamePrefix` och `sshRSAPublicKey`. Se beskrivningarna i `azuredeploy.json` och alternativ för andra parametrar.  
 

5. Skapa ett Container Service-kluster genom att skicka filen med parametrarna för distribution med följande kommando, där:

    * **RESOURCE_GROUP** är namnet på resursgruppen som du skapade i det föregående steget.
    * **DEPLOYMENT_NAME** (valfritt) är namnet du ger distributionen.
    * **TEMPLATE_URI** är platsen för distributionsfilen `azuredeploy.json`. Den här URI:n måste vara rådatafilen, inte en pekare till GitHub-gränssnittet. Du kan hitta den här URI:en genom att markera filen `azuredeploy.json` i GitHub och klicka på **Raw**-knappen.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Du kan även ange parametrar som en JSON-formaterad sträng i kommandoraden. Använd ett kommando som liknar följande:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > Distributionen tar normalt flera minuter för att slutföras.
    > 

### <a name="equivalent-powershell-commands"></a>Motsvarande PowerShell-kommandon
Du kan även distribuera en mall för ett Azure Container Service-kluster med PowerShell. Det här dokumentet utgår från version 1.0 av [Azure PowerShell-modulen](https://azure.microsoft.com/blog/azps-1-0/).

1. För att distribuera ett DC/OS-, Docker Swarm- eller Kubernetes-kluster, väljer du en av följande mallar. Observera att DC/OS- och Swarm-mallarna är likadana, med undantag för standardvalet av orchestrator.

    * [DC/OS-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Innan du skapar ett kluster i din Azure-prenumeration måste du kontrollera att PowerShell-sessionen har loggats in på Azure. Det kan du göra med kommandot `Get-AzureRMSubscription`:

    ```powershell
    Get-AzureRmSubscription
    ```

3. Om du behöver logga in på Azure kan du använda kommandot `Login-AzureRMAccount`:

    ```powershell
    Login-AzureRmAccount
    ```

4. Du bör använda en ny resursgrupp för distributionen. Skapa en resursgrupp genom att använda kommandot `New-AzureRmResourceGroup` och ange ett resursgruppnamn och en målregion:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. När du har skapat en resursgrupp kan du skapa klustret med nedanstående kommando. URI:n för den önskade mallen anges för parametern `-TemplateUri`. När du kör det här kommandot uppmanar PowerShell dig att ange parametervärden för distributionen.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Ange mallparametrar
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



<!--HONumber=Feb17_HO1-->


