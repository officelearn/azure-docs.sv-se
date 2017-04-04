---
title: "Använda Redgate för att läsa in data till ditt Azure-informationslager | Microsoft Docs"
description: "Ta reda på hur du använder Redgates Data Platform Studio i olika scenarier för datalagringshantering."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: cb0b5489ccfabb0e2a4ee412162a18930073b309
ms.lasthandoff: 01/24/2017



---
# <a name="load-data-with-redgate-data-platform-studio"></a>Läs in data med Redgates Data Platform Studio
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

I den här självstudien visar vi hur du använder [Redgates Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) för att flytta data från en lokal SQL Server till Azure SQL Data Warehouse. Data Platform Studio tillämpar de lämpligaste kompatibilitetskorrigeringarna och -optimeringarna, så det är det snabbaste sättet att komma igång med SQL Data Warehouse.

> [!NOTE]
> [Redgate](http://www.red-gate.com) tillhandahåller olika SQL Server-verktyg, och är sedan länge Microsoft-partner. Den här funktionen i Data Platform Studio har gjorts tillgängligt kostnadsfritt för såväl kommersiell och icke-kommersiell användning.
> 
> 

## <a name="before-you-begin"></a>Innan du börjar
### <a name="create-or-identify-resources"></a>Skapa eller identifiera resurser
Innan du sätter igång med den här självstudien behöver du följande:

* **Lokal SQL Server-databas**: De data du vill importera till SQL Data Warehouse måste komma från en lokal SQL Server (version 2008R2 eller senare). Data Platform Studio kan inte importera data direkt från en Azure SQL Database eller från textfiler.
* **Azure Storage-konto**: Data Platform Studio mellanlagrar data i Azure Blob Storage innan de läses in till SQL Data Warehouse. Lagringskontot måste använda distributionsmodellen Resource Manager (standard), inte den klassiska distributionsmodellen. Om du inte har ett lagringskonto kan du lära dig hur du skapar ett lagringskonto. 
* **SQL Data Warehouse**: I den här självstudien flyttas data från en lokal SQL Server till SQL Data Warehouse, så du måste ha ett informationslager online. Om du inte redan har ett informationslager kan du lära dig hur du skapar ett Azure SQL Data Warehouse.

> [!NOTE]
> Prestandan blir högre om lagringskontot och informationslagret skapas i samma region.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Steg 1: Logga in på Data Platform Studio med ditt Azure-konto
Öppna webbläsaren och navigera till webbplatsen [Data Platform Studio](https://www.dataplatformstudio.com/). Logga in med samma Azure-konto som du använde för att skapa lagringskontot och informationslagret. Om din e-postadress är associerad med både ett arbets- eller skolkonto och ett Microsoft-konto måste du välja det konto som har åtkomst till resurserna.

> [!NOTE]
> Om det här är första gången du använder Data Platform Studio uppmanas du att bevilja programmet behörighet för att hantera dina Azure-resurser.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Steg 2: Starta guiden Importera
Från DPS-huvudskärmen väljer du länken för att importera till Azure SQL Data Warehouse för att starta importguiden.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Steg 3: Installera Data Platform Studio-gatewayen
För att kunna ansluta den lokala SQL Server-databasen behöver du installera DPS-gatewayen. Gatewayen är en klientagent som ger åtkomst till din lokala miljö. Den extraherar data och överför dem till ditt lagringskonto. Dina data passerar aldrig Redgates servrar. Så här installerar du gatewayen:

1. Klicka på länken **Skapa gateway**
2. Hämta och installera gatewayen med det medföljande installationsprogrammet

![][2]

> [!NOTE]
> Gatewayen kan installeras på en dator med nätverksåtkomst till källplatsens SQL Server-databas. Den ansluter till SQL Server-databasen med hjälp av Windows-autentisering med den aktuella användarens autentiseringsuppgifter.
> 
> 

När du har installerat gatewayen ändras dess status till Ansluten och du kan välja Nästa.

## <a name="step-4-identify-the-source-database"></a>Steg 4: Identifiera källdatabasen
I textrutan *Ange servernamn* anger du namnet på den server som är värd för databasen och väljer **Nästa**. Välj sedan databasen som du vill importera data från på den nedrullningsbara menyn.

![][3]

DPS söker igenom den valda databasen efter tabeller att importera. Som standard importerar DPS alla tabeller i databasen. Du kan markera eller avmarkera tabeller genom att expandera länken Alla tabeller. Välj knappen Nästa för att gå vidare.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Steg 5: Välj ett lagringskonto för att mellanlagra data
DPS uppmanar dig att ange en plats där datan ska mellanlagras. Välj ett befintligt lagringskonto från din prenumeration och välj **Nästa**.

> [!NOTE]
> DPS skapar en ny blob-behållare i det valda lagringskontot och använder en specifik mapp för varje import.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Steg 6: Välj ett informationslager
Välj sedan en [Azure SQL Data Warehouse](http://aka.ms/sqldw)-onlinedatabas som du importerar datan till. När du har valt databasen måste du ange autentiseringsuppgifterna för att ansluta till databasen. Välj sedan **Nästa**.

![][5]

> [!NOTE]
> DPS slår samman källdatatabellerna med informationslagret. DPS varnar dig om tabellnamnet kräver att de befintliga tabellerna i informationslagret skrivs över. Om du vill kan du ta bort eventuella befintliga objekt i informationslagret genom att markera alternativet för att ta bort alla befintliga objekt före import.
> 
> 

## <a name="step-7-import-the-data"></a>Steg 7: Importera data
DPS bekräftar att du vill importera datan. Klicka bara på knappen Starta import för att påbörja dataimporten.

![][6]

I DPS visas förloppet för extraheringen och dataöverföringen från den lokala SQL Server-databasen samt förloppet för import till SQL Data Warehouse.

![][7]

När importen är slutförd får du en sammanfattning av dataimporten och en ändringsrapport för kompatibilitetskorrigeringarna som har utförts.

![][8]

## <a name="next-steps"></a>Nästa steg
Om du vill utforska dina data mer i SQL Data Warehouse kan du börja med att läsa följande:

* [Fråga Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Visualisera data med Power BI][Visualize data with Power BI]

Om du vill veta mer om Redgates Data Platform Studio:

* [Besök startsidan för DPS](http://www.dataplatformstudio.com/)
* [Titta på en demonstration av DPS på Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Om du vill ha en översikt över andra sätt att migrera och läsa in dina data i SQL Data Warehouse kan du se:

* [Migrera lösningen till SQL Data Warehouse][Migrate your solution to SQL Data Warehouse]
* [Läs in data till Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md)

Om du vill ha fler utvecklingstips kan du se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

