---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 650ce0cc9586118b30593767c6a3ddb92f494ac3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972665"
---
### <a name="standard-and-neural-voices"></a>Standard-och neurala-röster

Använd den här tabellen för att fastställa tillgängligheten för standard-och neurala-röster per region/slut punkt:

| Region | Slutpunkt | Standard röster | Neurala-röster |
|--------|----------|-----------------|---------------|
| Australien, östra | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Brasilien, södra | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Kanada, centrala | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Asien, östra | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA, östra 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Frankrike, centrala | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Indien, centrala | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Japan, östra | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Japan, västra | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Sydkorea, centrala | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| USA, norra centrala | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| Norra Europa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| USA, södra centrala | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Sydostasien | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Storbritannien, södra | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Europa, västra | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| USA, västra | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nej |
| USA, västra 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |

> [!TIP]
> [Röster i för hands versionen](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) är endast tillgängliga i dessa tre regioner: östra USA, västeuropa och Sydostasien.

### <a name="custom-voices"></a>Anpassade röster

Om du har skapat ett anpassat röst teckensnitt använder du den slut punkt som du har skapat. Du kan också använda slut punkterna som anges nedan och ersätta `{deploymentId}` med distributions-ID: t för din röst modell.

| Region | Slutpunkt |
|--------|----------|
| Australien, östra | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brasilien, södra | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada, centrala | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Central US | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asien, östra | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| East US | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, östra 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Frankrike, centrala | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indien, centrala | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan, östra | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan, västra | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sydkorea, centrala | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, norra centrala | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Norra Europa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, södra centrala | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sydostasien | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Storbritannien, södra | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa, västra | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, västra | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, västra 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
