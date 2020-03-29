---
title: Installera talbehållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurationsalternativen för tal-till-text-helmdiagram.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971333"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Tal-till-text (underdiagram: diagram/talTotext)

Om du vill åsidosätta paraplydiagrammet `speechToText.` lägger du till prefixet på en parameter för att göra det mer specifikt. Till exempel åsidosätter den motsvarande parametern, till exempel `speechToText.numberOfConcurrentRequest` åsidosätter `numberOfConcurrentRequest`.

|Parameter|Beskrivning|Default|
| -- | -- | -- |
| `enabled` | Om **tal-till-text-tjänsten** är aktiverad. | `false` |
| `numberOfConcurrentRequest` | Antalet samtidiga begäranden för **tal-till-text-tjänsten.** Det här diagrammet beräknar automatiskt CPU- och minnesresurser, baserat på det här värdet. | `2` |
| `optimizeForAudioFile`| Om tjänsten behöver optimera för ljudinmatning via ljudfiler. Om `true`det här diagrammet allokerar mer CPU-resurs till service. | `false` |
| `image.registry`| Avbildningsregistret **för tal-till-text-docker.** | `containerpreview.azurecr.io` |
| `image.repository` | Bilddatabasen för tad-docker från **tal till text.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Bildtaggen för docker från **tal till text.** | `latest` |
| `image.pullSecrets` | Bildhemligheterna för att dra **tal-till-text-dockerbilden.** | |
| `image.pullByHash`| Om dockerbilden dras av hash. Om `true` `image.hash` , krävs. | `false` |
| `image.hash`| Det är en tystningsbild mellan tal och **text.** Används endast `image.pullByHash: true`när .  | |
| `image.args.eula`(krävs) | Anger att du har accepterat licensen. Det enda giltiga värdet är`accept` | |
| `image.args.billing`(krävs) | URI-värdet för faktureringsslutpunkt är tillgängligt på Azure-portalens sida för talöversikt. | |
| `image.args.apikey`(krävs) | Används för att spåra faktureringsinformation. ||
| `service.type` | Tjänsttypen Kubernetes för **tal-till-text-tjänsten.** Mer information finns i instruktioner för [Kubernetes-tjänsttyper](https://kubernetes.io/docs/concepts/services-networking/service/) och verifierar support för molnleverantörer. | `LoadBalancer` |
| `service.port`|  Porten för **tal-till-text-tjänsten.** | `80` |
| `service.annotations` | **Tal-till-text-anteckningarna** för tjänstens metadata. Anteckningar är nyckelvärdespar. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Om [den automatiska vågensträttsenheten](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktiverad. Om `true` `speech-to-text-autoscaler` kommer kommer att distribueras i Kubernetes klustret. | `true` |
| `service.podDisruption.enabled` | Om budgeten för [pod-avbrott](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktiverad. Om `true` `speech-to-text-poddisruptionbudget` kommer kommer att distribueras i Kubernetes klustret. | `true` |