---
title: Ansluta till SQLServer eller Azure SQL Database - Azure Logikappar | Microsoft Docs
description: Hur du åt och hantera SQL-databaser lokalt eller i molnet genom att automatisera arbetsflöden med Azure Logikappar
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
ms.openlocfilehash: dccb91c782408a5fed5c3ef1b68f9918823ce402
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296296"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Ansluta till SQLServer eller Azure SQL-databas från Azure Logikappar

Den här artikeln visar hur du kan komma åt data i SQL-databasen från inuti en logikapp med SQL Server-anslutningen. På så sätt kan du automatisera uppgifter, processer och arbetsflöden som hanterar din SQL-data och resurser genom att skapa logikappar. Kopplingen fungerar för både [SQL Server på lokala](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) och [Azure SQL Database i molnet](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Du kan skapa logikappar som körs när den utlöses av händelser i din SQL-databas eller i andra system, till exempel Dynamics CRM Online. Dina logic apps kan också hämta, infoga och ta bort data och köra SQL-frågor och lagrade procedurer. Du kan till exempel skapa en logikapp som automatiskt söker efter nya poster i Dynamics CRM Online, lägger till objekt till SQL-databasen för nya poster, och skickar sedan e-postaviseringar.

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du har använt logikappar granska [vad är Azure Logikappar](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Connector-specifik teknisk information finns i <a href="https://docs.microsoft.com/connectors/sql/" target="blank">referens för SQL Server-koppling</a>.

## <a name="prerequisites"></a>Förutsättningar

* Logikappen där du behöver åtkomst till SQL-databasen. Om du vill starta din logikapp med en SQL-utlösare, behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* En [Azure SQL database](../sql-database/sql-database-get-started-portal.md) eller en [SQL Server-databas](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Tabellerna måste ha data så att din logikapp kan returnera resultat vid anrop av åtgärder. Om du skapar en Azure SQL Database, använder du exemplet databaser som ingår. 

* SQL server-namnet, databasnamnet, användarnamnet och lösenordet. Du behöver dessa autentiseringsuppgifter så att du kan godkänna din logik för att ansluta till SQL-servern. 

  * Du kan hitta detaljerna i anslutningssträngen eller i Azure portal under Egenskaper för SQL-databas för Azure SQL Database:

    ”Server = tcp: <*yourServerName*>. database.windows.net,1433;Initial katalog = <*yourDatabaseName*>; Spara säkerhetsinformation = False; Användar-ID = <*användarnamn*>; Lösenord = <*yourPassword*>; MultipleActiveResultSets = False; Kryptera = True; TrustServerCertificate = False; Timeout för anslutningen = 30 ”;

  * Du kan hitta detaljerna i anslutningssträngen för SQL Server: 

    ”Server = <*yourServerAddress*>; Database = <*yourDatabaseName*>; Användar-Id = <*användarnamn*>; Lösenord = <*yourPassword*> ”;

* Innan du kan ansluta logikappar till lokalt system, t.ex SQL Server, måste du [konfigurera ett lokalt datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du välja gatewayen när du skapar SQL-anslutning för din logikapp.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Lägga till SQL-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett specifikt villkor uppfylls. Varje gång utlösaren-utlöses Logic Apps motorn skapar en logik app-instansen och börjar köras appens arbetsflöde.

1. Skapa en tom logikapp, vilket öppnar Designer för Logic Apps i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. I sökrutan anger du ”SQLServer” som filter. Välj SQL-utlösare som du vill använda från listan över utlösare. 

   Det här exemplet väljer du den här utlösaren: **SQL Server - när ett objekt skapas**

   ![Välj ”SQLServer - när ett objekt skapas” utlösare](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Om du uppmanas anslutningsinformation [skapa SQL-anslutning nu](#create-connection). 
   Om det finns redan en anslutning, väljer du den **tabellnamn** som du vill använda i listan.

   ![Välj tabell](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Ange den **intervall** och **frekvens** egenskaper som anger hur ofta logikappen kontrollerar tabellen.

   Det här exemplet kontrollerar bara den markerade tabellen inget annat. 
   Lägg till åtgärder som utför uppgifter som du vill använda för att göra något mer intressant. 
   
   Till exempel om du vill visa det nya objektet i tabellen, kan du lägga till andra åtgärder, exempelvis skapa en fil som innehåller fält från tabellen och skicka e-postaviseringar. 
   Mer information om andra åtgärder för den här anslutningen eller andra kopplingar, se [Logic Apps kopplingar](../connectors/apis-list.md).

5. När du är klar i verktygsfältet designer väljer **spara**. 

   Det här steget aktiverar automatiskt och publicerar logikappen live i Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Lägg till SQL-åtgärd

I Azure Logikappar en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med den [upprepning utlösaren](../connectors/connectors-native-recurrence.md), och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. Öppna logikappen i designern för Logic Apps i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. I logik App Designer under utlösare eller åtgärd, Välj **nytt steg** > **lägga till en åtgärd**.

   ![Välj ”nytt steg”, ”Lägg till en åtgärd”](./media/connectors-create-api-sqlazure/add-action.png)
   
   Om du vill lägga till en åtgärd mellan befintliga steg, håller du muspekaren över anslutande pilen. 
   Välj på plustecknet (**+**) som visas och sedan välja **lägga till en åtgärd**.

2. I sökrutan anger du ”SQLServer” som filter. Välj alla SQL-åtgärder som du vill använda från listan över åtgärder. 

   I det här exemplet väljer du den här åtgärden, som hämtar en enskild post: **SQL Server - Get-raden**

   ![Skriver ”SQLServer”, markerar ”SQLServer - Get rad”](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Om du uppmanas anslutningsinformation [skapa SQL-anslutning nu](#create-connection). 
   Om anslutningen finns, väljer du en **tabellnamn**, och ange den **rad-ID** för den post som du vill använda.

   ![Ange namnet på tabellen och rad-ID](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Det här exemplet returnerar bara en rad från den valda tabellen inget annat. 
   Om du vill visa data i den här raden kan du lägga till andra åtgärder som skapar en fil med fält från raden för senare granskning och spara filen i ett lagringskonto i molnet. Mer information om andra åtgärder i den här anslutningen eller andra kopplingar, se [Logic Apps kopplingar](../connectors/apis-list.md).

4. När du är klar i verktygsfältet designer väljer **spara**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Bearbeta data i grupp

När du arbetar med resultatmängder så stort att anslutningen inte returnerar alla resultat på samma gång eller om du vill bättre kontroll över storlek och struktur för din resultatuppsättningar kan du använda *sidbrytning*, som hjälper dig att hantera de resultat som mindre uppsättningar. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

När komma eller infoga flera rader, din logikapp kan gå igenom dessa objekt med hjälp av en [ *tills loop* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) inom dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). Men ibland logikappen måste arbeta med postuppsättningar så stor som tusentals eller miljoner rader som du vill minimera kostnaderna för anrop till databasen. 

Skapa i stället en <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *lagrade proceduren* </a> som körs i din SQL-instans och använder den **Välj - ORDER BY** instruktionen för att organisera resultatet som du vill. Den här lösningen ger mer kontroll över storleken och strukturen för dina resultat. Din logikapp anropar den lagrade proceduren med hjälp av SQL Server-anslutningen **köra den lagrade proceduren** åtgärd. 

Lösningsinformation finns i följande artiklar:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL-sidbrytning för bulk dataöverföring med Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Välj - ORDER BY-satser</a>

## <a name="connector-specific-details"></a>Connector-specifik information

Teknisk information om den här anslutningen utlösare, åtgärder och begränsningar finns i [kopplingens Referensinformation](/connectors/sql/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps kopplingar](../connectors/apis-list.md)

