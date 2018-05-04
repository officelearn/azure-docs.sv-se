---
title: Tjänstens huvudnamn för Azure Kubernetes-kluster
description: Skapa och hantera en tjänsts huvudnamn för Azure Active Directory för ett Kubernetes-kluster i AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 81f455668e81c2a6c21b66d85199da3f475e7265
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="service-principals-with-azure-container-service-aks"></a>Tjänstens huvudnamn med Azure Container Service (AKS)

Ett AKS-kluster kräver ett [Azure Active Directory-huvudnamn för tjänsten][aad-service-principal] för att kunna interagera med Azure-API:er. Tjänstens huvudnamn krävs för att dynamiskt skapa och hantera resurser som [Azure Load Balancer][azure-load-balancer-overview].

Den här artikeln beskriver olika alternativ för att konfigurera ett huvudnamn för tjänsten för ett Kubernetes-kluster i AKS.

## <a name="before-you-begin"></a>Innan du börjar


För att skapa ett Azure AD-huvudnamn för tjänsten måste du ha behörighet att registrera ett program med din Azure AD-klientorganisation, samt behörighet att tilldela programmet till en roll i din prenumeration. Om du inte har de behörigheter som du behöver kan du be din Azure AD- eller prenumerationsadministratör att tilldela de nödvändiga behörigheterna eller att skapa ett huvudnamn för tjänsten för Kubernetes-klustret.

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.27 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Skapa SP med AKS-kluster

När du distribuerar ett AKS-kluster med kommandot `az aks create` har du möjlighet att automatiskt generera ett huvudnamn för tjänsten.

I följande exempel skapas ett AKS-kluster och eftersom ett befintlig huvudnamn för tjänsten inte har angetts så skapas ett för klustret. Kontot måste ha rätt behörigheter för att skapa ett huvudnamn för tjänsten för att kunna slutföra den här åtgärden.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Använda en befintlig SP

Ett befintligt Azure AD-huvudnamn för tjänsten kan användas eller skapas i förväg för användning med ett AKS-kluster. Det här är användbart när du distribuerar ett kluster från Azure Portal där du måste ange information om tjänstens huvudnamn. När du använder ett befintligt huvudnamn för tjänsten måste klienthemligheten konfigureras som ett lösenord.

## <a name="pre-create-a-new-sp"></a>Skapa en ny SP i förväg

Använd kommandot [az ad sp create-for-rbac][az-ad-sp-create] för att skapa tjänstens huvudnamn med Azure CLI.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

De utdata som genereras liknar följande. Anteckna `appId` och `password`. De här värdena används när du skapar ett AKS-kluster.

```json
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
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Om du distribuerar ett AKS-kluster med hjälp av Azure Portal så anger du värdet `appId` i fältet **Service principal client ID** (Klient-ID för tjänstens huvudnamn) och värdet `password` i fältet **Service principal client secret** (Klienthemlighet för tjänstens huvudnamn) i konfigurationsformuläret för AKS-klustret.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Tänk på följande när du arbetar med AKS och Azure AD-tjänstens huvudnamn.

* Tjänstobjektet för Kubernetes är en del av klusterkonfigurationen. Men använd inte identiteten för att distribuera klustret.
* Varje tjänstobjekt är associerat med ett Azure AD-program. Tjänstobjektet för ett Kubernetes-kluster kan associeras med ett giltigt Azure Active Directory-programnamn (till exempel: `https://www.contoso.org/example`). URL:en för programmet behöver inte vara en verklig slutpunkt.
* När du anger **klient-id:t** för tjänstens huvudnamn använder du värdet för `appId` (som visas i den här artikeln) eller motsvarande `name` för tjänstens huvudnamn (till exempel `https://www.contoso.org/example`).
* På virtuella huvud- och noddatorer i Kubernetes-klustret lagras autentiseringsuppgifterna för tjänstobjektet i filen `/etc/kubernetes/azure.json`.
* Om du använder kommandot `az aks create` för att generera tjänstobjektet automatiskt skrivs autentiseringsuppgifterna för tjänstobjektet till filen `~/.azure/aksServicePrincipal.json` på den dator som används för att köra kommandot.
* När du tar bort ett AKS-kluster som har skapats av `az aks create` tas inte tjänstens huvudnamn som skapades automatiskt bort. Använd `az ad sp delete --id $clientID` för att ta bort det.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory-tjänstens huvudnamn finns i programdokumentationen för Azure AD.

> [!div class="nextstepaction"]
> [Objekt för program och tjänstens huvudnamn][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md