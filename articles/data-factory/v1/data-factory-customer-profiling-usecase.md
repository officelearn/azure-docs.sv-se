---
title: Användningsfall - Kundprofilering
description: Lär dig hur Azure Data Factory används för att skapa ett datadrivet arbetsflöde (pipeline) till profilspelkunder.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c570f988dea894b8106405f4e427edb386a3e74a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969296"
---
# <a name="use-case---customer-profiling"></a>Användningsfall - Kundprofilering
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite med lösningsacceleratorer.  Mer information om Cortana Intelligence finns i [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). I det här dokumentet beskriver vi ett enkelt användningsfall som hjälper dig att komma igång med att förstå hur Azure Data Factory kan lösa vanliga analysproblem.

## <a name="scenario"></a>Scenario
Contoso är ett spelföretag som skapar spel för flera plattformar: spelkonsoler, handhållna enheter och persondatorer. Som spelare spelar dessa spel, stor mängd loggdata produceras som spårar användningsmönster, spelstil och preferenser för användaren.  I kombination med demografiska, regionala och produktdata kan Contoso utföra analyser för att vägleda dem om hur de kan förbättra spelarnas upplevelse och rikta in dem för uppgraderingar och köp i spelet. 

Contoso s mål är att identifiera up-sell/cross-sell möjligheter baserat på spel historia av sina spelare och lägga till övertygande funktioner för att driva företagens tillväxt och ge en bättre upplevelse för kunderna. För detta användningsfall använder vi ett spelföretag som ett exempel på ett företag. Företaget vill optimera sina spel baserat på spelarnas beteende. Dessa principer gäller för alla företag som vill engagera sina kunder kring sina varor och tjänster och förbättra sina kunders upplevelse.

I den här lösningen vill Contoso utvärdera effektiviteten i en marknadsföringskampanj som nyligen har lanserats. Vi börjar med råa spelloggar, bearbetar och berikar dem med geolokaliseringsdata, ansluter dem till referensdata för annonsering och kopierar dem slutligen till en Azure SQL-databas för att analysera kampanjens inverkan.

## <a name="deploy-solution"></a>Distribuera lösning
Allt du behöver för att komma åt och prova det här enkla användningsfallet är en [Azure-prenumeration,](https://azure.microsoft.com/pricing/free-trial/)ett [Azure Blob-lagringskonto](../../storage/common/storage-account-create.md)och en [Azure SQL-databas](../../sql-database/sql-database-get-started.md). Du distribuerar pipelinen för kundprofilering från panelen **Exempelpipelor** på startsidan för datafabriken.

1. Skapa en datafabrik eller öppna en befintlig datafabrik. Se [Kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg för att skapa en datafabrik.
2. Klicka på panelen **Exempelpipellines** i **datafabriken** för datafabriken.

    ![Exempel på pipelines-panel](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klicka på den **kundprofilering** som du vill distribuera i bladet **Exempelpipelar.**

    ![Exempel på rörledningar blad](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurationsinställningar för exemplet. Till exempel ditt Azure-lagringskontonamn och -nyckel, Azure SQL-servernamn, databas, användar-ID och lösenord.

    ![Provblad](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange konfigurationsinställningarna klickar du på **Skapa** för att skapa/distribuera exempelpipelarna och länkade tjänster/tabeller som används av pipelines.
6. Du ser statusen för distributionen på exempelpanelen som du klickade på tidigare på **exempelpipelornas pipelines-blad.**

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När distributionen **har slutförts** på panelen för exemplet stänger du bladet **Exempelpipelar.**  
8. På **datafabriksbladet** ser du att länkade tjänster, datauppsättningar och pipelines läggs till i datafabriken.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Lösning: översikt
Det här enkla användningsfallet kan användas som ett exempel på hur du kan använda Azure Data Factory för att inta, förbereda, transformera, analysera och publicera data.

![Arbetsflödet slutpunkt till slutpunkt](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Den här figuren visar hur datapipelines visas i Azure-portalen när de har distribuerats.

1. **PartitionGameLogsPipeline** läser av råa spelhändelser från blob-lagring och skapar partitioner baserat på år, månad och dag.
2. **EnrichGameLogsPipeline sammanfogar** partitionerade spelhändelser med geokodreferensdata och berikar data genom att mappa IP-adresser till motsvarande geo-platser.
3. **Den AnalyzeMarketingCampaignPipeline** pipeline använder berikade data och bearbetar den med reklamdata för att skapa den slutliga produktionen som innehåller marknadsföring kampanj effektivitet.

I det här exemplet används Data Factory för att dirigera aktiviteter som kopierar indata, transformerar och bearbetar data och matar ut de slutliga data till en Azure SQL-databas.  Du kan också visualisera nätverket av datapipeldor, hantera dem och övervaka deras status från användargränssnittet.

## <a name="benefits"></a>Fördelar
Genom att optimera sina användarprofilanalyser och anpassa den till affärsmål kan spelföretaget snabbt samla in användningsmönster och analysera effektiviteten i sina marknadsföringskampanjer.

