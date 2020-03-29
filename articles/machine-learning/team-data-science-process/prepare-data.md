---
title: Prep-data för ML Studio (klassisk) - Team Data Science Process
description: Förbehandla och rensa data för att förbereda den så att den används effektivt för maskininlärning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720052"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Uppgifter för att förbereda data för förbättrad Machine Learning
Förbehandling och rengöring av data är viktiga uppgifter som måste utföras innan en datauppsättning kan användas för modellutbildning. Rådata är ofta bullriga och otillförlitliga och kan saknas värden. Att använda sådana data för modellering kan ge vilseledande resultat. Dessa uppgifter är en del av Team Data Science Process (TDSP) och följer vanligtvis en första utforskning av en datauppsättning som används för att identifiera och planera den förbehandling som krävs. Mer detaljerade instruktioner om TDSP-processen finns i stegen i [teamdatavetenskapsprocessen](overview.md).

Förbearbetnings- och rengöringsuppgifter, som datautforskningsuppgiften, kan utföras i en mängd olika miljöer, till exempel SQL eller Hive eller Azure Machine Learning Studio (klassisk), och med olika verktyg och språk, till exempel R eller Python, beroende på var dina data lagras och hur den är formaterad. Eftersom TDSP är iterativ karaktär, kan dessa uppgifter ske vid olika steg i arbetsflödet för processen.

Den här artikeln introducerar olika databehandlingskoncept och uppgifter som kan utföras antingen före eller efter intag av data i Azure Machine Learning Studio (klassisk).

Ett exempel på datautforskning och förbearbetning som görs i [Pre-processing data](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) Azure Machine Learning Studio (klassisk) finns i förbearbetningsdatavideon.

## <a name="why-pre-process-and-clean-data"></a>Varför förbehandla och rensa data?
Verkliga data samlas in från olika källor och processer och det kan innehålla oegentligheter eller korrupta data som äventyrar kvaliteten på datauppsättningen. De typiska datakvalitetsproblem som uppstår är:

* **Ofullständig**: Data saknar attribut eller innehåller värden som saknas.
* **Noisy**: Data innehåller felaktiga poster eller extremvärden.
* **Inkonsekvent**: Data innehåller motstridiga poster eller avvikelser.

Kvalitetsdata är en förutsättning för kvalitetsförutsägande modeller. För att undvika "skräp i, skräp ut" och förbättra datakvalitet och därmed modellprestanda, är det absolut nödvändigt att genomföra en datahälsa skärm för att upptäcka dataproblem tidigt och besluta om motsvarande databehandling och rengöring steg.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Vilka är några typiska data hälsa skärmar som används?
Vi kan kontrollera den allmänna kvaliteten på data genom att kontrollera:

* Antalet **poster**.
* Antalet **attribut** (eller **funktioner**).
* **Attributdatatyperna** (nominell, ordningsvis eller kontinuerlig).
* Antalet **värden som saknas**.
* **Välformade** data.
  * Om data finns i TSV eller CSV kontrollerar du att kolumnavgränsare och radavgränsare alltid är korrekt separata kolumner och rader.
  * Om data är i HTML- eller XML-format kontrollerar du om data är välformade baserat på deras respektive standarder.
  * Tolkning kan också vara nödvändig för att extrahera strukturerad information från halvstrukturerade eller ostrukturerade data.
* **Inkonsekventa dataposter**. Kontrollera att det är tillåtet att kontrollera intervallet med värden. Om data till exempel innehåller student-GPA (genomsnitt för resultatpunkt) kontrollerar du om GPA ligger i det angivna intervallet, säg 0~4.

När du hittar problem med data krävs **bearbetningssteg,** vilket ofta innebär rengöring av saknade värden, datanormalisering, diskretisering, textbearbetning för att ta bort och/eller ersätta inbäddade tecken som kan påverka datajustering, blandade datatyper i vanliga fält och andra.

**Azure Machine Learning använder välformade tabelldata**.  Om data redan är i tabellform kan förbearbetning av data utföras direkt med Azure Machine Learning Studio (klassisk) i Machine Learning.  Om data inte är i tabellform, säg att det är i XML, kan tolkning krävas för att konvertera data till tabellform.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Vilka är några av de viktigaste uppgifterna i förbehandling av data?
* **Datarengöring**: Fyll i saknade värden, identifiera och ta bort bullriga data och extremvärden.
* **Dataomvandling**: Normalisera data för att minska dimensioner och brus.
* **Datareduktion**: Exempel på dataposter eller attribut för enklare datahantering.
* **Datadissering**: Konvertera kontinuerliga attribut till kategoriska attribut för användarvänlighet med vissa maskininlärningsmetoder.
* **Textrengöring**: ta bort inbäddade tecken som kan orsaka dataförsprång, till exempel inbäddade flikar i en tabbavgränsad datafil, inbäddade nya rader som kan slå poster, till exempel.

I avsnitten nedan beskrivs några av dessa databehandlingssteg.

## <a name="how-to-deal-with-missing-values"></a>Hur hanterar man saknade värden?
För att hantera saknade värden är det bäst att först identifiera orsaken till de värden som saknas för att bättre hantera problemet. Typiska metoder för värdehantering som saknas är:

* **Borttagning**: Ta bort poster med saknade värden
* **Dummy substitution**: Ersätt saknade värden med ett *unknown* dummy-värde: t.ex.
* **Genomsnittlig ersättning**: Om de saknade uppgifterna är numeriska, ersätt de saknade värdena med medelvärdet.
* **Frekvent substitution**: Om de saknade uppgifterna är kategoriska, ersätt de saknade värdena med det vanligaste objektet
* **Regressionsersättning**: Använd en regressionsmetod för att ersätta saknade värden med regresserade värden.  

## <a name="how-to-normalize-data"></a>Hur normaliserar man data?
Datanormalisering skalar om numeriska värden till ett angivet intervall. Populära data normaliseringsmetoder inkluderar:

* **Min-Max Normalisering**: Omvandla data linjärt till ett intervall, till exempel mellan 0 och 1, där minvärdet skalas till 0 och maxvärde till 1.
* **Z-poängs normalisering**: Skala data baserat på medelvärde och standardavvikelse: dela skillnaden mellan data och medelvärdet med standardavvikelsen.
* **Decimalskalning**: Skala data genom att flytta decimaltecknet för attributvärdet.  

## <a name="how-to-discretize-data"></a>Hur diskreta data?
Data kan diskretiseras genom att konvertera kontinuerliga värden till nominella attribut eller intervall. Några sätt att göra detta är:

* **Lagerplats för lika bredd:** Dela upp intervallet för alla möjliga värden för ett attribut i N-grupper av samma storlek och tilldela de värden som hamnar på en lagerplats med lagerplatsnumret.
* **Lagerplats för lika höjd:** Dela upp intervallet för alla möjliga värden för ett attribut i N-grupper, var och en med samma antal instanser, och tilldela sedan de värden som hamnar på en lagerplats med lagerplatsnumret.  

## <a name="how-to-reduce-data"></a>Hur kan man minska data?
Det finns olika metoder för att minska datastorleken för enklare datahantering. Beroende på datastorlek och domän kan följande metoder användas:

* **Postprovtagning:** Ta prov på dataposterna och välj endast den representativa delmängden från data.
* **Attributs sampling:** Välj bara en delmängd av de viktigaste attributen från data.  
* **Aggregering**: Dela upp data i grupper och lagra siffrorna för varje grupp. Till exempel kan det dagliga intäktsantalet för en restaurangkedja under de senaste 20 åren aggregeras till månatliga intäkter för att minska storleken på data.  

## <a name="how-to-clean-text-data"></a>Hur rensar man textdata?
**Textfält i tabelldata** kan innehålla tecken som påverkar kolumnjustering och/eller postgränser. Inbäddade flikar i en flikseparerad fil orsakar till exempel kolumnförpassning och inbäddade nya radtecken bryter postlinjer. Felaktig textkodningshantering när du skriver eller läser text leder till informationsförlust, oavsiktlig introduktion av oläsliga tecken (som null) och kan också påverka texttolkning. Noggrann tolkning och redigering kan krävas för att rensa textfält för korrekt justering och/eller för att extrahera strukturerade data från ostrukturerade eller halvstrukturerade textdata.

**Datautforskning** ger en tidig bild av data. Ett antal dataproblem kan upptäckas under det här steget och motsvarande metoder kan användas för att lösa dessa problem.  Det är viktigt att ställa frågor som vad som är orsaken till frågan och hur frågan kan ha införts. Den här processen hjälper dig också att bestämma vilka åtgärder för databearbetning som måste vidtas för att lösa dem. Identifiera de slutliga användningsfallen och personas kan också användas för att prioritera databehandlingsarbetet.

## <a name="references"></a>Referenser
> *Data Mining: Begrepp och tekniker*, Tredje upplagan, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber, och Jian Pei
> 
> 

