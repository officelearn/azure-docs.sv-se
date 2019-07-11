---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurationsalternativen för tal till text helm-diagram.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717212"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Tal till Text (underordnade diagram: diagram/speechToText)

Om du vill åsidosätta diagrammet ”samlingsnamnet”, lägger du till prefixet `speechToText.` på någon parameter så att de blir mer specifika. Exempelvis åsidosätter motsvarande parameter t.ex. `speechToText.numberOfConcurrentRequest` åsidosätter `numberOfConcurrentRequest`.

|Parameter|Beskrivning|Standard|
| -- | -- | -- |
| `enabled` | Om den **tal till text** -tjänsten är aktiverad. | `false` |
| `numberOfConcurrentRequest` | Antal samtidiga begäranden för den **tal till text** service. Det här diagrammet beräknar automatiskt processor- och resurser, baserat på det här värdet. | `2` |
| `optimizeForAudioFile`| Om tjänsten behöver att optimera för ljudinspelning via ljudfiler. Om `true`, det här diagrammet ska allokera mer processorresurser till tjänsten. | `false` |
| `image.registry`| Den **tal till text** docker-avbildningsregister. | `containerpreview.azurecr.io` |
| `image.repository` | Den **tal till text** docker avbildningslagringsplatsen. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Den **tal till text** docker bildtagg. | `latest` |
| `image.pullSecrets` | Bild-hemligheter för att ta emot den **tal till text** docker-avbildning. | |
| `image.pullByHash`| Om docker-avbildningen hämtas-hash. Om `true`, `image.hash` krävs. | `false` |
| `image.hash`| Den **tal till text** hash för docker-avbildning. Endast används när `image.pullByHash: true`.  | |
| `image.args.eula` (krävs) | Anger du har accepterat licensen. Det enda giltiga värdet är `accept` | |
| `image.args.billing` (krävs) | Fakturering slutpunkten URI-värdet är tillgänglig på översiktssidan för Azure-portalens tal. | |
| `image.args.apikey` (krävs) | Används för att spåra faktureringsinformation. ||
| `service.type` | Typen av tjänst för Kubernetes i **tal till text** service. Se den [Kubernetes tjänsttyper instruktioner](https://kubernetes.io/docs/concepts/services-networking/service/) för mer information och kontrollera providerstöd i molnet. | `LoadBalancer` |
| `service.port`|  Porten för den **tal till text** service. | `80` |
| `service.autoScaler.enabled` | Om den [vågrät Pod Autoskalningen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktiverad. Om `true`, `speech-to-text-autoscaler` ska distribueras i Kubernetes-klustret. | `true` |
| `service.podDisruption.enabled` | Om den [Pod avbrott Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktiverad. Om `true`, `speech-to-text-poddisruptionbudget` ska distribueras i Kubernetes-klustret. | `true` |