---
title: Rensa och förbereda data för Azure Machine Learning | Microsoft Docs
description: Förbearbeta och rensa data för att förbereda den för machine learning.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: bdf659ec-4881-4324-8b9c-747cbfa0c3cd
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 127c51b9a2617c6b8520d972a3cd4b6c3bbcddd1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837702"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Uppgifter för att förbereda data för förbättrad Machine Learning
Förbearbetning och rensa data är viktiga uppgifter som vanligtvis utföras innan dataset kan användas effektivt för machine learning. Rådata är ofta mycket brus och otillförlitliga och saknar värden. Med hjälp av dessa data för modellering kan ge missvisande resultat. Dessa uppgifter är en del av Team Data vetenskap processen (TDSP) och följ vanligtvis en inledande undersökning av en datamängd som används för att identifiera och planera före bearbetning som krävs. Mer detaljerad information om processen TDSP finns de steg som beskrivs i den [Team datavetenskap Process](overview.md).

Föregående bearbetning och rensa aktiviteter som aktiviteten data från kartläggning av naturresurser kan utföras i en mängd olika miljöer, till exempel SQL- eller Hive- eller Azure Machine Learning Studio och med olika verktyg och språk som R eller Python, beroende på om dina data lagras och hur den har formaterats. Eftersom TDSP iterativ karaktär, kan dessa uppgifter ske på flera steg i arbetsflödet för processen.

Den här artikeln introducerar olika databearbetning begrepp och uppgifter som kan utföras före eller efter att mata in data i Azure Machine Learning.

Ett exempel på datagranskning och före som bearbetas i Azure Machine Learning studio finns i [förbearbetning av data i Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) video.

## <a name="why-pre-process-and-clean-data"></a>Varför Förbearbeta och rensa data?
Verkligt data har samlats in från olika källor och processer och den kan innehålla oegentligheter eller skadade data genom att kompromettera kvaliteten på datamängden. Vanliga data quality problem som uppstår är:

* **Ofullständig**: Data saknar attribut eller som innehåller värden som saknas.
* **Störningar**: Data innehåller felaktiga poster eller avvikare.
* **Inkonsekvent**: Data innehåller poster i konflikt eller avvikelser.

Data kvalitet är en förutsättning för kvalitet förutsägelsemodeller. Om du vill undvika ”skräp i skräp ut” och förbättra kvalitet och därför modellen prestanda, är det viktigt att utföra en data hälsa skärm för att upptäcka problem tidigt och fatta beslut om motsvarande databehandling och rengöringsband steg.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Vilka är några vanliga hälsa skärmar som används?
Vi kan kontrollera allmänna kvaliteten data genom att kontrollera:

* Antalet **poster**.
* Antalet **attribut** (eller **funktioner**).
* Attributet **datatyper** (nominell, ordningstal eller kontinuerlig).
* Antalet **saknade värden**.
* **-Format** av data.
  * Kontrollera att kolumnen avgränsare och rad avgränsare alltid korrekt separata kolumner och rader om data är i TVS eller CSV.
  * Om data är i HTML- eller XML-format, kontrollerar du om informationen är korrekt formaterad baserat på deras respektive standarder.
  * Parsning kan också vara nödvändigt för att kunna extrahera strukturerad information från delvis strukturerade eller Ostrukturerade data.
* **Inkonsekvent dataposter**. Kontrollera att intervallet för värden är tillåtna. t.ex. Om data innehåller student GPA, kontrollera om GPA är i det angivna intervallet, säger 0 ~ 4.

När du upptäcker problem med data, **bearbetningssteg** finns behov som ofta omfattar rensa värden som saknas, databasnormalisering, discretization, text-bearbetning för att ta bort och/eller ersätta inbäddade tecken som kan påverka datajustering, blandat datatyperna gemensamma fält och andra.

**Azure Machine Learning förbrukar korrekt tabelldata**.  Om data redan finns i tabellform kan före databehandling utföras direkt med Azure Machine Learning i Machine Learning Studio.  Om data inte är i tabellform, XML Säg, kan parsning krävas för att konvertera data till tabellform.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Vilka är några av de viktiga uppgifterna i före databearbetning?
* **Datarensning**: Fyll i eller saknade värden, identifiera och ta bort störningar data och avvikare.
* **DTS**: normalisera data för att minska dimensioner och brus.
* **Data minskning**: exempel dataposter eller attribut för enklare datahantering av.
* **Data discretization**: konvertera kontinuerliga attribut till kategoriska attribut för användarvänlighet med vissa machine learning-metoder.
* **Rensa text**: ta bort inbäddade tecken som kan orsaka data feljusteringarna för till exempel inbäddade flikar i en datafil med tabbavgränsade, inbäddade nya rader som kan bryta poster, osv.

I avsnitten nedan finns information om några av de här stegen för databearbetning.

## <a name="how-to-deal-with-missing-values"></a>Hur du arbetar med värden som saknas?
Om du vill hantera värden som saknas, är det bäst att först identifiera orsaken till värden som saknas för att bättre hantera problemet. Vanliga saknas värdet hantering metoderna är:

* **Ta bort**: ta bort poster med värden som saknas
* **Dummy ersättning**: ersätta saknade värden med ett värde för dummy: t. ex. *okänd* för kategoriska eller 0 för numeriska värden.
* **Innebära ersättning**: om data som saknas är numeriska ersätter värden som saknas med medelvärde.
* **Frekventa ersättning**: om data som saknas är kategoriska ersätter värden som saknas med de vanligaste artikeln
* **Regression ersättning**: använda en regression metod för att ersätta värden som saknas med regressed värden.  

## <a name="how-to-normalize-data"></a>Hur normalisera data?
Databasnormalisering skalas igen numeriska värden till ett visst intervall. Populära databasnormalisering metoderna är:

* **Min Max normalisering**: linjärt Transformera data till ett intervall, säg mellan 0 och 1, där det lägsta värdet skalas till 0 och högsta värdet till 1.
* **Z-score normalisering**: skala data baserat på medelvärdet och standardavvikelsen: dela skillnaden mellan data och medelvärdet av standardavvikelsen.
* **Decimal skalning**: skala data genom att flytta decimaltecknet attributvärdet.  

## <a name="how-to-discretize-data"></a>Hur ska användas vid diskretisering data?
Data kan vara diskretisera genom att konvertera kontinuerlig värden till nominell attribut eller intervall. Det finns några olika sätt att göra detta:

* **Lika breda Diskretisering**: delar intervallet för alla möjliga värden för ett attribut i N grupper av samma storlek och tilldela värden som ligger i en bin med bin nummer.
* **Lika höjd Diskretisering**: dela intervallet för alla möjliga värden för ett attribut i N grupper med samma antal instanser, och sedan tilldela värden som ligger i en bin med bin nummer.  

## <a name="how-to-reduce-data"></a>Hur du minskar data?
Det finns olika metoder för att minska storleken på data för enklare hantering. Följande metoder kan användas beroende på datastorleken och domänen:

* **Registrera provtagning**: exempel dataposter och välj endast representativ underuppsättning data.
* **Attributet provtagning**: Välj endast en delmängd av de viktigaste attribut från data.  
* **Aggregeringen**: dela in data i grupper och lagra siffror för varje grupp. Dagliga intäkter talen i en restaurang kedja under de senaste 20 åren kan till exempel aggregeras månatliga inkomster att minska storleken på data.  

## <a name="how-to-clean-text-data"></a>Så här rengör textdata?
**Textfält i tabelldata** kan innehålla tecken som påverkar kolumner justering och/eller post gränser. För t.ex. inbäddade flikar i en fil med tabbavgränsade orsak kolumnen feljusteringarna och inbäddade bryta tecken för ny rad poster rader. Felaktig text kodning hantering vid skrivning/läsning text leder till förlust av information, oavsiktligt introduktion oläsliga tecken, t.ex. null-värden och kan även påverka text parsning. Noggrann parsning och redigera kan krävas för att rensa textfält för rätt justering och/eller extrahera strukturerade data från text Ostrukturerade och halvstrukturerade data.

**Datagranskning** ger en tidig vy i data. Ett antal problem kan vara inte är öppnade avslöjar under det här steget och motsvarande metoder kan användas för att lösa dessa problem.  Det är viktigt att ställa frågor, till exempel vad som är källan till problemet och hur problemet kan har införts. Hjälper dig också att besluta om databearbetning steg som måste åtgärdas för att lösa dem. Vilken typ av insights som en har för avsikt att härleda från data kan också användas för att prioritera databearbetning arbete.

## <a name="references"></a>Referenser
> *Datautvinning: Koncept och tekniker*, tredje utgåvan Morgan Kaufmann 2011 Jiawei Han och Micheline Kamber Jian Pei
> 
> 

