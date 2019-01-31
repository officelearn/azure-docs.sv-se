---
title: Visualisera SQL Data Warehouse-data med Power BI Microsoft Azure
description: Visualisera SQL Data Warehouse-data med Power BI
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: df1fe833a5b0cb65372f8df73fb17231463d4d64
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466276"
---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI
De här självstudierna visar hur du använder Power BI för att ansluta till SQL Data Warehouse och skapa några grundläggande visualiseringar.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse förinstallerad med AdventureWorksDW-databasen. För att etablera ett informationslager, se [skapa ett SQL Data Warehouse](create-data-warehouse-portal.md) och väljer att läsa in exempeldata. Om du redan har ett data warehouse men inte har exempeldata, kan du [läsa in WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Ansluta till databasen
Om du vill öppna Power BI och ansluta till databasen AdventureWorksDW:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **SQL-databaser** och välj din AdventureWorks SQL Data Warehouse-databas.
   
    ![Hitta din databas](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klicka på knappen Öppna i Power BI.
   
    ![Power BI-knappen](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Du bör nu se SQL Data Warehouse-anslutningssidan som visar webbadressen för databasen. Klicka på Nästa.
   
    ![Power BI-anslutning](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Ange din Azure SQL server-användarnamn och lösenord.
   
    ![Power BI inloggning](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Klicka på AdventureWorksDW-datauppsättningen på det vänstra bladet för att öppna databasen.
   
    ![Power BI öppna AdventureWorksDW](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Skapa en rapport
Du är nu redo att använda Power BI för att analysera dina AdventureWorksDW-exempeldata. För att utföra analysen har AdventureWorksDW en vy som heter AggregateSales. Vyn innehåller några nyckelvärden för att analysera företagets försäljning.

1. Klicka på vyn AggregateSales i högra fältet för att expandera den och skapa en karta över försäljningsbelopp enligt postnummer. Klicka på kolumnerna PostalCode och SalesAmount för att markera dem.
   
    ![Powerbi väljer AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Powerbi automatiskt identifieras geografiska data och placera den på en karta för dig.
   
    ![Power BI-karta](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Det här steget skapar ett stapeldiagram som visar försäljningsintäkter efter kund. Om du vill skapa stapeldiagrammet, går du till den expanderade AggregateSales-vyn. Klicka på fältet SalesAmount. Dra fältet Customer Income till vänster och släpp det i axeln.
   
    ![Powerbi väljer axel](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Diagrammet över till vänster.
   
    ![Power BI-stapel](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Det här steget skapar ett linjediagram som visar försäljning per beställningsdatum. Gå till den expanderade AggregateSales-vyn för att skapa linjediagrammet. Klicka på SalesAmount och OrderDate. I kolumnen visualiseringar klickar du på linjediagram-ikonen, vilket är den första ikonen i den andra raden under visualiseringar.
   
    ![Powerbi väljer linjediagram](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Nu har du en rapport som visar tre olika datavisualiseringar.
   
    ![Power BI-linje](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Du kan spara ditt arbete när som helst genom att klicka på **Arkiv** och välja **Spara**.

## <a name="using-direct-connect"></a>Använda Direct Connect
Som med Azure SQL Database kan SQL Data Warehouse Direct Connect logiska pushdown tillsammans med analysfunktionerna i Power BI. Med Direct Connect skickas frågor tillbaka till Azure SQL Data Warehouse i realtid medan du utforskar dessa data.  Den här funktionen i kombination med skalan för SQL Data Warehouse, kan du skapa dynamiska rapporter på några få minuter mot flera terabyte med data. Dessutom tillåter införandet av öppna i Power BI-knappen användare att ansluta Power BI direkt till SQL Data Warehouse utan att samla in information från andra delar av Azure.

När du använder direkt ansluta:

* Ange det fullständigt kvalificerade servernamnet vid anslutning.
* Se till att brandväggsreglerna för databasen är konfigurerade för att ge tillgång till Azure-tjänster.
* Varje åtgärd, som att markera en kolumn eller lägga till ett filter frågor direkt mot informationslagret.
* Panelerna uppdateras automatiskt och cirka 15 minuter.
* Frågor och svar är inte tillgängligt för Direct Connect datauppsättningar.
* Schemaändringar ingår automatiskt.
* Alla frågor med Direct Connect når tidsgränsen efter två minuter.

Dessa begränsningar och anteckningar kan ändras när upplevelserna förbättra.

## <a name="next-steps"></a>Nästa steg
Nu när du har värmt upp med exempeldata, kan du se mer information om att [utveckla](sql-data-warehouse-overview-develop.md), [läsa in](design-elt-data-loading.md) eller [migrera](sql-data-warehouse-overview-migrate.md). Eller ta en titt på [Power BI-webbplatsen](http://www.powerbi.com/).
