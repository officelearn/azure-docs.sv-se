---
title: Autentisera med ett Azure Container Registry
description: Autentiseringsalternativ för ett Azure Container Registry, inklusive att logga in med en Azure Active Directory identitet, med hjälp av tjänstens huvud namn och att använda valfria admin-autentiseringsuppgifter.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 82fe80e098ee95c09c4a1400068ab813910e0e1a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309838"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autentisera med ett privat Docker-behållar register

Det finns flera sätt att autentisera med ett Azure Container Registry, som är tillämpligt för ett eller flera register användnings scenarier.

Du kan logga in i ett register direkt via [enskild inloggning](#individual-login-with-azure-ad), eller så kan dina program och behållar dirigering utföra obevakad, eller "dirigerad" autentisering med hjälp av en Azure Active Directory (Azure AD) [tjänstens huvud namn](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Individuell inloggning med Azure AD

När du arbetar med ditt register direkt, till exempel hämtar bilder till och skickar avbildningar från en utvecklings arbets Station, autentiserar du med hjälp av kommandot [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) i [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

När du loggar in med `az acr login`använder CLI den token som skapades när du körde [AZ](/cli/azure/reference-index#az-login) -inloggningen för att sömlöst autentisera din session med ditt register. När du har loggat in på det här sättet cachelagras dina autentiseringsuppgifter och `docker` efterföljande kommandon i din session kräver inte något användar namn eller lösen ord. 

För register åtkomst rekommenderar vi att token `az acr login` som används av är giltig i 1 timme, så vi rekommenderar att du alltid loggar in i registret innan `docker` du kör ett kommando. Om din token går ut kan du uppdatera den genom att använda `az acr login` kommandot igen för att autentisera igen. 

Att `az acr login` använda med Azure-identiteter ger [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md). I vissa fall kanske du vill logga in i ett register med din egen identitet i Azure AD. För scenarier med olika tjänster eller för att hantera behovet av en arbets grupp där du inte vill hantera individuell åtkomst kan du också logga in med en [hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Tjänstens huvud namn

Om du tilldelar ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md) till ditt register kan programmet eller tjänsten använda det för konsol lös autentisering. Tjänstens huvud namn tillåter [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) till ett register och du kan tilldela flera tjänst huvud namn till ett register. Med flera tjänst huvud namn kan du definiera olika åtkomst för olika program.

Tillgängliga roller för ett behållar register är:

* **AcrPull**: pull

* **AcrPush**: pull och push

* **Ägare**: pull, push och tilldela roller till andra användare

En fullständig lista över roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

För CLI-skript för att skapa ett program-ID för tjänstens huvud namn och lösen ord för att autentisera med ett Azure Container Registry, eller om du vill använda ett befintligt huvud namn för tjänsten, se [Azure Container Registry autentisering med tjänstens huvud namn](container-registry-auth-service-principal.md).

Tjänstens huvud namn möjliggör konsol lös anslutning till ett register i både pull-och push-scenarier som följande:

  * *Hämta*: Distribuera behållare från ett register till Orchestration-system, inklusive Kubernetes, DC/OS och Docker Swarm. Du kan också hämta från behållar register till relaterade Azure-tjänster som [Azure Kubernetes service](container-registry-auth-aks.md), [Azure Container instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)och andra.

  * *Push*: Bygg behållar avbildningar och skicka dem till ett register med kontinuerliga integrerings-och distributions lösningar som Azure-pipeliner eller Jenkins.

Du kan också logga in direkt med ett huvud namn för tjänsten. När du kör följande kommando anger du tjänstens huvud namn (username) och lösen ord när du uppmanas att göra det. Metod tips för att hantera inloggnings uppgifter finns i [](https://docs.docker.com/engine/reference/commandline/login/) kommando referensen Docker login:

```
docker login myregistry.azurecr.io
```

När du är inloggad cachelagrar Docker autentiseringsuppgifterna, så du behöver inte komma ihåg appens ID.

> [!TIP]
> Du kan återskapa lösen ordet för ett huvud namn för tjänsten genom att köra kommandot [AZ AD SP reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest) .
>

## <a name="admin-account"></a>Administratörs konto

Varje behållar register innehåller ett administratörs användar konto, som är inaktiverat som standard. Du kan aktivera administratörs användaren och hantera dess autentiseringsuppgifter i Azure Portal, eller med hjälp av Azure CLI eller andra Azure-verktyg.

> [!IMPORTANT]
> Administratörs kontot har utformats för att en enskild användare ska kunna komma åt registret, främst i testnings syfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörs kontot med flera användare. Alla användare som autentiseras med administratörs kontot visas som en enskild användare med push-och pull-åtkomst till registret. Genom att ändra eller inaktivera det här kontot inaktive ras register åtkomst för alla användare som använder sina autentiseringsuppgifter. Individuell identitet rekommenderas för användare och tjänst huvud namn för konsol lösta scenarier.
>

Administratörs kontot tillhandahålls med två lösen ord, som båda kan återskapas. Med två lösen ord kan du upprätthålla anslutning till registret genom att använda ett lösen ord när du återskapar det andra. Om administratörs kontot är aktiverat kan du skicka användar namn och lösen ord till `docker login` kommandot när du uppmanas att ange grundläggande autentisering för registret. Exempel:

```
docker login myregistry.azurecr.io 
```


Om du vill aktivera administratörs användaren för ett befintligt register kan du använda `--admin-enabled` parametern för kommandot [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) i Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Du kan aktivera administratörs användaren i Azure Portal genom att navigera i registret, välja **åtkomst nycklar** under **Inställningar**och sedan **Aktivera** under **Administratörs användare**.

![Aktivera administratörens användar gränssnitt i Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Nästa steg

* [Skicka din första avbildning med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
