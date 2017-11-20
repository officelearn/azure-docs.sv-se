---
title: "Tjänstens huvudnamn för Azure Kubernetes-kluster | Microsoft- Docs"
description: "Skapa och hantera en tjänsts huvudnamn för Azure Active Directory för ett Kubernetes-kluster i AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6c61d99f1d023ac643455faae10ef284f1f5bb14
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Tjänstens huvudnamn med Azure Container Service (AKS)

Ett AKS-kluster kräver ett [Azure Active Directory-huvudnamn för tjänsten](../active-directory/develop/active-directory-application-objects.md) för att kunna interagera med Azure-API:er. Tjänstens huvudnamn krävs för att dynamiskt hantera resurser som [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) och [lager 4 för Azure Load Balancer](../load-balancer/load-balancer-overview.md).

Den här artikeln beskriver olika alternativ för att konfigurera ett huvudnamn för tjänsten för ett Kubernetes-kluster i AKS.

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Se [AKS-snabbstart](./kubernetes-walkthrough.md) om du behöver de här objekten.

För att skapa ett Azure AD-huvudnamn för tjänsten måste du ha behörighet att registrera ett program med din Azure AD-klientorganisation, samt behörighet att tilldela programmet till en roll i din prenumeration. Om du inte har de behörigheter som du behöver kan du be din Azure AD- eller prenumerationsadministratör att tilldela de nödvändiga behörigheterna eller att skapa ett huvudnamn för tjänsten för Kubernetes-klustret.

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.21 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Skapa SP med AKS-kluster

När du distribuerar ett AKS-kluster med kommandot `az aks create` har du möjlighet att automatiskt generera ett huvudnamn för tjänsten.

I följande exempel skapas ett AKS-kluster och eftersom ett befintlig huvudnamn för tjänsten inte har angetts så skapas ett för klustret. Kontot måste ha rätt behörigheter för att skapa ett huvudnamn för tjänsten för att kunna slutföra den här åtgärden.

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Använda en befintlig SP

Ett befintligt Azure AD-huvudnamn för tjänsten kan användas eller skapas i förväg för användning med ett AKS-kluster. Det här är användbart när du distribuerar ett kluster från Azure Portal där du måste ange information om tjänstens huvudnamn.

När du använder ett befintligt huvudnamn för tjänsten måste det uppfylla följande krav:

- Omfattning: prenumerationen som används för att distribuera klustret
- Roll: Deltagare
- Klienthemlighet: måste vara ett lösenord

## <a name="pre-create-a-new-sp"></a>Skapa en ny SP i förväg

Använd kommandot [az ad sp create-for-rbac]() för att skapa tjänstens huvudnamn med Azure CLI.

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

De utdata som genereras liknar följande. Anteckna `appId` och `password`. De här värdena används när du skapar ett AKS-kluster.

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Använda en befintlig SP

När du använder ett huvudnamn för tjänsten som skapats i förväg anger du `appId` och `password` som argumentvärden till kommandot `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-princal <appId> ----client-secret <password>
```

Om du distribuerar ett AKS-kluster från Azure Portal kan du ange dessa värden i konfigurationsformuläret för AKS-klustret.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Tänk på följande när du arbetar med AKS och Azure AD-tjänstens huvudnamn.

* Tjänstobjektet för Kubernetes är en del av klusterkonfigurationen. Men använd inte identiteten för att distribuera klustret.
* Varje tjänstobjekt är associerat med ett Azure AD-program. Tjänstobjektet för ett Kubernetes-kluster kan associeras med ett giltigt Azure Active Directory-programnamn (till exempel: `https://www.contoso.org/example`). URL:en för programmet behöver inte vara en verklig slutpunkt.
* När du anger **klient-ID:t** för tjänstobjektet kan du använda värdet för `appId` (som anges i den här artikeln) eller motsvarande `name` för tjänstobjektet (till exempel `https://www.contoso.org/example`).
* På virtuella master- och noddatorer i Kubernetes-klustret lagras autentiseringsuppgifter för tjänstens huvudnamn i filen /etc/kubernetes/azure.json.
* Om du använder kommandot `az aks create` för att generera tjänstobjektet automatiskt, skrivs autentiseringsuppgifterna för tjänstobjektet till filen ~/.azure/acsServicePrincipal.json på den dator som används för att köra kommandot.
* Om du använder kommandot `az aks create` för att generera tjänstobjektet automatiskt, kan tjänstobjektet även autentisera med ett [Azure-behållarregister](../container-registry/container-registry-intro.md) som skapats i samma prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory-tjänstens huvudnamn finns i programdokumentationen för Azure AD.

> [!div class="nextstepaction"]
> [Objekt för program och tjänstens huvudnamn](../active-directory/develop/active-directory-application-objects.md)
