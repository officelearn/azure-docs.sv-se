---
title: Entitetstyper – LUIS
description: En entitet hämtar data från en användare uttryck vid förutsägelse körning. Ett _valfritt_, sekundärt syfte är att öka förutsägelsen för avsikten eller andra entiteter genom att använda entiteten som en funktion.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585648"
---
# <a name="extract-data-with-entities"></a>Extrahera data med entiteter

En entitet hämtar data från en användare uttryck vid förutsägelse körning. Ett _valfritt_, sekundärt syfte är att öka förutsägelsen för avsikten eller andra entiteter genom att använda entiteten som en funktion.

Det finns flera typer av entiteter:

* [Maskininlärd enhet](reference-entity-machine-learned-entity.md)
* Ej identifierad maskin vara används som en nödvändig [funktion](luis-concept-feature.md) – för exakta text matchningar, mönster matchningar eller identifiering av fördefinierade entiteter
* [Pattern. any](#patternany-entity) -för att extrahera fri Forms text, till exempel bok titlar från ett [mönster](reference-entity-pattern-any.md)

Enheter som har registrerats av enheten ger det bredaste urvalet av data extraherings alternativ. Enheter som inte är baserade på enheten fungerar med text matchning och används som en [nödvändig funktion](#design-entities-for-decomposition) för en enhet eller avsikt som har lärts ur enheten.

## <a name="entities-represent-data"></a>Entiteter representerar data

Entiteter är data som du vill hämta från uttryck, till exempel namn, datum, produkt namn eller viktiga ord grupper. En uttryck kan innehålla flera entiteter eller ingen alls. Ett klient program _kan_ behöva data för att utföra dess uppgift.

Entiteter måste märkas konsekvent i alla utbildnings-yttranden för varje avsikt i en modell.

 Du kan definiera egna entiteter eller använda fördefinierade entiteter för att spara tid för vanliga koncept som [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordnings](luis-reference-prebuilt-ordinal.md)tal, [e-post](luis-reference-prebuilt-email.md)och [telefonnummer](luis-reference-prebuilt-phonenumber.md).

|Yttrande|Entitet|Data|
|--|--|--|
|Köp 3 biljetter till New York|Fördefinierat nummer<br>Mål|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Entiteter är valfria men rekommenderas

När [avsikter](luis-concept-intent.md) krävs är entiteter valfria. Du behöver inte skapa entiteter för varje koncept i appen, men endast för de där klient programmet behöver data eller entiteten fungerar som ett tips eller en signal till en annan entitet eller avsikt.

När ditt program utvecklar och ett nytt behov av data identifieras kan du lägga till lämpliga entiteter i LUIS-modellen senare.

## <a name="entity-compared-to-intent"></a>Entitet jämfört med avsikt

Entiteten representerar ett data koncept _i uttryck_. En avsikt klassificerar _hela uttryck_.

Tänk på följande fyra yttranden:

|Yttrande|Förväntad avsikt|Extraherade enheter|Förklaring|
|--|--|--|--|
|Hjälp|Hjälp|-|Inget att extrahera.|
|Skicka något|sendSomething|-|Inget att extrahera. Modellen har inte någon nödvändig funktion för att extrahera `something` i den här kontexten och det finns ingen mottagare angiven.|
|Skicka Bob a present|sendSomething|`Bob`, `present`|Modellen extraheras `Bob` genom att lägga till en nödvändig funktion i en fördefinierad entitet `personName` . En enhet som har registrerats av enheten har använts för att extrahera `present` .|
|Skicka Bob en ruta med choklad|sendSomething|`Bob`, `box of chocolates`|De två viktiga delarna av data `Bob` och `box of chocolates` har extraherats av enheter som har registrerats av enheten.|

## <a name="design-entities-for-decomposition"></a>Utforma entiteter för dekomposition

Med enhets inlärda entiteter kan du utforma ditt program schema för dekomposition och dela upp ett stort koncept i underentiteter.

Genom att utforma för dekomposition kan LUIS returnera en djup grad av enhets matchning till klient programmet. Detta gör att klient programmet kan fokusera på affärs regler och lämna data upplösning till LUIS.

En dator som har lärt enhets utlösare baserat på den kontext som lärts genom exempel yttranden.

Enhets [**inlärda entiteter**](tutorial-machine-learned-entity.md) är de översta utpackare. Underentiteter är underordnade entiteter till enheter som har lärts ur enheten.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Entitetstyper

En underordnad enhet till en överordnad måste vara en enhet som har lärts från enheten. Underentiteten kan använda en icke-känd entitet som en [funktion](luis-concept-feature.md).

Välj entiteten baserat på hur data ska extraheras och hur de ska representeras när den har extraherats.

|Entitetstyp|Syfte|
|--|--|
|[**Datorn – lärt sig**](tutorial-machine-learned-entity.md)|Extrahera kapslade, komplexa data som lärts från etiketterade exempel. |
|[**Lista**](reference-entity-list.md)|Lista över objekt och deras synonymer som extraheras med **exakt text matchning**.|
|[**Mönster. alla**](#patternany-entity)|Entiteten där det är svårt att hitta slutet på enheten, eftersom entiteten är kostnads fri. Endast tillgängligt i [mönster](luis-concept-patterns.md).|
|[**Fördefinierade**](luis-reference-prebuilt-entities.md)|Redan utbildad för att extrahera viss typ av data, till exempel URL eller e-post. Några av dessa fördefinierade entiteter definieras i ett [text](https://github.com/Microsoft/Recognizers-Text) projekt med öppen källkod. Om din kultur eller entitet inte stöds för närvarande, bidrar du till projektet.|
|[**Reguljärt uttryck**](reference-entity-regular-expression.md)|Använder reguljärt uttryck för **exakt text matchning**.|

## <a name="extracting-contextually-related-data"></a>Extrahera sammanhangsbaserade relaterade data

En uttryck kan innehålla två eller flera förekomster av en entitet där innebörden av data baseras på kontexten i uttryck. Ett exempel är en uttryck för att boka en flygning som har två geografiska platser, ursprung och mål.

`Book a flight from Seattle to Cairo`

De två platserna måste extraheras på ett sätt som klient programmet känner av typen av varje plats för att kunna slutföra biljett köpet.

Om du vill extrahera ursprung och mål skapar du två underentiteter som en del av biljett enheten för biljett ordningen som har lärts in. Skapa en nödvändig funktion som använder geographyV2 för var och en av underentiteterna.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Använda de funktioner som krävs för att begränsa entiteter

Läs mer om de [funktioner som krävs](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entiteten Pattern.any

Ett mönster. alla finns bara i ett [mönster](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Överskrida app-gränser för entiteter

Kontakta supporten om du behöver mer än [gränsen](luis-limits.md#model-limits). Det gör du genom att samla in detaljerad information om systemet, gå till [Luis](luis-reference-regions.md#luis-website) -webbplatsen och sedan välja **support**. Om din Azure-prenumeration innehåller support tjänster kontaktar du [teknisk support för Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status för enhets förutsägelse

LUIS-portalen visar när entiteten har en annan enhets förutsägelse än den entitet som du har valt för ett exempel-uttryck. Den här olika poängen baseras på den aktuella tränade modellen. Använd den här informationen för att lösa inlärnings fel med ett eller flera av följande:
* Skapa en [funktion](luis-concept-feature.md) för entiteten för att hjälpa till att identifiera entitetens koncept
* Lägg till fler [exempel på yttranden](luis-concept-utterance.md) och etikett med entiteten
* [Granska aktiva utbildnings förslag](luis-concept-review-endpoint-utterances.md) för alla yttranden som tas emot vid förutsägelse slut punkten som kan hjälpa dig att identifiera entitetens koncept.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om utmärkt [yttranden](luis-concept-utterance.md).

Se [Lägg till entiteter](luis-how-to-add-entities.md) för att lära dig mer om hur du lägger till entiteter i Luis-appen.

Se [självstudie: extrahera strukturerade data från användare uttryck med enhets identifierade entiteter i language Understanding (Luis)](tutorial-machine-learned-entity.md) om du vill lära dig hur du extraherar strukturerade data från en uttryck med hjälp av den dator som har lärts.

