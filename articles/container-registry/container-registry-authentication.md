---
title: Alternativ för registerautentisering
description: Autentiseringsalternativ för ett privat Azure-behållarregister, inklusive inloggning med en Azure Active Directory-identitet, med hjälp av tjänstobjektnamn och med hjälp av valfria administratörsautentiseringsuppgifter.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247051"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autentisera med ett Azure-behållarregister

Det finns flera sätt att autentisera med ett Azure-behållarregister, som var och en gäller för ett eller flera scenarier för registeranvändning.

Rekommenderade sätt är att autentisera till ett register direkt via [individuell inloggning](#individual-login-with-azure-ad)eller så kan dina program och behållarorketorer utföra obevakad eller "huvudlös", autentisering med hjälp av ett Azure Active Directory [-tjänsthuvudnamn](#service-principal)(Azure AD).

## <a name="authentication-options"></a>Autentiseringsalternativ

I följande tabell visas tillgängliga autentiseringsmetoder och rekommenderade scenarier. Mer information finns i länkat innehåll.

| Metod                               | Så här autentiserar du                                           | Scenarier                                                            | RBAC                             | Begränsningar                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuell AD-identitet](#individual-login-with-azure-ad)                | `az acr login` i Azure CLI                             | Interaktiv push/pull av utvecklare, testare                                    | Ja                              | AD-token måste förnyas var tredje timme     |
| [HUVUDNAMN FÖR AD-tjänsten](#service-principal)                  | `docker login`<br/><br/>`az acr login`i Azure CLI<br/><br/> Inloggningsinställningar för registret i API:er eller verktyg<br/><br/> [Kubernetes dra hemlighet](container-registry-auth-kubernetes.md)                                           | Obevakad push från CI/CD-pipeline<br/><br/> Obevakad dragning till Azure eller externa tjänster  | Ja                              | SP lösenord standard utgång är 1 år       |                                                           
| [Integrera med AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Bifoga register när AKS-kluster har skapats eller uppdaterats  | Obevakad dragning till AKS-kluster                                                  | Nej, endast pull-åtkomst             | Endast tillgängligt med AKS-kluster            |
| [Hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` i Azure CLI                                       | Obevakad push från Azure CI/CD-pipeline<br/><br/> Obevakad dragning till Azure-tjänster<br/><br/>   | Ja                              | Använd endast från Azure-tjänster som [stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Administratörsanvändare](#admin-account)                            | `docker login`                                          | Interaktiv push/pull av enskild utvecklare eller testare                           | Nej, dra alltid och tryck på åtkomst  | Ett konto per register, rekommenderas inte för flera användare         |
| [Åtkomsttoken för databasscope](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`i Azure CLI   | Interaktiv push/pull till databas av enskild utvecklare eller testare<br/><br/> Obevakad push/pull till databasen av enskilda system eller externa enheter                  | Ja                              | Inte för närvarande integrerad med AD-identitet  |

## <a name="individual-login-with-azure-ad"></a>Individuell inloggning med Azure AD

När du arbetar med registret direkt, till exempel dra av bilder till och skicka avbildningar från en utvecklingsarbetsstation, autentisera med hjälp av az [acr-inloggningskommandot](/cli/azure/acr?view=azure-cli-latest#az-acr-login) i [Azure CLI:](/cli/azure/install-azure-cli)

```azurecli
az acr login --name <acrName>
```

När du loggar `az acr login`in med använder CLI den token som skapades när du utförde [az-inloggning](/cli/azure/reference-index#az-login) för att sömlöst autentisera din session med registret. För att slutföra autentiseringsflödet måste Docker installeras och köras i din miljö. `az acr login`använder Docker-klienten för att ange `docker.config` en Azure Active Directory-token i filen. När du har loggat in på det här sättet `docker` cachelagras dina autentiseringsuppgifter och efterföljande kommandon i sessionen kräver inget användarnamn eller lösenord.

> [!TIP]
> Används `az acr login` också för att autentisera en enskild identitet när du vill skicka eller hämta andra artefakter än Docker-avbildningar till registret, till exempel [OCI-artefakter](container-registry-oci-artifacts.md).  


För registeråtkomst är token `az acr login` som används av giltig i **3 timmar,** så vi rekommenderar `docker` att du alltid loggar in i registret innan du kör ett kommando. Om din token upphör att gälla kan `az acr login` du uppdatera den genom att använda kommandot igen för att återauktorisera. 

Om `az acr login` du använder med [Azure-identiteter](../role-based-access-control/role-assignments-portal.md)får rollbaserad åtkomst . För vissa scenarier kanske du vill logga in på ett register med din egen individuella identitet i Azure AD. För scenarier över flera tjänster eller för att hantera behoven hos en arbetsgrupp eller ett utvecklingsarbetsflöde där du inte vill hantera individuell åtkomst kan du också logga in med en [hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Tjänstens huvudnamn

Om du tilldelar ett [tjänsthuvudnamn](../active-directory/develop/app-objects-and-service-principals.md) till registret kan ditt program eller din tjänst använda det för huvudlös autentisering. Tjänstobjektnamn tillåter [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) till ett register och du kan tilldela flera tjänsthuvudnamn till ett register. Med flera tjänsthuvudnamn kan du definiera olika åtkomst för olika program.

De tillgängliga rollerna för ett behållarregister är:

* **AcrPull**: dra

* **AcrPush**: dra och tryck

* **Ägare**: dra, pusha och tilldela roller till andra användare

En fullständig lista över roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

Cli-skript som skapar ett tjänsthuvudnamn för autentisering med ett Azure-behållarregister och mer vägledning finns i [Azure Container Registry-autentisering med tjänsthuvudnamn](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Administratörskonto

Varje behållarregister innehåller ett administratörsanvändarkonto som är inaktiverat som standard. Du kan aktivera administratörsanvändaren och hantera dess autentiseringsuppgifter i Azure-portalen, eller genom att använda Azure CLI eller andra Azure-verktyg.

> [!IMPORTANT]
> Administratörskontot är utformat för att en enskild användare ska komma åt registret, främst för testning. Vi rekommenderar inte att du delar administratörskontouppgifterna mellan flera användare. Alla användare som autentiserar med administratörskontot visas som en enda användare med push och pull-åtkomst till registret. Om du ändrar eller inaktiverar det här kontot inaktiveras registeråtkomst för alla användare som använder dess autentiseringsuppgifter. Individuell identitet rekommenderas för användare och tjänsthuvudnamn för huvudlösa scenarier.
>

Administratörskontot har två lösenord, som båda kan återskapas. Med två lösenord kan du behålla anslutningen till registret med ett lösenord medan du återskapar det andra. Om administratörskontot är aktiverat kan du skicka användarnamnet och lösenordet till `docker login` kommandot när du uppmanas att ange grundläggande autentisering till registret. Ett exempel:

```
docker login myregistry.azurecr.io 
```

Bästa praxis för att hantera inloggningsuppgifter finns i kommandotreferens [för docker.For](https://docs.docker.com/engine/reference/commandline/login/) best practices to manage login credentials, see the docker login command reference.

Om du vill aktivera administratörsanvändaren för `--admin-enabled` ett befintligt register kan du använda parametern för kommandot [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) i Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Du kan aktivera administratörsanvändaren i Azure-portalen genom att navigera i registret, välja **Åtkomstnycklar** under **INSTÄLLNINGAR**och sedan **Aktivera** under **Admin-användare**.

![Aktivera användargränssnittet för administratörer i Azure-portalen][auth-portal-01]

## <a name="next-steps"></a>Nästa steg

* [Pusha din första avbildning med Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
