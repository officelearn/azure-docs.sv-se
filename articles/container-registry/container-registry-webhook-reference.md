---
title: Azure Container registret webhook-Schemareferens
description: Webhook begäran JSON-nyttolast referens för Azure-behållare registernyckeln.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: f62477a4c68abf1617d9689047913fd820ee5461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container webhook register

Du kan [konfigurerar webhooks](container-registry-webhook.md) för behållaren registret som genererar händelser när vissa åtgärder utförs mot den. Du kan till exempel aktivera webhooks som aktiveras på behållaren avbildningen `push` och `delete` åtgärder. När en webhook utlöses skickar Azure Container registret en HTTP eller HTTPS-begäran som innehåller information om händelsen till en slutpunkt som du anger. Slutpunkten kan bearbeta webhook och fungerar därför.

Följande avsnitt innehåller information om schemat för webhook-begäranden som genereras av händelser som stöds. Händelse-avsnitt innehåller nyttolast-schemat för händelsetypen, en exempel-nyttolasten i begäran och en eller flera exempel på kommandon som ska utlösa webhooken.

Information om hur du konfigurerar webhooks för din Azure-behållaren registernyckeln finns [med Azure Container registret webhooks](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-begäranden

### <a name="http-request"></a>HTTP-begäran

En utlösta webhook gör ett HTTP `POST` begäran till URL-slutpunkt som du angav när du konfigurerade webhooken.

### <a name="http-headers"></a>HTTP-huvuden

Webhook-förfrågningar innehålla en `Content-Type` av `application/json` om du inte har angett en `Content-Type` anpassad rubrik för din webhooken.

Inga andra huvuden läggs till begäran utöver dessa anpassade huvuden som du har angett för webhooken.

## <a name="push-event"></a>Push-händelse

Webhook utlöses när en avbildning av behållare skickas till en databas.

### <a name="push-event-payload"></a>Push-händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|Sträng|ID för webhook-händelsen.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|Sträng|Åtgärden som utlöste webhook-händelsen.|
|[mål](#target)|Komplex typ|Mål för den händelse som utlöste webhook-händelsen.|
|[Begäran](#request)|Komplex typ|Den begäran som skapade webhook-händelsen.|

### <a name="target"></a>mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|Sträng|Det refererade objektet MIME-typ.|
|`size`|Int32|Antal byte av innehållet. Samma som fältlängden.|
|`digest`|Sträng|Sammanfattningen av innehåll, som definieras i registernyckeln V2 http-API-specifikationen.|
|`length`|Int32|Antal byte av innehållet. Samma som fältet storlek.|
|`repository`|Sträng|Databasens namn.|
|`tag`|Sträng|Taggnamnet avbildningen.|

### <a name="request"></a>Begäran

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|Sträng|ID för begäran som initierade händelsen.|
|`host`|Sträng|Externa värdnamnet för register-instans som anges av värden HTTP-huvudet på inkommande begäranden.|
|`method`|Sträng|Metoden begäran som skapade händelsen.|
|`useragent`|Sträng|Användaren agent huvudet i begäran.|

### <a name="payload-example-push-event"></a>Nyttolasten exempel: push-händelse

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

Exempel [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) kommando som utlöser den **push** händelse webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Ta bort händelser

Webhook utlöses när en databas eller manifestet tas bort. Aktiveras inte när en tagg har tagits bort.

### <a name="delete-event-payload"></a>Ta bort händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|Sträng|ID för webhook-händelsen.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|Sträng|Åtgärden som utlöste webhook-händelsen.|
|[mål](#delete_target)|Komplex typ|Mål för den händelse som utlöste webhook-händelsen.|
|[Begäran](#delete_request)|Komplex typ|Den begäran som skapade webhook-händelsen.|

### <a name="delete_target"></a> mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|Sträng|Det refererade objektet MIME-typ.|
|`digest`|Sträng|Sammanfattningen av innehåll, som definieras i registernyckeln V2 http-API-specifikationen.|
|`repository`|Sträng|Databasens namn.|

### <a name="delete_request"></a> Begäran

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|Sträng|ID för begäran som initierade händelsen.|
|`host`|Sträng|Externa värdnamnet för register-instans som anges av värden HTTP-huvudet på inkommande begäranden.|
|`method`|Sträng|Metoden begäran som skapade händelsen.|
|`useragent`|Sträng|Användaren agent huvudet i begäran.|

### <a name="payload-example-delete-event"></a>Nyttolasten exempel: ta bort händelser

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

Exempel [Azure CLI 2.0](/cli/azure/acr) kommandon för att utlösa en **ta bort** händelse webhook:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Nästa steg

[Med hjälp av Azure-behållare registret webhooks](container-registry-webhook.md)