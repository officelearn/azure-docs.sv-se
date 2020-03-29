---
title: Entitetstyper - LUIS
titleSuffix: Azure Cognitive Services
description: 'Entiteter extraherar data från uttrycket. Entitetstyper ger dig förutsägbar extrahering av data. Det finns två typer av entiteter: maskininlärd och icke-maskinlärd. Det är viktigt att veta vilken typ av entitet du arbetar med i yttranden.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221033"
---
# <a name="entities-and-their-purpose-in-luis"></a>Enheter och deras syfte i LUIS

Det primära syftet med entiteter är att ge klientprogrammet förutsägbar extrahering av data. Ett _valfritt,_ sekundärt syfte är att öka förutsägelsen av avsikten eller andra entiteter med deskriptorer.

Det finns två typer av enheter:

* maskinlärd - från kontext
* icke-maskininlärd - för exakta textmatchningar, mönstermatchningar eller identifiering av fördefinierade enheter

Maskininlärda entiteter ger det bredaste utbudet av datautvinningsalternativ. Icke-maskininlärda entiteter fungerar efter textmatchning och kan användas oberoende av eller som en [begränsning](#design-entities-for-decomposition) för en datorinlärd entitet.

## <a name="entities-represent-data"></a>Entiteter representerar data

Entiteter är data som du vill hämta från uttrycket, till exempel namn, datum, produktnamn eller någon betydande grupp av ord. Ett uttryck kan innehålla många entiteter eller inga alls. Ett klientprogram _kan_ behöva data för att utföra sin uppgift.

Entiteter måste märkas konsekvent över alla utbildningsyttranden för varje avsikt i en modell.

 Du kan definiera dina egna entiteter eller använda fördefinierade entiteter för att spara tid för vanliga begrepp som [datetimeV2,](luis-reference-prebuilt-datetimev2.md) [ordinal,](luis-reference-prebuilt-ordinal.md) [e-post](luis-reference-prebuilt-email.md)och [telefonnummer](luis-reference-prebuilt-phonenumber.md).

|Yttrande|Entitet|Data|
|--|--|--|
|Köp 3 biljetter till New York|Fördefinierat tal<br>Plats.Destination|3<br>New York|
|Köp en biljett från New York till London den 5 mars|Plats.Ursprung<br>Plats.Destination<br>Fördefinierad datetimeV2|New York<br>London<br>den 5 mars 2018|

### <a name="entities-are-optional"></a>Entiteter är valfria

Även om avsikter krävs är entiteter valfria. Du behöver inte skapa entiteter för alla koncept i appen, utan bara för de som krävs för att klientprogrammet ska kunna vidta åtgärder.

Om dina yttranden inte har data som klientprogrammet kräver behöver du inte lägga till entiteter. När ditt program utvecklas och ett nytt behov av data identifieras kan du lägga till lämpliga entiteter i LUIS-modellen senare.

## <a name="entity-compared-to-intent"></a>Entitet jämfört med avsikt

Entiteten representerar ett datakoncept i det uttryck som du vill extrahera.

Ett uttryck kan eventuellt innehålla entiteter. Som jämförelse _krävs_ förutsägelsen av avsikten för ett yttrande och representerar hela uttrycket. LUIS kräver exempel yttranden finns i en avsikt.

Tänk på följande 4 yttranden:

|Yttrande|Avsikt förutsagd|Enheter som extraherats|Förklaring|
|--|--|--|--|
|Hjälp|Hjälp|-|Inget att extrahera.|
|Skicka något|skickaNågonting|-|Inget att extrahera. Modellen har inte tränats `something` att extrahera i detta sammanhang, och det finns ingen mottagare heller.|
|Skicka Bob en present|skickaNågonting|`Bob`, `present`|Modellen har tränats med den fördefinierade entiteten [personName,](luis-reference-prebuilt-person.md) som har extraherat namnet `Bob`. En maskininlärd entitet `present`har använts för att extrahera .|
|Skicka Bob en låda choklad|skickaNågonting|`Bob`, `box of chocolates`|De två viktiga `Bob` databitarna `box of chocolates`och , har extraherats av entiteter.|

## <a name="design-entities-for-decomposition"></a>Designentiteter för nedbrytning

Det är bra entitetsdesign för att göra din entitet på den högsta nivån till en dator som lärs in på entiteten. Detta gör det möjligt för ändringar i entitetsdesignen över tid och användning av underkomponenter (underordnade **entiteter),** eventuellt med **begränsningar** och **deskriptorer,** för att bryta ned entiteten på den översta nivån i de delar som klientprogrammet behöver.

Genom att designa för nedbrytning kan LUIS returnera en djup grad av entitetsmatchning till klientprogrammet. På så sätt kan klientprogrammet fokusera på affärsregler och lämna datamatchning till LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Maskininlärda entiteter är primära datainsamlingar

[**Maskininlärda entiteter**](tutorial-machine-learned-entity.md) är den översta dataenheten. Underkomponenter är underordnade entiteter för datorinlärda entiteter.

En datorinlärd entitet utlöser baserat på det sammanhang som lärs ut genom utbildningsyttranden. **Begränsningar** är valfria regler som tillämpas på en datorinlärd entitet som ytterligare begränsar utlösande baseras på den exakta textmatchningsdefinitionen för en enhet som inte har lärt sig utan maskin, till exempel en [lista](reference-entity-list.md) eller [Regex](reference-entity-regular-expression.md). En dator `size` som lärt sig en dator kan `sizeList` till exempel ha `size` ett villkor för en listentitet som begränsar entiteten till att utlösa endast när värden som finns i entiteten `sizeList` påträffas.

[**Deskriptorer**](luis-concept-feature.md) är funktioner som används för att öka relevansen av ord eller fraser för förutsägelse. De kallas *deskriptorer* eftersom de används för att *beskriva* en avsikt eller entitet. Deskriptorer beskriver särskiljande egenskaper eller attribut för data, till exempel viktiga ord eller fraser som LUIS observerar och lär sig igenom.

När du skapar en fraslistefunktion i LUIS-appen aktiveras den globalt som standard och gäller jämnt i alla avsikter och entiteter. Men om du använder fraslistan som en deskriptor (funktion) för en maskininlärd entitet (eller *modell)* minskar dess omfattning till att endast gälla för den modellen och används inte längre med alla andra modeller. Om du använder en fraslista som en deskriptor till en modell kan nedbrytningen avstämd genom att hjälpa till med noggrannheten för den modell den tillämpas på.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Entitetstyper

Välj entiteten baserat på hur data ska extraheras och hur de ska representeras när de har extraherats.

|Entitetstyp|Syfte|
|--|--|
|[**Maskinlärd**](tutorial-machine-learned-entity.md)|Maskininlärda entiteter lär sig av kontexten i uttrycket. Överordnad gruppering av entiteter, oavsett entitetstyp. Detta gör variation av placering i exempel yttranden betydande. |
|[**Lista**](reference-entity-list.md)|Lista över objekt och deras synonymer som extraherats med **exakt textmatchning**.|
|[**Pattern.any**](reference-entity-pattern-any.md)|Entitet där slutet av entiteten är svår att fastställa. |
|[**Färdiga**](luis-reference-prebuilt-entities.md)|Redan utbildad för att extrahera specifika typer av data som URL eller e-post. Vissa av dessa fördefinierade entiteter definieras i projektet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) med öppen källkod. Om din specifika kultur eller entitet för närvarande inte stöds bidrar du till projektet.|
|[**Reguljärt uttryck**](reference-entity-regular-expression.md)|Använder reguljärt uttryck för **exakt textmatchning**.|

## <a name="extracting-contextually-related-data"></a>Extrahera kontextuellt relaterade data

Ett uttryck kan innehålla två eller flera förekomster av en entitet där datas innebörd baseras på kontexten i uttrycket. Ett exempel är ett uttryck för att boka en flygning som har två platser, ursprung och destination.

`Book a flight from Seattle to Cairo`

De två exemplen på en entitet `location` måste extraheras. Klientprogrammet måste känna till vilken typ av plats var och en för att slutföra biljettköpet.

Det finns två tekniker för att extrahera kontextuellt relaterade data:

 * Entiteten `location` är en maskininlärd entitet och använder två `origin` `destination` delkomponententiteter för att samla in och (föredras)
 * Entiteten `location` använder `origin` två **roller** och`destination`

Flera entiteter kan finnas i ett uttryck och kan extraheras utan att använda nedbrytning eller roller om den kontext där de används inte har någon betydelse. Om uttrycket till exempel innehåller en `I want to travel to Seattle, Cairo, and London.`lista över platser är det här en lista där varje objekt inte har en ytterligare betydelse.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Använda underkomponentenheter i en maskininlärd entitet för att definiera kontext

Du kan använda en [**maskininlärd entitet**](tutorial-machine-learned-entity.md) för att extrahera data som beskriver åtgärden att boka en flygning och sedan dela upp entiteten på den översta nivån i de separata delar som behövs av klientprogrammet.

I det `Book a flight from Seattle to Cairo`här exemplet kan den `travelAction` översta entiteten `flight from Seattle to Cairo`vara och märkas för att extrahera . Sedan skapas två underkomponententiteter, anropade `origin` och `destination`, båda med `geographyV2` ett villkor som tillämpas av den fördefinierade entiteten. I utbildningsyttrandena `origin` `destination` är och märkta på rätt sätt.

### <a name="using-entity-role-to-define-context"></a>Använda entitetsroll för att definiera kontext

En roll är ett namngivet alias för en entitet baserat på kontext i uttryck. En roll kan användas med valfri fördefinierad eller anpassad entitetstyp och användas i både exempelyttranden och mönster. I det här `location` exemplet behöver `origin` entiteten två roller och `destination` och båda måste markeras i exempelyttrandena.

Om LUIS `location` hittar men inte kan bestämma rollen returneras platseniteten fortfarande. Klientprogrammet skulle behöva följa upp med en fråga för att avgöra vilken typ av plats användaren menade.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Om du behöver mer än det maximala antalet enheter

Om du behöver mer än gränsen kontaktar du supporten. För att göra det, samla in detaljerad information om ditt system, gå till [LUIS](luis-reference-regions.md#luis-website) webbplats och välj sedan **Support**. Om din Azure-prenumeration innehåller supporttjänster kontaktar du [Azures tekniska support](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status för förutsägelse av entitet

LUIS-portalen visar när entiteten, i ett exempel yttrande, har en annan entitet förutsägelse än den entitet du valt. Denna annorlunda poäng baseras på den aktuella tränade modellen.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om bra [yttranden](luis-concept-utterance.md).

Se [Lägga till entiteter](luis-how-to-add-entities.md) om du vill veta mer om hur du lägger till entiteter i LUIS-appen.

Se [Självstudiekurs: Extrahera strukturerade data från användarutseende med datorinlärda entiteter i Språkförståelse (LUIS)](tutorial-machine-learned-entity.md) om du vill lära dig hur du extraherar strukturerade data från ett uttryck med hjälp av den maskininlärda entiteten.
 
