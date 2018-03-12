---
title: "Översikt över Microsoft Azure Data Lake Analytics | Microsoft Docs"
description: "Data Lake Analytics är en Azure Big Data-tjänst med vilken du kan använda data för att driva ditt företag med hjälp av kunskap från dina data i molnet, oavsett deras storlek eller plats."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 316c35fa4b04bdb251c2b9ae14f6b32f4e4bf939
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Översikt över Microsoft Azure Data Lake Analytics

## <a name="what-is-azure-data-lake-analytics"></a>Vad är Azure Data Lake Analytics?
Azure Data Lake Analytics är en tjänst på begäran som gör det enklare att analysera stordata. I stället för att distribuera, konfigurera och justera maskinvara, kan du skriva frågor genom vilka du kan omvandla dina data och extrahera värdefulla insikter. Med Analytics-tjänsten kan du hantera jobb av alla skalor direkt bara genom att ange hur mycket kraft du behöver. Du betalar endast för jobbet när tjänsten körs vilket gör den kostnadseffektiv. Den innehåller också stöd för U-SQL, ett datorspråk som kombinerar fördelarna hos SQL med kraften i tvingande kod. Med U-SQL kan du analysera data i Data Lake Store, SQL Server i Azure, Azure SQL Database och Azure SQL Data Warehouse.

## <a name="dynamic-scaling"></a>Dynamisk skalning
  
Data Lake Analytics har konstruerats för molnskalning och prestanda.  Resurser etableras dynamiskt och du kan utföra analyser på terabyte eller även exabyte av data. När jobbet är slutfört rullas resurser ner automatiskt och du betalar endast för den processorkraft som används. När du ökar eller minskar storleken på data som lagras eller mängden beräkning som används behöver du inte skriva om kod. Du kan helt och hållet fokusera på affärslogiken och inte på hur du bearbetar och lagrar stora datamängder.

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Utveckla snabbare, felsök och optimera smartare med hjälp av välbekanta verktyg
  
Data Lake Analytics har djupgående integrering med Visual Studio, så att du kan använda välbekanta verktyg för att köra, felsöka och finjustera din kod. Med visualiseringar av dina U-SQL-jobb kan du se hur koden körs i skala, så att du lätt kan identifiera prestandaflaskhalsar och optimera kostnader.


## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: Enkelt och bekant, kraftfullt och utökningsbart
  
Data Lake Analytics innehåller U-SQL, ett frågespråk som utökar den bekanta, enkla, deklarativa kompetensen hos SQL med den expressiva kraften i C#. U-SQL-språket bygger på samma distribuerade körning som används i system för stordata i Microsoft. Miljontals SQL- och .NET-utvecklare kan nu bearbeta och analysera alla sina data med kunskaper som de redan har.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Kan helt integreras med dina IT-investeringar
  
Data Lake Analytics kan använda dina befintliga IT-investeringar för identitet, hantering, säkerhet och datalagring. Detta förenklar datastyrning och gör det enkelt att utöka dina aktuella program. Data Lake Analytics är integrerat med Active Directory, så att användare och behörigheter kan hanteras, och integrerad övervakning och granskning ingår.

## <a name="affordable-and-cost-effective"></a>Prisvärt och kostnadseffektivt

Data Lake Analytics är en kostnadseffektiv lösning för arbetsbelastningar av stordata som körs. Du betalar per projekt när data bearbetas. Det krävs ingen maskinvara, licenser eller tjänstspecifikt supportavtal. Systemet skalas automatiskt uppåt eller nedåt när jobbet startas och är klart, vilket innebär att du aldrig betalar för mer än vad du behöver. [Läs mer om att kontrollera kostnader och spara pengar](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c).
    
## <a name="works-with-all-your-azure-data"></a>Fungerar med alla dina Azure-data
  
Data Lake Analytics samverkar med Azure Data Lake Store för högsta prestanda, dataflöde och parallellisering. Det fungerar också med Azure Storage-blobbar, Azure SQL Database och Azures informationslager.

## <a name="next-steps"></a>Nästa steg
 
  * Komma igång med Data Lake Analytics med hjälp av [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md)
  * Hantera Azure Data Lake Analytics med hjälp av [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
  * [Kontrollera kostnader och spara pengar med Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
