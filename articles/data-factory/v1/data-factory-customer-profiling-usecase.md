---
title: Användningsfall - Kundprofilering
description: Lär dig hur Azure Data Factory för att skapa ett datadrivna arbetsflöde (pipeline) som profilen spel kunder.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: cc9dad4584c8edc47181e4a73ffe11a2e08de2f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620820"
---
# <a name="use-case---customer-profiling"></a>Användningsfall - Kundprofilering
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite solution Accelerator-verktyg.  Mer information om Cortana Intelligence finns [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). I det här dokumentet beskriver vi enkla användningsfall som hjälper dig att komma igång med att förstå hur Azure Data Factory kan lösa vanliga problem med analytics.

## <a name="scenario"></a>Scenario
Contoso är ett spel företag som skapar spel för flera plattformar: konsoler, hand hålls enheter och datorer (datorer). När spelare spelar dessa spel, produceras stora volymer av loggdata som spårar användningsmönster, spel-format och inställningar för användaren.  När den kombineras med demografisk, regionala och produktdata Contoso kan utföra analyser som hjälper dem om att förbättra spelare dem för uppgraderingar och i spelet inköp. 

Contosos målet är att identifiera dig-säljer/cross-säljer affärsmöjligheter baserat på dess spelare spel tidigare och lägga till tilltalande funktioner i enheten tillväxt och ge en bättre upplevelse för kunder. För den här användningsfall använder vi ett spel företag som ett exempel på ett företag. Företaget vill optimera dess spel baserat på spelare beteende. Dessa principer som gäller för alla företag som vill engagera kunderna runt dess varor och tjänster och förbättra sina kunder.

Contoso vill utvärdera effektiviteten hos en marknadsföringskampanj den nyligen har startats i den här lösningen. Vi börjar med raw spel loggar, bearbeta och utöka dem med geolokalisering data, ansluta med reklam referensdata och slutligen kopierar du dem till en Azure SQL Database för att analysera kampanjens påverkan.

## <a name="deploy-solution"></a>Distribuera lösningen
Allt du behöver för att komma åt och prova att använda den här enkla användningsfall är en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/), en [Azure Blob storage-konto](../../storage/common/storage-create-storage-account.md#create-a-storage-account), och en [Azure SQL Database](../../sql-database/sql-database-get-started.md). Du distribuerar kunden profilering pipeline från den **exempel pipelines** panelen på startsidan i din data factory.

1. Skapa en datafabrik eller öppna en befintlig datafabrik. Se [kopiera data från Blob Storage till SQL-databas med hjälp av Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) steg för att skapa en datafabrik.
2. I den **DATAFABRIKEN** bladet för data factory klickar du på den **exempel pipelines** panelen.

    ![Exempel pipelines sida vid sida](./media/data-factory-samples/SamplePipelinesTile.png)
3. I den **exempel pipelines** bladet, klickar du på den **kunden profilering** som du vill distribuera.

    ![Exempel pipelines bladet](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurationsinställningar för. Till exempel din Azure storage-kontonamnet och nyckeln, Azure SQL-servernamnet, databas, användar-ID och lösenord.

    ![Exempel-bladet](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange inställningarna klickar du på **skapa** att skapa/distribuera exempel pipelines och länkade tjänster/tabeller som används av pipelines.
6. Du ser statusen för distributionen på panelen exempel du klickade på tidigare på den **exempel pipelines** bladet.

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När du ser den **distributionen lyckades** meddelande på panelen för det här exemplet Stäng den **exempel pipelines** bladet.  
8. På **DATA FACTORY** bladet som du ser att länkade tjänster, datauppsättningar och pipelines läggs till din data factory.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Lösningsöversikt
Den här enkla användningsfall kan användas som ett exempel på hur du kan använda Azure Data Factory för att mata in, förbereda, transformera, analysera och publicera data.

![Arbetsflödet slutpunkt till slutpunkt](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Denna bild visar hur data pipelines visas i Azure-portalen när de har distribuerats.

1. Den **PartitionGameLogsPipeline** läser rådata spel händelser från blob storage och skapar partitioner baserat på år, månad och dag.
2. Den **EnrichGameLogsPipeline** ansluter partitionerade spel händelser med geo kod referensdata och förbättra data genom att mappa IP-adresser till motsvarande geoplatser.
3. Den **AnalyzeMarketingCampaignPipeline** pipeline använder utökade data och bearbetar med reklam-data för att skapa det slutgiltiga resultatet som innehåller marknadsföring kampanj effektivitet.

I det här exemplet används Data Factory för att dirigera aktiviteter som att kopiera indata, transformering och processen data och det slutliga utdata till en Azure SQL Database.  Du kan också visualisera data pipelines nätverk, hantera dem och övervaka deras status i användargränssnittet.

## <a name="benefits"></a>Fördelar
Genom att optimera sina analyser och justera med affärsmål kan spel företag snabbt samla in användningsmönster och analysera effektivitet med dess marknadsföringskampanjer.

