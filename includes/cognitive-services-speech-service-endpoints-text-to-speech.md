---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 4f48fc0ad65984df42e2a85853f39e49d7481302
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020446"
---
### <a name="standard-and-neural-voices"></a>Standard- och neural röster

Använd den här tabellen för att avgöra tillgängligheten för standard- och neural röster av region/slutpunkten:

| Region | Slutpunkt | Standard röster | Neural Voices |
|--------|----------|-----------------|---------------|
| Östra Australien | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Centrala Kanada | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Centrala USA | https://centralus.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Östasien | https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Östra USA | https://eastus.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| USA, östra 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Frankrike, centrala | https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Centrala Indien | https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Östra Japan | https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Sydkorea, centrala | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Norra centrala USA | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Norra Europa | https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Södra centrala USA | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Sydostasien | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Storbritannien, södra | https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Västra Europa | https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |
| Västra USA | https://westus.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Nej |
| Västra USA 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/v1 | Ja | Ja |

### <a name="custom-voices"></a>Anpassade röster

Om du har skapat en anpassad rösttyp kan använda den slutpunkt som du har skapat. Du kan också använda de slutpunkter som anges nedan, där du ersätter den `{deploymentId}` med distributions-ID för din röst-modell.

| Region | Slutpunkt |
|--------|----------|
| Östra Australien | https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Centrala Kanada | https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Centrala USA | https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Östasien | https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Östra USA | https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| USA, östra 2 | https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Frankrike, centrala | https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Centrala Indien | https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Östra Japan | https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Sydkorea, centrala | https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Norra centrala USA | https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Norra Europa | https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Södra centrala USA | https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Sydostasien | https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Storbritannien, södra | https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Västra Europa | https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Västra USA | https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Västra USA 2 | https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
