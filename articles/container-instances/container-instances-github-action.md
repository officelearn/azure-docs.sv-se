---
title: Distribuera behållarinstans med GitHub-åtgärd
description: Konfigurera en GitHub-åtgärd som automatiserar steg för att skapa, skicka och distribuera en behållaravbildning till Azure Container Instances
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258047"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurera en GitHub-åtgärd för att skapa en behållarinstans

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) är en uppsättning funktioner i GitHub för att automatisera dina arbetsflöden för programvaruutveckling på samma plats som du lagrar kod och samarbetar om pull-begäranden och problem.

Använd åtgärden [Distribuera till Azure Container Instances](https://github.com/azure/aci-deploy) GitHub för att automatisera distribution av en behållare till Azure Container Instances. Med åtgärden kan du ange egenskaper för en behållarinstans som liknar dem i kommandot [az-behållare.][az-container-create]

Den här artikeln visar hur du konfigurerar ett arbetsflöde i en GitHub-repo som utför följande åtgärder:

* Skapa en avbildning från en Dockerfile
* Skicka avbildningen till ett Azure-behållarregister
* Distribuera behållaravbildningen till en Azure-behållarinstans

Den här artikeln innehåller två sätt att konfigurera arbetsflödet:

* Konfigurera ett arbetsflöde själv i en GitHub-repo med hjälp av åtgärden Distribuera till Azure Container Instances och andra åtgärder.  
* Använd `az container app up` kommandot i [tillägget Distribuera till Azure](https://github.com/Azure/deploy-to-azure-cli-extension) i Azure CLI. Det här kommandot effektiviserar skapandet av GitHub-arbetsflödet och distributionsstegen.

> [!IMPORTANT]
> GitHub-åtgärden för Azure Container Instances är för närvarande i förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Krav

* **GitHub-konto** - Skapa https://github.com ett konto på om du inte redan har ett.
* **Azure CLI** - Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra Azure CLI-stegen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].
* **Azure-behållarregister** - Om du inte har ett, skapa ett Azure-behållarregister på basic-nivån med hjälp av [Azure CLI,](../container-registry/container-registry-get-started-azure-cli.md) [Azure-portalen](../container-registry/container-registry-get-started-portal.md)eller andra metoder. Ta del av resursgruppen som används för distributionen, som används för GitHub-arbetsflödet.

## <a name="set-up-repo"></a>Ställ in repo

* Om du vill veta mer i den hÃ¤r artikeln anvÃ¤nder du GitHub som forkÃ¤nder följande databas:https://github.com/Azure-Samples/acr-build-helloworld-node

  Den här repo innehåller en Dockerfile och källfiler för att skapa en behållaravbildning av en liten webbapp.

  ![Skärmbild av knappen Förgrening (markerad) i GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Se till att åtgärder är aktiverade för databasen. Navigera till den kluslade databasen och välj > **Inställningsåtgärder**. **Settings** I **Behörigheter för Åtgärder**kontrollerar du att Aktivera åtgärder från lokala och tredje part för den här **databasen** är markerat.

## <a name="configure-github-workflow"></a>Konfigurera GitHub-arbetsflöde

### <a name="create-service-principal-for-azure-authentication"></a>Skapa tjänsthuvudnamn för Azure-autentisering

I GitHub-arbetsflödet måste du ange Azure-autentiseringsuppgifter för att autentisera till Azure CLI. I följande exempel skapas ett tjänsthuvudnamn med rollen Deltagare som är begränsad till resursgruppen för behållarregistret.

Hämta först resurs-ID:et för din resursgrupp. Ersätt namnet på din grupp i följande kommando [för az-gruppvisning:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Använd [az ad sp create-for-rbac för][az-ad-sp-create-for-rbac] att skapa tjänstens huvudnamn:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Utdata liknar följande:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Spara JSON-utdata eftersom den används i ett senare steg. Ta också del `clientId`av , som du måste uppdatera tjänstens huvudnamn i nästa avsnitt.

### <a name="update-service-principal-for-registry-authentication"></a>Uppdatera tjänstens huvudnamn för registerautentisering

Uppdatera azure-tjänstens huvudnamnsautentiseringsuppgifter för att tillåta push- och pull-behörigheter för behållarregistret. Med det här steget kan GitHub-arbetsflödet använda tjänstens huvudnamn för att [autentisera med behållarregistret](../container-registry/container-registry-auth-service-principal.md). 

Hämta resurs-ID:n för behållarregistret. Ersätt namnet på registret i följande [kommando för az acr-reglage:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Använd [az-rolltilldelning skapa][az-role-assignment-create] för att tilldela rollen AcrPush, vilket ger push och pull-åtkomst till registret. Ersätt klient-ID:et för ditt tjänsthuvudnamn:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Spara autentiseringsuppgifter i GitHub-repo

1. I GitHub-användargränssnittet navigerar du till den kluvna databasen och väljer **Inställningar** > **hemligheter**. 

1. Välj **Lägg till en ny hemlighet** om du vill lägga till följande hemligheter:

|Hemlighet  |Värde  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Hela JSON-utdata från tjänstens huvudsakliga skapande |
|`REGISTRY_LOGIN_SERVER`   | Inloggningsserverns namn på registret (alla gemener). Exempel: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  Från `clientId` JSON-utdata från tjänstens huvudsakliga skapande       |
|`REGISTRY_PASSWORD`     |  Från `clientSecret` JSON-utdata från tjänstens huvudsakliga skapande |
| `RESOURCE_GROUP` | Namnet på den resursgrupp som du använde för att begränsa tjänstens huvudnamn |

### <a name="create-workflow-file"></a>Skapa arbetsflödesfil

1. I GitHub-användargränssnittet väljer du **Åtgärder** > **nytt arbetsflöde**.
1. Välj **Konfigurera ett arbetsflöde själv**.
1. I **Redigera ny fil**klistrar du in följande YAML-innehåll för att skriva över exempelkoden. Acceptera standardfilnamnet `main.yml`eller ange ett filnamn som du väljer.
1. Välj **Starta commit**, ange kort och utökad beskrivning av ditt genomförande och välj Commit ny **fil**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Validera arbetsflöde

När du har arkiverat arbetsflödesfilen utlöses arbetsflödet. Om du vill granska arbetsflödesstatus navigerar du till **Åtgärder** > **arbetsflöden**. 

![Visa arbetsflödesstatus](./media/container-instances-github-action/github-action-progress.png)

Se [Hantera en arbetsflödeskörning](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) för information om hur du visar status och resultat för varje steg i arbetsflödet.

När arbetsflödet är klart hämtar du information om behållarinstansen med namnet *aci-sampleapp* genom att köra kommandot [az container show.][az-container-show] Ersätt namnet på resursgruppen: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Utdata liknar följande:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

När instansen har etablerats navigerar du till behållarens FQDN i webbläsaren för att visa den webbapp som körs.

![Köra webbapp i webbläsaren](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Använda distribuera till Azure-tillägg

Du kan också använda [tillägget Distribuera till Azure](https://github.com/Azure/deploy-to-azure-cli-extension) i Azure CLI för att konfigurera arbetsflödet. Kommandot `az container app up` i tillägget tar indataparametrar från dig för att konfigurera ett arbetsflöde för distribution till Azure Container Instances. 

Arbetsflödet som skapas av Azure CLI liknar det arbetsflöde som du kan [skapa manuellt med GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Ytterligare förutsättningar

Förutom [förutsättningarna](#prerequisites) och [ãympã¤nstÃ¤5ningsinställningarna fÃ¶r](#set-up-repo) det här scenariot måste du installera **tillägget Distribuera till Azure** fÃ¶r Azure CLI.

Kör kommandot [az extension add][az-extension-add] för att installera tillägget:

```azurecli
az extension add \
  --name deploy-to-azure
```

Information om hur du söker efter, installerar och hanterar tillägg finns i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Kör `az container app up`

Om du vill köra kommandot [az container app up][az-container-app-up] anger du minst:

* Namnet på ditt Azure-behållarregister, till exempel *mitt register*
* URL:en till din GitHub-repo, till exempel`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Exempel-kommando:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Förlopp av kommandot

* När du uppmanas till det anger du dina GitHub-autentiseringsuppgifter eller tillhandahåller en [GitHub-token](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) för personlig åtkomst (PAT) som har *repo-* och *användaromfattningar* för att autentisera med registret. Om du anger GitHub-autentiseringsuppgifter skapar kommandot en PAT åt dig.

* Kommandot skapar repo hemligheter för arbetsflödet:

  * Autentiseringsuppgifter för tjänstens huvudnamn för Azure CLI
  * Autentiseringsuppgifter för åtkomst till Azure-behållarregistret

* När kommandot har genomförts i arbetsflödesfilen till din repo utlöses arbetsflödet. 

Utdata liknar följande:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Validera arbetsflöde

Arbetsflödet distribuerar en Azure-behållarinstans med basnamnet på din GitHub-repo, i det här fallet *acr-build-helloworld-nod*. I webbläsaren kan du bläddra till länken för att visa den webbapp som körs. Om appen lyssnar på en annan port än 8080 anger du den i webbadressen i stället.

Information om hur du visar arbetsflödesstatus och resultat för varje steg i GitHub-användargränssnittet finns i [Hantera en arbetsflödeskörning](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

## <a name="clean-up-resources"></a>Rensa resurser

Stoppa behållarinstansen med kommandot [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Om du vill ta bort resursgruppen och alla resurser i den kör du kommandot [az group delete:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

Bläddra bland [GitHub Marketplace](https://github.com/marketplace?type=actions) för fler åtgärder för att automatisera utvecklingsarbetsflödet


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
