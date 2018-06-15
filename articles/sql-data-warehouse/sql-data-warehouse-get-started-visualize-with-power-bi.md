---
title: Visualisera SQL Data Warehouse-data med Power BI Microsoft Azure
description: Visualisera SQL Data Warehouse-data med Power BI
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 52581a87caac419a79caab647cc9c5a4ee7453ba
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601585"
---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI
De här självstudierna visar hur du använder Power BI för att ansluta till SQL Data Warehouse och skapa några grundläggande visualiseringar.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse förinstallerad med AdventureWorksDW-databasen. Om du vill etablera ett datalager, se [skapa ett SQL Data Warehouse](create-data-warehouse-portal.md) och väljer att läsa in exempeldata. Om du redan har ett data warehouse men inte har exempeldata, kan du [ladda WideWorldImportersDW](load-data-wideworldimportersdw.md).

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

2. Det här steget skapar ett stapeldiagram som visar försäljningsintäkter efter kund. Om du vill skapa stapeldiagrammet går du till den expanderade AggregateSales-vyn. Klicka på fältet SalesAmount. Dra fältet Customer Income till vänster och släpp det i axeln.
   
    ![Powerbi väljer axeln](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Liggande diagram över till vänster.
   
    ![Power BI-stapel](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Det här steget skapar ett linjediagram som visar försäljning per beställningsdatum. Om du vill skapa linjediagrammet, går du till den expanderade AggregateSales-vyn. Klicka på SalesAmount och OrderDate. I kolumnen visualiseringar klickar du på linjediagram-ikonen, vilket är den första ikonen i den andra raden under visualiseringar.
   
    ![Powerbi väljer linjediagram](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Nu har du en rapport som visar tre olika datavisualiseringar.
   
    ![Power BI-linje](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Du kan spara ditt arbete när som helst genom att klicka på **Arkiv** och välja **Spara**.

## <a name="using-direct-connnect"></a>Med hjälp av direkt koppla de olika processtegen
Som med Azure SQL Database kan SQL Data Warehouse Direct Connect logiska pushdown tillsammans med analytiska funktionerna i Power BI. Med Direct Connect skickas frågor tillbaka till din Azure SQL Data Warehouse i realtid att utforska data.  Den här funktionen kan kombineras med skalan för SQL Data Warehouse, kan du skapa dynamiska rapporter i minuter mot terabyte data. Dessutom tillåter introduktionen av funktionen Öppna i Power BI-knappen användare att ansluta Power BI direkt till deras SQL Data Warehouse utan att samla in information från andra delar av Azure.

När du använder direkt ansluta:

* Ange det fullständigt kvalificerade servernamnet vid anslutning.
* Se till att brandväggsreglerna för databasen är konfigurerade att tillåta åtkomst till Azure-tjänster.
* Varje åtgärd som att markera en kolumn, eller lägga till ett filter, frågar direkt datalagret.
* Paneler uppdateras automatiskt och ungefär var 15: e minut.
* Frågor och svar är inte tillgängliga för datauppsättningar som Direct Connect.
* Schemaändringar ingår automatiskt.
* Alla frågor med Direct Connect gör timeout efter två minuter.

Dessa begränsningar och anteckningar kan ändras när upplevelser förbättra.

## <a name="next-steps"></a>Nästa steg
Nu när du har värmt upp med exempeldata, kan du se mer information om att [utveckla](sql-data-warehouse-overview-develop.md), [läsa in](design-elt-data-loading.md) eller [migrera](sql-data-warehouse-overview-migrate.md). Eller ta en titt på [Power BI-webbplatsen](http://www.powerbi.com/).
