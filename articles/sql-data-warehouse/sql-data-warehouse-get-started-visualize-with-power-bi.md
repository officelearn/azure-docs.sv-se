---
title: Visualisera data med Power BI Microsoft Azure
description: Visualisera SQL Data Warehouse-data med Power BI
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: eea4e3b0b1f0e4ec3eaf3e0aba8952f6693d2921
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685788"
---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI
De här självstudierna visar hur du använder Power BI för att ansluta till SQL Data Warehouse och skapa några grundläggande visualiseringar.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse förinstallerad med AdventureWorksDW-databasen. Information om hur du etablerar ett informations lager finns i [skapa ett SQL Data Warehouse](create-data-warehouse-portal.md) och välja att läsa in exempel data. Om du redan har ett data lager men inte har exempel data, kan du [läsa in informations lagret wideworldimportersdw](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Anslut till databasen
Om du vill öppna Power BI och ansluta till databasen AdventureWorksDW:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **SQL-databaser** och välj din AdventureWorks SQL Data Warehouse-databas.
   
    ![Hitta din databas](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klicka på knappen Öppna i Power BI.
   
    ![Power BI-knappen](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Du bör nu se SQL Data Warehouse-anslutningssidan som visar webbadressen för databasen. Klicka på Nästa.
   
    ![Power BI-anslutning](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Ange ditt användar namn och lösen ord för Azure SQL Server.
   
    ![Power BI inloggning](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Öppna databasen genom att klicka på AdventureWorksDW-datauppsättningen på det vänstra bladet.
   
    ![Power BI öppna AdventureWorksDW](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. skapa en rapport
Du är nu redo att använda Power BI för att analysera dina AdventureWorksDW-exempeldata. För att utföra analysen har AdventureWorksDW en vy som heter AggregateSales. Vyn innehåller några nyckelvärden för att analysera företagets försäljning.

1. Klicka på vyn AggregateSales i högra fältet för att expandera den och skapa en karta över försäljningsbelopp enligt postnummer. Klicka på kolumnerna PostalCode och SalesAmount för att markera dem.
   
    ![Power BI väljer AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automatiskt identifierade geografiska data och Lägg till dem i en karta.
   
    ![Power BI-karta](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Det här steget skapar ett stapeldiagram som visar försäljningsintäkter efter kund. Om du vill skapa stapeldiagrammet går du till den expanderade AggregateSales-vyn. Klicka på fältet SalesAmount. Dra fältet Customer Income till vänster och släpp det i axeln.
   
    ![Power BI väljer axel](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Stapeldiagrammet över vänster.
   
    ![Power BI-stapel](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Det här steget skapar ett linjediagram som visar försäljning per beställningsdatum. Om du vill skapa linje diagrammet går du till den expanderade AggregateSales-vyn. Klicka på SalesAmount och OrderDate. I kolumnen visualiseringar klickar du på ikonen linje diagram, som är den första ikonen på den andra raden under visualiseringar.
   
    ![Power BI väljer linje diagram](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Nu har du en rapport som visar tre olika datavisualiseringar.
   
    ![Power BI-linje](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Du kan spara ditt arbete när som helst genom att klicka på **Arkiv** och välja **Spara**.

## <a name="using-direct-connect"></a>Använda direkt anslutning
Precis som med Azure SQL Database kan SQL Data Warehouse Direct Connect tillåta logiska mottagnings tillsammans med analys funktionerna i Power BI. Med direkt anslutning skickas frågor tillbaka till Azure SQL Data Warehouse i real tid när du utforskar data.  Den här funktionen, kombinerat med SQL Data Warehouse skala, gör att du kan skapa dynamiska rapporter på några minuter mot flera terabyte data. Dessutom gör introduktionen av knappen Öppna i Power BI att användarna direkt kan ansluta Power BI till sina SQL Data Warehouse utan att samla in information från andra delar av Azure.

När du använder Direct Connect:

* Ange det fullständigt kvalificerade Server namnet vid anslutning.
* Se till att brand Väggs reglerna för databasen är konfigurerade för att tillåta åtkomst till Azure-tjänster.
* Varje åtgärd, som att markera en kolumn eller lägga till ett filter, frågar data lagret direkt.
* Paneler uppdateras automatiskt och ungefär var 15: e minut.
* Q & A är inte tillgängligt för direkt Connect-datauppsättningar.
* Schema ändringar införlivas automatiskt.
* Alla direkta anslutnings frågor upphör att vara efter 2 minuter.

Dessa begränsningar och anteckningar kan ändras när upplevelsen förbättras.

## <a name="next-steps"></a>Nästa steg
Nu när vi har gett dig tid att öka med exempel data, se hur man [utvecklar](sql-data-warehouse-overview-develop.md) eller [läser in](design-elt-data-loading.md). Eller ta en titt på [Power BI-webbplatsen](https://www.powerbi.com/).
