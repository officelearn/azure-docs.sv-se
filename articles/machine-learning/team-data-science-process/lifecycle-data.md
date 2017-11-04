---
title: "Datainsamling och förstå fas i livscykeln Team datavetenskap Process - Azure | Microsoft Docs"
description: "Mål, uppgifter och leveranser för datainsamling och förstå steget datavetenskap projekt"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: bfdc6339d8247f3acdf3b7797035e155ef0c1fd1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="data-acquisition-and-understanding"></a>Förvärv och förståelse av data

Den här artikeln beskrivs mål, uppgifter och leveranser som är associerade med datainsamling och förstå fas av Team Data vetenskap processen (TDSP). Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Så här fungerar för företag**
   2. **Datainsamling och förstå**
   3. **Modeling**
   4. **Distribution**
   5. **Kundens godkännande**

Här är en bild av TDSP livscykeln: 

![TDSP livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Generera en ren, högkvalitativ datauppsättning vars relation till target-variabler är att förstå. Leta upp datauppsättningen i lämplig analytics miljön så att du är redo att modellen.
* Utveckla en lösningsarkitektur för data-pipeline som uppdaterar och poäng data regelbundet.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

   * **Mata in data** i analytiska målmiljön.
   * **Utforska data** att avgöra om data quality är tillräcklig för att besvara frågan. 
   * **Konfigurera en data-pipeline** att poängsätta nya eller regelbundet uppdatera data.

### <a name="ingest-the-data"></a>Mata in data
Ställa in processen att flytta data från källplatser till de platser där du kör analytics-åtgärder, t.ex utbildning och förutsägelser. Teknisk information och alternativ för hur du flyttar data med olika Azure datatjänster finns [läser in data i miljöer med lagring för analys av](ingest-data.md). 

### <a name="explore-the-data"></a>Utforska data
Innan du träna modeller, måste du utveckla en god förståelse av data. Verkliga datauppsättningar är ofta mycket brus, saknar värden eller ha en värd om andra skillnader. Du kan använda sammanfatta data och visuella granska kvaliteten på dina data och ange den information du behöver att bearbeta data innan den är klar för modellering. Den här processen är ofta iterativ.

TDSP innehåller ett automatiskt verktyg som kallas [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), för att visualisera data och förbereda data sammanfattningsrapporter. Vi rekommenderar att du börjar med IDEAR först för att utforska data för att utveckla ursprungliga data förstå interaktivt med ingen kodning. Du kan sedan skriva anpassad kod för datagranskning och visualisering. Information om rensning av data finns [uppgifter du förbereder data för förbättrad maskininlärning](prepare-data.md).  

När du är nöjd med kvaliteten på rengörs data är nästa steg att bättre förstå de mönster som ingår i data. Detta hjälper dig att välja och utveckla en lämplig förutsägelsemodell för dina mål. Leta för bevis för hur väl anslutna data är målet. Därefter bestämmer du om det finns tillräckligt med information för att gå vidare med nästa steg för förutsägelsemodellering. Den här processen är igen, ofta iterativ. Du kan behöva söka efter nya datakällor med exaktare eller mer relevant att utöka den datamängd som ursprungligen identifierades i det föregående steget. 

### <a name="set-up-a-data-pipeline"></a>Konfigurera en data-pipeline
Utöver den inledande införandet och rensa data, vanligtvis behöver du ställa in en process för att samla in nya data eller uppdatering av data regelbundet som en del av en pågående learning process. Det gör du genom att skapa en pipeline för data eller ett arbetsflöde. Den [flytta data från en lokal SQL Server-instans till Azure SQL Database med Azure Data Factory](move-sql-azure-adf.md) artikeln ger ett exempel på hur du ställer in en pipeline med [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

I det här steget kan utveckla du en lösningsarkitektur för data-pipeline. Du kan utveckla pipeline parallellt med nästa steg i projektet vetenskapliga data. Beroende på dina affärsbehov och begränsningar i din befintliga system som den här lösningen integreras kan pipeline vara något av följande: 

   * Batch-baserade
   * Direktuppspelning eller realtid 
   * En hybrid 

## <a name="artifacts"></a>Artefakter
Följande är resultat i det här steget:

   * [Data quality rapporten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): den här rapporten innehåller data sammanfattningar, relationer mellan varje attribut och mål, variabel rangordning och mycket mer. Den [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) verktyg som tillhandahålls som en del av TDSP kan snabbt skapa rapporten på alla tabular datamängd, till exempel en CSV-fil eller en relationella tabell. 
   * **Lösningsarkitektur**: lösningsarkitekturen kan vara ett diagram, eller beskrivning av dina data i pipeline som du använder för att köra bedömningen eller förutsägelser för nya data när du har skapat en modell. Den innehåller också pipelinen för att träna om din modell baserat på nya data. Lagra dokument i den [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) directory när du använder mallen TDSP directory struktur.
   * **Kontrollpunkt beslut**: innan du börjar full-funktionen tekniker och modellskapandet du omvärdera projektet för att avgöra om det förväntade värdet är tillräcklig för att fortsätta arbetar den. Du kan till exempel vara redo att fortsätta måste du samla in mer data eller Avbryt projektet som data inte finns för att besvara frågan.

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Så här fungerar för företag](lifecycle-business-understanding.md)
   2. [Datainsamling och förstå](lifecycle-data.md)
   3. [Modeling](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundens godkännande](lifecycle-acceptance.md)

Vi ger fullständig slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgång](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar illustrerar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](http://aka.ms/datascienceprocess).
