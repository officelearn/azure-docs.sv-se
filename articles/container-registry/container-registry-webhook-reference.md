---
title: Schemareferens i Azure Container Registry webhook
description: Webhook-begäran JSON-nyttolast referens för Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: danlep
ms.openlocfilehash: 42790905509e2ea8bbba87587ed01b1929221db5
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329327"
---
# <a name="azure-container-registry-webhook-reference"></a>Referens för Azure Container Registry-webhook

Du kan [konfigurerar webhooks](container-registry-webhook.md) för ditt behållarregister som genererar händelser när vissa åtgärder som utförs mot den. Till exempel aktivera webhooks som utlösts av behållaravbildning `push` och `delete` åtgärder. När en webhook har utlösts skickar en HTTP eller HTTPS-begäran som innehåller information om händelsen till en slutpunkt som du anger i Azure Container Registry. Slutpunkten kan sedan bearbeta webhooken och agera utifrån dessa.

Följande avsnitt beskriver schemat för webhook-begäranden som genereras av händelser som stöds. Händelse-avsnitt innehåller nyttolast-schemat för händelsetypen, en exempel-nyttolasten för begäran och en eller flera exempel på kommandon som ska utlösa webhooken.

Information om hur du konfigurerar webhooks för Azure container registry finns i [med hjälp av Azure Container Registry-webhookar](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-begäranden

### <a name="http-request"></a>HTTP-begäran

En utlösta webhook gör ett HTTP `POST` begäran till URL-slutpunkt som du angav när du konfigurerade webhooken.

### <a name="http-headers"></a>HTTP-huvuden

Webhook-begäran innehålla en `Content-Type` av `application/json` om du inte har angett en `Content-Type` anpassat sidhuvud för din webhook.

Inga andra rubriker läggs till i begäran utöver dessa anpassade sidhuvuden som du har angett för webhook.

## <a name="push-event"></a>Push-händelse

Webhook-utlöst när en behållaravbildning skickas till en databas.

### <a name="push-event-payload"></a>Push-händelsenyttolast

|Element|Type|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelse.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|String|Den åtgärd som utlöste händelsen webhook.|
|[target](#target)|Komplex typ|Mål för den händelse som utlöste händelsen webhook.|
|[request](#request)|Komplex typ|Den begäran som genereras av webhook-händelse.|

### <a name="target"></a>mål

|Element|Type|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typ för det refererade objektet.|
|`size`|Int32|Antal byte av innehållet. Samma som fältet längd.|
|`digest`|String|Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen.|
|`length`|Int32|Antal byte av innehållet. Samma som fältet.|
|`repository`|String|Namnet på lagringsplatsen.|
|`tag`|String|Taggnamnet bild.|

### <a name="request"></a>begäran

|Element|Type|Beskrivning|
|------------------|----------|-----------|
|`id`|String|ID för begäran som initierade händelsen.|
|`host`|String|Externt tillgängliga värdnamnet för registry-instans som den anges av HTTP-rubriken på inkommande begäranden.|
|`method`|String|Metoden för begäran som genererade händelsen.|
|`useragent`|String|Användaren agent huvudet för begäran.|

### <a name="payload-example-push-event"></a>Nyttolast-exempel: push-händelse

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exempel [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) kommando som utlöser den **push** händelse-webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Ta bort händelse

Webhook-utlöst när lagringsplats eller manifest tas bort. Utlöses inte när en tagg har tagits bort.

### <a name="delete-event-payload"></a>Ta bort händelsenyttolast

|Element|Type|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelse.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|String|Den åtgärd som utlöste händelsen webhook.|
|[target](#delete_target)|Komplex typ|Mål för den händelse som utlöste händelsen webhook.|
|[request](#delete_request)|Komplex typ|Den begäran som genereras av webhook-händelse.|

### <a name="delete_target"></a> Mål

|Element|Type|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typ för det refererade objektet.|
|`digest`|String|Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen.|
|`repository`|String|Namnet på lagringsplatsen.|

### <a name="delete_request"></a> Begäran

|Element|Type|Beskrivning|
|------------------|----------|-----------|
|`id`|String|ID för begäran som initierade händelsen.|
|`host`|String|Externt tillgängliga värdnamnet för registry-instans som den anges av HTTP-rubriken på inkommande begäranden.|
|`method`|String|Metoden för begäran som genererade händelsen.|
|`useragent`|String|Användaren agent huvudet för begäran.|

### <a name="payload-example-delete-event"></a>Nyttolasten exemplet: ta bort händelse

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exempel [Azure CLI](/cli/azure/acr) kommandon som utlösaren en **ta bort** händelse-webhook:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="next-steps"></a>Nästa steg

[Med Azure Container Registry-webhookar](container-registry-webhook.md)