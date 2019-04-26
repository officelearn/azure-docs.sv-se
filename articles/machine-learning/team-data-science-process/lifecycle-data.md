---
title: Datainsamling och förståelse för Team Data Science Process
description: Mål, uppgifter och slutprodukter för datainsamling och förstå steget i dina data science-projekt
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e29f36897dd52fcb09456768a799209a385d74fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303524"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Datainsamling och förstå steget i Team Data Science Process

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är kopplade till datainsamling och förstå steg för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Förståelse för verksamheten**
   2. **Data förvärv och förståelse av**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Generera en ren, hög kvalitet datauppsättning vars relation till target-variabler är tolkas. Leta upp datauppsättningen i lämplig analytics-miljö så att du är redo att modellen.
* Utveckla en lösningsarkitektur av din datapipeline för som uppdateras och poängsätter data regelbundet.

## <a name="how-to-do-it"></a>Gör så
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

   * **Mata in data** till analytiska målmiljön.
   * **Utforska data** att avgöra om data quality är tillräcklig för att besvara frågan. 
   * **Konfigurera en datapipeline** att poängsätta nya eller regelbundet uppdateras data.

### <a name="ingest-the-data"></a>Mata in data
Ställa in processen att flytta data från platserna som källa till målplatser där du kör analytics-åtgärder, t.ex. träning och förutsägelser. Teknisk information och alternativ för hur du flyttar data med olika Azure-datatjänster i [läser in data i lagringsmiljöer för analys](ingest-data.md). 

### <a name="explore-the-data"></a>Utforska data
Innan du träna modeller, måste du utveckla en god förståelse av data. Verkliga datauppsättningar är ofta mycket brus, saknar värden eller har en mängd andra avvikelser. Du kan använda sammanfatta data och visualisering för att granska kvaliteten på dina data och ge den information du behöver att bearbeta data innan den är redo för modellering. Den här processen är ofta iterativ.

TDSP ger ett automatiserade verktyg som kallas [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), för att visualisera data och förbereda data sammanfattningsrapporter. Vi rekommenderar att du börjar med IDEAR först för att utforska data för att utveckla ursprungliga data förstå interaktivt med ingen kodning. Du kan sedan skriva anpassad kod för datautforskning och visualisering. Anvisningar för rensning av data finns i [uppgifter för att förbereda data för förbättrad machine learning](prepare-data.md).  

När du är nöjd med kvaliteten på rensade data, är nästa steg att bättre förstå mönster som ingår i data. På så sätt kan du välja och utveckla en lämplig förutsägelsemodell för målet. Sök efter bevis för hur väl anslutna data är till målet. Sedan avgör om det finns tillräckligt med data för att gå vidare med nästa steg för modellering. Den här processen är igen, ofta iterativ. Du kan behöva hitta nya datakällor med mer exakta eller mer relevant data att utöka den datauppsättning som ursprungligen identifierades i det föregående steget. 

### <a name="set-up-a-data-pipeline"></a>Konfigurera en datapipeline
Förutom den första inmatningen och rensning av data, vanligtvis måste du ställa in en process för att rangordna nya data eller uppdatera data regelbundet som en del av en pågående learning. Du kan göra detta genom att ställa in en datapipeline eller ett arbetsflöde. Den [flytta data från en lokal SQL Server-instans till Azure SQL Database med Azure Data Factory](move-sql-azure-adf.md) artikeln innehåller ett exempel på hur du ställer in en pipeline med [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

I det här steget kan du utveckla en lösningsarkitektur av din datapipeline för. Du kan utveckla pipelinen parallellt med nästa steg i datavetenskapsprojekt. Beroende på dina affärsbehov och begränsningarna för dina befintliga system där den här lösningen integreras kan pipelinen vara något av följande: 

   * Batch-baserade
   * Streaming eller realtid 
   * En hybrid 

## <a name="artifacts"></a>Artefakter
Följande är slutprodukter i det här steget:

   * [Data quality rapporten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Den här rapporten omfattar sammanfattningar av data, relationerna mellan varje attribut och mål, variabel rangordning och mycket mer. Den [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) verktyg som tillhandahålls som en del av TDSP kan snabbt skapa den här rapporten på valfri tabular datamängd, till exempel en CSV-fil eller en relationsdatabastabell. 
   * **Lösningsarkitektur**: Lösningsarkitekturen kan vara ett diagram eller beskrivning av din pipeline för data som används för att köra bedömning eller förutsägelser på nya data när du har skapat en modell. Den innehåller också pipeline för att träna din modell som baseras på nya data. Store dokument i den [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) directory när du använder TDSP directory struktur mall.
   * **Kontrollpunkt beslut**: Innan du börjar fullständig funktionsframställning och modellskapandet omvärdera du projektet för att avgöra om det förväntade värdet är tillräckliga för att fortsätta att försöka den. Du kan till exempel vara redo att fortsätta, måste du samla in mer data eller Avbryt projektet eftersom data inte finns för att besvara frågan.

## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
