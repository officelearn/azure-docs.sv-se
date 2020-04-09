---
title: Installera talbehållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurationsalternativen för text-till-tal-helmdiagram.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874368"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Text-till-tal (underdiagram: diagram/textTillSpeech)

Om du vill åsidosätta paraplydiagrammet `textToSpeech.` lägger du till prefixet på en parameter för att göra det mer specifikt. Till exempel åsidosätter den motsvarande parametern, till exempel `textToSpeech.numberOfConcurrentRequest` åsidosätter `numberOfConcurrentRequest`.

|Parameter|Beskrivning|Default|
| -- | -- | -- |
| `enabled` | Om **text-till-tal-tjänsten** är aktiverad. | `false` |
| `numberOfConcurrentRequest` | Antalet samtidiga begäranden för **text-till-tal-tjänsten.** Det här diagrammet beräknar automatiskt CPU- och minnesresurser, baserat på det här värdet. | `2` |
| `optimizeForTurboMode`| Om tjänsten behöver optimera för textinmatning via textfiler. Om `true`det här diagrammet allokerar mer CPU-resurs till service. | `false` |
| `image.registry`| Avbildningsregistret **för text-till-tal-docker.** | `containerpreview.azurecr.io` |
| `image.repository` | Bilddatabasen **för text-till-tal-docker.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Bildtaggen **för text-till-tal-docker.** | `latest` |
| `image.pullSecrets` | Bildhemligheterna för att dra **från text till tal** docker. | |
| `image.pullByHash`| Om dockerbilden dras av hash. Om `true` `image.hash` , krävs. | `false` |
| `image.hash`| Bildhage för **text-till-tal-docker.** Används endast `image.pullByHash: true`när .  | |
| `image.args.eula`(krävs) | Anger att du har accepterat licensen. Det enda giltiga värdet är`accept` | |
| `image.args.billing`(krävs) | URI-värdet för faktureringsslutpunkt är tillgängligt på Azure-portalens sida för talöversikt. | |
| `image.args.apikey`(krävs) | Används för att spåra faktureringsinformation. ||
| `service.type` | Tjänsttypen Kubernetes för **text-till-tal-tjänsten.** Mer information finns i instruktioner för [Kubernetes-tjänsttyper](https://kubernetes.io/docs/concepts/services-networking/service/) och verifierar support för molnleverantörer. | `LoadBalancer` |
| `service.port`|  Porten för **text-till-tal-tjänsten.** | `80` |
| `service.annotations` | **Text-till-tal-anteckningarna** för tjänstens metadata. Anteckningar är nyckelvärdespar. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Om [den automatiska vågensträttsenheten](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktiverad. Om `true` `text-to-speech-autoscaler` kommer kommer att distribueras i Kubernetes klustret. | `true` |
| `service.podDisruption.enabled` | Om budgeten för [pod-avbrott](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktiverad. Om `true` `text-to-speech-poddisruptionbudget` kommer kommer att distribueras i Kubernetes klustret. | `true` |