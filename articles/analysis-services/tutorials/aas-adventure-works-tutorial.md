---
title: Azure Analysis Services – Självstudiekurs för Adventure Works | Microsoft Docs
description: Presentation av självstudiekursen för Adventure Works för Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 92bab3e6dcea0b6b234d361a346698be15088fc0
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191521"
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services – Självstudiekurs för Adventure Works

Den här självstudien tillhandahåller lektioner om hur du skapar och distribuerar en tabellmodell på kompatibilitetsnivån 1400 genom att använda Visual Studio [Analysis Services-projekt](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects) eller [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
Om du inte har använt Analysis Services eller tabellmodellering tidigare är den här självstudien det snabbaste sättet att lära dig hur du skapar och distribuerar en grundläggande tabellmodell med Visual Studio. Om du uppfyller förhandskraven bör det ta mellan två och tre timmar att slutföra självstudien.  
  
## <a name="what-you-learn"></a>Detta får du får lära dig   
  
-   Så här skapar du ett nytt projekt för tabellmodeller på den **kompatibilitetsnivån 1400** i Visual Studio.
  
-   Så här importerar du data från en relationsdatabas till arbetsytedatabasen i ett tabellmodellprojekt.  
  
-   Hur du skapar och hanterar relationer mellan tabeller i modellen.  
  
-   Hur du skapar beräknade kolumner, mått och KPI:er som hjälper användarna att analysera viktiga affärsmått.  
  
-   Hur du skapar och hanterar perspektiv och hierarkier som hjälper användarna att enkelt bläddra i modelldata genom att tillhandahålla affärs- och programspecifika översikter.  
  
-   Hur du skapar partitioner som delar upp tabelldata i mindre, logiska delar som kan bearbetas oberoende av andra partitioner.  
  
-   Hur du skyddar modellobjekt och data genom att skapa roller med användarmedlemmar.  
  
-   Så här distribuerar du en tabellmodell till en **Azure Analysis Services** server eller **SQL Server 2017 Analysis Services** servern med hjälp av Visual Studio.  
  
## <a name="prerequisites"></a>Förutsättningar  
För att slutföra den här kursen behöver du:  
  
-   En Analysis Services-server i Azure. Registrera dig för en kostnadsfri [utvärderingsversion av Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) och [skapa en server](../analysis-services-create-server.md). 

-   Ett [Azure SQL Server Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) med **AdventureWorksDW-exempeldatabasen** eller ett SQL Server Data Warehouse med [Adventure Works-exempeldatabasen](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks).

    **Viktigt!** Om du installerar exempeldatabasen till en lokal SQL Server Data Warehouse och distribuera din modell till en Azure Analysis Services-server, en [lokal datagateway](../analysis-services-gateway.md) krävs.

-   Den senaste versionen av [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) för Visual Studio.

-   Den senaste versionen av [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   Ett klientprogram som till exempel [Power BI Desktop](https://powerbi.microsoft.com/desktop/) eller Excel. 

## <a name="scenario"></a>Scenario  
Den här självstudien är baserad på Adventure Works Cycles, ett fiktivt företag. Adventure Works är ett stort, multinationellt företag som tillverkar och distribuerar cyklar, delar och tillbehör till kommersiella marknader i Nordamerika, Europa och Asien. Företaget har 500 anställda. Dessutom har Adventure Works flera regionala försäljningsteam inom sitt marknadsområde. Ditt projekt är att skapa en tabellmodell för försäljnings- och marknadsföringsanvändare som analyserar Internetförsäljningsdata i AdventureWorksDW-databasen.  
  
För att slutföra den här självstudien måste du slutföra ett antal lektioner. Varje lektion innehåller olika uppgifter. Du måste slutföra alla uppgifter i rätt ordning för att slutföra lektionen. En viss lektion kan innehålla flera uppgifter som leder till samma resultat, men tillvägagångssättet för att slutföra uppgifterna skiljer sig åt. Den här metoden visar att det ofta finns fler än ett sätt att slutföra en uppgift och fungerar även som en utmaning eftersom du måste använda kunskaper som du har lärt dig under tidigare lektioner och uppgifter.  
  
Syftet med lektionerna är att lära dig att redigera en grundläggande tabellmodell med hjälp av många av funktionerna i SSDT. Eftersom varje lektion bygger på den föregående lektionen bör du genomföra lektionerna i rätt ordning.
  
Den här självstudiekursen innehåller inte lektioner om att hantera en server i Azure Portal, hantera en server eller databas med hjälp av SSMS eller att använda ett klientprogram för att bläddra i modelldata. 


## <a name="lessons"></a>Lektioner  
Den här självstudien innehåller följande uppgifter:  
  
|Lektion|Uppskattad tidsåtgång|  
|----------|------------------------------|  
|[1 - Skapa ett nytt projekt för tabellmodeller](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minuter|  
|[2 - Hämta data](../tutorials/aas-lesson-2-get-data.md)|10 minuter|  
|[3 - Markera som datumtabell](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minuter|  
|[4 - Skapa relationer](../tutorials/aas-lesson-4-create-relationships.md)|10 minuter|  
|[5 - Skapa beräknade kolumner](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minuter|
|[6 - Skapa mått](../tutorials/aas-lesson-6-create-measures.md)|30 minuter|  
|[7 - Skapa KPI:er](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minuter|  
|[8 - Skapa perspektiv](../tutorials/aas-lesson-8-create-perspectives.md)|5 minuter|  
|[9 - Skapa hierarkier](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minuter|  
|[10 - Skapa partitioner](../tutorials/aas-lesson-10-create-partitions.md)|15 minuter|  
|[11 - Skapa roller](../tutorials/aas-lesson-11-create-roles.md)|15 minuter|  
|[12 - Analysera i Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minuter| 
|[13 - Distribuera](../tutorials/aas-lesson-13-deploy.md)|5 minuter|  
  
## <a name="supplemental-lessons"></a>Kompletterande lektioner  
Du måste inte slutföra de här lektionerna för att slutföra självstudien, men de kan hjälpa dig att bättre förstå avancerade funktioner för redigering av tabellmodeller.  
  
|Lektion|Uppskattad tidsåtgång|  
|----------|------------------------------|  
|[Detaljrader](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minuter|
|[Dynamisk säkerhet](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minuter|
|[Ojämna hierarkier](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minuter| 

  
## <a name="next-steps"></a>Nästa steg  
Kom igång genom att se [lektion 1: Skapa ett nytt projekt för Tabellmodeller](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

