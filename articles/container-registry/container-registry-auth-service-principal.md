---
title: Autentisera med tjänstens huvudnamn
description: Ge åtkomst till avbildningar i ditt privata behållarregister med hjälp av ett huvudnamn för Tjänsten Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455403"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry autentisering med tjänsthuvudnamn

Du kan använda huvudnumret för Azure Active Directory `docker push` (Azure AD) för att tillhandahålla behållaravbildning och `pull` åtkomst till behållarregistret. Genom att använda ett huvudnamn för tjänsten kan du ge tillgång till "huvudlösa" tjänster och program.

## <a name="what-is-a-service-principal"></a>Vad är ett huvudnamn för tjänsten?

Azure *AD-tjänsthuvudnamn* ger åtkomst till Azure-resurser i din prenumeration. Du kan tänka dig ett tjänsthuvudnamn som en användaridentitet för en tjänst, där "tjänst" är ett program, en tjänst eller en plattform som behöver komma åt resurserna. Du kan konfigurera ett tjänsthuvudnamn med åtkomsträttigheter som endast är begränsade till de resurser som du anger. Konfigurera sedan programmet eller tjänsten så att tjänstens huvudnamn används för att komma åt dessa resurser.

I kontexten för Azure Container Registry kan du skapa ett Azure AD-tjänsthuvudnamn med pull, push and pull eller andra behörigheter till ditt privata register i Azure. En fullständig lista finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Varför använda ett huvudnamn för tjänsten?

Med hjälp av ett Azure AD-tjänsthuvudnamn kan du ange begränsad åtkomst till ditt privata behållarregister. Skapa olika tjänsthuvudnamn för var och en av dina program eller tjänster, var och en med skräddarsydda åtkomsträttigheter till ditt register. Och eftersom du kan undvika att dela autentiseringsuppgifter mellan tjänster och program kan du rotera autentiseringsuppgifter eller återkalla åtkomst för endast tjänstens huvudnamn (och därmed programmet) som du väljer.

Konfigurera till exempel webbprogrammet så att det använder `pull` ett huvudnamn för tjänsten som endast ger det `push` `pull` avbildningsåtkomst, medan byggsystemet använder ett huvudnamn för tjänsten som ger det både och åtkomst. Om utvecklingen av programmet byter ägare kan du rotera dess autentiseringsuppgifter för tjänstens huvudnamn utan att påverka byggsystemet.

## <a name="when-to-use-a-service-principal"></a>När ska ett tjänsthuvudnamn användas

Du bör använda ett huvudnamn för tjänsten för att ge registeråtkomst i **huvudlösa scenarier**. Det vill säga alla program, tjänster eller skript som måste skicka eller hämta behållaravbildningar på ett automatiserat eller på annat sätt obevakat. Ett exempel:

  * *Dra:* Distribuera behållare från ett register till orchestration system inklusive Kubernetes, DC / OS och Docker Swarm. Du kan också hämta från behållarregister till relaterade Azure-tjänster som [Azure Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)och andra.

  * *Push:* Skapa behållaravbildningar och skicka dem till ett register med hjälp av kontinuerliga integrations- och distributionslösningar som Azure Pipelines eller Jenkins.

För individuell åtkomst till ett register, till exempel när du manuellt hämtar en behållaravbildning till din utvecklingsarbetsstation, rekommenderar vi att du använder din egen [Azure AD-identitet](container-registry-authentication.md#individual-login-with-azure-ad) i stället för registeråtkomst (till exempel med [az acr-inloggning][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Exempelskript

Du hittar föregående exempelskript för Azure CLI på GitHub, samt versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autentisera med tjänstens huvudnamn

När du har ett tjänsthuvudnamn som du har beviljat åtkomst till behållarregistret kan du konfigurera dess autentiseringsuppgifter `docker login` för åtkomst till "huvudlösa" tjänster och program eller ange dem med kommandot. Ange följande värden:

* **Användarnamn** - tjänsthuvudnamnsprogram-ID (kallas även *klient-ID)*
* **Lösenord** - tjänstens huvudlösenord (kallas även *klienthemlighet)*

Varje värde är ett GUID i formuläret `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Du kan återskapa lösenordet för ett tjänsthuvudnamn genom att köra kommandot [az ad sp reset-credentials.](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
>

### <a name="use-credentials-with-azure-services"></a>Använda autentiseringsuppgifter med Azure-tjänster

Du kan använda tjänsthuvudnamnsautentiseringsuppgifter från alla Azure-tjänster som autentiserar med ett Azure-behållarregister.  Använd autentiseringsuppgifter för tjänsten i stället för registrets administratörsautentiseringsuppgifter för en mängd olika scenarier.

Använd till exempel autentiseringsuppgifterna för att hämta en avbildning från ett Azure-behållarregister till [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Använd med dockerinloggning

Du kan `docker login` köra med ett huvudnamn för tjänsten. I följande exempel skickas tjänstens huvudprogram-ID `$SP_APP_ID`i miljövariabeln `$SP_PASSWD`och lösenordet i variabeln . Bästa praxis för att hantera Docker-autentiseringsuppgifter finns i kommandotreferens [för docker.](https://docs.docker.com/engine/reference/commandline/login/)

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

När Docker har loggat in cachelagrar du autentiseringsuppgifterna.

### <a name="use-with-certificate"></a>Använd med certifikat

Om du har lagt till ett certifikat i tjänstens huvudnamn kan du logga in på Azure CLI med certifikatbaserad autentisering och sedan använda kommandot [az acr-inloggning][az-acr-login] för att komma åt ett register. Om du använder ett certifikat som en hemlighet i stället för ett lösenord, är det ytterligare säkerhet när du använder CLI. 

Ett självsignerat certifikat kan skapas när du [skapar ett huvudnamn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli). Du kan också lägga till ett eller flera certifikat i ett befintligt tjänsthuvudnamn. Om du till exempel använder något av skripten i den här artikeln för att skapa eller uppdatera ett tjänsthuvudnamn med behörighet att hämta eller skicka avbildningar från ett register lägger du till ett certifikat med kommandot [az ad sp-återställning.][az-ad-sp-credential-reset]

Om du vill använda tjänstens huvudnamn med certifikat för att [logga in på Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)måste certifikatet vara i PEM-format och inkludera den privata nyckeln. Om certifikatet inte är i det format som `openssl` krävs använder du ett verktyg som konverterar det. När du kör [az-inloggning][az-login] för att logga in på CLI med tjänstens huvudnamn, ange även tjänstens huvudnamns program-ID och Active Directory-klient-ID. I följande exempel visas dessa värden som miljövariabler:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Kör sedan [az acr-inloggning][az-acr-login] för att autentisera med registret:

```azurecli
az acr login --name myregistry
```

CLI använder den token som `az login` skapades när du körde för att autentisera sessionen med registret.

## <a name="next-steps"></a>Nästa steg

* Se [autentiseringsöversikten](container-registry-authentication.md) för andra scenarier som ska autentiseras med ett Azure-behållarregister.

* Ett exempel på hur du använder ett Azure-nyckelvalv för att lagra och hämta autentiseringsuppgifter för tjänstens huvudnamn för ett behållarregister finns i självstudien för att [skapa och distribuera en behållaravbildning med ACR-uppgifter](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
