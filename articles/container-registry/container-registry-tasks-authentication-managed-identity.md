---
title: Använd en hanterad identitet med Azure Container Registry uppgifter
description: Ge en Azure Container Registry uppgifts åtkomst till Azure-resurser, inklusive andra privata behållar register genom att tilldela en hanterad identitet för Azure-resurser.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311536"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Använd en Azure-hanterad identitet i ACR-uppgifter 

Använd en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) för att AUTENTISERA från ACR uppgifter till ett Azure Container Registry eller andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter i kod. Du kan till exempel använda en hanterad identitet för att hämta eller skicka behållar avbildningar till ett annat register som ett steg i en aktivitet.

I den här artikeln får du lära dig mer om hanterade identiteter och hur du:

> [!div class="checklist"]
> * Aktivera en tilldelad identitet eller en användardefinierad identitet på en ACR-uppgift
> * Ge identitets åtkomst till Azure-resurser, till exempel andra Azure Container register
> * Använd den hanterade identiteten för att komma åt resurserna från en aktivitet 

Den här artikeln kräver att du kör Azure CLI-version 2.0.66 eller senare för att skapa Azure-resurser. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

En hanterad identitet för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan konfigurera [vissa Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), inklusive ACR-aktiviteter, med en hanterad identitet. Använd sedan identiteten för att få åtkomst till andra Azure-resurser utan att skicka autentiseringsuppgifter i kod eller skript.

Hanterade identiteter är av två typer:

* *Användarspecifika identiteter*, som du kan tilldela till flera resurser och bevara så länge du vill. Användarspecifika identiteter är för närvarande en för hands version.

* En *Systemhanterad identitet*, som är unik för en speciell resurs, till exempel en ACR-aktivitet och varar för den aktuella resursens livs längd.

När du har konfigurerat en Azure-resurs med en hanterad identitet ger du identitets åtkomst till en annan resurs, precis som alla säkerhets objekt. Du kan till exempel tilldela en hanterad identitet en roll med pull, push och pull eller andra behörigheter till ett privat behållar register i Azure. (En fullständig lista över register roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).) Du kan ge en identitets åtkomst till en eller flera resurser.

## <a name="create-container-registries"></a>Skapa behållar register

I den här självstudien behöver du tre behållar register:

* Du använder det första registret för att skapa och köra ACR-uppgifter. I den här artikeln kallas det här käll registret för *register*. 
* De andra och tredje registren är mål register för den första exempel uppgiften för att skicka en avbildning som den bygger. I den här artikeln heter mål registren för *customregistry1* och *customregistry2*.

Ersätt med dina egna register namn i senare steg.

Om du inte redan har de nödvändiga Azure Container register, se [snabb start: Skapa ett privat behållar register med hjälp](container-registry-get-started-azure-cli.md)av Azure CLI. Du behöver inte skicka avbildningar till registret ännu.

## <a name="example-task-with-a-system-assigned-identity"></a>Exempel: Uppgift med en tilldelad identitet

Det här exemplet visar hur du skapar en [aktivitet med flera steg](container-registry-tasks-multi-step.md) med en tilldelad identitet. Aktiviteten skapar en avbildning och använder sedan identiteten för att autentisera med två mål register för att skicka avbildningen.

Stegen för den här exempel uppgiften definieras i en [yaml-fil](container-registry-tasks-reference-yaml.md) med `testtask.yaml`namnet. Filen finns i katalogen multipleRegistries i exemplen [ACR-tasks](https://github.com/Azure-Samples/acr-tasks) lagrings platsen. Filen reproduceras här:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Skapa uppgift med systemtilldelad identitet

Skapa aktiviteten med *flera register* genom att köra följande [AZ ACR uppgift Create][az-acr-task-create] -kommando. Aktivitets kontexten är mappen multipleRegistries i exemplen lagrings platsen och kommandot refererar till filen `testtask.yaml` i lagrings platsen. `--assign-identity` Parametern utan ytterligare värde skapar en tilldelad identitet för aktiviteten. Den här aktiviteten har ställts in så att du måste aktivera den manuellt, men du kan ställa in den så att den körs när commit skickas till lagrings platsen eller om en pull-begäran görs. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

I kommandots utdata `identity` visar avsnittet en identitet av typen `SystemAssigned` som anges i uppgiften. `principalId` Är tjänstens huvud namn-ID för identiteten:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Använd kommandot [AZ ACR Task show][az-acr-task-show] för att lagra `principalId` i en variabel som ska användas i senare kommandon:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Ge identitets-push-behörigheter till två mål behållar register

I det här avsnittet ger du den systemtilldelade identiteten behörighet att skicka push till de två mål registren, med namnet *customregistry1* och *customregistry2*.

Använd först kommandot [AZ ACR show][az-acr-show] för att hämta resurs-ID för varje register och lagra ID: n i variabler:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Använd kommandot [AZ roll tilldelning skapa][az-role-assignment-create] för att tilldela identiteten `acrpush` rollen till varje register. Den här rollen har behörighet att hämta och push-överföra avbildningar till ett behållar register.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Lägg till autentiseringsuppgifter för mål registret i uppgiften

Använd nu kommandot [AZ ACR Task Credential Add][az-acr-task-credential-add] för att lägga till identitetens autentiseringsuppgifter för aktiviteten så att den kan autentiseras med båda mål registren.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Använd kommandot [AZ ACR Task Run][az-acr-task-run] för att utlösa uppgiften manuellt. Parametern används för att skicka inloggnings Server namnen för de två mål registren som värden för variablerna `REGISTRY1` och `REGISTRY2`. `--set`

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

Utdata liknar följande:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Exempel: Uppgift med en användardefinierad identitet

I det här exemplet skapar du en användardefinierad identitet med behörighet att läsa hemligheter från ett Azure Key Vault. Du tilldelar den här identiteten till en aktivitet i multisteg som läser hemligheten, skapar en avbildning och loggar in på Azure CLI för att läsa avbildnings tag gen.

### <a name="create-a-key-vault-and-store-a-secret"></a>Skapa ett nyckel valv och lagra en hemlighet

Först, om du behöver, skapa en resurs grupp med namnet *myResourceGroup* på den *östra* platsen med följande [AZ Group Create][az-group-create] -kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [AZ-valv][az-keyvault-create] för att skapa ett nyckel valv. Se till att ange ett unikt nyckel valvs namn. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Lagra en exempel hemlighet i nyckel valvet med kommandot [AZ Key Vault Secret set][az-keyvault-secret-set] :

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Du kanske till exempel vill lagra autentiseringsuppgifter för att autentisera med ett privat Docker-register så att du kan hämta en privat avbildning.

### <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet med namnet *myACRTasksId* i din prenumeration med kommandot [AZ Identity Create][az-identity-create] . Du kan använda samma resurs grupp som du använde tidigare för att skapa ett behållar register eller nyckel valv, eller ett annat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Om du vill konfigurera identiteten i följande steg använder du kommandot [AZ Identity show][az-identity-show] för att lagra identitetens resurs-ID och tjänstens huvud NAMNS-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Ge identitets åtkomst till nyckel valv för att läsa hemlighet

Kör följande [AZ-nyckel valv set-princip][az-keyvault-set-policy] kommando för att ange en åtkomst princip i nyckel valvet. I följande exempel tillåts den användare-tilldelade identiteten att hämta hemligheter från nyckel valvet. Den här åtkomsten krävs senare för att köra en aktivitet med flera steg.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Bevilja identitets läsar åtkomst till resurs gruppen för registret

Kör följande [AZ roll tilldelning skapa][az-role-assignment-create] -kommando för att tilldela identiteten en läsar roll, i det här fallet resurs gruppen som innehåller käll registret. Den här rollen krävs senare för att köra en multi-Step-aktivitet.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Skapa uppgift med användardefinierad identitet

Nu ska du skapa en [aktivitet med flera steg](container-registry-tasks-multi-step.md) och tilldela den användarens tilldelade identitet. I den här exempel aktiviteten skapar du en yaml- `managed-identities.yaml` [fil](container-registry-tasks-reference-yaml.md) med namnet i en lokal arbets katalog och klistrar in följande innehåll. Se till att ersätta Key Vault-namnet i filen med namnet på ditt nyckel valv

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Den här uppgiften gör följande:

* Kontrollerar att den kan komma åt hemligheten i ditt nyckel valv. Det här steget är i demonstrations syfte. I ett verkligt scenario kan du behöva ett uppgifts steg för att få autentiseringsuppgifter för att få åtkomst till en privat Docker Hub-lagrings platsen.
* Skapar och push `mywebsite` -överför avbildningen till käll registret.
* Loggar in på Azure CLI för att visa `my-website` avbildnings taggarna i käll registret.

Skapa en aktivitet med namnet *msitask* och skicka den till resurs-ID: t för den användare-tilldelade identitet som du skapade tidigare. Den här exempel uppgiften skapas från `managed-identities.yaml` filen som du sparade i din lokala arbets katalog, så du måste aktivera den manuellt.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

I kommandots utdata `identity` visar avsnittet en identitet av typen `UserAssigned` som anges i uppgiften. `principalId` Är tjänstens huvud namn-ID för identiteten:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Använd kommandot [AZ ACR Task Run][az-acr-task-run] för att utlösa uppgiften manuellt. `--set` Parametern används för att skicka käll register namnet till uppgiften:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Utdata visar att hemligheten har lösts, att avbildningen har skapats och skickats och att aktiviteten loggar in i Azure CLI med identiteten för att läsa avbildnings tag gen från käll registret:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder hanterade identiteter med Azure Container Registry uppgifter och hur du:

> [!div class="checklist"]
> * Aktivera en tilldelad identitet eller användare som tilldelats till en ACR-uppgift
> * Ge identitets åtkomst till Azure-resurser, till exempel andra Azure Container register
> * Använd den hanterade identiteten för att komma åt resurserna från en aktivitet  

* Lär dig mer om [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
