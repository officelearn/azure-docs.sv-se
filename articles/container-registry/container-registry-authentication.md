---
title: Alternativ för autentisering av registret
description: Autentiseringsalternativ för ett Azure Container Registry, inklusive att logga in med en Azure Active Directory identitet, med hjälp av tjänstens huvud namn och att använda valfria admin-autentiseringsuppgifter.
ms.topic: article
ms.date: 12/21/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29e23f6a983ccc2197e609511aee2ce13726ed0f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455387"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autentisera med ett privat Docker-behållar register

Det finns flera sätt att autentisera med ett Azure Container Registry, som är tillämpligt för ett eller flera register användnings scenarier.

Rekommenderade sätt är att autentisera till ett register direkt via [enskild inloggning](#individual-login-with-azure-ad), eller så kan dina program och behållar dirigering utföra obevakad, eller "dirigerad" autentisering med hjälp av en Azure Active Directory (Azure AD) [tjänstens huvud namn](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Individuell inloggning med Azure AD

När du arbetar med ditt register direkt, till exempel hämtar bilder till och skickar avbildningar från en utvecklings arbets Station, autentiserar du med hjälp av kommandot [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) i [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

När du loggar in med `az acr login`använder CLI den token som skapades när du körde [AZ-inloggningen](/cli/azure/reference-index#az-login) för att sömlöst autentisera din session med ditt register. När du har loggat in på det här sättet cachelagras dina autentiseringsuppgifter och efterföljande `docker` kommandon i din session kräver inte något användar namn eller lösen ord. 

För register åtkomst är den token som används av `az acr login` giltig i 1 timme, så vi rekommenderar att du alltid loggar in i registret innan du kör ett `docker`-kommando. Om din token går ut kan du uppdatera den genom att använda kommandot `az acr login` igen för att autentisera igen. 

Användning av `az acr login` med Azure-identiteter ger [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md). I vissa fall kanske du vill logga in i ett register med din egen identitet i Azure AD. För scenarier med olika tjänster eller för att hantera behovet av en arbets grupp där du inte vill hantera individuell åtkomst kan du också logga in med en [hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Tjänstens huvud namn

Om du tilldelar ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md) till ditt register kan programmet eller tjänsten använda det för konsol lös autentisering. Tjänstens huvud namn tillåter [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) till ett register och du kan tilldela flera tjänst huvud namn till ett register. Med flera tjänst huvud namn kan du definiera olika åtkomst för olika program.

Tillgängliga roller för ett behållar register är:

* **AcrPull**: pull

* **AcrPush**: pull och push

* **Ägare**: pull, push och tilldela roller till andra användare

En fullständig lista över roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

För CLI-skript för att skapa ett huvud namn för tjänsten för autentisering med ett Azure Container Registry och rikt linjer för att använda ett huvud namn för tjänsten, se [Azure Container Registry autentisering med tjänstens huvud namn](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Administratörs konto

Varje behållar register innehåller ett administratörs användar konto, som är inaktiverat som standard. Du kan aktivera administratörs användaren och hantera dess autentiseringsuppgifter i Azure Portal, eller med hjälp av Azure CLI eller andra Azure-verktyg.

> [!IMPORTANT]
> Administratörs kontot har utformats för att en enskild användare ska kunna komma åt registret, främst i testnings syfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörs kontot mellan flera användare. Alla användare som autentiseras med administratörs kontot visas som en enskild användare med push-och pull-åtkomst till registret. Genom att ändra eller inaktivera det här kontot inaktive ras register åtkomst för alla användare som använder sina autentiseringsuppgifter. Individuell identitet rekommenderas för användare och tjänst huvud namn för konsol lösta scenarier.
>

Administratörs kontot tillhandahålls med två lösen ord, som båda kan återskapas. Med två lösen ord kan du upprätthålla anslutning till registret genom att använda ett lösen ord när du återskapar det andra. Om administratörs kontot är aktiverat kan du skicka användar namnet och lösen ordet till kommandot `docker login` när du uppmanas att ange grundläggande autentisering för registret. Till exempel:

```
docker login myregistry.azurecr.io 
```

Metod tips för att hantera inloggnings uppgifter finns i kommando referensen [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Om du vill aktivera administratörs användaren för ett befintligt register kan du använda parametern `--admin-enabled` för kommandot [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) i Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Du kan aktivera administratörs användaren i Azure Portal genom att navigera i registret, välja **åtkomst nycklar** under **Inställningar**och sedan **Aktivera** under **Administratörs användare**.

![Aktivera administratörens användar gränssnitt i Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Nästa steg

* [Skicka din första avbildning med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
