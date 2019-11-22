---
title: Avsikter och entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: En enda avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är en syfte eller målet som uttrycks i en användares uttryck. Definiera en uppsättning avsikter som motsvarar för åtgärder som användare vill dra i ditt program.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280889"
---
# <a name="intents-in-your-luis-app"></a>Avsikter i din LUIS-app

Ett intent som representerar en aktivitet eller åtgärd du vill utföra. Det är en syfte eller målet som uttrycks i en användares [uttryck](luis-concept-utterance.md).

Definiera en uppsättning avsikter som motsvarar för åtgärder som användare vill dra i ditt program. Exempelvis definierar en reseapp flera avsikter:

Resor appavsikter   |   Exempel på yttranden   | 
------|------|
 BookFlight (Boka flyg)     |   ”Boka mig en rör sig till Rio nästa vecka” <br/> ”Tar dig mig till Rio på 24th” <br/> ”Jag behöver en plan biljett nästa söndag till Rio de Janeiro”    |
 Hälsning     |   ”Hej” <br/>”Hello” <br/>”God morgon”  |
 CheckWeather | ”Vad är vädret som i Boston”? <br/> ”Visa prognosen för den här helgen” |
 Ingen         | ”Hämta mig ett cookie-recept”<br>”Lakers vinna”? |

Alla program levereras med det fördefinierade syftet "[ingen](#none-intent)", vilket är återställnings avsikten. 

## <a name="prebuilt-domains-provide-intents"></a>Fördefinierade domäner tillhandahålla avsikter
Förutom de som du definierar kan du använda färdiga syften från en av de [fördefinierade domänerna](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Returnera alla avsikter poäng
Du kan tilldela ett uttryck för till en enda avsikt. När LUIS tar emot en uttryck på slut punkten returnerar den som standard den främsta avsikten för uttryck. 

Om du vill ha poängen för alla avsikter för uttryck kan du ange en flagga i frågesträngen för förutsägelse-API: et. 

|Förutsägelse-API-version|Flagga|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Avsikten jämfört med entiteten
Avsikten representerar en åtgärd som programmet ska vidta för användaren och som baseras på hela uttryck. Ett uttryck kan ha bara en övre bedömning avsikt, men den kan ha många entiteter. 

<a name="how-do-intents-relate-to-entities"></a>

Skapa en avsikt när användarens _avsikt_ skulle utlösa en åtgärd i klient programmet, t. ex. ett anrop till funktionen checkweather (). Skapa sedan entiteter som representerar de parametrar som krävs för att utföra åtgärden. 

|Avsikt   | Entitet | Exempeluttryck   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {”type”: ”plats”, ”enhet”: ”seattle”}<br>{”type”: ”builtin.datetimeV2.date","entity”: ”morgon”, ”lösning”: ”2018-05-23”} | Vad är vädret som i `Seattle` `tomorrow`? |
| CheckWeather | {”type”: ”date_range”, ”enhet”: ”den här helgen”} | Visa mig prognosen för `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Fördefinierade domän avsikter

[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) innehåller metoder med yttranden. 

## <a name="none-intent"></a>Avsikten Ingen

Den **ingen** avsikt skapas, men lämnas tomt avsiktligt. Den **ingen** avsikten är en obligatorisk avsikten och kan inte tas bort eller bytt namn. Fyll den med yttranden som är utanför din domän.

**Ingen** avsikt är till gång till återställnings avsikten, viktigt i varje app och bör ha 10% av den totala yttranden. Den används för att lära LUIS yttranden som inte är viktiga i programdomänen (ämnesområde). Om du inte lägger till yttranden för den **ingen** avsikt, LUIS tvingar ett uttryck som ligger utanför domänen till en domän avsikter. Detta kommer förskjuta förutsägelse poängen med undervisar LUIS fel syftet för uttryck. 

När en uttryck förutsägs som none-avsikt kan klient programmet ställa frågor till fler frågor eller ange en meny för att dirigera användaren till giltiga val. 

## <a name="negative-intentions"></a>Negativt avsikter 
Om du vill fastställa negativa och positiva avsikter som ”jag **vill** en bil” och ”jag **inte** vill ha en bil”, du kan skapa två avsikter (en positiv och en negativ) och Lägg till lämpliga yttranden för varje. Eller du kan skapa en enda avsikten och markera två olika positiva och negativa villkoren som en entitet.  

## <a name="intents-and-patterns"></a>Avsikter och mönster

Om du har ett exempel på yttranden, som kan definieras delvis eller helt som ett reguljärt uttryck, bör du överväga att använda [entiteten för reguljära uttryck](luis-concept-entity-types.md#regular-expression-entity) med ett [mönster](luis-concept-patterns.md). 

Om du använder en entitet med reguljära uttryck garanteras data extraheringen så att mönstret matchas. Mönster matchningen garanterar att en exakt avsikt returneras. 

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
