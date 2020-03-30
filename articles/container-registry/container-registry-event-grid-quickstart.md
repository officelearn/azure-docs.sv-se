---
title: Snabbstart - Skicka händelser till händelserutnät
description: I den här snabbstarten aktiverar du Händelserutnätshändelser för behållarregistret och skickar sedan begärandeavbildningsre push och borttagning av händelser till ett exempelprogram.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403233"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Snabbstart: Skicka händelser från privat containerregister till Event Grid

Azure Event Grid är en fullständigt hanterad händelseroutningstjänst som ger enhetlig händelseförbrukning med hjälp av en publiceringsprenumerera-modell. I den här snabbstarten använder du Azure CLI för att skapa ett behållarregister, prenumerera på registerhändelser och distribuerar sedan ett exempelwebbprogram för att ta emot händelserna. Slutligen utlöser du `push` `delete` behållaravbildning och händelser och visar händelsenyttolasten i exempelprogrammet.

När du har slutfört stegen i den här artikeln visas händelser som skickas från behållarregistret till Event Grid i exempelwebbappen:

![Webbläsare som gör exempelwebbprogrammet med tre mottagna händelser][sample-app-01]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI-kommandona i den här artikeln är formaterade för **Bash-skalet.** Om du använder ett annat skal som PowerShell eller Kommandotolk kan du behöva justera radförekommande tecken eller variabla tilldelningsrader därefter. I den här artikeln används variabler för att minimera mängden kommandoredigering som krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där du distribuerar och hanterar dina Azure-resurser. Följande kommando [för az-grupp skapa][az-group-create] skapar en resursgrupp med namnet *myResourceGroup* i *regionen eastus.* Om du vill använda ett annat namn `RESOURCE_GROUP_NAME` för resursgruppen anger du ett annat värde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Distribuera sedan ett behållarregister till resursgruppen med följande kommandon. Innan du kör kommandot [az acr create][az-acr-create] anger du `ACR_NAME` ett namn för registret. Namnet måste vara unikt i Azure och är begränsat till 5-50 alfanumeriska tecken.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

När registret har skapats returnerar Azure CLI utdata som liknar följande:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Skapa en slutpunkt för händelser

I det här avsnittet använder du en Resource Manager-mall som finns i en GitHub-databas för att distribuera ett förbyggt exempelwebbprogram till Azure App Service. Senare prenumererar du på registrets Event Grid-händelser och anger den här appen som den slutpunkt som händelserna skickas till.

Om du vill distribuera `SITE_NAME` exempelappen anger du ett unikt namn för webbappen och kör följande kommandon. Webbplatsnamnet måste vara unikt i Azure eftersom det utgör en del av det fullständigt kvalificerade domännamnet (FQDN) för webbappen. I ett senare avsnitt navigerar du till appens FQDN i en webbläsare för att visa registrets händelser.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen har slutförts (det kan ta några minuter) öppnar du en webbläsare och navigerar till din webbapp för att se till att den körs:

`http://<your-site-name>.azurewebsites.net`

Du bör se exempelappen återges utan att händelsemeddelanden visas:

![Webbläsare som visar exempelwebbapp utan att några händelser visas][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på registerhändelser

I Event Grid prenumererar du på ett *ämne* för att berätta vilka händelser du vill spåra och var du ska skicka dem. Följande kommando för att skapa domänhändelser för [händelse-prenumeration][az-eventgrid-event-subscription-create] prenumererar på behållarregistret som du skapade och anger webbappens URL som den slutpunkt som den ska skicka händelser till. De miljövariabler som du har fyllt i i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen är klar bör du se utdata som liknar följande:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Utlösa registerhändelser

Nu när exempelappen är igång och du prenumererar på registret med Event Grid är du redo att generera vissa händelser. I det här avsnittet använder du ACR-uppgifter för att skapa och skicka en behållaravbildning till registret. ACR-uppgifter är en funktion i Azure Container Registry som gör att du kan skapa behållaravbildningar i molnet, utan att behöva Docker Engine installerad på din lokala dator.

### <a name="build-and-push-image"></a>Skapa och pusha bild

Kör följande Azure CLI-kommando för att skapa en behållaravbildning från innehållet i en GitHub-databas. Som standard skickar ACR-uppgifter automatiskt en framgångsrikt byggd avbildning till `ImagePushed` registret, vilket genererar händelsen.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Du bör se utdata som liknar följande medan ACR-uppgifter bygger och sedan skickar bilden. Följande exempelutdata har trunkerats för korthet.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Om du vill kontrollera att den inbyggda avbildningen finns i registret kör du följande kommando för att visa taggarna i databasen "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Taggen "v1" för den bild du har skapat ska visas i utdata, ungefär som följande:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Ta bort bilden

Nu, generera `ImageDeleted` en händelse genom att ta bort bilden med [az acr borttagning][az-acr-repository-delete] kommandot:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Du bör se utdata som liknar följande och be om bekräftelse för att ta bort manifestet och tillhörande bilder:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Visa registerhändelser

Du har nu drivit en avbildning till registret och sedan tagit bort den. Navigera till webbappen Loggboken och du `ImageDeleted` `ImagePushed` bör se både och händelser. Du kan också se en prenumerationsverifieringshändelse som genereras genom att kommandot körs i avsnittet [Prenumerera på registerhändelser.](#subscribe-to-registry-events)

Följande skärmbild visar exempelappen med de `ImageDeleted` tre händelserna och händelsen utökas för att visa dess information.

![Webbläsare som visar exempelappen med ImagePushed- och ImageDeleted-händelser][sample-app-03]

Grattis! Om du `ImagePushed` ser `ImageDeleted` händelserna och händelser skickar registret händelser till Event Grid och Event Grid vidarebefordrar dessa händelser till slutpunkten för webbappen.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med de resurser som du har skapat i den här snabbstarten kan du ta bort dem alla med följande Azure CLI-kommando. När du tar bort en resursgrupp tas alla resurser som den innehåller bort permanent.

**VARNING:** Denna operation är oåterkallelig. Kontrollera att du inte längre behöver någon av resurserna i gruppen innan du kör kommandot.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Händelseschema för händelserutnät

Du hittar händelseschemareferensen för Azure Container Registry i dokumentationen till Event Grid:

[Azure Event Grid-händelseschema för behållarregister](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett behållarregister, skapat en avbildning med ACR-uppgifter, tagit bort det och förbrukat registrets händelser från Event Grid med ett exempelprogram. Gå sedan vidare till acr-uppgiftssjälvstudien om du vill veta mer om hur du skapar behållaravbildningar i molnet, inklusive automatiska versioner av basavbildningsuppdatering:

> [!div class="nextstepaction"]
> [Skapa behållaravbildningar i molnet med ACR-uppgifter](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
