---
title: Azure Container Registry autentisering med ett huvud namn för tjänsten
description: Ge åtkomst till avbildningar i ditt privata behållar register med hjälp av ett Azure Active Directory tjänstens huvud namn.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 16ad37eaa50f0c3825d131338cc4a0abdc369978
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262881"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry autentisering med tjänstens huvud namn

Du kan använda ett Azure Active Directory (Azure AD) tjänstens huvud namn för att tillhandahålla behållar avbildning `docker push` och @no__t 1-åtkomst till ditt behållar register. Genom att använda ett huvud namn för tjänsten kan du ge åtkomst till "konsol löst" tjänster och program.

## <a name="what-is-a-service-principal"></a>Vad är ett huvudnamn för tjänsten?

Azure AD *-tjänstens huvud namn* ger åtkomst till Azure-resurser i din prenumeration. Du kan tänka på ett huvud namn för tjänsten som användar identitet för en tjänst, där "tjänst" är alla program, tjänster eller plattformar som behöver åtkomst till resurserna. Du kan konfigurera ett huvud namn för tjänsten med enbart åtkomst rättigheter för de resurser som du anger. Konfigurera sedan ditt program eller din tjänst att använda tjänstens huvud namn för att få åtkomst till dessa resurser.

I samband med Azure Container Registry kan du skapa en Azure AD-tjänstens huvud namn med pull, push och pull eller andra behörigheter till ditt privata register i Azure. En fullständig lista finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Varför ska jag använda ett huvud namn för tjänsten?

Genom att använda ett Azure AD-tjänstens huvud namn kan du ge begränsad åtkomst till ditt privata behållar register. Skapa olika tjänst huvud namn för var och en av dina program eller tjänster, var och en med anpassade åtkomst rättigheter till registret. Eftersom du kan undvika att dela autentiseringsuppgifter mellan tjänster och program kan du rotera autentiseringsuppgifter eller återkalla åtkomsten för enbart tjänstens huvud namn (och därmed det program) du väljer.

Du kan till exempel konfigurera ditt webb program så att det använder ett huvud namn för tjänsten som tillhandahåller avbildning `pull` endast åtkomst, medan ditt build-system använder ett huvud namn för tjänsten som ger det både `push`-och `pull`-åtkomst. Om utvecklingen av dina program ändringar händer kan du rotera dess autentiseringsuppgifter för tjänst principen utan att påverka build-systemet.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvud namn för tjänsten

Du bör använda ett huvud namn för tjänsten för att ge register åtkomst i **konsolbaserade scenarier**. Det vill säga alla program, tjänster eller skript som måste push-överföra eller hämta behållar avbildningar på ett automatiserat eller på annat sätt obevakat sätt. Exempel:

  * *Hämta*: Distribuera behållare från ett register till Orchestration-system, inklusive KUBERNETES, DC/OS och Docker Swarm. Du kan också hämta från behållar register till relaterade Azure-tjänster som [Azure Kubernetes service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)och andra.

  * *Push*: Bygg behållar avbildningar och skicka dem till ett register med kontinuerlig integrering och distributions lösningar som Azure-pipeliner eller Jenkins.

För individuell åtkomst till ett register, till exempel när du hämtar en behållar avbildning manuellt till din utvecklings arbets Station, rekommenderar vi att du använder din egen [Azure AD-identitet](container-registry-authentication.md#individual-login-with-azure-ad) i stället för register åtkomst (till exempel med [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Exempelskript

Du kan hitta föregående exempel skript för Azure CLI på GitHub, samt versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autentisera med tjänstens huvud namn

När du har fått ett huvud namn för tjänsten som du har beviljat åtkomst till behållar registret kan du konfigurera dess autentiseringsuppgifter för åtkomst till "automatiskt" tjänster och program, eller ange dem med kommandot `docker login`. Ange följande värden:

* **Användar namn** – program-ID för tjänstens huvud namn (kallas även *klient-ID*)
* **Lösen** ord för tjänstens huvud namn (kallas även *klient hemlighet*)

Varje värde är ett GUID för formuläret `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Du kan återskapa lösen ordet för ett huvud namn för tjänsten genom att köra kommandot [AZ AD SP reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Använd autentiseringsuppgifter med Azure-tjänster

Du kan använda autentiseringsuppgifter för tjänstens huvud namn från alla Azure-tjänster som kan autentiseras med ett Azure Container Registry.  Använd autentiseringsuppgifterna för tjänstens huvud namn i stället för registrets administratörsautentiseringsuppgifter för en mängd olika scenarier.

Använd till exempel autentiseringsuppgifterna för att hämta en avbildning från ett Azure Container Registry till [Azure Container instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Använd med Docker-inloggning

Du kan också köra `docker login` med ett huvud namn för tjänsten. I följande exempel skickas ID: t för tjänstens huvud namn i miljövariabeln `$SP_APP_ID`, och lösen ordet i variabeln `$SP_PASSWD`. Metod tips för att hantera Docker-autentiseringsuppgifter finns i kommando referensen [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

När du har loggat in cachelagras autentiseringsuppgifterna av Docker.

## <a name="next-steps"></a>Nästa steg

* I [översikten över autentiseringen](container-registry-authentication.md) finns andra scenarier för att autentisera med ett Azure Container Registry.

* Ett exempel på hur du använder ett Azure Key Vault för att lagra och hämta autentiseringsuppgifter för tjänstens huvud namn för ett behållar register finns i självstudien för att [skapa och distribuera en behållar avbildning med ACR uppgifter](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
