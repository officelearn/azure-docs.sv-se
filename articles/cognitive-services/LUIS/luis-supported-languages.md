---
title: Stöd för lokalisering med THOMAS appar i Azure | Microsoft Docs
description: Läs mer om de språk som har stöd för THOMAS.
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2017
ms.author: cahann
ms.openlocfilehash: 1eabc01ee07f8791680738a156471e3efe2c44ff
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356113"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>Kultur-specifika förståelse för THOMAS appar

En THOMAS appen specifik kultur och kan inte ändras när den har angetts. 

## <a name="multi-language-luis-apps"></a>Flera språk THOMAS appar
Om du behöver ett klientprogram för flera språk THOMAS, till exempel en chatbot, har du några alternativ. Om THOMAS stöder alla språk kan utveckla du en THOMAS app för varje språk. Varje THOMAS app har en unik app-ID och logg för slutpunkten. Om du behöver ange språk förstå för ett språk som inte stöder THOMAS, du kan använda [Microsoft översättare API](../Translator/translator-info-overview.md) skicka utterance till THOMAS slutpunkten för att översätta utterance till ett språk som stöds, och ta emot den resulterande resultat.

## <a name="languages-supported"></a>Språk som stöds
THOMAS förstår utterances på följande språk:


| Språk |Nationella inställningar  |  Fördefinierade domän | Fördefinierade entitet | Frasen förslag | **[Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | 
|--|--|:--:|:--:|:--:|:--:|
| Amerikansk engelska |`en-US` | ✔ | ✔  |✔|✔|
| Kanadensiska franska |`fr-CA` |-|   -   |-|✔|
| *[Kinesiska](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederländska |`nl-NL` |-|  -   |-|✔|
| Franska (Frankrike) |`fr-FR` |-| ✔ |✔ |✔|
| Tyska |`de-DE` |-| ✔ |✔ |✔|
| Italienska |`it-IT` |-| ✔ |✔|✔|
| *[Japanska](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Viktiga frasen|
| Koreanska |`ko-KR` |-|   -   |-|Viktiga frasen|
| Portugisiska (Brasilien) |`pt-BR` |-| ✔ |✔ |inte alla underordnade kulturer|
| Spanska (Spanien) |`es-ES` |-| ✔ |✔|✔|
| Spanska (Mexiko)|`es-MX` |-|  -   |✔|✔|


Språkstöd varierar för [färdiga entiteter](luis-reference-prebuilt-entities.md) och [färdiga domäner](luis-reference-prebuilt-domains.md). 

### <a name="chinese-support-notes"></a>* Kinesiska stöder anteckningar

 - I den `zh-cn` kultur, THOMAS förväntar sig förenklad kinesiska teckenuppsättningen i stället för den traditionella teckenuppsättningen.
 - Namn på avsikter, enheter, funktioner och reguljära uttryck kan vara i kinesiska eller latinska tecken.
 - Finns det [färdiga domäner referens ](luis-reference-prebuilt-domains.md) information om vilka fördefinierade domäner som stöds i den `zh-cn` kultur.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Japanska stöder anteckningar

 - Eftersom THOMAS stöder inte syntaktiska analys och kommer inte att förstå skillnaden mellan Keigo och informell japanska, måste du tillämpa olika nivåer av förfarande utbildning exempel för dina program. 
     - でございます är inte samma som です. 
     - です är inte samma som だ. 

### <a name="text-analytics-support-notes"></a>** Text analytics har stöd för anteckningar
Endast portugisiska stöds för subkulturer: `pt-PT` och `pt-BR`. Alla kulturer stöds på primära kultur-nivå. Mer information om textanalys [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). 

### <a name="speech-api-supported-languages"></a>Språk för tal-API som stöds
Se tal [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) för tal dictation läge språk.

### <a name="bing-spell-check-supported-languages"></a>Bing stavningskontroll stöds språk
Se Bing stavningskontroll [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) för en lista över språk som stöds och status.

## <a name="rare-or-foreign-words-in-an-application"></a>Sällsynta eller främmande ord i ett program
I den `en-us` kultur, THOMAS lär sig att skilja mest orden, inklusive slang. I den `zh-cn` kultur, THOMAS lär sig att skilja mellan de flesta kinesiska tecken. Om du använder ett ovanligt ord i `en-us` eller teckendata i `zh-cn`, och du ser att THOMAS verkar inte kan skilja den ord eller tecken kan du lägga till detta ord eller tecken till en [funktionen frasen lista](luis-how-to-add-features.md). Till exempel ska utanför kultur för program – det vill säga ord--läggas till en fras-list-funktion. Den här listan frasen markeras ej utbytbara, att indikera att en uppsättning sällsynta ord utgör en klass som THOMAS ska lära dig att identifiera, men de är inte synonymer eller utbytbara med varandra.

### <a name="hybrid-languages"></a>Hybrid-språk
Hybrid språk kombinera ord från två kulturer, till exempel engelska och kinesiska. Dessa språk stöds inte i THOMAS eftersom en app är baserad på en enda kultur.

## <a name="tokenization"></a>Tokenisering
Om du vill utföra maskininlärning THOMAS delar en utterance i [token](luis-glossary.md#token) baserat på kultur. 

|Språk|  varje blanksteg eller specialtecken | tecknet nivå|sammansatta ord|[principfilerna entitet returnerades](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Kinesiska||✔||✔|
|Nederländska|||✔|✔|
|Engelska (en-us)|✔ ||||
|Franska (fr-FR)|✔||||
|Franska (fr-CA)|✔||||
|Tyska|||✔|✔|
|Italienska|✔||||
|Japanska||||✔|
|Koreanska||✔||✔|
|Portugisiska (Brasilien)|✔||||
|Spanska (es-ES)|✔||||
|Spanska (es-MX)|✔||||

 