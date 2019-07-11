---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurationsalternativen för text till tal helm-diagram.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717249"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Text till tal (underordnade diagram: diagram/textToSpeech)

Om du vill åsidosätta diagrammet ”samlingsnamnet”, lägger du till prefixet `textToSpeech.` på någon parameter så att de blir mer specifika. Exempelvis åsidosätter motsvarande parameter t.ex. `textToSpeech.numberOfConcurrentRequest` åsidosätter `numberOfConcurrentRequest`.

|Parameter|Beskrivning|Standard|
| -- | -- | -- |
| `enabled` | Om den **text till tal** -tjänsten är aktiverad. | `false` |
| `numberOfConcurrentRequest` | Antal samtidiga begäranden för den **text till tal** service. Det här diagrammet beräknar automatiskt processor- och resurser, baserat på det här värdet. | `2` |
| `optimizeForTurboMode`| Om tjänsten behöver att optimera för textinmatning via textfiler. Om `true`, det här diagrammet ska allokera mer processorresurser till tjänsten. | `false` |
| `image.registry`| Den **text till tal** docker-avbildningsregister. | `containerpreview.azurecr.io` |
| `image.repository` | Den **text till tal** docker avbildningslagringsplatsen. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Den **text till tal** docker bildtagg. | `latest` |
| `image.pullSecrets` | Bild-hemligheter för att ta emot den **text till tal** docker-avbildning. | |
| `image.pullByHash`| Om docker-avbildningen hämtas-hash. Om `true`, `image.hash` krävs. | `false` |
| `image.hash`| Den **text till tal** hash för docker-avbildning. Endast används när `image.pullByHash: true`.  | |
| `image.args.eula` (krävs) | Anger du har accepterat licensen. Det enda giltiga värdet är `accept` | |
| `image.args.billing` (krävs) | Fakturering slutpunkten URI-värdet är tillgänglig på översiktssidan för Azure-portalens tal. | |
| `image.args.apikey` (krävs) | Används för att spåra faktureringsinformation. ||
| `service.type` | Typen av tjänst för Kubernetes i **text till tal** service. Se den [Kubernetes tjänsttyper instruktioner](https://kubernetes.io/docs/concepts/services-networking/service/) för mer information och kontrollera providerstöd i molnet. | `LoadBalancer` |
| `service.port`|  Porten för den **text till tal** service. | `80` |
| `service.autoScaler.enabled` | Om den [vågrät Pod Autoskalningen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktiverad. Om `true`, `text-to-speech-autoscaler` ska distribueras i Kubernetes-klustret. | `true` |
| `service.podDisruption.enabled` | Om den [Pod avbrott Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktiverad. Om `true`, `text-to-speech-poddisruptionbudget` ska distribueras i Kubernetes-klustret. | `true` |