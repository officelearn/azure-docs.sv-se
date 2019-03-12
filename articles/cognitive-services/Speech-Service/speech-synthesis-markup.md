---
title: Tal syntes Markup Language - Speech Services
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
ms.openlocfilehash: 77e94fd9fc08ad9f7b14e5cf226a6e616b69a37e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533399"
---
# <a name="speech-synthesis-markup-language"></a>Tal syntes Markup Language

Tal syntes Markup Language (SSML) är en XML-baserade märkspråk som gör det möjligt att styra uttal och *prosody* av text till tal. Prosody refererar till om du vill och försäljningsargument tal – dess musik, om du vill. Du kan ange ord fonetiskt, ge tips för att tolka siffror, infoga pauser, kontroll försäljningsargument, volym, och priset med mera. Mer information finns i [tal syntes Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

En fullständig lista över språk som stöds, språk och röster (neural och standard), se [språkstöd](language-support.md#text-to-speech).

Följande avsnitt innehåller exempel för vanliga tal syntes uppgifter.

>[!IMPORTANT]
> För närvarande är prosody taggning endast tillgänglig för standard röster.

## <a name="add-a-break"></a>Lägga till en paus
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Engelsktalande förändringstakten
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Uttal av
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Ändra volym
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Ändra försäljningsargument
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Ändra försäljningsargument profil
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Använda flera röster
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Nästa steg

* [Språkstöd: röster, språk, språk](language-support.md)
