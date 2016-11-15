---
title: "Översikt över Microsoft Azure Data Lake Analytics | Microsoft Docs"
description: "Data Lake Analytics är en beräkningstjänst för stordata som gör att du kan använda data för att driva ditt företag med hjälp av kunskap från dina data i molnet, oavsett var de är och oavsett storleken. Data Lake Analytics gör detta på det enklaste, mest skalbara och mest ekonomiska möjliga sätt. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a6d2d6365afaecab57a100867a72b9aa2def3745


---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Översikt över Microsoft Azure Data Lake Analytics
## <a name="what-is-azure-data-lake-analytics"></a>Vad är Azure Data Lake Analytics?
Azure Data Lake Analytics är en ny tjänst som skapats för att enkelt göra analyser av stordata. Med den här tjänsten kan du fokusera på att skriva, köra och hantera jobb, i stället för att hantera distribuerad infrastruktur. I stället för att distribuera, konfigurera och justera maskinvara, kan du skriva frågor för att omvandla dina data och extrahera värdefulla insikter. Analytics-tjänsten kan hantera jobb av alla skalor direkt genom att bara ange skalan för hur mycket kraft du behöver. Du betalar endast för jobbet när tjänsten körs vilket gör den kostnadseffektiv. Analytics-tjänsten stöder Azure Active Directory så att du enkelt kan hantera åtkomst och roller, integrerade med ditt lokala identitetssystem. Den omfattar också U-SQL, ett språk som kombinerar fördelarna hos SQL med den direkta kraften hos användarkod. Med U-SQL:s skalbara distribuerade körning kan du effektivt analysera data i arkivet och mellan SQL-servrar i Azure, Azure SQL Database och Azure SQL Data Warehouse.

## <a name="key-capabilities"></a>De viktigaste funktionerna
* **Dynamisk skalning**
  
    Data Lake Analytics är från grunden konstruerad för molnskalning och prestanda.  Resurser etableras dynamiskt och du kan utföra analyser på terabyte eller även exabyte av data. När jobbet är slutfört rullas resurser ner automatiskt och du betalar endast för den processorkraft som används. När du ökar eller minskar storleken på data som lagras eller mängden beräkning som används behöver du inte skriva om kod. Detta gör att du kan fokusera endast på affärslogiken och inte på hur du bearbetar och lagrar stora datamängder.
* **Utveckla snabbare, felsök och optimera smartare med hjälp av välbekanta verktyg**
  
    Data Lake Analytics har djupgående integrering med Visual Studio, så att du kan använda välbekanta verktyg för att köra, felsöka och finjustera din kod. Med visualiseringar av dina U-SQL-jobb kan du se hur koden körs i skala, så att du lätt kan identifiera prestandaflaskhalsar och optimera kostnader.
* **U-SQL: enkelt och bekant, kraftfullt och utökningsbart**
  
    Data Lake Analytics innehåller U-SQL, ett frågespråk som utökar den bekanta, enkla, deklarativa kompetensen hos SQL med den expressiva kraften i C#. U-SQL-språket bygger på samma distribuerade körning som används i system för stordata i Microsoft. Miljontals SQL- och .NET-utvecklare kan nu bearbeta och analysera alla sina data med kunskaper som de redan har.
* **Kan helt integreras med dina IT-investeringar**
  
    Data Lake Analytics kan använda dina befintliga IT-investeringar för identitet, hantering, säkerhet och datalagring. Detta förenklar datastyrning och gör det enkelt att utöka dina aktuella program. Data Lake Analytics är integrerat med Active Directory för att hantera användare och behörigheter och övervakning och granskning ingår.
* **Prisvärt och kostnadseffektivt**
  
    Data Lake Analytics är en kostnadseffektiv lösning för arbetsbelastningar av stordata som körs. Du betalar per projekt när data bearbetas. Det krävs ingen maskinvara, licenser eller tjänstspecifikt supportavtal. Systemet skalas automatiskt uppåt eller nedåt när jobbet startas och är klart, vilket innebär att du aldrig betalar för mer än vad du behöver.
* **Fungerar med alla dina Azure-data**
  
    Data Lake Analytics kan arbeta med ett antal Azure-datakällor: Azure Blob-lagring, Azure SQL-databaser och naturligtvis Data Lake Analytics är speciellt optimerade för att fungera med Azure Data Lake Store, vilket ger högsta prestanda, genomströmning och parallellisering för dina arbetsbelastningar med stordata.

## <a name="see-also"></a>Se även
* Kom igång
  
  * [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
  * [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Kom igång med Data Lake Analytics med hjälp av Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
  * [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Kom igång med Azure Data Lake Analytics U-SQL-språk](data-lake-analytics-u-sql-get-started.md)
* U-SQL och utveckling
  
  * [Kom igång med Azure Data Lake Analytics U-SQL-språk](data-lake-analytics-u-sql-get-started.md)
  * [Använd U-SQL-fönstrets funktioner för Azure Data Lake Analytics-jobb](data-lake-analytics-use-window-functions.md)
  * [Utveckla användardefinierade U-SQL-operatörer för Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* Hantering
  
  * [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md)
  * [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
  * [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
  * [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Självstudier för slutpunkt till slutpunkt
  
  * [Använd interaktiva Azure Data Lake Analytics-självstudier](data-lake-analytics-use-interactive-tutorials.md)
  * [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
* Berätta vad du tycker
  
  <!-- Fixing broken links for Azure content migration from ACOM to DOCS. I can't find a suitable substitute for what appears to be a link that is no longer available. I am commenting out for now. The author can investigate in the future. Hyperlink text: Comment on our documentation backlog. Referenced file: data-lake-analytics-documentation-backlog.md -->
  * [Skicka en funktionsbegäran](http://aka.ms/adlafeedback)
  * [Få hjälp i forumen](http://aka.ms/adlaforums)




<!--HONumber=Nov16_HO2-->


