---
title: Återställa autentiseringsuppgifterna för ett Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du uppdaterar eller återställer autentiseringsuppgifter för tjänstens huvud namn för ett kluster i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: bda0ab50b829fa2e6d58e73b51e3a0a0f6c9e2af
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432917"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Uppdatera eller rotera autentiseringsuppgifterna för ett huvud namn för tjänsten i Azure Kubernetes service (AKS)

Som standard skapas AKS-kluster med ett huvud namn för tjänsten som har ett års förfallo tid. När du nära förfallo datumet kan du återställa autentiseringsuppgifterna för att utöka tjänstens huvud namn under ytterligare en tids period. Du kanske också vill uppdatera eller rotera autentiseringsuppgifterna som en del av en definierad säkerhets princip. Den här artikeln beskriver hur du uppdaterar autentiseringsuppgifterna för ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.65 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Välj att uppdatera eller skapa ett huvud namn för tjänsten

När du vill uppdatera autentiseringsuppgifterna för ett AKS-kluster kan du välja att:

* uppdatera autentiseringsuppgifterna för det befintliga tjänst huvud namnet som används av klustret, eller
* skapa ett huvud namn för tjänsten och uppdatera klustret för att använda dessa nya autentiseringsuppgifter.

### <a name="update-existing-service-principal-expiration"></a>Uppdatera befintlig förfallo datum för tjänstens huvud namn

Om du vill uppdatera autentiseringsuppgifterna för det befintliga huvud namnet för tjänsten hämtar du tjänstens huvud namn-ID för klustret med hjälp av kommandot [AZ AKS show][az-aks-show] . I följande exempel hämtas ID: t för klustret med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* . Tjänstens huvud namn-ID anges som en variabel med namnet *SP_ID* för användning i ytterligare kommando.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Med en variabel uppsättning som innehåller tjänstens huvud namns-ID, Återställ nu autentiseringsuppgifterna med [AZ AD SP Credential reset][az-ad-sp-credential-reset]. I följande exempel kan Azure-plattformen generera en ny säker hemlighet för tjänstens huvud namn. Den nya säkra hemligheten lagras också som en variabel.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Fortsätt nu med att [Uppdatera AKS-kluster med nya autentiseringsuppgifter](#update-aks-cluster-with-new-credentials). Det här steget krävs för att tjänstens huvud namn ska avspeglas i AKS-klustret.

### <a name="create-a-new-service-principal"></a>Skapa ett nytt huvud namn för tjänsten

Hoppa över det här steget om du väljer att uppdatera de befintliga autentiseringsuppgifterna för tjänstens huvud namn i föregående avsnitt. Fortsätt att [Uppdatera AKS-kluster med nya autentiseringsuppgifter](#update-aks-cluster-with-new-credentials).

Om du vill skapa ett huvud namn för tjänsten och sedan uppdatera AKS-klustret för att använda dessa nya autentiseringsuppgifter, använder du kommandot [AZ AD SP Create-for-RBAC][az-ad-sp-create] . I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De utdata som genereras påminner om de i följande exempel. Anteckna dina egna `appId` och `password`. Dessa värden används i nästa steg.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definiera nu variabler för tjänstens huvud namns-ID och klient hemligheten med hjälp av utdata från ditt eget [AZ AD SP Create-for-RBAC][az-ad-sp-create] -kommando, som visas i följande exempel. *SP_ID* är ditt *appId*och *SP_SECRET* är ditt *lösen ord*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Fortsätt nu med att [Uppdatera AKS-kluster med nya autentiseringsuppgifter](#update-aks-cluster-with-new-credentials). Det här steget krävs för att tjänstens huvud namn ska avspeglas i AKS-klustret.

## <a name="update-aks-cluster-with-new-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter

Oavsett om du väljer att uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvud namn eller skapa ett huvud namn för tjänsten, uppdaterar du nu AKS-klustret med dina nya autentiseringsuppgifter med hjälp av kommandot [AZ AKS Update-credentials][az-aks-update-credentials] . Variablerna för *--Service-Principal* och *--client-Secret* används:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Det tar en stund innan autentiseringsuppgifterna för tjänstens huvud namn uppdateras i AKS.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har tjänstens huvud namn för AKS-klustret uppdaterats. Mer information om hur du hanterar identitet för arbets belastningar i ett kluster finns i [metod tips för autentisering och auktorisering i AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
