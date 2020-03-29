---
title: Avsikter och entiteter - LUIS
titleSuffix: Azure Cognitive Services
description: En enskild avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares uttryck. Definiera en uppsättning avsikter som motsvarar åtgärder som användarna vill vidta i ditt program.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220978"
---
# <a name="intents-in-your-luis-app"></a>Avsikter i LUIS-appen

En avsikt representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares [yttrande](luis-concept-utterance.md).

Definiera en uppsättning avsikter som motsvarar åtgärder som användarna vill vidta i ditt program. En reseapp definierar till exempel flera avsikter:

Avsikter om reseapp   |   Exempel på yttranden   | 
------|------|
 BookFlight (Boka flyg)     |   "Boka ett flyg till Rio nästa vecka" <br/> "Flyg mig till Rio den 24: e" <br/> "Jag behöver en flygbiljett nästa söndag till Rio de Janeiro"    |
 Hälsning     |   "Hej" <br/>"Hej" <br/>"Bra läge"  |
 CheckWeather | "Hur är vädret i Boston?" <br/> "Visa mig prognosen för den här helgen" |
 Inget         | "Ge mig en cookie recept"<br>"Vann Lakers?" |

Alla program levereras med den fördefinierade avsikten "[Ingen](#none-intent)", vilket är reservmetoden. 

## <a name="prebuilt-domains-provide-intents"></a>Fördefinierade domäner ger avsikter
Förutom avsikter som du definierar kan du använda fördefinierade avsikter från en av de [fördefinierade domänerna](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Returnera alla avsikters poäng
Du tilldelar ett uttryck till en enda avsikt. När LUIS tar emot ett uttryck på slutpunkten returneras som standard den översta avsikten för yttrandet. 

Om du vill ha poängen för alla avsikter för uttrycket kan du ange en flagga i frågesträngen för förutsägelse-API:et. 

|Förutsägelse API-version|Flagga|
|--|--|
|V2|`verbose=true`|
|V3 (på andra)|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Avsikt jämfört med entitet
Avsikten representerar åtgärd som programmet ska vidta för användaren och baseras på hela uttrycket. Ett uttryck kan bara ha en avsikt med högsta poängsättning, men det kan ha många entiteter. 

<a name="how-do-intents-relate-to-entities"></a>

Skapa en avsikt när användarens _avsikt_ skulle utlösa en åtgärd i klientprogrammet, som ett anrop till funktionen checkweather(). Skapa sedan entiteter för att representera parametrar som krävs för att utföra åtgärden. 

|Avsikt   | Entitet | Exempel på yttrande   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Hur är vädret i? `Seattle` `tomorrow` |
| CheckWeather | { "typ": "date_range", "entitet": "den här helgen" } | Visa mig prognosen för`this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Fördefinierade domänavsikter

[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) ger avsikter med yttranden. 

## <a name="none-intent"></a>Avsikten Ingen

**Avsikten Ingen** skapas men lämnas tom med flit. **Avsikten Ingen** är en obligatorisk avsikt och kan inte tas bort eller byta namn. Fyll den med yttranden som ligger utanför domänen.

**Avsikten Ingen** är reservavsikten, viktig i varje app och bör ha 10 % av de totala yttrandena. Den används för att lära LUIS yttranden som inte är viktiga i appdomänen (ämnesområde). Om du inte lägger till några yttranden för **ingen** avsikten tvingar LUIS ett uttryck som ligger utanför domänen till en av domänens avsikter. Detta kommer att förvränga förutsägelsepoängen genom att lära LUIS fel avsikt för yttrandet. 

När ett uttryck förutses som ingen avsikt, klientprogrammet kan ställa fler frågor eller ge en meny för att styra användaren till giltiga val. 

## <a name="negative-intentions"></a>Negativa avsikter 
Om du vill bestämma negativa och positiva avsikter, till exempel "Jag **vill ha** en bil" och "Jag vill **inte** ha en bil", kan du skapa två avsikter (en positiv och en negativ) och lägga till lämpliga yttranden för varje. Du kan också skapa en enda avsikt och markera de två olika positiva och negativa termerna som en entitet.  

## <a name="intents-and-patterns"></a>Avsikter och mönster

Om du har exempelyttranden, som kan definieras delvis eller helt som ett reguljärt uttryck, kan du använda [entiteten för reguljärt uttryck](luis-concept-entity-types.md#regular-expression-entity) som parats ihop med ett [mönster](luis-concept-patterns.md). 

Om du använder en entitet för reguljära uttryck garanteras datautvinningen så att mönstret matchas. Mönstermatchningen garanterar att en exakt avsikt returneras. 

## <a name="intent-balance"></a>Balans med avsikt
Appdomänens avsikter bör ha en balans mellan yttranden för varje avsikt. Har inte en avsikt med 10 yttranden och en annan avsikt med 500 yttranden. Detta är inte balanserat. Om du har den här situationen granskar du avsikten med 500 yttranden för att se om många av avsikterna kan omorganiseras till ett [mönster](luis-concept-patterns.md). 

**Avsikten Ingen** ingår inte i saldot. Den avsikten bör innehålla 10 % av de totala yttrandena i appen.

## <a name="intent-limits"></a>Begränsningar för avsikt
Granska [gränser](luis-boundaries.md#model-boundaries) för att förstå hur många avsikter du kan lägga till i en modell. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Om du behöver mer än det maximala antalet avsikter 
Tänk först på om systemet använder för många avsikter. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kan flera avsikter kombineras till enstaka avsikter med entiteter 
Avsikter som är för lika kan göra det svårare för LUIS att skilja mellan dem. Avsikter bör varieras tillräckligt för att fånga de viktigaste uppgifterna som användaren ber om, men de behöver inte fånga varje sökväg koden tar. BookFlight och FlightCustomerService kan till exempel vara separata avsikter i en reseapp, men BookInternationalFlight och BookDomesticFlight är för lika. Om ditt system behöver skilja dem, använd entiteter eller annan logik i stället för avsikter. 

### <a name="dispatcher-model"></a>Avsändare modell
Läs mer om hur du kombinerar LUIS- och QnA maker-appar med [leveransmodellen](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Begär hjälp för appar med stort antal avsikter
Om det inte fungerar att minska antalet avsikter eller dela upp dina avsikter i flera appar kontaktar du supporten. Om din Azure-prenumeration innehåller supporttjänster kontaktar du [Azures tekniska support](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [entiteter](luis-concept-entity-types.md), som är viktiga ord som är relevanta för avsikter
* Läs om hur du [lägger till och hanterar avsikter](luis-how-to-add-intents.md) i LUIS-appen.
* Granska [metodtips](luis-concept-best-practices.md) för avsikt
