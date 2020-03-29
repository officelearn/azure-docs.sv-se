---
title: Händelseschema för Azure Event Grid Container-register
description: Beskriver de egenskaper som tillhandahålls för containerregisterhändelser med Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60345472"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Azure Event Grid-händelseschema för behållarregister

Den här artikeln innehåller egenskaper och schema för containerregisterhändelser.En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure Container Registry avger följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Utlöses när en bild trycks in. |
| Microsoft.ContainerRegistry.ImageDeleted | Utlöses när en bild tas bort. |
| Microsoft.ContainerRegistry.ChartPushed | Utlöses när ett Helm-diagram trycks in. |
| Microsoft.ContainerRegistry.ChartDeleted | Utlöses när ett Helm-diagram tas bort. |

## <a name="example-event"></a>Exempel händelse

I följande exempel visas schemat för en skjuten bildhändelse: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat för en borttagen bildhändelse är liknande:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat för en skjuten diagramhändelse liknar schemat för en avbildad pushad händelse, men den innehåller inget begärandeobjekt:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat för en borttagen diagramhändelse liknar schemat för en borttagen händelse, men det innehåller inget begärandeobjekt:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för bloblagring. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | Händelse-ID. |
| timestamp | sträng | Den tidpunkt då händelsen inträffade. |
| åtgärd | sträng | Åtgärden som omfattar den tillsynte händelsen. |
| Mål | objekt | Målet för händelsen. |
| Begäran | objekt | Begäran som genererade händelsen. |

Målobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| mediaType (mediaType) | sträng | MIME-typen för det refererade objektet. |
| size | heltal | Antalet byte av innehållet. Samma som fältet Längd. |
| digest | sträng | Sammanfattningen av innehållet, enligt definitionen i registret V2 HTTP API Specifikation. |
| length | heltal | Antalet byte av innehållet. Samma som fältet Storlek. |
| Databasen | sträng | Databasnamnet. |
| tagg | sträng | Taggnamnet. |
| namn | sträng | Diagrammets namn. |
| version | sträng | Diagramversionen. |

Objektet för begäran har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | ID:et för begäran som initierade händelsen. |
| Addr | sträng | IP- eller värdnamnet och eventuellt port för klientanslutningen som initierade händelsen. Det här värdet är RemoteAddr från standard http-begäran. |
| värd | sträng | Registerinstansens externt tillgängliga värdnamn, enligt vad som anges av http-värdhuvudet på inkommande begäranden. |
| metod | sträng | Begäran metod som genererade händelsen. |
| Useragent | sträng | Användaragenthuvudet för begäran. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
