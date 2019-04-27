---
title: Ansluta till SQLServer eller Azure SQL-databas – Azure Logic Apps | Microsoft Docs
description: Så här att komma åt och hantera SQL-databaser lokalt eller i molnet genom att automatisera arbetsflöden med Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 29d53c7fbd26d3c8e2356ce82ff25c7e1b165728
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541155"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Ansluta till SQLServer eller Azure SQL Database från Azure Logic Apps

Den här artikeln visar hur du kan komma åt data i din SQL-databas i en logikapp med SQL Server-anslutningen. På så sätt kan du automatisera uppgifter, processer och arbetsflöden som hanterar din SQL-data och resurser genom att skapa logikappar. Anslutningen fungerar för både [SQL Server lokalt](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) och för [Azure SQL Database i molnet](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Du kan skapa logikappar som körs när den utlöses av händelser i din SQL-databas eller i andra system, som Dynamics CRM Online. Dina logic apps kan också hämta, infoga och ta bort data samt köra SQL-frågor och lagrade procedurer. Du kan till exempel skapa en logikapp som kontrollerar automatiskt nya poster i Dynamics CRM Online, lägger till objekt till din SQL-databas för eventuella nya poster och skickar sedan e-postaviseringar.

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Specifika teknisk information finns i den <a href="https://docs.microsoft.com/connectors/sql/" target="blank">referens för SQL Server-anslutningen</a>.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Logikappen där du behöver åtkomst till din SQL-databas. Om du vill börja din logikapp med en SQL-utlösare, som du behöver en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* En [Azure SQL-databas](../sql-database/sql-database-get-started-portal.md) eller en [SQL Server-databas](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Dina tabeller måste ha data så att logikappen kan returnera resultat när du anropar åtgärder. Om du skapar en Azure SQL Database kan använda du exempeldatabaser som ingår. 

* SQL-servernamnet, databasnamnet, användarnamnet och lösenordet. Du behöver dessa autentiseringsuppgifter så att du kan auktorisera din logik för att ansluta till SQLServer. 

  * Du kan hitta detaljerna i anslutningssträngen eller i Azure-portalen under Egenskaper för SQL-databas för Azure SQL Database:

    ”Server = tcp: <*yourServerName*>. database.windows.net,1433;Initial Catalog = <*yourDatabaseName*>; Spara säkerhetsinformation = False; Användar-ID = <*användarnamn*>; Lösenord = <*yourPassword*>; För MultipleActiveResultSets = False; Kryptera = True; TrustServerCertificate = False; Tidsgräns för anslutning = 30 ”;

  * Du kan hitta detaljerna i anslutningssträngen för SQL Server: 

    ”Server = <*yourServerAddress*>; Database = <*yourDatabaseName*>; Användar-Id = <*användarnamn*>; Lösenord = <*yourPassword*> ”;

* Innan du kan ansluta logikappar till den lokala system, t.ex SQL Server, måste du [ställa in en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du kan välja gatewayen när du skapar SQL-anslutning för din logikapp.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Lägg till SQL-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

1. I Azure portal eller Visual Studio, skapar du en tom logikapp som öppnas Logic Apps Designer. Det här exemplet används Azure-portalen.

2. I sökrutan anger du ”SQLServer” som filter. Välj SQL-utlösare som du vill använda från listan över utlösare. 

   Välj den här utlösaren i det här exemplet: **SQLServer - när ett objekt skapas**

   ![Välj ”SQLServer - när ett objekt skapas” utlösare](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Om du uppmanas anslutningsinformation [skapar din SQL-anslutning nu](#create-connection). 
   Om anslutningen redan finns, väljer du den **tabellnamn** som du vill använda från listan.

   ![Välj tabell](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Ange den **intervall** och **frekvens** egenskaper som anger hur ofta din logikapp kontrollerar tabellen.

   Det här exemplet kontrollerar endast den markerade tabellen, inget annat. 
   Lägga till åtgärder som utför uppgifter som du vill för att göra något mer intressant. 
   
   Till exempel för att visa det nya objektet i tabellen, kan du lägga till andra åtgärder, till exempel skapa en fil som innehåller fält från tabellen och sedan skicka e-postaviseringar. 
   Läs om andra åtgärder för den här anslutningen eller andra anslutningsappar i [Logic Apps-anslutningsprogram](../connectors/apis-list.md).

5. När du är klar på verktygsfältet för appdesignern väljer **spara**. 

   Det här steget aktiverar automatiskt och publicerar logikappen live i Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Lägg till SQL-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med den [upprepningsutlösare](../connectors/connectors-native-recurrence.md), och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. Öppna logikappen i Logic Apps Designer i Azure portal eller Visual Studio. Det här exemplet används Azure-portalen.

2. I Logic App Designer, utlösaren eller åtgärden, väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Välj ”nytt steg”, ”Lägg till en åtgärd”](./media/connectors-create-api-sqlazure/add-action.png)
   
   Flytta musen över den anslutande pilen för att lägga till en åtgärd mellan befintliga steg. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

2. I sökrutan anger du ”SQLServer” som filter. Välj alla SQL-åtgärder som du vill från åtgärdslistan över. 

   Välj den här åtgärden, som hämtar en enskild post i det här exemplet: **SQLServer – hämta rad**

   ![Ange ”SQLServer”, Välj ”SQLServer - hämta rad”](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Om du uppmanas anslutningsinformation [skapar din SQL-anslutning nu](#create-connection). 
   Om anslutningen finns, väljer du en **tabellnamn**, och ange den **rad-ID** för den post som du vill.

   ![Ange tabellnamnet och rad-ID](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Det här exemplet returnerar bara en rad från den markerade tabellen, inget annat. 
   Om du vill visa data i den här raden kan du lägga till andra åtgärder som skapar en fil med fält från raden för senare granskning och lagra filen i ett molnlagringskonto. Läs om andra åtgärder i den här anslutningen eller andra anslutningsappar i [Logic Apps-anslutningsprogram](../connectors/apis-list.md).

4. När du är klar på verktygsfältet för appdesignern väljer **spara**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Bearbeta data i grupp

När du arbetar med resultatmängder så stor att anslutningen inte returnerar alla resultat på samma gång eller om du vill bättre kontroll över storlek och strukturen för din resultatuppsättningar kan du använda *sidbrytning*, som hjälper dig att hantera de resultat som mindre uppsättningar. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

När du får eller hur du infogar flera rader, din logikapp kan gå igenom dessa objekt med hjälp av en [ *until-loop* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) inom dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). Men ibland logikappen har att arbeta med postuppsättningar så stor, till exempel tusentals eller miljontals rader, som du vill minimera kostnaderna för anrop till databasen. 

Skapa i stället en <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *lagrade proceduren* </a> som körs i din SQL-instans och använder den **Välj - ORDER BY** -uttrycket för att ordna resultaten som du vill. Den här lösningen ger dig större kontroll över storlek och strukturen för dina resultat. Logikappen anropar den lagrade proceduren med hjälp av SQL Server-anslutningen **köra den lagrade proceduren** åtgärd. 

Lösningsinformation finns i följande artiklar:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL-sidbrytning för bulk-dataöverföring med Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Välj - ORDER BY-satser</a>

## <a name="connector-specific-details"></a>Information om specifika

Teknisk information om den här kopplingen utlösare, åtgärder och begränsningar finns i den [kopplingens Referensinformation](/connectors/sql/). 

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)

