---
title: Schemareferens för registrets webbkrok
description: Referens för JSON-nyttolast för webhook-begäranden i ett Azure-behållarregister, som genereras när webhooks är aktiverade för artefakt push eller ta bort händelser
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455970"
---
# <a name="azure-container-registry-webhook-reference"></a>Webbkroksreferens för Azure Container-registret

Du kan [konfigurera webhooks](container-registry-webhook.md) för behållarregistret som genererar händelser när vissa åtgärder utförs mot det. Aktivera till exempel webkrokar som utlöses när en behållaravbildning eller Helm-diagram skjuts till ett register eller tas bort. När en webhook utlöses utfärdar Azure Container Registry en HTTP- eller HTTPS-begäran som innehåller information om händelsen till en slutpunkt som du anger. Din slutpunkt kan sedan bearbeta webhook och agera därefter.

I följande avsnitt beskrivs schemat för webhook-begäranden som genereras av händelser som stöds. Händelseavsnitten innehåller nyttolastschemat för händelsetypen, ett exempel på nyttolast för begäran och ett eller flera exempelkommandon som skulle utlösa webhooken.

Information om hur du konfigurerar webhooks för ditt Azure-behållarregister finns i [Använda Azure Container Registry webhooks](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-begäranden

### <a name="http-request"></a>HTTP-begäran

En utlöst webhook gör `POST` en HTTP-begäran till url-slutpunkten som du angav när du konfigurerade webhooken.

### <a name="http-headers"></a>HTTP-huvuden

Webhook-begäranden `Content-Type` `application/json` innehåller en av om `Content-Type` du inte har angett ett anpassat huvud för din webhook.

Inga andra rubriker läggs till i begäran utöver de anpassade rubriker som du kanske har angett för webhooken.

## <a name="push-event"></a>Push-händelse

Webhook utlöses när en behållaravbildning skjuts till en databas.

### <a name="push-event-payload"></a>Nyttolast för push-händelse

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelsen.|
|`timestamp`|DateTime|Den tidpunkt då webhook-händelsen utlöstes.|
|`action`|String|Åtgärden som utlöste webhook-händelsen.|
|[Mål](#target)|Komplex typ|Målet för händelsen som utlöste webhook-händelsen.|
|[Begäran](#request)|Komplex typ|Begäran som genererade webhook-händelsen.|

### <a name="target"></a><a name="target"></a>Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typen för det refererade objektet.|
|`size`|Int32|Antalet byte av innehållet. Samma som fältet Längd.|
|`digest`|String|Sammanfattningen av innehållet, enligt definitionen i registret V2 HTTP API Specifikation.|
|`length`|Int32|Antalet byte av innehållet. Samma som fältet Storlek.|
|`repository`|String|Databasnamnet.|
|`tag`|String|Namnet på bildtaggen.|

### <a name="request"></a><a name="request"></a>Begäran

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|String|ID:et för begäran som initierade händelsen.|
|`host`|String|Registerinstans externt tillgängliga värdnamn, enligt vad som anges av HTTP-värdhuvudet på inkommande begäranden.|
|`method`|String|Begäran metod som genererade händelsen.|
|`useragent`|String|Användaragenthuvudet för begäran.|

### <a name="payload-example-image-push-event"></a>Exempel på nyttolast: push-händelse för bild

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exempel [Docker CLI-kommando](https://docs.docker.com/engine/reference/commandline/cli/) som utlöser **händelsens push-händelsewehook:**

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Push-händelse för diagram

Webhook utlöses när ett Helm-diagram skjuts till en databas.

### <a name="chart-push-event-payload"></a>Nyttolast för push-händelse för diagram

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelsen.|
|`timestamp`|DateTime|Den tidpunkt då webhook-händelsen utlöstes.|
|`action`|String|Åtgärden som utlöste webhook-händelsen.|
|[Mål](#helm_target)|Komplex typ|Målet för händelsen som utlöste webhook-händelsen.|

### <a name="target"></a><a name="helm_target"></a>Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typen för det refererade objektet.|
|`size`|Int32|Antalet byte av innehållet.|
|`digest`|String|Sammanfattningen av innehållet, enligt definitionen i registret V2 HTTP API Specifikation.|
|`repository`|String|Databasnamnet.|
|`tag`|String|Namnet på diagramtaggen.|
|`name`|String|Diagrammets namn.|
|`version`|String|Diagramversionen.|

### <a name="payload-example-chart-push-event"></a>Exempel på nyttolast: push-händelse för diagram

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exempel på [Azure CLI-kommando](/cli/azure/acr) som utlöser chart_push-händelsewebbhook: **chart_push**

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Ta bort händelse

Webhook utlöses när en bilddatabas eller ett manifest tas bort. Utlöses inte när en tagg tas bort.

### <a name="delete-event-payload"></a>Ta bort händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelsen.|
|`timestamp`|DateTime|Den tidpunkt då webhook-händelsen utlöstes.|
|`action`|String|Åtgärden som utlöste webhook-händelsen.|
|[Mål](#delete_target)|Komplex typ|Målet för händelsen som utlöste webhook-händelsen.|
|[Begäran](#delete_request)|Komplex typ|Begäran som genererade webhook-händelsen.|

### <a name="target"></a><a name="delete_target"></a>Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typen för det refererade objektet.|
|`digest`|String|Sammanfattningen av innehållet, enligt definitionen i registret V2 HTTP API Specifikation.|
|`repository`|String|Databasnamnet.|

### <a name="request"></a><a name="delete_request"></a>Begäran

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|String|ID:et för begäran som initierade händelsen.|
|`host`|String|Registerinstans externt tillgängliga värdnamn, enligt vad som anges av HTTP-värdhuvudet på inkommande begäranden.|
|`method`|String|Begäran metod som genererade händelsen.|
|`useragent`|String|Användaragenthuvudet för begäran.|

### <a name="payload-example-image-delete-event"></a>Exempel på nyttolast: händelse för borttagning av bilder

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exempel på [Azure CLI-kommandon](/cli/azure/acr) som utlöser en **borttagningshändelsewehook:**

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Händelse för borttagning av diagram

Webhook utlöses när ett Helm-diagram eller en databas tas bort. 

### <a name="chart-delete-event-payload"></a>Visa ta bort händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelsen.|
|`timestamp`|DateTime|Den tidpunkt då webhook-händelsen utlöstes.|
|`action`|String|Åtgärden som utlöste webhook-händelsen.|
|[Mål](#chart_delete_target)|Komplex typ|Målet för händelsen som utlöste webhook-händelsen.|

### <a name="target"></a><a name="chart_delete_target"></a>Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typen för det refererade objektet.|
|`size`|Int32|Antalet byte av innehållet.|
|`digest`|String|Sammanfattningen av innehållet, enligt definitionen i registret V2 HTTP API Specifikation.|
|`repository`|String|Databasnamnet.|
|`tag`|String|Namnet på diagramtaggen.|
|`name`|String|Diagrammets namn.|
|`version`|String|Diagramversionen.|

### <a name="payload-example-chart-delete-event"></a>Exempel på nyttolast: händelse för borttagning av diagram

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exempel på [Azure CLI-kommando](/cli/azure/acr) som utlöser **chart_delete** händelsewebbhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Nästa steg

[Använda webhooks för Azure Container-behållarregister](container-registry-webhook.md)