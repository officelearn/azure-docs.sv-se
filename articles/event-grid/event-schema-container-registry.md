---
title: Händelseschema för Azure Event Grid Container Registry
description: Beskriver de egenskaper som anges för behållaren Reigstry händelser med Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: 6f00d4f249543ece0eb8db4a8e040300d55b2de8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462852"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Azure Event Grid-Händelseschema för Container Registry

Den här artikeln innehåller egenskaperna och schemat för Container Registry-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

BLOB-lagring genererar följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Utlöses när en bild skickas. |
| Microsoft.ContainerRegistry.ImageDeleted | Utlöses när en bild tas bort. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visar schemat för en avbildning som push-överfört händelse: 

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

Schemat för en bild som tas bort händelsen liknar:

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

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| ämne | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för BLOB storage. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | Händelse-ID. |
| tidsstämpel | sträng | Den tid då händelsen inträffade. |
| åtgärd | sträng | Den åtgärd som omfattar den angivna händelsen. |
| mål | objekt | Målet för händelsen. |
| begäran | objekt | Den begäran som skapade händelsen. |

Målobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| mediaType | sträng | MIME-typ för det refererade objektet. |
| storlek | heltal | Antal byte av innehållet. Samma som fältet längd. |
| Sammanfattad | sträng | Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen. |
| Längd | heltal | Antal byte av innehållet. Samma som fältet. |
| Lagringsplats | sträng | Namnet på lagringsplatsen. |
| tagg | sträng | Taggnamnet. |

Begäranobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | ID för begäran som initierade händelsen. |
| addr | sträng | Den IP- eller värdnamn och möjligen porten för klientanslutningen som initierade händelsen. Det här värdet är RemoteAddr från vanlig http-begäran. |
| värd | sträng | Externt tillgängliga värdnamnet för registry-instans som den anges av http-rubriken på inkommande begäranden. |
| metod | sträng | Metoden för begäran som genererade händelsen. |
| USERAGENT | sträng | Användaren agent huvudet för begäran. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
