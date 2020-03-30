---
title: Visualisera data med Power BI Microsoft Azure
description: Visualisera SQL Data Warehouse-data med Power BI
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350952"
---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI
De här självstudierna visar hur du använder Power BI för att ansluta till SQL Data Warehouse och skapa några grundläggande visualiseringar.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Krav
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse förinstallerad med AdventureWorksDW-databasen. Information om hur du etablerar ett informationslager finns i [Skapa ett SQL-datalager](create-data-warehouse-portal.md) och välja att läsa in exempeldata. Om du redan har ett informationslager men inte har exempeldata kan du [läsa in WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Anslut till databasen
Om du vill öppna Power BI och ansluta till databasen AdventureWorksDW:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **SQL-databaser** och välj din AdventureWorks SQL Data Warehouse-databas.
   
    ![Hitta din databas](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klicka på knappen Öppna i Power BI.
   
    ![Power BI-knappen](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Du bör nu se SQL Data Warehouse-anslutningssidan som visar webbadressen för databasen. Klicka på Nästa.
   
    ![Power BI-anslutning](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Ange användarnamn och lösenord för Din Azure SQL-server.
   
    ![Logga in för Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Om du vill öppna databasen klickar du på AdventureWorksDW-datauppsättningen på det vänstra bladet.
   
    ![Power BI öppna AdventureWorksDW](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Skapa en rapport
Du är nu redo att använda Power BI för att analysera dina AdventureWorksDW-exempeldata. För att utföra analysen har AdventureWorksDW en vy som heter AggregateSales. Vyn innehåller några nyckelvärden för att analysera företagets försäljning.

1. Klicka på vyn AggregateSales i högra fältet för att expandera den och skapa en karta över försäljningsbelopp enligt postnummer. Klicka på kolumnerna PostalCode och SalesAmount för att markera dem.
   
    ![Power BI väljer AggregateSales](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI kände automatiskt igen geografiska data och placerar dem i en karta åt dig.
   
    ![Power BI-karta](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Det här steget skapar ett stapeldiagram som visar försäljningsintäkter efter kund. Om du vill skapa stapeldiagrammet går du till den utökade aggregatesalesvyn. Klicka på fältet SalesAmount. Dra fältet Customer Income till vänster och släpp det i axeln.
   
    ![Power BI väljer axel](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Stapeldiagrammet till vänster.
   
    ![Power BI-stapel](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Det här steget skapar ett linjediagram som visar försäljning per beställningsdatum. Om du vill skapa linjediagrammet går du till den utökade aggregatesalesvyn. Klicka på SalesAmount och OrderDate. Klicka på ikonen Linjediagram i kolumnen Visualiseringar, som är den första ikonen på den andra raden under visualiseringar.
   
    ![Power BI väljer linjediagram](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Nu har du en rapport som visar tre olika datavisualiseringar.
   
    ![Power BI-linje](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Du kan spara ditt arbete när som helst genom att klicka på **Arkiv** och välja **Spara**.

## <a name="using-direct-connect"></a>Använda Direct Connect
Precis som med Azure SQL Database tillåter SQL Data Warehouse Direct Connect logisk pushdown tillsammans med de analytiska funktionerna i Power BI. Med Direct Connect skickas frågor tillbaka till ditt Azure SQL Data Warehouse i realtid när du utforskar data.  Med den här funktionen, i kombination med omfattningen av SQL Data Warehouse, kan du skapa dynamiska rapporter på några minuter mot terabyte data. Dessutom tillåter introduktionen av knappen Öppna i Power BI användare att direkt ansluta Power BI till sitt SQL Data Warehouse utan att samla in information från andra delar av Azure.

När du använder Direct Connect:

* Ange det fullständigt kvalificerade servernamnet vid anslutning.
* Kontrollera att brandväggsregler för databasen är konfigurerade för att tillåta åtkomst till Azure-tjänster.
* Varje åtgärd, till exempel att välja en kolumn eller lägga till ett filter, frågar direkt informationslagret.
* Paneler uppdateras automatiskt och ungefär var 15:e minut.
* Q&A är inte tillgängligt för Direct Connect-datauppsättningar.
* Schemaändringar infogas automatiskt.
* Alla Direct Connect-frågor kommer att time out efter 2 minuter.

Dessa begränsningar och anteckningar kan ändras när erfarenheterna förbättras.

## <a name="next-steps"></a>Nästa steg
Nu när vi har gett dig lite tid att värma upp med provdata, se hur du [utvecklar](sql-data-warehouse-overview-develop.md) eller [laddar](design-elt-data-loading.md). Eller ta en titt på [Power BI-webbplatsen](https://www.powerbi.com/).
