---
title: Speech Synthesis Markup Language (SSML) - Speech Services
titleSuffix: Azure Cognitive Services
description: Använder tal syntes Markup Language för att styra uttal och prosody i text till tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021445"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Tal syntes Markup Language (SSML) är en XML-baserade märkspråk som gör det möjligt att styra uttal och *prosody* av text till tal. Prosody refererar till om du vill och försäljningsargument tal – dess musik, om du vill. Du kan ange ord fonetiskt, ge tips för att tolka siffror, infoga pauser, kontroll försäljningsargument, volym, och priset med mera. Mer information finns i [tal syntes Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). 

En fullständig lista över språk som stöds, språk och röster (neural och standard), se [språkstöd](language-support.md#text-to-speech).

Följande avsnitt innehåller exempel för vanliga tal syntes uppgifter.

## <a name="adjust-speaking-style-for-neural-voices"></a>Justera samtalsstil för neurala röster

Du kan använda SSML för att justera samtalsstil när du använder någon av de neural rösterna.

Som standard Syntetiserar tjänsten text till tal i en neutral formatmall. Neural röster utöka SSML med en `<mstts:express-as>` element som: texten omvandlas till syntetiskt tal i olika talar format. För närvarande stöds endast style-taggar med de här röster:

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

Talar style ändringar kan tillämpas på nivån mening. Formaten varierar röst. Om en typ inte stöds, returnerar tjänsten syntetiskt tal som standard språkneutralt format.

| Röst | Stil | Beskrivning | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Representerar en känsla som är positiva och nöjd |
| | type=`empathy` | Representerar en uppfattning om sköta och förstå |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Representerar en formell ton liknar nyheter sändningar |
| | type=`sentiment ` | Ger ett vidrör meddelande eller en historia |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Lägga till en paus
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Engelsktalande förändringstakten

Talar hastighet kan tillämpas på standard röster på ett ord eller en mening på servernivå. Talar hastighet kan endast tillämpas på neural röster på nivån mening.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Uttal av
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Ändra volym

Volymändringar kan tillämpas på standard röster på ett ord eller en mening på servernivå. Volymändringar kan endast tillämpas på neural röster på nivån mening.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Ändra försäljningsargument

Försäljningsargument ändringar kan tillämpas på standard röster på ett ord eller en mening på servernivå. Försäljningsargument ändringar kan endast tillämpas på neural röster på nivån mening.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Ändra försäljningsargument profil

> [!IMPORTANT]
> Försäljningsargument sluten ändringar stöds inte med neural röster.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Använda flera röster
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Nästa steg

* [Språkstöd: röster, språk, språk](language-support.md)
