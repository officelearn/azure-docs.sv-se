---
title: "Datainsamling och förstå steget i teamet datavetenskap processen livscykel – Azure | Microsoft Docs"
description: "Mål, uppgifter och leveranser för datainsamling och förstå fas för dina datavetenskap projekt."
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
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>Förvärv och förståelse av data

Det här avsnittet beskriver mål, uppgifter, och leveranser som är associerade med den **datainsamling och förstå fas** processens Team datavetenskap. Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

* **Så här fungerar för företag**
* **Datainsamling och förstå**
* **Modeling**
* **Distribution**
* **Kundens godkännande**

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* En ren, högkvalitativ dataset förstås vars relationer till target-variabler som finns i lämplig analytics-miljö redo att modellen.
* En lösningsarkitektur för data-pipeline för att uppdatera och poängsätta data regelbundet har utvecklats.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

* **Mata in data** i analytiska målmiljön.
* **Utforska data** att avgöra om data quality är tillräcklig för att besvara frågan. 
* **Konfigurera en data-pipeline** att poängsätta nya eller regelbundet uppdatera data.

### <a name="21-ingest-the-data"></a>2.1 mata in data
Ställa in processen att flytta data från källplatser till målplatserna där analytics-åtgärder som utbildning och förutsägelser ska köras. Teknisk information och alternativ för hur du gör detta med olika Azure data services finns i [läser in data i miljöer med lagring för analys av](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 utforska data
Innan du träna modeller, måste du utveckla en god förståelse av data. Verkliga datauppsättningar är ofta mycket brus eller saknar värden eller ha en värd om andra skillnader. Sammanfatta data och visuella kan användas för att granska kvaliteten på dina data och ange den information som behövs för att bearbeta data innan den är klar för modellering. Den här processen är ofta iterativ.

TDSP ger en automatiserad verktyget [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) för att visualisera data och förbereda data sammanfattningsrapporter. Vi rekommenderar att du börjar med IDEAR först för att utforska data för att utveckla ursprungliga data förstå interaktivt med ingen kodning och sedan skriva anpassad kod för datagranskning och visualisering. Information om rensning av data finns [uppgifter du förbereder data för förbättrad maskininlärning](prepare-data.md).  

När du är nöjd med kvaliteten på rengörs data, är nästa steg att bättre förstå de mönster som ingår i de data som hjälper dig välja och utveckla en lämplig förutsägelsemodell för dina mål. Leta för bevis för hur väl anslutna data är målet och om det finns tillräckligt med information för att gå vidare med nästa steg för förutsägelsemodellering. Den här processen är igen, ofta iterativ. Du kan behöva söka efter nya datakällor med exaktare eller mer relevant att utöka dataset som ursprungligen identifierades i det föregående steget.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 Konfigurera en data-pipeline
Utöver den inledande införandet och rensa data, vanligtvis behöver du ställa in en process för att samla in nya data eller uppdatering av data regelbundet som en del av en pågående learning process. Detta kan göras genom att skapa en pipeline för data eller ett arbetsflöde. Här är en [exempel](move-sql-azure-adf.md) för hur du ställer in en pipeline med [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

En lösningsarkitektur för data-pipeline utvecklas i det här steget. Pipelinen också utvecklas parallellt med de följande stegen i datavetenskap projektet. Pipelinen kanske batch-baserade eller direktuppspelning/real-gång eller en hybrid beroende på dina affärsbehov och begränsningarna i din befintliga system som den här lösningen integreras. 

## <a name="artifacts"></a>Artefakter
Följande är resultat i det här steget.

* [**Data Quality rapporten**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): den här rapporten innehåller data sammanfattningar, relationer mellan varje attribut och mål, variabel rangordning osv. Den [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) verktyg som tillhandahålls som en del av TDSP kan snabbt skapa den här rapporten för alla tabular datauppsättningen som en CSV-fil eller en relationella tabell. 
* **Lösningsarkitektur**: Detta kan vara ett diagram eller en beskrivning av dina data pipeline som används för att köra bedömningen eller förutsägelser för nya data när du har skapat en modell. Den innehåller också pipelinen för att träna om din modell baserat på nya data. Dokumentet är lagrad i den [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) directory när du använder mallen TDSP directory struktur.
* **Kontrollpunkt beslut**: innan du börjar fullständig funktionen tekniker och modellskapandet du omvärdera projektet för att avgöra om det förväntade värdet är tillräcklig för att fortsätta arbetar den. Du kan till exempel vara redo att fortsätta måste du samla in mer data eller Avbryt projektet som data inte finns för att besvara frågan.

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för Team av vetenskapliga data:

* [1. Så här fungerar för företag](lifecycle-business-understanding.md)
* [2. Datainsamling och förstå](lifecycle-data.md)
* [3. Modeling](lifecycle-modeling.md)
* [4. Distribution](lifecycle-deployment.md)
* [5. Kundens godkännande](lifecycle-acceptance.md)

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program.  

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.