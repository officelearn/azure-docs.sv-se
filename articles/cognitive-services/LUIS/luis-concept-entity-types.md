---
title: Entitetstyper – LUIS
titleSuffix: Azure Cognitive Services
description: 'Entiteter extraherar data från uttryck. Entitetstyper ger dig förutsägbar data extrahering. Det finns två typer av entiteter: maskin-och icke-maskin-inlärd. Det är viktigt att veta vilken typ av entitet du arbetar med i yttranden.'
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221033"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entiteter och deras syfte i LUIS

Det primära syftet med entiteter är att ge klient programmet förutsägbar data extrahering. Ett _valfritt_, sekundärt syfte är att öka förutsägelsen för avsikten eller andra enheter med beskrivningar.

Det finns två typer av entiteter:

* känd dator – från kontext
* icke-identifierad för exakta text matchningar, mönster matchningar eller identifiering efter färdiga entiteter

Enheter som har registrerats av enheten ger det bredaste urvalet av data extraherings alternativ. Enheter som inte är baserade på enheten fungerar med text matchning och kan användas oberoende av varandra eller som en [begränsning](#design-entities-for-decomposition) på en enhet som har lärts ur enheten.

## <a name="entities-represent-data"></a>Entiteter som representerar data

Entiteter är data som du vill hämta från uttryck, till exempel namn, datum, produkt namn eller viktiga ord grupper. Ett uttryck kan innehålla många entiteter eller inga alls. Ett klient program _kan_ behöva data för att utföra dess uppgift.

Entiteter måste märkas konsekvent i alla utbildnings-yttranden för varje avsikt i en modell.

 Du kan definiera egna entiteter eller använda fördefinierade entiteter för att spara tid för vanliga koncept som [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordnings](luis-reference-prebuilt-ordinal.md)tal, [e-post](luis-reference-prebuilt-email.md)och [telefonnummer](luis-reference-prebuilt-phonenumber.md).

|Yttrande|Entitet|Data|
|--|--|--|
|Köpa 3 biljetter New York|Fördefinierade tal<br>Location.Destination|3<br>New York|
|Köp en biljett från New York till London 5 mars|Location.Origin<br>Location.Destination<br>Fördefinierade datetimeV2|New York<br>London<br>5 mars 2018|

### <a name="entities-are-optional"></a>Entiteter är valfria

Avsikter är obligatoriska, är entiteter valfria. Du behöver inte skapa entiteter för varje koncept i appen, men endast för de som krävs för att klient programmet ska vidta åtgärder.

Om din yttranden inte har data som krävs av klient programmet behöver du inte lägga till entiteter. När ditt program utvecklar och ett nytt behov av data identifieras kan du lägga till lämpliga entiteter i LUIS-modellen senare.

## <a name="entity-compared-to-intent"></a>Entiteten jämfört med avsikt

Entiteten representerar ett data koncept i uttryck som du vill extrahera.

En uttryck kan eventuellt innehålla entiteter. Vid jämförelse _krävs_ en förutsägelse av avsikten för en uttryck och representerar hela uttryck. LUIS kräver att exempel yttranden finns i ett avsikts syfte.

Tänk på följande 4 yttranden:

|Yttrande|Avsikten förutse|Extraherade enheter|Förklaring|
|--|--|--|--|
|Hjälp|hjälp|-|Inget att extrahera.|
|Skicka något|sendSomething|-|Inget att extrahera. Modellen har inte tränats att extrahera `something` i den här kontexten och det finns ingen mottagare.|
|Skicka Bob a present|sendSomething|`Bob`, `present`|Modellen har tränats med en [personName](luis-reference-prebuilt-person.md) -fördefinierad entitet som har extraherat namnet `Bob`. En enhet som har registrerats av enheten har använts för att extrahera `present`.|
|Skicka Bob en ruta med choklad|sendSomething|`Bob`, `box of chocolates`|De två viktiga data bitarna `Bob` och `box of chocolates`har extraherats av entiteter.|

## <a name="design-entities-for-decomposition"></a>Utforma entiteter för dekomposition

Det är en bra enhets design för att göra din enhet på högsta nivå till en enhet som har lärts av enheten. Detta möjliggör ändringar i din enhets design över tid och användningen av **del komponenter** (underordnade entiteter), eventuellt med **begränsningar** **och beskrivare**, för att dela upp entiteten på den översta nivån i de delar som krävs av klient programmet.

Genom att utforma för dekomposition kan LUIS returnera en djup grad av enhets matchning till klient programmet. Detta gör att klient programmet kan fokusera på affärs regler och lämna data upplösning till LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Enheter som har lärts från enheten är primära data samlingar

Enhets [**inlärda entiteter**](tutorial-machine-learned-entity.md) är den översta data enheten. Del komponenter är underordnade entiteter till enheter som har lärts ur enheten.

En dator som har lärt enhets utlösare baserat på den kontext som lärts genom utbildning yttranden. **Begränsningar** är valfria regler som tillämpas på en enhet som har registrerats av enheten och som ytterligare begränsar utlösare baserat på exakt text matchnings definitionen för en icke-känd entitet, till exempel en [lista](reference-entity-list.md) eller ett [regex](reference-entity-regular-expression.md). En `size` enhets medveten entitet kan till exempel ha en begränsning för en `sizeList` lista entitet som begränsar `size` entiteten så att den endast utlöses när värden som finns i `sizeList` entiteten påträffas.

[**Beskrivningar**](luis-concept-feature.md) är funktioner som används för att förbättra relevansen hos ord eller fraser för förutsägelsen. De kallas *beskrivningar* eftersom de används för att *beskriva* ett avsikts-eller enhets namn. Beskrivningar beskriver särskiljande kännetecken eller dataattribut, t. ex. viktiga ord eller fraser som LUIS studerar och lär sig genom.

När du skapar en Frass List funktion i LUIS-appen aktive ras den globalt som standard och tillämpas jämnt över alla syften och entiteter. Men om du använder fras listan som en beskrivning (funktion) för en enhet som har registrerats av enheten (eller *modellen*), så minskar dess omfattning så att den endast gäller för den modellen och inte längre används med alla andra modeller. Genom att använda en fras lista som en beskrivning av en modell får du en nedbrytning genom att hjälpa till med precisionen för den modell som den tillämpas på.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typer av enheter

Välj entiteten baserat på hur data ska extraheras och hur de ska representeras när den har extraherats.

|Entitetstyp|Syfte|
|--|--|
|[**Datorn – lärt sig**](tutorial-machine-learned-entity.md)|Enheter som lärts från enheten lär sig från kontexten i uttryck. Överordnad gruppering av entiteter, oavsett typ av enhet. På så sätt blir variationen av placering i exemplet yttranden stor. |
|[**Lista**](reference-entity-list.md)|Lista över objekt och deras synonymer som extraheras med **exakt text matchning**.|
|[**Mönster. alla**](reference-entity-pattern-any.md)|Entiteten där slutet av entiteten är svårt att fastställa. |
|[**Fördefinierade**](luis-reference-prebuilt-entities.md)|Redan utbildad för att extrahera viss typ av data, till exempel URL eller e-post. Några av dessa fördefinierade entiteter definieras i ett [text](https://github.com/Microsoft/Recognizers-Text) projekt med öppen källkod. Om din specifika kultur eller entitet inte stöds för närvarande, bidra till projektet.|
|[**Reguljärt uttryck**](reference-entity-regular-expression.md)|Använder reguljärt uttryck för **exakt text matchning**.|

## <a name="extracting-contextually-related-data"></a>Extrahera sammanhangsbaserade relaterade data

En uttryck kan innehålla två eller flera förekomster av en entitet där innebörden av data baseras på kontexten i uttryck. Ett exempel är en uttryck för att boka en flygning som har två platser, ursprung och mål.

`Book a flight from Seattle to Cairo`

De två exemplen för en `location` entitet måste extraheras. Klient programmet måste känna till typen av plats för var och en för att kunna slutföra biljett köpet.

Det finns två metoder för att extrahera sammanhangsbaserade data:

 * `location` entiteten är en enhet som har lärts in, och använder två del komponent enheter för att avbilda `origin` och `destination` (rekommenderas)
 * `location` entiteten använder två **roller** i `origin` och `destination`

Flera entiteter kan finnas i en uttryck och kan extraheras utan att använda dekomposition eller roller om kontexten där de används inte har någon betydelse. Om uttryck till exempel innehåller en lista över platser `I want to travel to Seattle, Cairo, and London.`, är det här en lista där varje objekt inte har någon ytterligare innebörd.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Använda del komponent enheter för en enhet som har lärts in en enhet för att definiera kontext

Du kan använda en [**enhet som har registrerats**](tutorial-machine-learned-entity.md) av enheten för att extrahera data som beskriver åtgärden att boka en flygning och sedan dela upp entiteten på den översta nivån i de separata delar som krävs av klient programmet.

I det här exemplet `Book a flight from Seattle to Cairo`den översta entiteten `travelAction` och etiketteras för att extrahera `flight from Seattle to Cairo`. Sedan skapas två del komponent enheter som kallas `origin` och `destination`, både med en begränsning som tillämpas på den färdiga `geographyV2` entiteten. I utbildningen yttranden är `origin` och `destination` rätt etiketter.

### <a name="using-entity-role-to-define-context"></a>Använd Entity-rollen för att definiera kontext

En roll är ett namngivet alias för en entitet baserat på en kontext i uttryck. En roll kan användas med en fördefinierad eller anpassad entitetstyp och används i båda exemplen yttranden och Patterns. I det här exemplet behöver `location` entiteten två roller för `origin` och `destination` och båda behöver markeras i exemplet yttranden.

Om LUIS hittar `location` men inte bestämmer rollen, returneras fortfarande plats enheten. Klient programmet måste följa upp med en fråga för att avgöra vilken typ av plats som användaren avsåg.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Om du behöver mer än det maximala antalet enheter

Kontakta supporten om du behöver mer än gränsen. Det gör du genom att samla in detaljerad information om systemet, gå till [Luis](luis-reference-regions.md#luis-website) -webbplatsen och sedan välja **support**. Om din Azure-prenumeration innehåller support tjänster kontaktar du [teknisk support för Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status för enhets förutsägelse

LUIS-portalen visar när entiteten, i ett exempel-uttryck, har en annan enhets förutsägelse än den entitet som du har valt. Den här olika poängen baseras på den aktuella tränade modellen.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om utmärkt [yttranden](luis-concept-utterance.md).

Se [Lägg till entiteter](luis-how-to-add-entities.md) för att lära dig mer om hur du lägger till entiteter i Luis-appen.

Se [självstudie: extrahera strukturerade data från användare uttryck med enhets identifierade entiteter i language Understanding (Luis)](tutorial-machine-learned-entity.md) om du vill lära dig hur du extraherar strukturerade data från en uttryck med hjälp av den dator som har lärts.
 
