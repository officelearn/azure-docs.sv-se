---
title: "Autentisera med en Azure-behållaren registret | Microsoft Docs"
description: "Hur du loggar in till en Azure-behållaren registret med hjälp av ett huvudnamn för tjänsten Azure Active Directory eller ett administratörskonto"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d7d2ae0e9b1f7850332d151d78a4a5fdb013777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autentisera med ett privat Docker behållare register
Om du vill arbeta med behållare avbildningar i Azure-behållaren-registret, logga in med den `docker login` kommando. Du kan logga in med antingen en  **[Azure Active Directory-tjänstens huvudnamn](../active-directory/active-directory-application-objects.md)**  eller registret-specifika **administratörskonto**. Den här artikeln innehåller mer information om dessa identiteter.

## <a name="service-principal"></a>Tjänstens huvudnamn

Du kan tilldela ett huvudnamn för tjänsten i registret och använda den för grundläggande Docker-autentisering. Du bör använda ett huvudnamn för tjänsten för de flesta scenarier. Ange app-ID och lösenord för tjänsten huvudnamn för den `docker login` kommandot som visas i följande exempel:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

När du loggade in cachelagrar Docker autentiseringsuppgifter så att du inte behöver komma ihåg app-ID.

> [!TIP]
> Om du vill kan du återskapa lösenordet för ett huvudnamn för tjänsten genom att köra den `az ad sp reset-credentials` kommando.
>

Tjänstens huvudnamn Tillåt [rollbaserad åtkomst](../active-directory/role-based-access-control-configure.md) till ett register. Tillgängliga roller är:
  * Läsare (pull endast åtkomst).
  * Deltagare (pull och push).
  * Ägaren (pull-, push- och tilldela roller till andra användare).

Anonym åtkomst är inte tillgänglig på Azure-behållare register. Du kan använda för offentliga bilder [Docker-hubb](https://docs.docker.com/docker-hub/).

Du kan tilldela flera tjänstens huvudnamn i registret, där du kan definiera åtkomst för olika användare eller program. Tjänstens huvudnamn också aktivera ”fjärradministrerade” anslutning till ett register i utvecklare eller DevOps scenarier, till exempel i följande exempel:

  * Distribution i behållare från ett register till orchestration system, inklusive DC/OS, Docker Swarm och Kubernetes. Du kan också dra behållaren register till relaterade Azure-tjänster som [Behållartjänsten](../container-service/index.yml), [Apptjänst](../app-service/index.yml), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/), med mera.

  * Kontinuerlig integration och distribuera lösningar (till exempel Visual Studio Team Services eller Jenkins) som bygger avbildningar för behållaren och skicka dem till ett register.


## <a name="admin-account"></a>Administratörskonto
Med varje register som du skapar skapas ett administratörskonto automatiskt. Kontot är inaktiverat som standard, men du kan aktivera och hantera autentiseringsuppgifter, till exempel via den [portal](container-registry-get-started-portal.md#create-a-container-registry) eller med hjälp av den [Azure CLI 2.0 kommandon](container-registry-get-started-azure-cli.md#create-a-container-registry). Varje administratörskontot tillhandahålls med två lösenord som genereras. De båda lösenorden kan du hantera anslutningar till registret med hjälp av ett lösenord när du återskapar det andra lösenordet. Om kontot är aktiverat kan du skickar användarnamnet och antingen lösenord för att den `docker login` kommandot för grundläggande autentisering i registret. Exempel:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> Administratörskontot är avsett för en enskild användare att komma åt registret, främst för testning. Det rekommenderas inte att dela konto administratörsautentiseringsuppgifter bland andra användare. Alla användare som visas som en enskild användare i registret. Ändra eller inaktivera det här kontot inaktiverar du registret åtkomst för alla användare som använder autentiseringsuppgifterna.
>

### <a name="next-steps"></a>Nästa steg
* [Push-en avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md).
* Mer information om autentisering i behållaren registret preview finns i [blogginlägget](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).
