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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487595"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entiteter och deras syfte i LUIS

Det primära syftet med entiteter är att ge klient programmet förutsägbar data extrahering. Ett _valfritt_, sekundärt syfte är att öka förutsägelsen för avsikten med beskrivningar. 

Det finns två typer av entiteter: 

* känd dator – från kontext
* ej känd dator – för exakta text matchningar

Börja alltid med en enhet som har lärts ur enheten eftersom den tillhandahåller det bredaste urvalet av data extraherings alternativ.

## <a name="entity-compared-to-intent"></a>Entitet jämfört med avsikt

Entiteten representerar ett data koncept i uttryck som du vill extrahera. 

Tänk på följande tre yttranden:

|Yttrande|Extraherade data|Förklaring|
|--|--|--|
|`Help`|-|Inget att extrahera.|
|`Send Bob a present`|Bob, presentera|Bob är definitivt viktigt att slutföra uppgiften. Det kan finnas tillräckligt med information eller också kan roboten behöva klargöra vad som är aktuellt med en uppföljnings fråga.|
|`Send Bob a box of chocolates.`|De två viktiga delarna av data, Robert och kartongen för choklad är viktiga för att slutföra användarens begäran.|

En uttryck kan innehålla flera entiteter eller ingen alls. Ett klient program _kan_ behöva entiteten för att utföra sin uppgift. 

Vid jämförelse _krävs_ en förutsägelse av avsikten för en uttryck och representerar hela uttryck. LUIS kräver att exempel yttranden finns i ett avsikts syfte. Om den primära avsikten med uttryck inte är viktig för klient programmet lägger du till alla yttranden till ingen avsikt. 

Om du hittar, senare i appens livs cykel, vill du dela upp yttranden kan du enkelt göra det. Det kan vara att organisera yttranden medan du redigerar, eller så kan du använda den förväntade avsikten i klient programmet. 

Det finns inget krav på att använda den förväntade avsikten i klient programmet, men den returneras som en del av svaret på förutsägelse slut punkten.

## <a name="entities-represent-data"></a>Entiteter representerar data

Entiteter är data som du vill hämta från uttryck. Detta kan vara ett namn, ett datum, ett produkt namn eller en grupp med ord. 

|Yttrande|Entitet|Data|
|--|--|--|
|Köp 3 biljetter till New York|Fördefinierat nummer<br>Plats. mål|3<br>New York|
|Köp ett ärende från New York till London den 5 mars|Plats. ORIGIN<br>Plats. mål<br>Färdiga datetimeV2|New York<br>London<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entiteter är valfria men rekommenderas

När avsikter krävs är entiteter valfria. Du behöver inte skapa entiteter för varje koncept i appen, men endast för de som krävs för att klient programmet ska vidta åtgärder. 

Om din yttranden inte har information som din robot behöver för att fortsätta behöver du inte lägga till dem. När din app är vuxen kan du lägga till dem senare. 

Om du inte är säker på hur du ska använda informationen lägger du till några vanliga fördefinierade entiteter som [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordnings](luis-reference-prebuilt-ordinal.md)tal, [e-post](luis-reference-prebuilt-email.md)och [telefonnummer](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Utforma entiteter för dekomposition

Påbörja din enhets design med en enhet som har lärts från enheten. På så sätt kan du enkelt utforma och ändra entiteten över tid. Lägg till under **komponenter** (underordnade entiteter) med **begränsningar** och **beskrivningar** för att slutföra entitetens design. 

Genom att utforma för dekomposition kan LUIS returnera en djup grad av enhets matchning till klient programmet. Detta gör att ditt klient program kan fokusera på affärs regler och lämna data upplösning till LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Enheter som har lärts från enheten är primära data samlingar

Enhets inlärda entiteter är den översta data enheten. Del komponenter är underordnade entiteter till enheter som har lärts ur enheten. 

**Begränsningar** är matchande entiteter med exakt text som tillämpar regler för att identifiera och extrahera data. **Beskrivningar** är funktioner som används för att förbättra relevansen hos ord eller fraser för förutsägelsen.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typer av entiteter

Välj entiteten baserat på hur data ska extraheras och hur de ska representeras när den har extraherats.

|Entitetstyp|Syfte|
|--|--|
|[**Datorn – lärt sig**](#composite-entity)|Överordnad gruppering av entiteter, oavsett typ av enhet. Enheter som lärts från enheten lär sig från kontexten i uttryck. På så sätt blir variationen av placering i exemplet yttranden stor. |
|[**Lista**](#list-entity)|Lista över objekt och deras synonymer som extraheras med **exakt text matchning**.|
|[**Mönster. alla**](#patternany-entity)|Entiteten där slutet av entiteten är svårt att fastställa. |
|[**Fördefinierade**](#prebuilt-entity)|Redan utbildad för att extrahera viss typ av data, till exempel URL eller e-post. Några av dessa fördefinierade entiteter definieras i ett [text](https://github.com/Microsoft/Recognizers-Text) projekt med öppen källkod. Om din kultur eller entitet inte stöds för närvarande, bidrar du till projektet.|
|[**Reguljärt uttryck**](#regular-expression-entity)|Använder reguljärt uttryck för **exakt text matchning**.|

### <a name="entity-role-defines-context"></a>Enhets roll definierar kontext

En entitets roll är det namngivna aliaset baserat på kontexten i uttryck. Ett exempel är en uttryck för att boka en flygning som har två platser, ursprung och mål.

`Book a flight from Seattle to Cairo`

De två exemplen för en `location` entitet måste extraheras. Klient programmet måste känna till typen av plats för var och en för att kunna slutföra biljett köpet. `location` entiteten behöver två roller för `origin` och `destination` och måste markeras i exemplet yttranden. 

Om LUIS hittar `location` men inte bestämmer rollen, returneras fortfarande plats enheten. Klient programmet måste följa upp med en fråga för att avgöra vilken typ av plats som användaren avsåg. 

Flera entiteter kan finnas i en uttryck och kan extraheras utan att använda roller. Om meningen i meningen anger enhet svärdet, ska en roll användas.

Om uttryck innehåller en lista över platser `I want to travel to Seattle, Cairo, and London.`, är det här en lista där varje objekt inte har någon ytterligare innebörd. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Om du behöver fler än det högsta antalet entiteter 

Kontakta supporten om du behöver mer än gränsen. Det gör du genom att samla in detaljerad information om systemet, gå till [Luis](luis-reference-regions.md#luis-website) -webbplatsen och sedan välja **support**. Om din Azure-prenumeration innehåller support tjänster kontaktar du [teknisk support för Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>Status för enhets förutsägelse

LUIS-portalen visar när entiteten, i ett exempel-uttryck, har en annan enhets förutsägelse än den entitet som du har valt. Den här olika poängen baseras på den aktuella tränade modellen. 

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om utmärkt [yttranden](luis-concept-utterance.md). 

Se [Lägg till entiteter](luis-how-to-add-entities.md) för att lära dig mer om hur du lägger till entiteter i Luis-appen.
