---
title: Förbered data för ML Studio (klassisk) – team data science process
description: Förbearbeta och rensa data för att förbereda den för att användas effektivt för machine learning.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720052"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Uppgifter för att förbereda data för förbättrad Machine Learning
För bearbetning och rensning av data är viktiga uppgifter som måste utföras innan en data uppsättning kan användas för modell träning. Rådata är ofta bort störande och otillförlitliga och kan sakna värden. Med hjälp av sådana data för modellering kan ge vilseledande resultat. Dessa uppgifter är en del av Team Data Science Process (TDSP) och följ vanligtvis en inledande undersökning av en datauppsättning som används för att identifiera och planera förbearbetning som krävs. Mer detaljerad information om TDSP-processen finns i stegen som beskrivs i [team data science-processen](overview.md).

För bearbetnings-och rengörings aktiviteter, t. ex. data utforsknings uppgift, kan utföras i en mängd olika miljöer, till exempel SQL eller Hive eller Azure Machine Learning Studio (klassisk) och med olika verktyg och språk, till exempel R eller python, beroende på var dina data finns lagras och hur den formateras. Eftersom TDSP är iterativ sin natur, kan dessa uppgifter utföras på olika steg i arbetsflödet av processen.

I den här artikeln beskrivs olika data bearbetnings koncept och uppgifter som kan utföras antingen före eller efter att data matas in i Azure Machine Learning Studio (klassisk).

Ett exempel på data utforskning och för bearbetning som utförs i Azure Machine Learning Studio (klassisk) finns i videon om [för bearbetning av data](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Varför Förbearbeta och rensa data?
Verkliga data har samlats in från olika källor och processer och den kan innehålla oegentligheter eller skadade data att kompromissa med kvaliteten på datauppsättningen. De typiska data kvalitetsproblem som kan uppstå är:

* **Ofullständig**: data saknar attribut eller innehåller värden som saknas.
* **Brus**: data innehåller felaktiga poster eller avvikare.
* **Inkonsekvent**: data innehåller motstridiga poster eller avvikelser.

Kvalitet data är en förutsättning för kvalitet förutsägelsemodeller. Om du vill undvika ”skräpinsamling i skräpinsamling ut” och förbättra datakvaliteten och därför modellera prestanda, är det viktigt att utföra en dataskärm hälsotillstånd för att upptäcka problem med tidigt och välja motsvarande databehandlingen och rengöringsband steg.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Vilka är några vanliga data hälsotillstånd skärmarna som används?
Vi kan kontrollera allmänna kvaliteten på data genom att kontrollera:

* Antalet **poster**.
* Antalet **attribut** (eller **funktioner**).
* Attributets **data typer** (nominellt, ordnings tal eller kontinuerliga).
* Antalet **saknade värden**.
* **Välformulerade** data.
  * Om data i TSV- eller CSV kontrollerar du att kolumnen avgränsare och avgränsare för rad alltid korrekt avgränsa kolumner och rader.
  * Om data är i HTML eller XML-format, måste du kontrollera om data är felfritt baserat på deras respektive standarder.
  * Parsning kan också vara nödvändigt för att kunna extrahera strukturerad information från delvis strukturerade eller Ostrukturerade data.
* **Inkonsekventa data poster**. Kontrollera värdeintervallet tillåts. Om t. ex. data innehåller student BETYGs klass (genomsnitt) kontrollerar du om GPA är inom det angivna intervallet, ungefär 0 ~ 4.

När du hittar problem med data är **bearbetnings stegen** nödvändiga, vilket ofta inbegriper rensning av saknade värden, data normalisering, diskretiseringsmetoden stämmer, text bearbetning för att ta bort och/eller ersätta inbäddade tecken som kan påverka data justeringen, blandade data typer i gemensamma fält och andra.

**Azure Machine Learning använder välformulerade tabell data**.  Om data redan finns i tabell form kan data för bearbetning utföras direkt med Azure Machine Learning Studio (klassisk) i Machine Learning.  Om data inte är i tabellform, säg XML, kan parsning krävas för att konvertera data till tabellformat.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Vilka är några av de viktigaste uppgifterna i förbearbetning av data?
* **Data rensning**: Fyll i saknade värden, identifiera och ta bort data och avvikande störningar.
* **Data omvandling**: normalisera data för att minska dimensioner och brus.
* **Data reducering**: exempel på data poster eller attribut för enklare data hantering.
* **Data diskretiseringsmetoden stämmer**: konvertera kontinuerliga attribut till kategoriska-attribut för enkel användning med vissa Machine Learning-metoder.
* **Text rensning**: ta bort inbäddade tecken som kan orsaka data fel justering, till exempel inbäddade flikar i en tabbavgränsade datafil, inbäddade nya rader som kan bryta poster, till exempel.

I avsnitten nedan finns information om några av de här stegen för databearbetning.

## <a name="how-to-deal-with-missing-values"></a>Hur du arbetar med värden som saknas?
Om du vill hantera värden som saknas, är det bäst att först identifiera orsaken till värdena som saknas för att bättre hantera problemet. Vanliga saknas värdet hantering av metoderna är:

* **Borttagning**: ta bort poster med saknade värden
* **Dummy-ersättning**: Ersätt saknade värden med ett dummy-värde: t. ex. *okänd* för kategoriska eller 0 för numeriska värden.
* **Medel ersättning**: om data som saknas är numeriska ersätter du värdena som saknas med medelvärdet.
* **Frekvent ersättning**: om data som saknas är kategoriska ersätter du de värden som saknas med det vanligaste objektet
* **Regressions ersättning**: Använd en Regressions metod för att ersätta saknade värden med försämrat-värden.  

## <a name="how-to-normalize-data"></a>Så här att normalisera data?
Data normalisering skalar om numeriska värden till ett visst intervall. Populära normalisering metoderna är:

* **Minsta-Max normalisering**: linjärt transformera data till ett intervall, till exempel mellan 0 och 1, där det lägsta värdet skalas till 0 och Max värdet till 1.
* **Z-score normalisering**: skala data baserat på medelvärde och standard avvikelse: dividera skillnaden mellan data och medelvärdet med standard avvikelsen.
* **Decimal skalning**: skala data genom att flytta attributvärdets decimal tecken.  

## <a name="how-to-discretize-data"></a>Hur ska användas vid diskretisering data?
Data kan att diskretisera genom att konvertera kontinuerlig värden till nominell attribut eller intervall. Några sätt att göra detta på är:

* **Diskretisering med samma bredd**: dividera intervallet med alla möjliga värden för ett attribut till N grupper av samma storlek och tilldela de värden som infaller på en lager plats med lager plats numret.
* **Diskretisering med samma höjd**: dividera intervallet med alla möjliga värden för ett attribut till N grupper, där var och en innehåller samma antal instanser, och tilldela sedan de värden som infaller på en lager plats med lager plats numret.  

## <a name="how-to-reduce-data"></a>Hur du minskar data?
Det finns olika metoder för att minska storleken på data för enklare datahantering. Beroende på datastorlek och domänen, kan du använda följande metoder:

* **Post sampling**: exempel på data poster och välj den representativa del mängden från data.
* **Sampling av attribut**: Välj endast en delmängd av de viktigaste attributen från data.  
* **Aggregation**: dela in data i grupper och lagra numren för varje grupp. Dagliga intäkter numren på en restaurangkedja under de senaste 20 åren kan till exempel avgifter för månatlig intäkter att minska storleken på data.  

## <a name="how-to-clean-text-data"></a>Hur du rensar textdata?
**Textfält i tabell data** kan innehålla tecken som påverkar justering av kolumner och/eller post gränser. Till exempel är inbäddade flikar i en tabbavgränsade fil orsak till kolumn fel justering och inbäddade nya rad tecken Bryt post rader. Felaktig text kodnings hantering vid skrivning eller läsning av text leder till informations förlust, oavsiktlig introduktion av oläsliga tecken (t. ex. nullvärden) och kan också påverka text tolkning. Noggrann parsning och redigera kan krävas för att rensa textfält för rätt justering och/eller extrahera strukturerad data från för Ostrukturerade och halvstrukturerade textdata.

**Data utforskning** erbjuder en tidig vy över data. Ett antal problem med kan vara utan åtgärd under det här steget och motsvarande metoder kan användas för att åtgärda dessa problem.  Det är viktigt att ställa frågor, till exempel vad som är orsaken till problemet och hur problemet kan har införts. Den här processen hjälper dig också att bestämma de data bearbetnings steg som måste vidtas för att lösa dem. Att identifiera de slutliga användnings fallen och personer kan också användas för att prioritera data bearbetnings ansträngningen.

## <a name="references"></a>Referenser
> *Data utvinning: begrepp och tekniker*, tredje utgåvan, Morgan Kaufmann, 2011, Jiawei han, Micheline Kamber och Jian Pei
> 
> 

