---
title: Rensa och förbereda data för Azure Machine Learning | Microsoft Docs
description: Förbearbeta och rensa data för att förbereda den för machine learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 52457e19cede5d8d2b74d9c3d81ebf35e3ad06c4
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446568"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Uppgifter för att förbereda data för förbättrad Machine Learning
Bearbeta data i förväg och rensa data är viktiga uppgifter som normalt måste utföras innan datauppsättning kan användas effektivt för machine learning. Rådata är ofta bort störande och otillförlitliga och kan sakna värden. Med hjälp av sådana data för modellering kan ge vilseledande resultat. Dessa uppgifter är en del av Team Data Science Process (TDSP) och följ vanligtvis en inledande undersökning av en datauppsättning som används för att identifiera och planera förbearbetning som krävs. Mer detaljerad information om TDSP-processen, se de steg som beskrivs i den [Team Data Science Process](overview.md).

Bearbeta data i förväg och rensning av aktiviteter, till exempel aktiviteten data utforskning kan utföras i en mängd olika miljöer, till exempel SQL, Hive eller Azure Machine Learning Studio, och med olika verktyg och språk som R eller Python, beroende på dina data ska lagras och hur den formateras. Eftersom TDSP är iterativ sin natur, kan dessa uppgifter utföras på olika steg i arbetsflödet av processen.

Den här artikeln beskrivs olika databearbetning begrepp och aktiviteter som kan genomföras antingen före eller efter att mata in data i Azure Machine Learning.

Ett exempel på datagranskning och bearbeta data i förväg utföras inuti Azure Machine Learning studio finns i den [förbearbetning av data i Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) video.

## <a name="why-pre-process-and-clean-data"></a>Varför Förbearbeta och rensa data?
Verkliga data har samlats in från olika källor och processer och den kan innehålla oegentligheter eller skadade data att kompromissa med kvaliteten på datauppsättningen. De typiska data kvalitetsproblem som kan uppstå är:

* **Ofullständig**: Data saknar attribut eller som innehåller värden som saknas.
* **Bort störande**: Data innehåller felaktiga poster eller extremvärden.
* **Inkonsekvent**: Data innehåller poster i konflikt eller avvikelser.

Kvalitet data är en förutsättning för kvalitet förutsägelsemodeller. Om du vill undvika ”skräpinsamling i skräpinsamling ut” och förbättra datakvaliteten och därför modellera prestanda, är det viktigt att utföra en dataskärm hälsotillstånd för att upptäcka problem med tidigt och välja motsvarande databehandlingen och rengöringsband steg.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Vilka är några vanliga data hälsotillstånd skärmarna som används?
Vi kan kontrollera allmänna kvaliteten på data genom att kontrollera:

* Antalet **poster**.
* Antalet **attribut** (eller **funktioner**).
* Attributet **datatyper** (liten, ordningstal eller kontinuerlig).
* Antalet **saknade värden**.
* **Bra-format** av data.
  * Om data i TSV- eller CSV kontrollerar du att kolumnen avgränsare och avgränsare för rad alltid korrekt avgränsa kolumner och rader.
  * Om data är i HTML eller XML-format, måste du kontrollera om data är felfritt baserat på deras respektive standarder.
  * Parsning kan också vara nödvändigt för att kunna extrahera strukturerad information från delvis strukturerade eller Ostrukturerade data.
* **Inkonsekvent dataposter**. Kontrollera värdeintervallet tillåts. t.ex. Om data som innehåller student GPA, kontrollerar du om GPA ligger inom det angivna intervallet säger 0 ~ 4.

När du har hittat problem med data, **bearbetningssteg** behov som innebär ofta att rensa värden som saknas, databasnormalisering, discretization, text-bearbetning för att ta bort och/eller Ersätt inbäddade tecken som kan påverka data justering, blandade datatyper i vanliga, och andra fält.

**Azure Machine Learning förbrukar välformulerad tabelldata**.  Om data är redan i tabellform, kan förbearbetning av data utföras direkt med Azure Machine Learning i Machine Learning Studio.  Om data inte är i tabellform, säg XML, kan parsning krävas för att konvertera data till tabellformat.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Vilka är några av de viktigaste uppgifterna i förbearbetning av data?
* **Datarensning**: Fyll i eller saknade värden, identifiera och ta bort bort störande data och extremvärden.
* **Dataomvandling**: normalisera data för att minska dimensioner och bruset.
* **Dataminskning**: exempel på dataposter eller attribut för enklare datahantering.
* **Data discretization**: konvertera kontinuerliga attribut till kategoriska attribut för enkel användning med vissa machine learning-metoder.
* **Rensning av text**: ta bort inbäddad tecken som kan orsaka data misspassning, t.ex. inbäddade flikar i en datafil med tabbavgränsade inbäddade nya rader som kan bryta poster, osv.

I avsnitten nedan finns information om några av de här stegen för databearbetning.

## <a name="how-to-deal-with-missing-values"></a>Hur du arbetar med värden som saknas?
Om du vill hantera värden som saknas, är det bäst att först identifiera orsaken till värdena som saknas för att bättre hantera problemet. Vanliga saknas värdet hantering av metoderna är:

* **Borttagning av**: ta bort poster med värden som saknas
* **Dummy ersättningen**: Ersätt värden som saknas med ett värde för dummy: till exempel, *okänd* för kategoriska eller 0 för numeriska värden.
* **Innebära ersättningen**: om data som saknas är numeriska ersätter värden som saknas med medelvärde.
* **Frekventa ersättningen**: om data som saknas är kategoriska ersätter värden som saknas med det vanligaste objektet
* **Regression ersättningen**: använda en regression metod för att ersätta saknade värden med försämrad värden.  

## <a name="how-to-normalize-data"></a>Så här att normalisera data?
Databasnormalisering skalar igen numeriska värden till ett visst intervall. Populära normalisering metoderna är:

* **Min – Max normalisering**: linjärt omvandla data till ett intervall, säger mellan 0 och 1, där det lägsta värdet skalas till 0 eller det största värdet till 1.
* **Z-score normalisering**: skala data baserat på medelvärde och standardavvikelse: dela skillnaden mellan data och medelvärdet av standardavvikelsen.
* **Decimal skalning**: skala data genom att flytta decimaltecknet attributvärdet.  

## <a name="how-to-discretize-data"></a>Hur ska användas vid diskretisering data?
Data kan att diskretisera genom att konvertera kontinuerlig värden till nominell attribut eller intervall. Några sätt att göra detta på är:

* **Lika breda Datagruppering**: dela intervallen med alla möjliga värden för ett attribut i N-grupper av samma storlek och tilldela värden som faller på en lagerplats med bin-nummer.
* **Med samma höjd Datagruppering**: dela intervallen med alla möjliga värden för ett attribut i N-grupper som innehåller samma antal instanser och sedan tilldela värden som faller på en lagerplats med bin-nummer.  

## <a name="how-to-reduce-data"></a>Hur du minskar data?
Det finns olika metoder för att minska storleken på data för enklare datahantering. Beroende på datastorlek och domänen, kan du använda följande metoder:

* **Registrera Sampling**: exempel på dataposterna och bara välja representativa delmängden från data.
* **Attributet Sampling**: Välj endast en delmängd av de viktigaste attribut från data.  
* **Aggregering**: dela upp data i grupper och lagra nummer för varje grupp. Dagliga intäkter numren på en restaurangkedja under de senaste 20 åren kan till exempel avgifter för månatlig intäkter att minska storleken på data.  

## <a name="how-to-clean-text-data"></a>Hur du rensar textdata?
**Textfält i tabelldata** får innehålla tecken som påverkar kolumner justering och/eller post gränser. För t.ex. inbäddade flikar i en fil med tabbavgränsade orsak kolumnen misspassning och inbäddade bryter tecken för ny rad poster rader. Olämplig text kodning hantering vid skrivning/läsning text leder till förlust av information, oavsiktligt introduktionen av oläsliga tecken, t.ex. null-värden, och kan också påverkar text parsning. Noggrann parsning och redigera kan krävas för att rensa textfält för rätt justering och/eller extrahera strukturerad data från för Ostrukturerade och halvstrukturerade textdata.

**Datagranskning** erbjuder en tidig vy över data. Ett antal problem med kan vara utan åtgärd under det här steget och motsvarande metoder kan användas för att åtgärda dessa problem.  Det är viktigt att ställa frågor, till exempel vad som är orsaken till problemet och hur problemet kan har införts. Hjälper dig också att besluta om databearbetning steg som måste åtgärdas för att lösa dem. Vilken typ av insikter som någon har för avsikt att härleda från data kan också användas för att prioritera enklare för databearbetning.

## <a name="references"></a>Referenser
> *Datautvinning: Koncept och tekniker*, tredje utgåvan, Morgan Kaufmann 2011 Jiawei Han och Micheline Kamber Jian Pei
> 
> 

