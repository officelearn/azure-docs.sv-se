---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237069"
---
### <a name="standard-and-neural-voices"></a>Standard- och neural röster

Använd den här tabellen för att avgöra tillgängligheten för standard- och neural röster av region/slutpunkten:

| Region | Slutpunkt | Standard röster | Neural Voices |
|--------|----------|-----------------|---------------|
| Australien, östra | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Kanada, centrala | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Centrala USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Asien, östra | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Östra USA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA, östra 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Centrala Frankrike | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Indien, centrala | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Japan, östra | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Sydkorea, centrala | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| USA, norra centrala | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Europa, norra | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| USA, södra centrala | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Sydostasien | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Södra Storbritannien | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Europa, västra | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA, västra | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| USA, västra 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |

### <a name="custom-voices"></a>Anpassade röster

Om du har skapat en anpassad rösttyp kan använda den slutpunkt som du har skapat. Du kan också använda de slutpunkter som anges nedan, där du ersätter den `{deploymentId}` med distributions-ID för din röst-modell.

| Region | Slutpunkt |
|--------|----------|
| Australien, östra | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada, centrala | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centrala USA | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asien, östra | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Östra USA | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, östra 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centrala Frankrike | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indien, centrala | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan, östra | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sydkorea, centrala | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, norra centrala | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa, norra | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, södra centrala | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sydostasien | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Södra Storbritannien | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa, västra | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, västra | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, västra 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
