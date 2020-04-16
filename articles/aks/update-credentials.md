---
title: Återställa autentiseringsuppgifterna för ett kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du uppdaterar eller återställer tjänstens huvudnamn eller AAD-programbehörighet för ett AKS-kluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 8420771e32aa792aa79a07fdf4362ad0d9b45d48
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392635"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Uppdatera eller rotera autentiseringsuppgifterna för Azure Kubernetes Service (AKS)

Som standard skapas AKS-kluster med ett tjänsthuvudnamn som har ett års förfallotid. När du närmar dig utgångsdatumet kan du återställa autentiseringsuppgifterna för att utöka tjänstens huvudnamn ytterligare under ytterligare en tidsperiod. Du kanske också vill uppdatera eller rotera autentiseringsuppgifterna som en del av en definierad säkerhetsprincip. I den här artikeln beskrivs hur du uppdaterar dessa autentiseringsuppgifter för ett AKS-kluster.

Du kan också ha [integrerat AKS-klustret med Azure Active Directory][aad-integration]och använda det som autentiseringsprovider för klustret. I så fall har du ytterligare två identiteter som skapats för klustret, AAD-serverappen och AAD-klientappen, kan du också återställa dessa autentiseringsuppgifter.

Du kan också använda en hanterad identitet för behörigheter i stället för ett tjänsthuvudnamn. Hanterade identiteter är enklare att hantera än tjänstens huvudnamn och kräver inte uppdateringar eller rotationer. Mer information finns i [Använda hanterade identiteter](use-managed-identity.md).

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.65 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Uppdatera eller skapa ett nytt tjänsthuvudnamn för AKS-klustret

När du vill uppdatera autentiseringsuppgifterna för ett AKS-kluster kan du välja att:

* uppdatera autentiseringsuppgifterna för det befintliga tjänsthuvudnamn som används av klustret, eller
* skapa ett huvudnamn för tjänsten och uppdatera klustret för att använda dessa nya autentiseringsuppgifter.

### <a name="reset-existing-service-principal-credential"></a>Återställa autentiseringsuppgifter för befintligt huvudnamn för tjänsten

Om du vill uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvudnamn hämtar du tjänstens huvud-ID för klustret med kommandot [az aks show.][az-aks-show] I följande exempel får ID:t för klustret som heter *myAKSCluster* i resursgruppen *myResourceGroup.* Tjänstens huvud-ID anges som en variabel med namnet *SP_ID* för användning i ytterligare kommando.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Med en variabeluppsättning som innehåller tjänstens huvud-ID återställer du nu autentiseringsuppgifterna med [az ad sp-återställning .][az-ad-sp-credential-reset] I följande exempel kan Azure-plattformen generera en ny säker hemlighet för tjänstens huvudnamn. Denna nya säkra hemlighet lagras också som en variabel.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Nu fortsätta med att [uppdatera AKS kluster med nya autentiseringsuppgifter för tjänstens huvudnamn](#update-aks-cluster-with-new-service-principal-credentials). Det här steget är nödvändigt för att ändringarna av tjänsthuvudhuvudet ska kunna reflektera över AKS-klustret.

### <a name="create-a-new-service-principal"></a>Skapa ett nytt huvudnamn för tjänsten

Om du väljer att uppdatera de befintliga autentiseringsuppgifterna för tjänstens huvudnamn i föregående avsnitt hoppar du över det här steget. Fortsätt att [uppdatera AKS-klustret med nya autentiseringsuppgifter för tjänstens huvudnamn](#update-aks-cluster-with-new-service-principal-credentials).

Om du vill skapa ett huvudnamn för tjänsten och sedan uppdatera AKS-klustret för att använda dessa nya autentiseringsuppgifter använder du kommandot [az ad sp create-for-rbac.][az-ad-sp-create] I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

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

Definiera nu variabler för tjänstens huvud-ID och klienthemlighet med hjälp av utdata från ditt eget [az ad sp create-for-rbac-kommando,][az-ad-sp-create] som visas i följande exempel. Den *SP_ID* är din *appId*och *SP_SECRET* är ditt *lösenord:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Nu fortsätta med att [uppdatera AKS kluster med nya autentiseringsuppgifter för tjänstens huvudnamn](#update-aks-cluster-with-new-service-principal-credentials). Det här steget är nödvändigt för att ändringarna av tjänsthuvudhuvudet ska kunna reflektera över AKS-klustret.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter för tjänsthuvudnamn

Oavsett om du väljer att uppdatera autentiseringsuppgifterna för det befintliga tjänstens huvudnamn eller skapa ett huvudnamn för tjänsten uppdaterar du nu AKS-klustret med dina nya autentiseringsuppgifter med kommandot [az aks update-credentials.][az-aks-update-credentials] Variablerna för *--service-principal* och *--client-secret* används:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Det tar en stund innan tjänstens huvudautentiseringsuppgifter uppdateras i AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter för AAD-program

Du kan skapa nya AAD-server- och klientprogram genom att följa stegen för [AAD-integrering][create-aad-app]. Eller återställ dina befintliga AAD-program enligt [samma metod som för återställning av tjänstens huvudnamn](#reset-existing-service-principal-credential). Efter att du behöver bara uppdatera din kluster AAD Application autentiseringsuppgifter med samma [az aks uppdatering-autentiseringsuppgifter][az-aks-update-credentials] kommando men med hjälp av *--reset-aad* variabler.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Nästa steg

I den här artikeln uppdaterades tjänstens huvudnamn för AKS-klustret och AAD-integrationsprogrammen. Mer information om hur du hanterar identitet för arbetsbelastningar i ett kluster finns [i Metodtips för autentisering och auktorisering i AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
