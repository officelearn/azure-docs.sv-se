---
title: Riktlinjer för informationsdesign
titleSuffix: Azure Cognitive Services
description: Introduktion till riktlinjer för utformning av offentliggörande och bedömning av informationsnivå.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776393"
---
# <a name="disclosure-design-guidelines"></a>Utlämnande av designriktlinjer
Lär dig att bygga och behålla förtroendet hos kunderna genom att vara transparent om den syntetiska karaktären hos din röstupplevelse.

## <a name="what-is-disclosure"></a>Vad är utlämnande?

Utlämnande är ett sätt att låta folk veta att de&#39;interagera med eller lyssna på en röst som är syntetiskt genereras.

## <a name="why-is-disclosure-necessary"></a>Varför är utlämnande nödvändigt?

Behovet av att avslöja det syntetiska ursprunget till en datorgenererad röst är relativt nytt. Förr i tiden var datorgenererade röster uppenbarligen att - ingen skulle någonsin missfalla dem för en verklig person. Men varje dag förbättras realismen hos syntetiska röster, och de blir alltmer omöjliga att skilja från mänskliga röster.

## <a name="goals"></a>Mål
Dessa är de principer att tänka på när man utformar syntetiska röstupplevelser:

**Stärka förtroendet**
<br>Design med avsikt att misslyckas Turing Test utan att försämra upplevelsen. Låt användarna vara med på det faktum att de interagerar med en syntetisk röst samtidigt som de kan interagera sömlöst med upplevelsen.

**Anpassa sig till användningssammanhang**
<br>Förstå när, var och hur dina användare kommer att interagera med den syntetiska rösten för att ge rätt typ av avslöjande vid rätt tidpunkt.

**Ställ tydliga förväntningar**
<br>Tillåt användare att enkelt upptäcka och förstå agentens funktioner. Erbjud möjligheter att lära sig mer om syntetisk röstteknik på begäran.

**Omfamna misslyckande**
<br>Använd stunder av underlåtenhet att förstärka agentens kapacitet.

## <a name="how-to-use-this-guide"></a>Så här använder du den här guiden

Den här guiden hjälper dig att avgöra vilka informationsmönster som passar bäst för din syntetiska röstupplevelse. Vi ger sedan exempel på hur och när de ska användas. Var och en av dessa mönster är utformad för att maximera öppenhet med användare om syntetiskt tal samtidigt som trogen mänskliga-centrerad design.

Med tanke på den stora mängd designvägledning på röstupplevelser fokuserar vi här specifikt på:

1. [**Bedömning av offentliggörande**](#disclosure-assessment): En process för att avgöra vilken typ av avslöjande som rekommenderas för din syntetiska röstupplevelse

2. [**Så här avslöjar du**](concepts-disclosure-patterns.md): Exempel på avslöjandemönster som kan tillämpas på din syntetiska röstupplevelse

3. [**När du ska avslöja**](concepts-disclosure-patterns.md#when-to-disclose): Optimala stunder att avslöja under hela användarresan

## <a name="disclosure-assessment"></a>Bedömning av offentliggörande
Tänk dina användare&#39; förväntningar om en interaktion och det sammanhang i vilket de kommer att uppleva rösten. Om sammanhanget klargör att en syntetisk &quot;röst&quot; talar, kan utlämnandet vara minimalt, tillfälligt eller till och med onödigt. De huvudtyper av sammanhang som påverkar utlämnandet är persona-typ, scenariotyp och exponeringsnivå. Det hjälper också att överväga vem som kanske lyssnar.

### <a name="understand-context"></a>Förstå sammanhang

Använd det här kalkylbladet för att bestämma kontexten för din syntetiska röstupplevelse. Du kommer att tillämpa detta i nästa steg där du bestämmer din informationsnivå.

|                                    | Användningskontext                                                                                                                                                                                                                                                                                                                                                       | Potentiella risker & utmaningar                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Persona typ**               | **Om något av följande gäller passar din persona under kategorin "Människoliknande Persona":**<br><br><ul><li> Persona förkroppsligar en riktig människa oavsett om det är en fiktiv representation eller inte. (t.ex. fotografi eller datorgenererad återgivning av en verklig person)<br><br><li> Den syntetiska rösten är baserad på en röst för en allmänt igenkännlig verklig person (t.ex. kändis, politisk figur) | Ju mer människoliknande representationer du ger din persona, desto mer sannolikt en användare kommer att associera den med en verklig person, eller få dem att tro att innehållet talas av en verklig person snarare än datorgenererade. </ul>                                                                                                                                                                      |
| **2. Typ av scenario**            | **Om något av följande gäller passar din röstupplevelse under kategorin "Känslig":**<br><br><ul><li> Erhåller eller visar personlig information från användaren <br><br> <li> Sänder tidskänsliga nyheter/information (t.ex. nödvarning)<br><br><li> Syftar till att hjälpa verkliga människor att kommunicera med varandra (t.ex. läser personliga e-postmeddelanden / texter)<br><br> <li> Ger medicinsk / hälsohjälp </ul>            | Användningen av syntetisk röst kanske inte känns lämpligt eller pålitligt för de människor som använder den när ämnen är relaterade till känsliga, personliga eller brådskande frågor. De kan också förvänta sig samma nivå av empati och kontextuell medvetenhet som en verklig människa. |
| **3. Exponeringsnivå** |**Din röstupplevelse passar troligen under kategorin "Hög" om:** <br><br><ul><li>Användaren kommer att höra eller interagera med den syntetiska rösten ofta eller under en längre tid </ul>                                                                                                                                                                             | Vikten av att vara transparent och bygga förtroende med användarna är ännu högre när man etablerar långsiktiga relationer.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Bestäm informationsnivå

Använd följande diagram för att avgöra om din syntetiska röstupplevelse kräver hög eller låg information baserat på ditt användningskontext.

  ![Diagram över bedömning av offentliggörande](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Referensdokument

* [Avslöjande för Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Riktlinjer för ansvarsfull användning av syntetisk röstteknik](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating översikt](concepts-gating-overview.md)

## <a name="next-steps"></a>Nästa steg

* [Utlämnande av designmönster](concepts-disclosure-patterns.md)
