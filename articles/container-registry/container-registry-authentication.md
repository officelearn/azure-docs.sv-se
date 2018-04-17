---
title: Autentisera med en Azure-behållaren registret
description: Alternativ för autentisering för ett Azure-behållaren registret, inklusive Azure Active Directory-tjänsten säkerhetsobjekt direkt och via registret inloggningen.
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 349d4f8cba2967edcedb202979695d271283fa8b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autentisera med ett privat Docker behållare register

Det finns flera sätt att autentisera med en Azure-behållaren registret, som gäller för en eller flera Användningsscenarier för registret.

Du kan logga in till ett register direkt via [enskilda inloggningen](#individual-login-with-azure-ad), och dina program och behållare orchestrators kan utföra obevakade eller ”fjärradministrerade” autentisering med hjälp av en Azure Active Directory (AD Azure) [ tjänstens huvudnamn](#service-principal).

Azure Container registret stöder inte oautentiserad Docker-operationer eller anonym åtkomst. Du kan använda för offentliga bilder [Docker-hubb](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Enskilda inloggning med Azure AD

När du arbetar med registret direkt, till exempel dra bilder till och skicka bilder från utvecklingsdatorn, autentisera med hjälp av den [az acr inloggning](/cli/azure/acr?view=azure-cli-latest#az_acr_login) i den [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

När du loggar in med `az acr login`, CLI använder den token som skapas när du körde `az login` att sömlöst autentisera sessionen med registret. När du har loggat in det här sättet kan dina autentiseringsuppgifter är cachelagrade och efterföljande `docker` kommandon inte kräver ett användarnamn eller lösenord. Om din token upphör att gälla, kan du uppdatera den med hjälp av den `az acr login` kommandot igen för att autentiseras. Med hjälp av `az acr login` med identiteter som Azure tillhandahåller [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Tjänstens huvudnamn

Du kan tilldela en [tjänstens huvudnamn](../active-directory/develop/active-directory-application-objects.md) i registret och programmet eller tjänsten kan använda den för fjärradministrerade autentisering. Tjänstens huvudnamn Tillåt [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) registret, och du kan tilldela ett register flera tjänstens huvudnamn. Flera tjänstens huvudnamn kan du definiera olika åtkomst för olika program.

Tillgängliga roller är:

  * **Läsaren**: pull
  * **Deltagare**: pull och push
  * **Ägare**: pull-, push och tilldela roller till andra användare

Tjänstens huvudnamn aktivera fjärradministrerade anslutning till ett register i både sändnings- och scenarier som liknar följande:

  * *Läsaren*: distribution i behållare från ett register till orchestration system, inklusive Kubernetes DC/OS och Docker Swarm. Du kan också dra från behållaren register till relaterade Azure-tjänster som [AKS](../aks/index.yml), [Apptjänst](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), och övriga.

  * *Deltagare*: kontinuerlig integration och distribuera lösningar som Visual Studio Team Services VSTS () eller Jenkins som bygger avbildningar för behållaren och skicka dem till ett register.

> [!TIP]
> Du kan återskapa lösenordet för ett huvudnamn för tjänsten genom att köra den [az Återställ-autentiseringsuppgifter för sp ad](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) kommando.
>

Du kan också logga in direkt med en tjänstens huvudnamn. Ange app-ID och lösenord för tjänsten huvudnamn för den `docker login` kommando:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

När du loggade in cachelagrar Docker autentiseringsuppgifter så att du inte behöver komma ihåg app-ID.

Beroende på vilken version av Docker som du har installerat kan du se en säkerhetsvarning rekommenderar användning av den `--password-stdin` parameter. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Mer information finns i [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot referens.

Läs mer om hur du använder ett huvudnamn för tjänsten fjärradministrerade autentisering till ACR [Azure Container registret autentisering med tjänstens huvudnamn](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Administratörskonto

Varje behållare registret innehåller ett administratörskonto för användare som är inaktiverad som standard. Du kan aktivera administratörsanvändare och hantera sina autentiseringsuppgifter i den [Azure-portalen](container-registry-get-started-portal.md#create-a-container-registry), eller genom att använda Azure CLI.

> [!IMPORTANT]
> Administratörskontot är avsett för en enskild användare att komma åt registret, främst för testning. Vi rekommenderar inte dela administratörsautentiseringsuppgifter för kontot med flera användare. Alla användare som autentiseras med administratörskontot visas som en enskild användare med push och pull åtkomst till registret. Ändra eller inaktivera det här kontot inaktiverar du registret åtkomst för alla användare som använder sina autentiseringsuppgifter. Enskilda identitet rekommenderas för användare och tjänstens huvudnamn för fjärradministrerade scenarier.
>

Administratörskontot tillhandahålls med två lösenord som genereras. Två lösenord kan du hantera anslutning i registret med hjälp av ett lösenord när du återskapar den andra. Om administratörskontot är aktiverad, du kan ange användarnamnet och antingen lösenord för att den `docker login` kommandot för grundläggande autentisering i registret. Exempel:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Igen, Docker rekommenderar att du använder den `--password-stdin` parameter i stället för att tillhandahålla på kommandoraden för ökad säkerhet. Du kan också ange bara användarnamnet utan `-p`, och ange ditt lösenord när du uppmanas.

Om du vill aktivera administratörsanvändare för en befintlig registernyckeln kan du använda den `--admin-enabled` parameter för den [az acr uppdatering](/cli/azure/acr?view=azure-cli-latest#az_acr_update) i Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Du kan aktivera administratörsanvändare i Azure-portalen genom att navigera i registret, välja **åtkomstnycklar** under **inställningar**, sedan **aktivera** under **Admin användaren**.

![Aktivera administratörsanvändare Användargränssnittet i Azure-portalen][auth-portal-01]

## <a name="next-steps"></a>Nästa steg

* [Push-en avbildning med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
