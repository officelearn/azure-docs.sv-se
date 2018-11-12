---
title: Användningsfall - Kundprofilering
description: Lär dig hur Azure Data Factory för att skapa en datadriven arbetsflöde (pipeline) för att profilera spel kunder.
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
ms.openlocfilehash: 5345ca3a9c2f35b45669a3a54ecb42ca627cde32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259827"
---
# <a name="use-case---customer-profiling"></a>Användningsfall - Kundprofilering
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite med Lösningsacceleratorer.  Mer information om Cortana Intelligence finns [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). I det här dokumentet beskriver vi ett enkelt exempel som hjälper dig att komma igång med att förstå hur Azure Data Factory kan lösa vanliga problem med analytics.

## <a name="scenario"></a>Scenario
Contoso är ett spelföretag som skapar spel för flera plattformar: konsoler, som när enheter och datorer (datorer). När spelare spelar dessa spel, produceras stort antal loggdata som spårar de användningsmönster, spel-format och användarens inställningar.  När de kombineras med demografiska, regionala och produktdata, Contoso kan utföra analyser som hjälper dem om hur du förbättrar spelarnas upplevelse och mål för uppgraderingar och i spelet inköp. 

Contosos målet är att identifiera möjligheter för upp-säljer/korsförsäljning baserat på spel historiken för dess spelare och Lägg till attraktiva funktioner att öka tillväxten och ge kunderna en bättre upplevelse. För det här användningsfallet använder vi ett spelföretag som ett exempel på ett företag. Företaget vill optimera dess spel baserat på spelarnas beteende. Dessa principer gäller för alla verksamheter som vill engagera kunderna runt dess varor och tjänster och förbättra kundernas upplevelse.

Contoso vill utvärdera effekten av en marknadsföringskampanj som den har nyligen lanserat i den här lösningen. Vi börja med loggarna raw spel, bearbeta och utöka dem med geoplatsdata, träffa annonserar referensdata och slutligen kopierar du dem till en Azure SQL-databas för att analysera kampanjens påverkan.

## <a name="deploy-solution"></a>Distribuera lösningen
Allt du behöver för att komma åt och prova att använda den här enkelt exempel är en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/), en [Azure Blob storage-konto](../../storage/common/storage-quickstart-create-account.md), och en [Azure SQL Database](../../sql-database/sql-database-get-started.md). Du distribuerar kundprofilering pipeline från den **exempel pipelines** panelen på startsidan på din datafabrik.

1. Skapa en data factory eller öppna en befintlig datafabrik. Se [kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) steg att skapa en datafabrik.
2. I den **DATA FACTORY** bladet för data factory klickar du på den **exempel pipelines** panelen.

    ![Exemplet pipelines panel](./media/data-factory-samples/SamplePipelinesTile.png)
3. I den **exempel pipelines** bladet klickar du på den **kundprofilering** som du vill distribuera.

    ![Exemplet pipelines bladet](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurationsinställningar för exemplet. Till exempel din Azure storage-kontonamn och nyckel, Azure SQL-servernamnet, databas, användar-ID och lösenord.

    ![Exempel-bladet](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange konfigurationsinställningarna, klickar du på **skapa** att skapa/distribuera exempelpipelines och länkade tjänster/tabeller som används av pipelines.
6. Du ser status för distributionen på panelen exempel du klickade på tidigare på den **exempel pipelines** bladet.

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När du ser den **distributionen lyckades** meddelande på panelen för det här exemplet Stäng den **exempel pipelines** bladet.  
8. På **DATA FACTORY** bladet som du ser att länkade tjänster, datauppsättningar och pipeliner läggs till din datafabrik.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Lösningsöversikt
Den här enkelt exempel kan användas som ett exempel på hur du kan använda Azure Data Factory för att mata in, förbereda, transformera, analysera och publicera data.

![Arbetsflödet slutpunkt till slutpunkt](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Den här bilden illustrerar hur data-pipelines visas i Azure portal när de har distribuerats.

1. Den **PartitionGameLogsPipeline** läser raw spelhändelser från blob storage och skapar partitioner som baseras på år, månad och dag.
2. Den **EnrichGameLogsPipeline** ansluter till partitionerade spelhändelser med referensdata för geo-kod och förbättra data genom att mappa IP-adresser till motsvarande geoplatser.
3. Den **AnalyzeMarketingCampaignPipeline** pipeline använder avancerad och data och bearbetar dessa med reklam-data för att skapa det slutgiltiga resultatet som innehåller marknadsföring kampanjens effektivitet.

I det här exemplet används Data Factory för att dirigera aktiviteter som kopierar indata, transformera och bearbeta data och den slutgiltiga utdata till en Azure SQL Database.  Du kan också visualisera nätverket av datapipelines, hantera och övervaka deras status i användargränssnittet.

## <a name="benefits"></a>Fördelar
Genom att optimera sina användare profil analytics och justerar detta till affärsmålen kan spelföretag snabbt samla in användningsmönster och analysera effektiviteten i dess marknadsföringskampanjer.

