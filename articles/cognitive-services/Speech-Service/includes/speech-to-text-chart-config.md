---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurations alternativen för tal-till-text-Helm.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421925"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Tal till text (under diagram: diagram/speechToText)

Om du vill åsidosätta diagrammet "paraply" lägger du `speechToText.` till prefixet på valfri parameter för att göra det mer exakt. Den kommer till exempel att åsidosätta motsvarande parameter till exempel `speechToText.numberOfConcurrentRequest` åsidosättningar. `numberOfConcurrentRequest`

|Parameter|Beskrivning|Standardvärde|
| -- | -- | -- |
| `enabled` | Om tjänsten för **tal till text** är aktive rad. | `false` |
| `numberOfConcurrentRequest` | Antalet samtidiga förfrågningar för tjänsten **tal till text** . Det här diagrammet beräknar automatiskt processor-och minnes resurser baserat på det här värdet. | `2` |
| `optimizeForAudioFile`| Om tjänsten behöver optimeras för ljud inspelning via ljudfiler. Om `true`det här diagrammet kommer att ALLOKERA mer processor resurser till tjänsten. | `false` |
| `image.registry`| Registrerings avbildningen **tal till text** i Docker. | `containerpreview.azurecr.io` |
| `image.repository` | Bild lagrings platsen för **tal-till-text-** Docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Avbildnings tag gen till Docker för **tal till text** . | `latest` |
| `image.pullSecrets` | Bild hemligheterna för att hämta Docker-avbildningen från **tal till text** . | |
| `image.pullByHash`| Anger om Docker-avbildningen hämtas via hash. Om `true`, `image.hash` krävs. | `false` |
| `image.hash`| Bild **-hashen tal-till-text** Docker. Används endast när `image.pullByHash: true`.  | |
| `image.args.eula`kunna | Visar att du har accepterat licensen. Det enda giltiga värdet är`accept` | |
| `image.args.billing`kunna | URI-värdet för fakturerings slut punkten är tillgängligt på sidan med tal översikts sidan för Azure Portal. | |
| `image.args.apikey`kunna | Används för att spåra fakturerings information. ||
| `service.type` | Kubernetes tjänst typ för tjänsten **tal till text** . Se [anvisningarna för Kubernetes-tjänst typer](https://kubernetes.io/docs/concepts/services-networking/service/) för mer information och kontrol lera stöd för moln leverantörer. | `LoadBalancer` |
| `service.port`|  Porten för tjänsten **tal-till-text** . | `80` |
| `service.annotations` | **Tal-till-text-** anteckningar för tjänste-metadata. Anteckningar är nyckel värdes par. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Om den [automatiska skalnings funktionen för horisontella Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktive rad. Om `true` `speech-to-text-autoscaler` kommer att distribueras i Kubernetes-klustret. | `true` |
| `service.podDisruption.enabled` | Om [Pod störnings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktive rad. Om `true` `speech-to-text-poddisruptionbudget` kommer att distribueras i Kubernetes-klustret. | `true` |