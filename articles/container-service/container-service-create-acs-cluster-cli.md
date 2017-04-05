---
title: "Distribuera ett Docker-behållarkluster – Azure CLI | Microsoft Docs"
description: "Distribuera en Kubernetes-, DC/OS- eller Docker Swarm-lösning i Azure Container Service med hjälp av Azure CLI 2.0"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1dad536939f179cd8231d0f8805c1ff4335850d5
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Distribuera en Dockerbehållare med en värdlösning med hjälp av Azure CLI 2.0

Använd `az acs`-kommandona i Azure CLI 2.0 för att skapa och hantera kluster i Azure Container Service. Du kan även distribuera ett Azure Container Service-kluster via [Azure Portal](container-service-deployment.md) eller Azure Container Service-API:er.

Om du vill visa hjälp om `az acs`-kommandon använder du `-h`-parametern med valfritt kommando. Till exempel: `az acs create -h`.



## <a name="prerequisites"></a>Krav
För att skapa ett Azure Container Service-kluster med hjälp av Azure CLI 2.0 måste du
* ha ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/))
* ha installerat och konfigurerat [Azure CLI 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Kom igång 
### <a name="log-in-to-your-account"></a>Logga in på ditt konto
```azurecli
az login 
```

Följ anvisningarna för att logga in interaktivt. Andra metoder för att logga in finns i [Get started with Azure CLI 2.0](/cli/azure/get-started-with-az-cli2) (Kom igång med Azure CLI 2.0).

### <a name="set-your-azure-subscription"></a>Ange din Azure-prenumeration

Om du har mer än en Azure-prenumeration anger du standardprenumerationen. Exempel:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Vi rekommenderar att du skapar en resursgrupp för varje kluster. Ange en Azure-region där Azure Container Service är [tillgänglig](https://azure.microsoft.com/en-us/regions/services/). Exempel:

```azurecli
az group create -n acsrg1 -l "westus"
```
De utdata som genereras liknar följande:

![Skapa en resursgrupp](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Skapa ett Azure Container Service-kluster

Använd `az acs create` för att skapa ett kluster.
Både namnet på klustret och namnet på resursgruppen som skapades i föregående steg är obligatoriska parametrar. 

Andra indata är inställda på standardvärdena (se skärmbilden nedan) om de inte skrivs över med sina respektive växlar. Orchestrator är till exempel som standard konfigurerad för DC/OS. Och om du inte anger en skapas ett DNS-namnprefix baserat på klustrets namn.

![Användning av az acs create](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>Snabbt använda `acs create` med standardinställningarna
Om du har en offentlig SSH RSA-nyckelfil `id_rsa.pub` på standardplatsen (eller om du har skapat en för [OS X- och Linux](../virtual-machines/linux/mac-create-ssh-keys.md) eller [Windows](../virtual-machines/linux/ssh-from-windows.md)) så använder du ett kommando enligt följande:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Använd det här andra kommandot om du inte har en offentlig SSH-nyckel. Det här kommandot med `--generate-ssh-keys`-växeln skapar en åt dig.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

När du har angivit kommandot väntar du cirka 10 minuter medan klustret skapas. Kommandoutdata innehåller fullständiga kvalificerade domännamn (FQDN) för huvudserver- och agentnoder och ett SSH-kommando för att ansluta till den första huvudservern. Här är en sammanfattning över utdata:

![Bild av acs create](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> [Kubernetes-genomgången](container-service-kubernetes-walkthrough.md) visar hur du använder `az acs create` med standardvärden för att skapa ett Kubernetes-kluster.
>

## <a name="manage-acs-clusters"></a>Hantera ACS-kluster

Använd ytterligare `az acs`-kommandon för att hantera kluster. Här följer några exempel.

### <a name="list-clusters-under-a-subscription"></a>Visa en lista över kluster under en prenumeration

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Visa en lista över kluster i en resursgrupp

```azurecli
az acs list -g acsrg1 --output table
```

![ACS-lista](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Visa detaljer för ett behållartjänstkluster

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Visa ACS](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Skala klustret
Du kan både skala in och ut en agentnod. Parametern `new-agent-count` är det nya antalet agenter i ACS-klustret.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![ACS-skala](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Ta bort ett behållartjänstkluster
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Det här kommandot tar inte bort alla resurser (nätverk och lagring) som skapas när du skapar behållartjänsten. För att enkelt ta bort alla resurser bör du distribuera varje kluster i en specifik resursgrupp. Sedan kan du ta bort resursgruppen när klustret inte längre behövs.

## <a name="next-steps"></a>Nästa steg
Nu när du har ett fungerande kluster kan du visa dessa dokument för anslutnings- och hanteringsinformation:

* [Ansluta till ett Azure Container Service-kluster](container-service-connect.md)
* [Arbeta med Azure Container Service och DC/OS](container-service-mesos-marathon-rest.md)
* [Arbeta med Azure Container Service och Docker Swarm](container-service-docker-swarm.md)
* [Arbeta med Azure Container Service och Kubernetes](container-service-kubernetes-walkthrough.md)
