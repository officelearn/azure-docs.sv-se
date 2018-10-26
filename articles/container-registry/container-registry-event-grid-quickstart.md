---
title: Snabbstart – skicka Azure Container Registry-händelser till Event Grid
description: I den här snabbstarten du aktiverar Event Grid-händelser för ditt behållarregister och sedan skicka behållare bild push och ta bort händelser till ett exempelprogram.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.openlocfilehash: 60248da2bbdac751845a40720f8967203c562b4a
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085758"
---
# <a name="quickstart-send-container-registry-events-to-event-grid"></a>Snabbstart: Skicka container registerhändelser till Event Grid

Azure Event Grid är en fullständigt hanterad tjänst för händelsedirigering som ger enhetlig förbrukning med en Publicera-prenumerera modellen. I den här snabbstarten använder du Azure CLI för att skapa ett behållarregister, prenumerera på händelser i registret och sedan distribuera en exempelwebbapp för att ta emot händelser. Slutligen kan du utlösa behållaravbildning `push` och `delete` händelser och visa händelsenyttolast i exempelprogrammet.

När du har slutfört stegen i den här artikeln visas händelser som skickas från ditt behållarregister till Event Grid i exempelwebbappen:

![Webbläsarens rendering exempelwebbappen med tre mottagna händelser][sample-app-01]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI-kommandon i den här artikeln har formaterats för den **Bash** shell. Om du använder ett annat gränssnitt som PowerShell eller Kommandotolken, kan du behöva justeras rad fortsättning tecken eller variabeltilldelning rader. Den här artikeln används variabler för att minimera mängden kommandot redigering krävs.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där du kan distribuera och hantera Azure-resurser. Följande [az gruppen skapa] [ az-group-create] kommandot skapar en resursgrupp med namnet *myResourceGroup* i den *eastus* region. Om du vill använda ett annat namn för resursgruppen anger `RESOURCE_GROUP_NAME` på ett annat värde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Distribuera ett behållarregister i resursgruppen med följande kommandon. Innan du kör den [az acr skapa] [ az-acr-create] kommandot genom att ange `ACR_NAME` till ett namn för ditt register. Namnet måste vara unikt i Azure och är begränsad till 5 – 50 alfanumeriska tecken.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

När registret har skapats, returnerar Azure CLI utdata som liknar följande:

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

## <a name="create-an-event-endpoint"></a>Skapa en händelse-slutpunkt

I det här avsnittet ska använda du Resource Manager-mall finns i en GitHub-lagringsplats för att distribuera ett färdiga exempelwebbprogram till Azure App Service. Senare kan du prenumerera på ditt register Event Grid-händelser och ange den här appen som slutpunkt som händelserna ska skickas.

Om du vill distribuera exempelappen ange `SITE_NAME` till ett unikt namn för din webbapp och kör följande kommandon. Platsnamnet måste vara unikt i Azure eftersom den utgör en del av det fullständigt kvalificerade domännamnet (FQDN) för webbappen. I ett senare avsnitt gå till appens FQDN i en webbläsare för att visa händelser för ditt register.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

När distributionen har slutförts (det kan ta några minuter), öppna en webbläsare och navigera till webbappen och kontrollera att det körs:

`http://<your-site-name>.azurewebsites.net`

Du bör se exempelappen återges med inga händelsemeddelanden som visas:

![Webbläsare som visar exempelwebbapp med inga händelser visas][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Prenumerera på händelser för registret

I Event Grid kan du prenumerera på en *avsnittet* att tala om vilka händelser som du vill spåra och var du vill skicka dem. Följande [az eventgrid-händelseprenumeration skapa] [ az-eventgrid-event-subscription-create] kommandot prenumererar på behållarregistret du har skapat och anger URL: en för din webbapp som slutpunkt som den ska skicka händelser. De miljövariabler som du har fyllt i i tidigare avsnitt återanvänds här, så inga ändringar krävs.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

När prenumerationen har slutförts bör du se utdata som liknar följande:

```JSON
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

## <a name="trigger-registry-events"></a>Utlösarhändelser för registret

Nu när exempelappen är igång och körs och att du prenumererar på registret med Event Grid, är du redo att skapa vissa händelser. I det här avsnittet använder du ACR uppgifter för att bygga och överföra en behållaravbildning till registret. ACR uppgifter är en funktion i Azure Container Registry som hjälper dig att skapa behållaravbildningar i molnet, utan att behöva Docker-motorn installerad på den lokala datorn.

### <a name="build-and-push-image"></a>Skapa och skicka avbildning

Kör följande Azure CLI-kommando för att skapa en behållaravbildning från innehållet i en GitHub-lagringsplats. Som standard ACR uppgifter automatiskt skickar en har skapade avbildningen till registret, vilket genererar den `ImagePushed` händelse.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Du bör se utdata som liknar följande medan ACR aktiviteter skapar och skickar din avbildning. Följande exempel på utdata har trunkerats av utrymmesskäl.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
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

Kontrollera att den skapade avbildningen i registret, kör du följande kommando för att visa taggar i databasen ”myimage”:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Taggen ”v1” för den avbildning som du skapat visas i utdata liknar följande:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Ta bort avbildningen

Nu kan generera en `ImageDeleted` händelsen genom att ta bort avbildningen med den [az acr databasen delete] [ az-acr-repository-delete] kommando:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Du bör se utdata som liknar följande, där du uppmanas bekräfta att ta bort manifestet och associerade bilder:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Visa händelser för registret

Du har nu push-överförde en avbildning till ditt register och tar sedan bort den. Navigera till webbappen Grid Loggboken och du bör se både `ImageDeleted` och `ImagePushed` händelser. Du kan också se en händelse för verifiering av prenumeration som genereras genom att köra kommandot i den [prenumerera på händelser för registret](#subscribe-to-registry-events) avsnittet.

Följande skärmbild visar exempelapp med tre händelser och `ImageDeleted` händelse utökas för att visa dess egenskaper.

![Webbläsaren och visar exempelapp med ImagePushed och ImageDeleted händelser][sample-app-03]

Grattis! Om du ser den `ImagePushed` och `ImageDeleted` händelser, registret skickar händelser till Event Grid och Event Grid vidarebefordrar dessa händelser till web app-slutpunkten.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med de resurser som du skapade i den här snabbstarten kan du ta bort dem med följande Azure CLI-kommando. När du tar bort en resursgrupp tas tas alla resurser den innehåller bort permanent.

**VARNING**: den här åtgärden kan inte ångras. Var noga med att du inte längre behöver någon av resurserna i gruppen innan du kör kommandot.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Händelseschema för Event Grid

Du hittar Azure Container Registry händelsen meddelande-Schemareferens i Event Grid-dokumentationen:

[Azure Event Grid-Händelseschema för Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten du distribuerat ett behållarregister, skapat en avbildning med ACR uppgifter, tog bort den och har förbrukat registrets händelser från Event Grid med ett exempelprogram. Sedan gå vidare till självstudiekursen om ACR uppgifter att lära dig mer om hur du skapar behållaravbildningar i molnet, inklusive automatiserad utveckling på grundläggande uppdateringar:

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
[az-acr-create]: /cli/azure/acr/repository#az-acr-create
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
