---
title: Datainsamling och förståelse för Team Data Science Process
description: Mål, uppgifter och resultat för datainsamling och förståelse skede av dina datavetenskapliga projekt
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
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720511"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Datainsamling och förståelse skede av Team Data Science Process

I den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med datainsamlings- och förståelsesfasen av Team Data Science Process (TDSP). Den här processen innehåller en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapsprojekt. Livscykeln beskriver de stora faserna som projekt vanligtvis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP livscykel: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Skapa en ren datauppsättning av hög kvalitet vars relation till målvariablerna förstås. Leta reda på datauppsättningen i rätt analysmiljö så att du är redo att modellera.
* Utveckla en lösningsarkitektur för datapipelinen som uppdaterar och poängar data regelbundet.

## <a name="how-to-do-it"></a>Hur man gör det
Det finns tre huvuduppgifter som tas upp i detta skede:

   * **Inta data** i målalytiska miljön.
   * **Utforska data** för att avgöra om datakvaliteten är tillräcklig för att besvara frågan. 
   * **Konfigurera en datapipeline** för att få nya eller regelbundet uppdaterade data.

### <a name="ingest-the-data"></a>Inta data
Ställ in processen för att flytta data från källplatserna till de målplatser där du kör analysåtgärder, till exempel utbildning och förutsägelser. Teknisk information och alternativ för hur du flyttar data med olika Azure-datatjänster finns [i Läsa in data i lagringsmiljöer för analys](ingest-data.md). 

### <a name="explore-the-data"></a>Utforska data
Innan du tränar dina modeller måste du utveckla en god förståelse för data. Verkliga datauppsättningar är ofta bullriga, saknar värden eller har en mängd andra avvikelser. Du kan använda datasumrisering och visualisering för att granska kvaliteten på dina data och ange den information du behöver för att bearbeta data innan de är klara för modellering. Denna process är ofta iterativ.

TDSP tillhandahåller ett automatiserat verktyg, kallat [IDEAR,](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)för att visualisera data och förbereda datasammanfattningsrapporter. Vi rekommenderar att du börjar med IDEAR först för att utforska data för att hjälpa till att utveckla inledande data förståelse interaktivt utan kodning. Sedan kan du skriva anpassad kod för datautforskning och visualisering. Vägledning om hur du rensar data finns i [Uppgifter för att förbereda data för förbättrad maskininlärning](prepare-data.md).  

När du är nöjd med kvaliteten på rengjorda data är nästa steg att bättre förstå de mönster som är inneboende i data. Den här dataanalysen hjälper dig att välja och utveckla en lämplig prediktiv modell för ditt mål. Leta efter bevis för hur väl anslutna data är till målet. Ta sedan reda på om det finns tillräckligt med data för att gå vidare med nästa modelleringssteg. Återigen är denna process ofta iterativ. Du kan behöva hitta nya datakällor med mer exakta eller mer relevanta data för att öka den datauppsättning som ursprungligen identifierades i föregående steg. 

### <a name="set-up-a-data-pipeline"></a>Konfigurera en datapipeline
Förutom det första inmatnings- och rengöringen av data måste du vanligtvis ställa in en process för att få nya data eller uppdatera data regelbundet som en del av en pågående inlärningsprocess. Poängsättning kan slutföras med en datapipeline eller ett arbetsflöde. I artikeln [Flytta data från en lokal SQL Server-instans till Azure SQL Database med Azure Data Factory](move-sql-azure-adf.md) visas ett exempel på hur du konfigurerar en pipeline med Azure Data [Factory](https://azure.microsoft.com/services/data-factory/). 

I det här steget utvecklar du en lösningsarkitektur för datapipelinen. Du utvecklar pipelinen parallellt med nästa steg i datavetenskapsprojektet. Beroende på dina affärsbehov och begränsningarna i dina befintliga system som den här lösningen integreras i kan pipelinen vara något av följande alternativ: 

   * Batch-baserade
   * Streaming eller realtid 
   * En hybrid 

## <a name="artifacts"></a>Artefakter
Följande är slutprodukterna i detta skede:

   * [Datakvalitetsrapport:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md)Den här rapporten innehåller datasammanfattningar, relationerna mellan varje attribut och mål, variabel rangordning med mera. [IDEAR-verktyget](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) som tillhandahålls som en del av TDSP kan snabbt generera den här rapporten på alla tabelldatauppsättningar, till exempel en CSV-fil eller en relationstabell. 
   * **Lösningsarkitektur**: Lösningsarkitekturen kan vara ett diagram eller en beskrivning av datapipelinen som du använder för att köra bedömning eller förutsägelser på nya data när du har skapat en modell. Den innehåller också pipelinen för att omskola din modell baserat på nya data. Lagra dokumentet i [projektkatalogen](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) när du använder katalogstrukturmallen TDSP.
   * **Checkpoint beslut:** Innan du börjar full-funktionen teknik och modell byggnad, kan du omvärdera projektet för att avgöra om det förväntade värdet är tillräckligt för att fortsätta att driva den. Du kan till exempel vara redo att fortsätta, behöva samla in mer data eller överge projektet eftersom data inte finns för att besvara frågan.

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genomgångar som visar alla steg i processen för specifika scenarier. I artikeln [Exempelgenomgångar](walkthroughs.md) finns en lista över scenarier med länkar och miniatyrbeskrivningar. Genomgångarna illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [Använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
