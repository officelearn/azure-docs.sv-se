---
title: Använda en hanterad identitet med Azure Container Registry uppgifter
description: Ange ett Azure Container Registry uppgift åtkomst till Azure-resurser, inklusive andra privata behållarregister genom att tilldela en hanterad identitet för Azure-resurser.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148032"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Använda en Azure-hanterad identitet i ACR-uppgifter 

Använd en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) att autentisera från ACR-uppgifter till ett Azure container registry eller andra Azure-resurser utan att du behöver ange eller hantera autentiseringsuppgifter i kod. Till exempel använda en hanterad identitet till pull- eller push-överför avbildningar till ett annat register som ett steg i en aktivitet.

I den här artikeln får du lära dig mer om hanterade identiteter och hur du:

> [!div class="checklist"]
> * Aktivera en systemtilldelad identitet eller en Användartilldelad identitet för en ACR-aktivitet
> * Ge åtkomst till Azure-resurser, till exempel andra Azure-behållarregister identitet
> * Använda den hanterade identitet för åtkomst till resurser från en aktivitet 

Den här artikeln för att skapa Azure-resurser, kräver att du kör Azure CLI version 2.0.66 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

En hanterad identitet för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD Azure). Du kan konfigurera [vissa Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), inklusive ACR uppgifter med en hanterad identitet. Använd sedan identiteten för att få åtkomst till andra Azure-resurser utan att skicka autentiseringsuppgifter i kod eller skript.

Hanterade identiteter finns två typer av:

* *Användartilldelade identiteter*, som du kan tilldela flera resurser och bevara så länge som vill ha. Användartilldelade identiteter finns för närvarande i förhandsversion.

* En *system-hanterad identitet*, som är unik för en specifik resurs, till exempel en ACR-uppgift och varar i livslängden för den här resursen.

När du har konfigurerat en Azure-resurs med en hanterad identitet kan ge identitet åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Till exempel tilldela en hanterad identitet en roll med pull, push och pull eller andra behörigheter till ett privat behållarregister i Azure. (En fullständig lista över registret roller finns [Azure Container Registry roller och behörigheter](container-registry-roles.md).) Du kan ge en identity-åtkomst till en eller flera resurser.

## <a name="create-container-registries"></a>Skapa behållarregister

Den här självstudien behöver du tre behållarregister:

* Du kan använda första registret för att skapa och köra ACR uppgifter. I den här artikeln källa registrets heter *myregistry*. 
* Andra och tredje register är target-register för den första exempel aktiviteten att skicka en avbildning som den bygger. I den här artikeln, mål-register namnges *customregistry1* och *customregistry2*.

Ersätt med din egen registernamn i senare steg.

Om du inte redan har de nödvändiga Azure-behållarregister, se [snabbstarten: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md). Du behöver inte skicka avbildningar till registret ännu.

## <a name="example-task-with-a-system-assigned-identity"></a>Exempel: Uppgiften med en automatiskt genererad identitet

Det här exemplet visar hur du skapar en [flerstegstest uppgift](container-registry-tasks-multi-step.md) med en systemtilldelad identitet. Uppgiften skapas en avbildning och använder sedan identiteten för att autentisera med två mål-register för att överföra avbildningen.

Stegen för det här exemplet definieras i en [YAML-fil](container-registry-tasks-reference-yaml.md) med namnet `testtask.yaml`. Filen finns i katalogen multipleRegistries på den [acr-uppgifter](https://github.com/Azure-Samples/acr-tasks) exempel lagringsplatsen. Filen återges här:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Skapa uppgift med systemtilldelade identiteter

Skapa uppgiften *flera reg* genom att köra följande [az acr uppgift skapa] [ az-acr-task-create] kommando. Uppgift-kontexten är mappen multipleRegistries till exempel lagringsplatsen och kommandot refererar till filen `testtask.yaml` i lagringsplatsen. Den `--assign-identity` med inga ytterligare värde för parametern skapar en systemtilldelad identitet för aktiviteten. Den här aktiviteten har ställts in så att du behöver utlösa manuellt, men du kan konfigurera det ska köras när incheckningar skickas till lagringsplatsen eller en pullbegäran skickas. 

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

I utdata från kommandot den `identity` avsnittet visas en identitet av typen `SystemAssigned` har angetts i aktiviteten. Den `principalId` är ID för tjänstens huvudnamn identitetsinformationen:

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

Använd den [az acr uppgiften visa] [ az-acr-task-show] kommando för att lagra den `principalId` i en variabel, som ska användas i senare kommandon:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Ge identitet push-behörighet till två mål-behållarregister

I det här avsnittet, ge systemtilldelade identiteter behörighet att skicka till två mål register, med namnet *customregistry1* och *customregistry2*.

Först använder den [az acr show] [ az-acr-show] kommando för att hämta resurs-ID för varje register och lagra de ID: N i variabler:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Använd den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando för att tilldela identiteten den `acrpush` rollen till varje register. Den här rollen har behörighet att pull och push-avbildningar till ett behållarregister.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Lägg till mål-autentiseringsuppgifter för registret till aktivitet

Nu använda den [az acr uppgift autentiseringsuppgifter lägger du till] [ az-acr-task-credential-add] att lägga till autentiseringsuppgifter för den identiteten aktiviteten så att den kan autentisera med både mål-register.

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

### <a name="manually-run-the-task"></a>Manuellt köra aktiviteten

Använd den [az acr-uppgiftskörning] [ az-acr-task-run] kommando för att utlösa manuellt uppgiften. Den `--set` används för att skicka in inloggningen servernamnen för två mål-register som värden för variablerna uppgift `REGISTRY1` och `REGISTRY2`.

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

## <a name="example-task-with-a-user-assigned-identity"></a>Exempel: Uppgiften med en Användartilldelad identitet

I det här exemplet skapar du en Användartilldelad identitet med behörighet att läsa hemligheter från ett Azure key vault. Du kan tilldela den här identiteten för en långvariga aktivitet som läser hemligheten, skapas en avbildning och loggar in på Azure CLI för att läsa bildtaggen.

### <a name="create-a-key-vault-and-store-a-secret"></a>Skapa ett nyckelvalv och lagra en hemlighet

Om du vill skapa först en resursgrupp med namnet *myResourceGroup* i den *eastus* plats med följande [az gruppen skapa] [ az-group-create]kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd den [az keyvault skapa] [ az-keyvault-create] kommando för att skapa ett nyckelvalv. Glöm inte att ange ett unikt key vault-namn. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store en exempel-hemlighet i nyckelvalvet med den [az keyvault secret set] [ az-keyvault-secret-set] kommando:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Du kan till exempel vill lagra autentiseringsuppgifter för att autentisera med ett privat Docker-register så att du kan hämta en privat avbildning.

### <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet med namnet *myACRTasksId* i din prenumeration med hjälp av den [az identitet skapa] [ az-identity-create] kommando. Du kan använda samma resursgrupp som du använde tidigare för att skapa ett behållarregister eller nyckelvalvet eller ett annat namn.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Konfigurera identitet i följande steg med den [az identitet show] [ az-identity-show] kommando för att lagra Identitetens resurs-ID och ID för tjänstens huvudnamn i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Åtkomstbehörighet för keyvault för att läsa hemliga identitet

Kör följande [az keyvault set-policy] [ az-keyvault-set-policy] kommandot för att definiera en åtkomstprincip för nyckelvalvet. I följande exempel kan Användartilldelad identitet att hämta hemligheter från nyckelvalvet. Den här åtkomsten behövs senare att kunna köras av en uppgift i flera steg.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Bevilja identitet läsåtkomst till resursgruppen för registret

Kör följande [az-rolltilldelning skapa] [ az-role-assignment-create] kommando för att tilldela identiteten läsarrollen i det här fallet till resursgruppen som innehåller käll-registret. Den här rollen krävs senare för att kunna köras av en uppgift i flera steg.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Skapa uppgift med Användartilldelad identitet

Skapa nu en [flerstegstest uppgift](container-registry-tasks-multi-step.md) och tilldela den Användartilldelad identitet. Detta exempel skapar en [YAML-fil](container-registry-tasks-reference-yaml.md) med namnet `managed-identities.yaml` i en lokal arbetskatalog och klistra in följande innehåll. Se till att ersätta namn på key vault i filen med namnet på ditt nyckelvalv

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

* Kontrollerar att den har åtkomst till en hemlighet i nyckelvalvet. Det här steget är i exempelsyfte. Du kanske behöver en uppgift steget för att hämta autentiseringsuppgifter för åtkomst till ett privat Docker Hub-lagringsplats i ett verkligt scenario.
* Skapar och skickar den `mywebsite` avbildningen till registret källa.
* Loggar in Azure CLI för att lista de `my-website` bild taggar i käll-registret.

Skapa en uppgift som kallas *msitask* och skickar den resurs-ID för Användartilldelad identitet som du skapade tidigare. Detta exempel har skapats från den `managed-identities.yaml` filen som du sparade i din lokala arbetskatalog så att du behöver att utlösa det manuellt.

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

I utdata från kommandot den `identity` avsnittet visas en identitet av typen `UserAssigned` har angetts i aktiviteten. Den `principalId` är ID för tjänstens huvudnamn identitetsinformationen:

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

### <a name="manually-run-the-task"></a>Manuellt köra aktiviteten

Använd den [az acr-uppgiftskörning] [ az-acr-task-run] kommando för att utlösa manuellt uppgiften. Den `--set` parametern används för att skicka in registernamnet källa till aktivitet:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Utdata visar hemligheten har åtgärdats avbildningen har skapats och push-överfört och uppgiften loggar in på Azure CLI med identiteten för att läsa bildtaggen från käll-registret:

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

I den här artikeln har du lärt dig om hur du använder hanterade identiteter med Azure Container Registry åtgärder och hur du:

> [!div class="checklist"]
> * Aktivera en systemtilldelad identitet eller Användartilldelad på en ACR-uppgift
> * Ge åtkomst till Azure-resurser, till exempel andra Azure-behållarregister identitet
> * Använda den hanterade identitet för åtkomst till resurser från en aktivitet  

* Läs mer om [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/).

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
