---
title: Användaren avsikter
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ett intent som representerar en aktivitet eller åtgärd du vill utföra. Det är en syfte eller målet som uttrycks i en användares uttryck.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: ce95e5c9f5a71a23f4da6273a01c8f89fe687c1a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215324"
---
# <a name="concepts-about-intents-in-your-luis-app"></a>Begrepp om avsikter i LUIS-appen

Ett intent som representerar en aktivitet eller åtgärd du vill utföra. Det är en syfte eller målet som uttrycks i en användares [uttryck](luis-concept-utterance.md).

Definiera en uppsättning avsikter som motsvarar för åtgärder som användare vill dra i ditt program. Exempelvis definierar en reseapp flera avsikter:

Resor appavsikter   |   Exempel på yttranden   | 
------|------|
 BookFlight (Boka flyg)     |   ”Boka mig en rör sig till Rio nästa vecka” <br/> ”Tar dig mig till Rio på 24th” <br/> ”Jag behöver en plan biljett nästa söndag till Rio de Janeiro”    |
 Hälsning     |   ”Hej” <br/>”Hello” <br/>”God morgon”  |
 CheckWeather | ”Vad är vädret som i Boston”? <br/> ”Visa prognosen för den här helgen” |
 Ingen         | ”Hämta mig ett cookie-recept”<br>”Lakers vinna”? |

Alla program som levereras med fördefinierade avsikten ”[ingen](#none-intent-is-fallback-for-app)” som är reserv avsikten. 

## <a name="prebuilt-domains-provide-intents"></a>Fördefinierade domäner tillhandahålla avsikter
Du kan använda fördefinierade avsikter från någon av de fördefinierade domänerna förutom avsikter som du definierar. Mer information finns i [använda fördefinierade domäner i LUIS appar](luis-how-to-use-prebuilt-domains.md) vill veta mer om hur du anpassar avsikter från en domän som är färdiga för användning i din app.

## <a name="return-all-intents-scores"></a>Returnera alla avsikter poäng
Du kan tilldela ett uttryck för till en enda avsikt. När LUIS tar emot ett uttryck på slutpunkten, returnerar den ett övre det uttryck som används. Om du vill poäng för alla avsikter för uttryck, kan du ange `verbose=true` flaggan frågesträngen för API: et [tjänstslutpunktens anrop](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Avsikten jämfört med entiteten
Avsikten representerar åtgärden chattrobot bör ta för användaren och baseras på hela uttryck. Entiteten representerar ord eller fraser som ingår i uttryck. Ett uttryck kan ha bara en övre bedömning avsikt, men den kan ha många entiteter. 

<a name="how-do-intents-relate-to-entities"></a> Skapa ett intent när användarens _avsikt_ ska utlösa en åtgärd i ditt klientprogram, t.ex. ett anrop till funktionen checkweather(). Skapa sedan en entitet som representerar parametrar som krävs för att utföra åtgärden. 

|Exempel avsikt   | Entitet | Entitet i exempel yttranden   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {”type”: ”plats”, ”enhet”: ”seattle”}<br>{”type”: ”builtin.datetimeV2.date","entity”: ”morgon”, ”lösning”: ”2018-05-23”} | Vad är vädret som i `Seattle` `tomorrow`? |
| CheckWeather | {”type”: ”date_range”, ”enhet”: ”den här helgen”} | Visa mig prognosen för `this weekend` | 

## <a name="custom-intents"></a>Anpassade avsikter

På liknande sätt att vidta [yttranden](luis-concept-utterance.md) motsvarar ett enda syfte. Yttranden i dina krav kan använda någon [entitet](luis-concept-entity-types.md) i appen eftersom enheter inte är avsikten-specifika. 

## <a name="prebuilt-domain-intents"></a>Fördefinierade domän avsikter

[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) har avsikter med yttranden.  

## <a name="none-intent-is-fallback-for-app"></a>Ingen avsikt är reserv för app
Den **ingen** avsikten är en allomfattande eller återställningsplats avsikt. Den används för att lära LUIS yttranden som inte är viktiga i programdomänen (ämnesområde). Den **ingen** avsikt ska ha mellan 10 och 20 procent av total yttranden i programmet. Lämna inte ingen tomt. 

### <a name="none-intent-helps-conversation-direction"></a>Ingen avsiktlig hjälper konversationen riktning
När ett uttryck förväntas som ingen avsiktlig och returnerade till chattrobot med den förutsägelsen roboten kan ställa fler frågor eller ange en meny för att dirigera användare till giltiga alternativ i chattrobot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Inga yttranden inget avsikt snedställer förutsägelser
Om du inte lägger till yttranden för den **ingen** avsikt, LUIS tvingar ett uttryck som ligger utanför domänen till en domän avsikter. Detta kommer förskjuta förutsägelse poängen med undervisar LUIS fel syftet för uttryck. 

### <a name="add-utterances-to-the-none-intent"></a>Lägg till yttranden avsikt NONE
Den **ingen** avsikt skapas, men lämnas tomt avsiktligt. Fyll den med yttranden som är utanför din domän. En bra uttryck för **ingen** är något helt utanför appen, samt branschen appen fungerar. Till exempel en reseapp bör inte använda yttranden för **ingen** som kan relatera till reser till exempel reservationer, fakturering, mat, resor, last, tillhörande underhållning. 

Vilken typ av yttranden lämnas för ingen avsiktlig? Börja med ett specifikt att din robot inte bör svara på sådan ”vilken typ av dinosaur har blå tänderna”? Det här är en mycket specifik fråga långt utanför en reseapp. 

### <a name="none-is-a-required-intent"></a>Ingen är en obligatorisk avsikt
Den **ingen** avsikten är en obligatorisk avsikten och kan inte tas bort eller bytt namn.

## <a name="negative-intentions"></a>Negativt avsikter 
Om du vill fastställa negativa och positiva avsikter som ”jag **vill** en bil” och ”jag **inte** vill ha en bil”, du kan skapa två avsikter (en positiv och en negativ) och Lägg till lämpliga yttranden för varje. Eller du kan skapa en enda avsikten och markera två olika positiva och negativa villkoren som en entitet.  

## <a name="intent-balance"></a>Avsiktshantering saldo
Domän avsikter ska ha en balans mellan yttranden över varje avsikt. Har inte en avsikt med 10 yttranden och en annan avsikten med 500 yttranden. Detta är inte balanserade. Om du har den här situationen kan du granska avsikten med 500 yttranden att se om många av avsikter kan att ordna i en [mönstret](luis-concept-patterns.md). 

Den **ingen** avsikt ingår inte i saldot. Detta syfte ska innehålla 10% av de totala yttranden i appen.

## <a name="intent-limits"></a>Avsiktshantering gränser
Granska [gränser](luis-boundaries.md#model-boundaries) för att förstå hur många avsikter som du kan lägga till en modell. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Om du behöver mer än det maximala antalet avsikter 
Först bör övervägas om datorn är för många avsikter. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kan kombineras flera avsikter till enda avsikten med entiteter 
Avsikter som liknar för kan göra det svårare för LUIS för att skilja dem åt. Avsikter ska vara olika tillräckligt för att avbilda de viktigaste uppgifterna som du frågar efter, men de behöver att samla in varje sökväg som tar din kod. Till exempel BookFlight och FlightCustomerService kanske separat avsikter i en reseapp, men BookInternationalFlight och BookDomesticFlight liknar för. Om systemet måste skilja dem, använda entiteter eller andra logic snarare än avsikter. 

### <a name="dispatcher-model"></a>Dispatchern modell
Läs mer om hur du kombinerar LUIS och QnA maker-appar med den [dispatch modellen](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Be om hjälp för appar med betydande antal avsikter
Om du minskar antalet avsikter eller att dela upp dina avsikter i flera appar inte fungerar för dig, kontakta supporten. Om din Azure-prenumeration innehåller supporttjänster, kontakta [teknisk support för Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [entiteter](luis-concept-entity-types.md), vilket är viktigt ord som är relevanta för avsikter
* Lär dig hur du [lägga till och hantera avsikter](luis-how-to-add-intents.md) i LUIS-appen.
* Granska avsikt [bästa praxis](luis-concept-best-practices.md)
