---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurations alternativen för text till tal-Helm.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971308"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Text till tal (under diagram: diagram/textToSpeech)

Om du vill åsidosätta diagrammet "paraply" lägger du `textToSpeech.` till prefixet på valfri parameter för att göra det mer exakt. Den kommer till exempel att åsidosätta motsvarande parameter till exempel `textToSpeech.numberOfConcurrentRequest` `numberOfConcurrentRequest`åsidosättningar.

|Parameter|Beskrivning|Standard|
| -- | -- | -- |
| `enabled` | Om **text till tal** -tjänsten är aktive rad. | `false` |
| `numberOfConcurrentRequest` | Antalet samtidiga förfrågningar för **text till tal** -tjänsten. Det här diagrammet beräknar automatiskt processor-och minnes resurser baserat på det här värdet. | `2` |
| `optimizeForTurboMode`| Huruvida tjänsten måste optimera för text inmatade via textfiler. Om `true`det här diagrammet kommer att allokera mer processor resurser till tjänsten. | `false` |
| `image.registry`| Bild registret **för text till tal-** Docker. | `containerpreview.azurecr.io` |
| `image.repository` | **Text till tal** Docker-avbildningens lagrings plats. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Bildtaggen **text till tal** Docker. | `latest` |
| `image.pullSecrets` | Bild hemligheterna för att hämta **text till tal** Docker-avbildningen. | |
| `image.pullByHash`| Anger om Docker-avbildningen hämtas via hash. Om `true` ,`image.hash` krävs. | `false` |
| `image.hash`| Hash för **text till tal-** Docker-avbildning. Används endast när `image.pullByHash: true`.  | |
| `image.args.eula`kunna | Visar att du har accepterat licensen. Det enda giltiga värdet är`accept` | |
| `image.args.billing`kunna | URI-värdet för fakturerings slut punkten är tillgängligt på sidan med tal översikts sidan för Azure Portal. | |
| `image.args.apikey`kunna | Används för att spåra faktureringsinformation. ||
| `service.type` | Kubernetes tjänst typ för **text till tal** -tjänsten. Se [anvisningarna för Kubernetes-tjänst typer](https://kubernetes.io/docs/concepts/services-networking/service/) för mer information och kontrol lera stöd för moln leverantörer. | `LoadBalancer` |
| `service.port`|  Porten för **text till tal** -tjänsten. | `80` |
| `service.annotations` | **Text till tal-** anteckningar för tjänstens metadata. Anteckningar är nyckel värdes par. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Om den [automatiska skalnings](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) funktionen för horisontella pod är aktive rad. Om `true`kommerattdistribueras iKubernetes-klustret.`text-to-speech-autoscaler` | `true` |
| `service.podDisruption.enabled` | Om [Pod störnings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktive rad. Om `true`kommerattdistribueras iKubernetes-klustret.`text-to-speech-poddisruptionbudget` | `true` |