---
title: "Läs in data från SQL Server till Azure SQL Data Warehouse (SSIS) | Microsoft Docs"
description: "Visar hur du skapar ett paket för SQL Server Integration Services (SSIS) för att flytta data från en mängd olika datakällor till SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Läs in data från SQL Server till Azure SQL Data Warehouse (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Skapa ett SQL Server Integration Services (SSIS)-paket för att läsa in data från SQL Server till Azure SQL Data Warehouse. Du kan alternativt omstrukturera, transformera och rensa data som överförs via SSIS-dataflöde.

I den här kursen ska du:

* Skapa ett nytt Integration Services-projekt i Visual Studio.
* Ansluta till datakällor, inklusive SQL Server (som en källa) och SQL Data Warehouse (som mål).
* Utforma ett SSIS-paket som läser in data från källan till målet.
* Kör SSIS-paket för att läsa in data.

Den här kursen använder SQL Server som datakällan. SQL Server kan köras lokalt eller i en virtuell Azure-dator.

## <a name="basic-concepts"></a>Grundläggande begrepp
Paketet är arbetsenheten i SSIS. Relaterade paket grupperas i projekt. Du skapar projekt och design paket i Visual Studio med SQL Server Data Tools. Designprocessen är en visuell process där du dra komponenter från verktygslådan till designytan, ansluter dem, och ange deras egenskaper. När du har ditt paket kan distribuera du om du vill den till SQL Server för omfattande hantering, övervakning och säkerhet.

## <a name="options-for-loading-data-with-ssis"></a>Alternativ för att läsa in data med SSIS
SQL Server Integration Services (SSIS) är en flexibel uppsättning verktyg som ger en mängd olika alternativ för att ansluta till och läsa in data i SQL Data Warehouse.

1. Använd ett ADO NET mål för att ansluta till SQL Data Warehouse. Den här kursen använder ett ADO NET mål eftersom den har minst antal konfigurationsalternativ.
2. Använd en OLE DB-mål för att ansluta till SQL Data Warehouse. Det här alternativet kan ge något bättre prestanda än ADO NET målet.
3. Använd Azure Blob överför aktiviteten för att mellanlagra data i Azure Blob Storage. Använd sedan SSIS kör SQL-uppgiften för att starta en Polybase-skript som läser in data i SQL Data Warehouse. Det här alternativet ger den bästa prestandan av tre alternativ som visas här. För att få ladda upp Azure Blob-aktiviteten, hämtar den [Microsoft SQL Server 2016 Integration Services Feature Pack för Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Läs mer om Polybase i [PolyBase-guiden][PolyBase Guide].

## <a name="before-you-start"></a>Innan du börjar
För att gå igenom de här självstudierna, behöver du:

1. **SQL Server Integration Services (SSIS)**. SSIS är en komponent i SQL Server och kräver en utvärderingsversion eller en licensierad version av SQL Server. Om du vill hämta en utvärderingsversion av SQL Server 2016 Preview finns [SQL Server-utvärderingar][SQL Server Evaluations].
2. **Visual Studio**. Kostnadsfri Visual Studio Community Edition finns [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools för Visual Studio (SSDT)**. SQL Server Data Tools för Visual Studio finns [Hämta SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Exempeldata**. Den här kursen använder exempeldata som lagras i SQL Server i exempeldatabasen AdventureWorks som källdata som ska läsas in i SQL Data Warehouse. För att få exempeldatabasen AdventureWorks finns [AdventureWorks exempeldatabas för 2014][AdventureWorks 2014 Sample Databases].
5. **En SQL Data Warehouse-databas och behörigheter**. Den här självstudiekursen ansluter till en instans av SQL Data Warehouse och läser in data i den. Du måste ha behörigheter att skapa en tabell och läsa in data.
6. **En brandväggsregel**. Du måste skapa en brandväggsregel på SQL Data Warehouse med IP-adressen för den lokala datorn innan du kan överföra data till SQL Data Warehouse.

## <a name="step-1-create-a-new-integration-services-project"></a>Steg 1: Skapa ett nytt Integration Services-projekt
1. Starta Visual Studio.
2. På den **filen** väljer du **ny | Projektet**.
3. Navigera till den **installerat | Mallar | Business Intelligence | Integration Services** projekttyper.
4. Välj **Integration Services-projekt**. Ange värden för **namn** och **plats**, och välj sedan **OK**.

Visual Studio öppnas och skapar ett nytt projekt för Integration Services (SSIS). Visual Studio öppnas designer för det enda nya SSIS-paketet (Package.dtsx) i projektet. Du kan se på följande områden:

* Till vänster i **verktygslådan** SSIS-komponenter.
* I mitten designytan med flera flikar. Normalt använder du minst den **Kontrollflöde** och **dataflöde** flikar.
* Till höger i **Solution Explorer** och **egenskaper** fönster.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Steg 2: Skapa grundläggande dataflödet
1. Dra en Data flödar aktivitet från verktygslådan till mitten av designytan (på den **Kontrollflöde** fliken).
   
    ![][02]
2. Dubbelklicka på aktiviteten flöda Data för att gå till fliken dataflöde.
3. Dra en ADO.NET-datakälla från listan andra källor i verktygslådan till designytan. Käll-kort fortfarande markerat, ändra dess namn till **SQL Server-datakälla** i den **egenskaper** fönstret.
4. Dra en ADO.NET-mål från listan andra mål i verktygslådan till designytan under ADO.NET-källa. Mål-kort fortfarande markerat, ändra dess namn till **SQL DW mål** i den **egenskaper** fönstret.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Steg 3: Konfigurera käll-kort
1. Dubbelklickar du på käll-kortet för att öppna den **ADO.NET källa Editor**.
   
    ![][03]
2. På den **Connection Manager** för den **ADO.NET källa Editor**, klickar du på den **ny** knappen bredvid den **ADO.NET Anslutningshanteraren** lista Öppna den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan och skapa anslutningsinställningar för SQL Server-databas som den här kursen läser in data.
   
    ![][04]
3. I den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan klickar du på den **ny** för att öppna den **Connection Manager** dialogrutan och skapa en ny anslutning.
   
    ![][05]
4. I den **Connection Manager** dialogrutan Gör följande saker.
   
   1. För **Provider**, Välj SqlClient-dataprovidern.
   2. För **servernamn**, ange namnet på SQL Server.
   3. I den **logga in på servern** väljer eller ange autentiseringsinformation.
   4. I den **Anslut till en databas** väljer exempeldatabasen AdventureWorks.
   5. Klicka på **Anslutningstestet**.
      
       ![][06]
   6. Klicka i dialogrutan som rapporterar resultatet av Anslutningstestet **OK** att återgå till den **Connection Manager** dialogrutan.
   7. I den **Connection Manager** dialogrutan klickar du på **OK** att återgå till den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan.
5. I den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan klickar du på **OK** att återgå till den **ADO.NET källa Editor**.
6. I den **ADO.NET källa Editor**i den **namnet på tabellen eller vyn** väljer den **Sales.SalesOrderDetail** tabell.
   
    ![][07]
7. Klicka på **Preview** att se de första 200 raderna med data i källtabellen i den **Preview frågeresultat** dialogrutan.
   
    ![][08]
8. I den **Preview frågeresultat** dialogrutan klickar du på **Stäng** att återgå till den **ADO.NET källa Editor**.
9. I den **ADO.NET källa Editor**, klickar du på **OK** till Slutför konfigurationen av datakällan.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Steg 4: Anslut käll-kortet till målnätverkskort
1. Välj källa kortet i designvyn.
2. Välj en blå pil som sträcker sig från käll-kort och drar den till mål-redigeraren förrän den har fästs på plats.
   
    ![][10]
   
    I en typisk SSIS-paket använder du ett antal andra komponenter från verktygslådan SSIS mellan källan och målet att omstrukturera, transformera och rensa data som överförs via SSIS-dataflöde. Om du vill behålla det här exemplet så enkel som möjligt, ansluter vi källan direkt till målet.

## <a name="step-5-configure-the-destination-adapter"></a>Steg 5: Konfigurera målnätverkskort
1. Dubbelklicka på målnätverkskort att öppna den **ADO.NET mål Editor**.
   
    ![][11]
2. På den **Connection Manager** för den **ADO.NET mål Editor**, klickar du på den **ny** knappen bredvid den **Anslutningshanteraren** listan till Öppna den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan och skapa anslutningsinställningar för Azure SQL Data Warehouse-databas som den här kursen läser in data.
3. I den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan klickar du på den **ny** för att öppna den **Connection Manager** dialogrutan och skapa en ny anslutning.
4. I den **Connection Manager** dialogrutan Gör följande saker.
   1. För **Provider**, Välj SqlClient-dataprovidern.
   2. För **servernamn**, anger du namnet på SQL Data Warehouse.
   3. I den **logga in på servern** väljer **används SQL Server-autentisering** och ange autentiseringsinformation.
   4. I den **Anslut till en databas** väljer du en befintlig SQL Data Warehouse-databas.
   5. Klicka på **Anslutningstestet**.
   6. Klicka i dialogrutan som rapporterar resultatet av Anslutningstestet **OK** att återgå till den **Connection Manager** dialogrutan.
   7. I den **Connection Manager** dialogrutan klickar du på **OK** att återgå till den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan.
5. I den **konfigurera ADO.NET Anslutningshanteraren** dialogrutan klickar du på **OK** att återgå till den **ADO.NET mål Editor**.
6. I den **ADO.NET mål Editor**, klickar du på **ny** bredvid den **använder en tabell eller vy** att öppna den **Create Table** dialogrutan för att skapa en nya måltabellen med en kolumnlista som matchar källtabellen.
   
    ![][12a]
7. I den **Create Table** dialogrutan Gör följande saker.
   
   1. Ändra namnet på tabellen till **SalesOrderDetail**.
   2. Ta bort den **rowguid** kolumn. Den **uniqueidentifier** datatyp stöds inte i SQL Data Warehouse.
   3. Ändra datatypen för den **LineTotal** kolumnen till **pengar**. Den **decimal** datatyp stöds inte i SQL Data Warehouse. Information om vilka datatyper finns [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Klicka på **OK** att skapa tabellen och återgå till den **ADO.NET mål Editor**.
8. I den **ADO.NET mål Editor**, Välj den **mappningar** fliken för att se hur kolumner i källans mappas till kolumnerna i mål.
   
    ![][13]
9. Klicka på **OK** till Slutför konfigurationen av datakällan.

## <a name="step-6-run-the-package-to-load-the-data"></a>Steg 6: Kör för att läsa in data
Kör paketet genom att klicka på den **starta** knappen i verktygsfältet eller genom att välja något av de **kör** alternativ på den **felsöka** menyn.

När paketet börjar köras, se gul snurrande hjul som visar aktivitet samt antalet bearbetade hittills rader.

![][14]

När paketet har körts kan du se grön bockmarkering som anger lyckad samt det totala antalet rader med data som har lästs in från källan till målet.

![][15]

Grattis! Du har använt SQL Server Integration Services att läsa in data till Azure SQL Data Warehouse.

## <a name="next-steps"></a>Nästa steg
* Läs mer om SSIS-dataflöde. Börja här: [dataflöde][Data Flow].
* Lär dig att felsöka dina paket direkt i design-miljön. Börja här: [felsökningsverktyg för paketet utveckling][Troubleshooting Tools for Package Development].
* Lär dig mer om att distribuera SSIS-projekt och paket till Integration Services-servern eller en annan lagringsplats. Börja här: [projekt för distribution av och paket][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
