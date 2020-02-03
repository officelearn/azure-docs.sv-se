---
title: Datainsamling och förståelse för Team Data Science Process
description: Mål, uppgifter och slutprodukter för datainsamling och förstå steget i dina data science-projekt
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720511"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Datainsamling och förstå steget i Team Data Science Process

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är kopplade till datainsamling och förstå steg för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Affärs förståelse**
   2. **Data hämtning och förståelse**
   3. **Form**
   4. **Distribution**
   5. **Kund godkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Generera en ren, hög kvalitet datauppsättning vars relation till target-variabler är tolkas. Leta upp datauppsättningen i lämplig analytics-miljö så att du är redo att modellen.
* Utveckla en lösningsarkitektur av din datapipeline för som uppdateras och poängsätter data regelbundet.

## <a name="how-to-do-it"></a>Gör så
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

   * Mata in **data** i den analytiska mål miljön.
   * **Utforska data** för att avgöra om data kvaliteten är tillräcklig för att besvara frågan. 
   * **Konfigurera en datapipeline** för att skapa nya eller regelbundet uppdaterade data.

### <a name="ingest-the-data"></a>Mata in data
Ställa in processen att flytta data från platserna som källa till målplatser där du kör analytics-åtgärder, t.ex. träning och förutsägelser. För teknisk information och alternativ för att flytta data med olika Azure-datatjänster, se [Läs in data i lagrings miljöer för analys](ingest-data.md). 

### <a name="explore-the-data"></a>Utforska data
Innan du träna modeller, måste du utveckla en god förståelse av data. Verkliga datauppsättningar är ofta mycket brus, saknar värden eller har en mängd andra avvikelser. Du kan använda sammanfatta data och visualisering för att granska kvaliteten på dina data och ge den information du behöver att bearbeta data innan den är redo för modellering. Den här processen är ofta iterativ.

TDSP tillhandahåller ett automatiserat verktyg som kallas [idé](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)för att visualisera data och förbereda data sammanfattnings rapporter. Vi rekommenderar att du börjar med IDEAR först för att utforska data för att utveckla ursprungliga data förstå interaktivt med ingen kodning. Du kan sedan skriva anpassad kod för datautforskning och visualisering. Vägledning om hur du rensar data finns i [uppgifter för att förbereda data för förbättrad maskin inlärning](prepare-data.md).  

När du är nöjd med kvaliteten på rensade data, är nästa steg att bättre förstå mönster som ingår i data. Med den här data analysen kan du välja och utveckla en lämplig förutsägelse modell för målet. Sök efter bevis för hur väl anslutna data är till målet. Sedan avgör om det finns tillräckligt med data för att gå vidare med nästa steg för modellering. Den här processen är igen, ofta iterativ. Du kan behöva hitta nya datakällor med mer exakta eller mer relevant data att utöka den datauppsättning som ursprungligen identifierades i det föregående steget. 

### <a name="set-up-a-data-pipeline"></a>Konfigurera en datapipeline
Förutom den första inmatningen och rensning av data, vanligtvis måste du ställa in en process för att rangordna nya data eller uppdatera data regelbundet som en del av en pågående learning. Poängen kan vara slutförd med en datapipeline eller ett arbets flöde. [Med Azure Data Factory artikeln flytta data från en lokal SQL Server instans till Azure SQL Database med](move-sql-azure-adf.md) får du ett exempel på hur du konfigurerar en pipeline med [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

I det här steget kan du utveckla en lösningsarkitektur av din datapipeline för. Du kan utveckla pipelinen parallellt med nästa steg i datavetenskapsprojekt. Beroende på dina affärs behov och begränsningarna för dina befintliga system där den här lösningen integreras, kan pipelinen vara något av följande alternativ: 

   * Batch-baserade
   * Streaming eller realtid 
   * En hybrid 

## <a name="artifacts"></a>Artefakter
Följande är slutprodukter i det här steget:

   * [Data kvalitets rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): den här rapporten innehåller data sammanfattningar, relationerna mellan varje attribut och mål, variabel rangordning med mera. [Idé](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) verktyget som tillhandahålls som en del av TDSP kan snabbt generera den här rapporten på valfri tabell data uppsättning, till exempel en CSV-fil eller en Relations tabell. 
   * **Lösnings arkitektur**: lösnings arkitekturen kan vara ett diagram eller en beskrivning av din datapipeline som du använder för att köra poängsättning eller förutsägelser på nya data när du har skapat en modell. Den innehåller också pipeline för att träna din modell som baseras på nya data. Lagra dokumentet i [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) katalogen när du använder TDSP katalog struktur mal len.
   * **Kontroll punkts beslut**: innan du påbörjar fullständig teknik och modell utveckling kan du utvärdera om projektet för att avgöra om det förväntade värdet är tillräckligt för att fortsätta med det. Du kan till exempel vara redo att fortsätta, måste du samla in mer data eller Avbryt projektet eftersom data inte finns för att besvara frågan.

## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Affärs förståelse](lifecycle-business-understanding.md)
   2. [Data hämtning och förståelse](lifecycle-data.md)
   3. [Form](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kund godkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
