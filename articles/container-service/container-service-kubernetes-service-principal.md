---
title: "Tjänstens huvudnamn för Azure Kubernetes-kluster | Microsoft- Docs"
description: "Skapa och hantera ett huvudnamn för en Azure Active Directory-tjänst i ett Azure Container Service-kluster med Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 24e12e4606a5ec4fabf7046fe9847123033bb70a
ms.openlocfilehash: 073a9e66ac68643b27ecdd44a4ecac3ad79ec218


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Om Azure Active Directory-tjänstens huvudnamn för ett Kubernetes-kluster i Azure Container Service



I Azure Container Service kräver Kubernetes ett [Huvudnamn för Azure Active Directory-tjänsten](../active-directory/active-directory-application-objects.md) som ett tjänstkonto för att interagera med Azure API:er. Tjänstens huvudnamn krävs för att dynamiskt hantera resurser som [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) och lager 4 för [Azure Load Balancer](../load-balancer/load-balancer-overview.md).

Den här artikeln visar olika alternativ för att ange ett huvudnamn för tjänsten för Kubernetes-klustret. Om du t.ex. har installerat och konfigurerat [Azure CLI 2.0 (förhandsversion)](https://docs.microsoft.com/cli/azure/install-az-cli2), kan du köra kommandot [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) för att skapa Kubernetes-klustret och tjänstens huvudnamn på samma gång.

> [!NOTE]
> Stöd för Kubernetes i Azure Container Service förhandsvisas just nu.


## <a name="requirements-for-the-service-principal"></a>Krav för tjänstens huvudnamn

Följande är krav för Azure Active Directory-tjänstens huvudnamn i ett Kubernetes-kluster i Azure Container Service. 

* **Omfattning** -Azure-prenumerationen där klustret distribueras

* **Roll** - **deltagare**

* **Klienthemlighet** - måste vara ett lösenord. För närvarande kan du inte använda ett huvudnamn för tjänsten som konfigurerats för certifikatautentisering.

> [!NOTE]
> Varje tjänst är associerad med ett Azure Active Directory-program. Tjänsten huvudnamn för ett Kubernetes-kluster kan associeras med ett giltigt Azure Active Directory-programnamn.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Alternativ för tjänstens huvudnamn för ett Kubernetes-kluster

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Alternativ 1: Skicka klient-ID och klienthemlighet för tjänstens huvudnamn

Ange **klient-ID** (kallas ofta `appId`, för program-ID) och **klienthemlighet** (`password`) för en befintlig tjänsts huvudnamn som parametrar när du skapar Kubernetes-klustret. Om du använder ett befintligt huvudnamn för tjänsten, kontrollera att det uppfyller kraven i föregående avsnitt. Om du behöver skapa ett huvudnamn för tjänsten finns information i [Skapa ett huvudnamn för tjänsten](#create-a-service-principal-in-azure-active-directory) senare i den här artikeln.

Du kan ange dessa parametrar när du [distribuerar Kubernetes-klustret](./container-service-deployment.md) med portalen, Azure-kommandoradsgränssnittet (CLI) eller Azure PowerShell.

>[!TIP] 
>När du anger **klient-ID**, måste du använda `appId`, och inte `ObjectId`, av huvudnamnet för tjänsten.
>

I följande exempel visas ett sätt att överföra parametrarna med [Azure CLI](../xplat-cli-install.md) i [Resource Manager-läge](../xplat-cli-connect.md). I det här exemplet används [mallen Kubernetes-snabbstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Hämta](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) filen för mallparametrarna azuredeploy.parameters.json från GitHub.

2. För att ange tjänstens huvudnamn, anger du värden för `servicePrincipalClientId` och `servicePrincipalClientSecret` i filen. (Du måste också ange dina egna värden för `dnsNamePrefix` och `sshRSAPublicKey`. Dessa är den offentliga SSH-nyckeln till klustret.) Spara filen.

    ![Skicka parametrar för tjänstens huvudnamn](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Kör följande kommando, med hjälp av parameter `-e` för att ange sökvägen till filen azuredeploy.parameters.json. Det här kommandot distribuerar klustret i en befintlig resursgrupp som kallas `myResourceGroup`.

    ```CLI
    azure group deployment create -n myClusterName -g myResourceGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" -e azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>Alternativ 2: Generera tjänstens huvudnamn när klustret skapas med förhandsversionen av Azure CLI 2.0

Om du har installerat och konfigurerat [Azure CLI 2.0 (förhandsversion)](https://docs.microsoft.com/cli/azure/install-az-cli2), kan du köra kommandot [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) för att [skapa klustret](./container-service-create-acs-cluster-cli.md).

Som med andra alternativ för att skapa Kubernetes-kluster, kan du ange parametrar för en befintlig tjänsts huvudnamn när du kör `az acs create`. Men när du utelämnar parametrarna skapar Azure Container Service automatiskt ett huvudnamn för tjänsten. Detta sker transparent under distributionen. 

Följande kommando skapar ett Kubernetes-kluster och genererar både SSH-nycklar och autentiseringsuppgifter för tjänstens huvudnamn:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Skapa ett huvudnamn för tjänsten i Azure Active Directory

Om du vill skapa ett huvudnamn för tjänsten i Azure Active Directory för användning i Kubernetes-klustret, erbjuder Azure flera metoder. 

Kommandona i följande exempel visar hur du gör detta med [Azure CLI 2.0 (förhandsversion)](https://docs.microsoft.com/cli/azure/install-az-cli2). Du kan också skapa ett huvudnamn för tjänsten med [Azure-kommandoradsgränssnittet](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md), [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) eller den [klassiska portalen](../azure-resource-manager/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Kontrollera att du går igenom kraven för tjänstens huvudnamn tidigare i den här artikeln.
>

```console
az login

az account set --subscription="mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Det här returnerar utdata som liknar följande (visas omarbetade här):

![Skapa ett huvudnamn för tjänsten](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

De **klient-ID** (`appId`) och **klienthemligheten** (`password`) som du använder som parametrar för tjänstens huvudnamn för klusterdistribution är markerade.


Bekräfta huvudnamn för tjänsten genom att öppna ett nytt gränssnitt och kör följande kommandon och ersätt i `appId`, `password` och `tenant`:

```console 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Annat som är bra att tänka på


* När du anger **klient-ID** för tjänstens huvudnamn, kan du använda värdet för `appId` (som visas i den här artikeln) eller motsvarande huvudnamn för tjänsten `name` (till exempel `https://www.contoso.org/example`).

* Om du använder kommandot `az acs create` för att automatiskt generera huvudnamn för tjänsten, skrivs autentiseringsuppgifterna för huvudnamn för tjänsten till filen ~/.azure/acsServicePrincipal.json på den dator som används för att köra kommandot.

* På virtuella master- och noddatorer i Kubernetes-klustret lagras autentiseringsuppgifter för tjänstens huvudnamn i filen /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Kubernetes](container-service-kubernetes-walkthrough.md) i behållaren för tjänsteklustret.



<!--HONumber=Jan17_HO1-->


