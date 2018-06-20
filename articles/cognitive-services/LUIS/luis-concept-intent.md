---
title: Förstå avsikter i THOMAS appar i Azure | Microsoft Docs
description: Beskriver vilka avsikter som språk förstå Intelligent Service (THOMAS) appar.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 5c2feb0240b676d4e106cbda65aaaed7604a35c5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265160"
---
# <a name="intents-in-luis"></a>Avsikter i THOMAS

Syftet representerar en aktivitet eller åtgärd användaren vill utföra. Det är ett syfte eller målet i en användares [utterance](luis-concept-utterance.md).

Definiera en uppsättning avsikter som motsvarar åtgärder som användare vill göra i ditt program. Till exempel definierar en resa app flera avsikter:

Resa app avsikter   |   Exempel utterances   | 
------|------|
 BookFlight     |   ”Bok mig en rör sig till Rio nästa vecka” <br/> ”Föra mig att Rio på 24th” <br/> ”Jag behöver en plan biljett nästa söndag till Rio de Janeiro”    |
 Hälsning     |   ”Hej” <br/>”Hello” <br/>”God morgon”  |
 CheckWeather | ”Vad är väder som i Boston”? <br/> ”Visa prognosen för helgen” |
 Ingen         | ”Hämta mig ett cookie-recept”<br>”Lakers win”? |

Alla program som levereras med fördefinierade avsikten ”[ingen](#none-intent-is-fallback-for-app)” vilket är avsikten återställningsplats. 

## <a name="prebuilt-domains-provide-intents"></a>Fördefinierade domäner tillhandahåller avsikter
Du kan använda fördefinierade avsikter från en av de fördefinierade domänerna förutom avsikter som du definierar. Mer information finns i [använder fördefinierade domäner i THOMAS appar](luis-how-to-use-prebuilt-domains.md) att lära dig hur du anpassar avsikter från en fördefinierad domän i din app.

## <a name="return-all-intents-scores"></a>Returnera alla avsikter resultat
Du tilldelar en utterance till en enda avsikten. När THOMAS tar emot en utterance på slutpunkten, returnerar en övre avsett för att utterance. Om du vill använda resultat för alla avsikter för utterance kan du ange `verbose=true` flaggan på frågesträngen API [endpoint anropet](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Avsikten jämfört med entitet
Avsikten representerar åtgärden chatbot tidsåtgången för användaren och baserat på hela utterance. Entiteten representerar ord eller fraser som ingår i utterance. En utterance kan ha endast en upp bedömningen avsikt men den kan ha många entiteter. 

<a name="how-do-intents-relate-to-entities"></a> Skapa syftet när användarens _avsikt_ skulle utlösa en åtgärd i ditt klientprogram, t.ex. ett anrop till funktionen checkweather(). Skapa sedan en entitet för att representera parametrar som krävs för att utföra åtgärden. 

|Exempel avsikt   | Entitet | Entiteten i exempel utterances   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {”typ”: ”plats”, ”enhet”: ”seattle”}<br>{”typ”: ”builtin.datetimeV2.date","entity”: ”i morgon”, ”lösning”: ”2018-05-23”} | Vad väder precis som i `Seattle` `tomorrow`? |
| CheckWeather | {”typ”: ”date_range”, ”enhet”: ”helgen”} | Visa en prognos `this weekend` | 

## <a name="custom-intents"></a>Anpassade avsikter

På liknande sätt att vidta [utterances](luis-concept-utterance.md) motsvarar en enda avsikten. Utterances i din avsikt kan använda någon [entiteten](luis-concept-entity-types.md) i appen eftersom enheter som inte är avsikten-specifika. 

## <a name="prebuilt-domain-intents"></a>Fördefinierade domän avsikter

[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) har avsikter med utterances.  

## <a name="none-intent-is-fallback-for-app"></a>Ingen avsiktshantering är återställningsplats för app
Den **ingen** avsikten är en fångar in alla eller återställningsplats avsikt. Används för att utbilda THOMAS utterances som inte är viktiga i programdomänen (ämnesområde). Den **ingen** avsikt ska ha mellan 10 och 20 procent av totalt utterances i programmet. Inte lämna det tomt. 

### <a name="none-intent-helps-conversation-direction"></a>Ingen avsiktshantering hjälper konversation riktning
När en utterance prognoser som ingen avsiktshantering och returneras till chatbot med den förutsägelse av bot kan flera frågor eller ange en meny för att dirigera användare till giltiga alternativ i chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Inga utterances inget avsiktshantering skeva förutsägelser
Om du inte lägger till några utterances för den **ingen** avsiktshantering, THOMAS tvingar en utterance som är utanför domänen till en domän avsikter. Detta kommer skeva resultat för förutsägelse av lära THOMAS fel avsett för utterance. 

### <a name="add-utterances-to-the-none-intent"></a>Lägg till utterances avsiktshantering NONE
Den **ingen** avsikt skapas, men lämnas tomt avsiktligt. Fyll den med utterances som befinner sig utanför domänen. En bra utterance för **ingen** är något helt utanför appen samt bransch appen fungerar. Till exempel en resa app ska inte använda några utterances för **ingen** som kan avse reser till exempel reservationer, fakturering, mat, hotell-och restaurangbranschen, last, inflight underhållning. 

Vilken typ av utterances lämnas för inget avsiktshantering? Börja med ett specifikt att din bot inte bör besvara sådana ”vilken typ av dinosaur har blå tänder”? Det här är en mycket specifik fråga långt utanför en resa app. 

### <a name="none-is-a-required-intent"></a>Ingen är en obligatorisk avsikt
Den **ingen** avsikt är en obligatorisk avsikt och kan inte tas bort eller bytt namn.

## <a name="negative-intentions"></a>Negativt avsikter 
Om du vill fastställa negativa och positiva avsikter som ”jag **vill** en bil” och ”jag **inte** vill en bil”, kan du skapa två avsikter (ett positivt och ett negativt) och lägga till lämpliga utterances för varje. Eller skapa en enda avsikten och markera två olika positiva och negativa villkoren som en enhet.  

## <a name="intent-balance"></a>Avsiktshantering saldo
Appen domän avsikter ska ha en balans mellan utterances över varje avsikt. Har inte en avsikten med 10 utterances och en annan avsikten med 500 utterances. Detta är inte balanserade. Om du har den här situationen kan granska avsikten med 500 utterances att se om många av avsikter kan att omorganisera i en [mönster](luis-concept-patterns.md). 

Den **ingen** avsikt ingår inte i saldot. Att avsikten ska innehålla 10% av de totala utterances i appen.

## <a name="intent-limits"></a>Avsiktshantering gränser
Granska [gränser](luis-boundaries.md#model-boundaries) för att förstå hur många avsikter som du kan lägga till en modell. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Om du behöver mer än det maximala antalet avsikter 
Först eventuellt om datorn är för många avsikter. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kan kombineras flera avsikter till enda avsikten med enheter 
Avsikter för liknande kan göra det svårare för THOMAS att skilja åt. Avsikter ska variera tillräckligt för att avbilda huvuduppgifter som användaren frågar efter, men de inte behöver avbilda varje sökväg tar din kod. Till exempel BookFlight och FlightCustomerService kanske separat avsikter i resa app, men BookInternationalFlight och BookDomesticFlight liknar för. Om systemet måste skilja dem, använda entiteter eller andra logik i stället avsikter. 

### <a name="dispatcher-model"></a>Dispatcher modellen
Lär dig mer om hur du kombinerar THOMAS och frågor och svar om maker appar med den [dispatch modellen](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Be om hjälp för appar med stort antal avsikter
Kontakta supporten om du minskar antalet avsikter eller dela dina avsikter i flera appar inte fungerar för dig. Om din Azure-prenumeration innehåller supporttjänster, kontakta [Azure teknisk support](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [entiteter](luis-concept-entity-types.md), som är viktiga ord som är relevanta för avsikter
* Lär dig hur du [lägga till och hantera avsikter](luis-how-to-add-intents.md) i THOMAS appen.
* Granska avsikt [bästa praxis](luis-concept-best-practices.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website