---
title: Alternativ för autentisering av registret
description: Autentiseringsalternativ för ett privat Azure Container Registry, inklusive att logga in med en Azure Active Directory identitet, med hjälp av tjänstens huvud namn och med valfria administratörs behörighet.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712045"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autentisera med ett Azure Container Registry

Det finns flera sätt att autentisera med ett Azure Container Registry, som är tillämpligt för ett eller flera register användnings scenarier.

Rekommenderade sätt är att autentisera till ett register direkt via [enskild inloggning](#individual-login-with-azure-ad), eller så kan dina program och behållar dirigering utföra obevakad, eller "dirigerad" autentisering med hjälp av en Azure Active Directory (Azure AD) [tjänstens huvud namn](#service-principal).

## <a name="authentication-options"></a>Autentiseringsalternativ

I följande tabell visas tillgängliga autentiseringsmetoder och rekommenderade scenarier. Mer information finns i länkat innehåll.

| Metod                               | Så här autentiserar du                                           | Scenarier                                                            | RBAC                             | Begränsningar                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuell AD-identitet](#individual-login-with-azure-ad)                | `az acr login` i Azure CLI                             | Interaktiva push/pull från utvecklare, testare                                    | Ja                              | AD-token måste förnyas var 3: e timme     |
| [AD-tjänstens huvud namn](#service-principal)                  | `docker login`<br/><br/>`az acr login`i Azure CLI<br/><br/> Inloggnings inställningar för registret i API: er eller verktyg<br/><br/> [Kubernetes pull-hemlighet](container-registry-auth-kubernetes.md)                                           | Obevakad push från CI/CD-pipeline<br/><br/> Obevakad hämtning till Azure eller externa tjänster  | Ja                              | SP lösen ordets standard utgångs datum är 1 år       |                                                           
| [Integrera med AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Koppla registret när AKS-kluster har skapats eller uppdaterats  | Obevakad pull till AKS-kluster                                                  | Nej, endast pull-åtkomst             | Endast tillgängligt med AKS-kluster            |
| [Hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` i Azure CLI                                       | Obevakad push från Azure CI/CD-pipeline<br/><br/> Obevakad hämtning till Azure-tjänster<br/><br/>   | Ja                              | Använd endast från Azure-tjänster som har [stöd för hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Administratörs användare](#admin-account)                            | `docker login`                                          | Interaktiv push/pull från enskild utvecklare eller testare                           | Nej, Hämta alltid och push-åtkomst  | Ett enda konto per register, rekommenderas inte för flera användare         |
| [Databas – begränsad åtkomsttoken](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`i Azure CLI   | Interaktiv push/pull till lagrings plats av enskild utvecklare eller testare<br/><br/> Obevakad push/pull till lagrings plats av enskilda system eller externa enheter                  | Ja                              | Är för närvarande inte integrerat med AD-identitet  |

## <a name="individual-login-with-azure-ad"></a>Individuell inloggning med Azure AD

När du arbetar med ditt register direkt, till exempel hämtar bilder till och skickar avbildningar från en utvecklings arbets Station, autentiserar du med hjälp av kommandot [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) i [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

När du loggar in med `az acr login` använder CLI den token som skapades när du körde [AZ-inloggningen](/cli/azure/reference-index#az-login) för att sömlöst autentisera din session med ditt register. För att slutföra det här autentiseringsalternativet måste Docker vara installerat och köras i din miljö. `az acr login`använder Docker-klienten för att ange en Azure Active Directory token i `docker.config` filen. När du har loggat in på det här sättet cachelagras dina autentiseringsuppgifter och efterföljande `docker` kommandon i din session kräver inte något användar namn eller lösen ord.

> [!TIP]
> Används också `az acr login` för att autentisera en enskild identitet när du vill skicka eller ta emot andra artefakter än Docker-avbildningar till registret, till exempel [OCI-artefakter](container-registry-oci-artifacts.md).  


För register åtkomst rekommenderar vi att token som används av `az acr login` är giltig i **tre timmar**, så vi rekommenderar att du alltid loggar in i registret innan du kör ett `docker` kommando. Om din token går ut kan du uppdatera den genom att använda `az acr login` kommandot igen för att autentisera igen. 

`az acr login`Att använda med Azure-identiteter ger [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md). I vissa fall kanske du vill logga in på ett register med en egen individuell identitet i Azure AD. Du kan också logga in med en [hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md)för scenarier med olika tjänster eller för att hantera behoven hos en arbets grupp eller ett utvecklings arbets flöde där du inte vill hantera individuell åtkomst.

## <a name="service-principal"></a>Tjänstens huvudnamn

Om du tilldelar ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md) till ditt register kan programmet eller tjänsten använda det för konsol lös autentisering. Tjänstens huvud namn tillåter [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) till ett register och du kan tilldela flera tjänst huvud namn till ett register. Med flera tjänst huvud namn kan du definiera olika åtkomst för olika program.

Tillgängliga roller för ett behållar register är:

* **AcrPull**: pull

* **AcrPush**: pull och push

* **Ägare**: pull, push och tilldela roller till andra användare

En fullständig lista över roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

För CLI-skript för att skapa ett huvud namn för tjänsten för autentisering med ett Azure Container Registry, och mer information, se [Azure Container Registry autentisering med tjänstens huvud namn](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Administratörskonto

Varje behållar register innehåller ett administratörs användar konto, som är inaktiverat som standard. Du kan aktivera administratörs användaren och hantera dess autentiseringsuppgifter i Azure Portal, eller med hjälp av Azure CLI eller andra Azure-verktyg.

> [!IMPORTANT]
> Administratörs kontot har utformats för att en enskild användare ska kunna komma åt registret, främst i testnings syfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörs kontot mellan flera användare. Alla användare som autentiseras med administratörs kontot visas som en enskild användare med push-och pull-åtkomst till registret. Genom att ändra eller inaktivera det här kontot inaktive ras register åtkomst för alla användare som använder sina autentiseringsuppgifter. Individuell identitet rekommenderas för användare och tjänst huvud namn för konsol lösta scenarier.
>

Administratörs kontot tillhandahålls med två lösen ord, som båda kan återskapas. Med två lösen ord kan du upprätthålla anslutning till registret genom att använda ett lösen ord när du återskapar det andra. Om administratörs kontot är aktiverat kan du skicka användar namn och lösen ord till `docker login` kommandot när du uppmanas att ange grundläggande autentisering för registret. Ett exempel:

```
docker login myregistry.azurecr.io 
```

Metod tips för att hantera inloggnings uppgifter finns i kommando referensen [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

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
