---
title: Anslut till SQL Server eller Azure SQL Database-Azure Logic Apps | Microsoft Docs
description: Hur du kommer åt och hanterar SQL-databaser lokalt eller i molnet genom att automatisera arbets flöden med Azure Logic Apps
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
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050766"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Anslut till SQL Server eller Azure SQL Database från Azure Logic Apps

Den här artikeln visar hur du kan komma åt data i din SQL-databas inifrån en Logic-app med SQL Server-anslutningen. På så sätt kan du automatisera uppgifter, processer och arbets flöden som hanterar dina SQL-data och-resurser genom att skapa Logic Apps. Anslutningen fungerar både för [SQL Server lokalt](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) och för [Azure SQL Database i molnet](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Du kan skapa Logi Kap par som körs när de utlöses av händelser i din SQL-databas eller i andra system, till exempel Dynamics CRM Online. Dina Logi Kap par kan också hämta, infoga och ta bort data tillsammans med att köra SQL-frågor och lagrade procedurer. Du kan till exempel bygga en Logic app som automatiskt söker efter nya poster i Dynamics CRM Online, lägger till objekt i din SQL-databas för nya poster och sedan skickar e-postaviseringar.

Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app. Information om anslutningsspecifika teknisk information finns i referens för [SQL Server Connector](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Förutsättningar

* Den Logic-app där du behöver åtkomst till din SQL-databas. Om du vill starta din Logic-app med en SQL-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* En [Azure SQL-databas](../sql-database/sql-database-get-started-portal.md) eller en [SQL Server databas](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Dina tabeller måste innehålla data så att din Logi Kap par kan returnera resultat vid anrop av åtgärder. Om du skapar en Azure SQL Database kan du använda exempel databaser, som ingår. 

* SQL Server-namnet, databas namnet, ditt användar namn och ditt lösen ord. Du behöver dessa autentiseringsuppgifter så att du kan auktorisera din logik för att få åtkomst till din SQL Server. 

  * För Azure SQL Database kan du hitta informationen i anslutnings strängen eller i Azure Portal under SQL Database egenskaper:

    "Server = TCP: <*yourServerName*>. Database. Windows. net, 1433; Initial Catalog = <*yourDatabaseName*>; Behåll säkerhets information = falskt; Användar-ID = <*yourUserName*>; Password = <*yourPassword*>; MultipleActiveResultSets = falskt; Kryptera = sant; TrustServerCertificate = falskt; Timeout för anslutning = 30; "

  * För SQL Server kan du hitta informationen i anslutnings strängen: 

    "Server = <*yourServerAddress*>;D atabase = <*yourDatabaseName*>; Användar-ID = <*yourUserName*>; Password = <*yourPassword*>; "

* Innan du kan ansluta Logi Kap par till lokala system, till exempel SQL Server, måste du [Konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du välja gatewayen när du skapar SQL-anslutningen för din Logic app.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Lägg till SQL-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [](../logic-apps/logic-apps-overview.md#logic-app-concepts)utlösare som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

1. I Azure Portal eller Visual Studio skapar du en tom Logic-app som öppnas Logic Apps designer. I det här exemplet används Azure Portal.

2. I rutan Sök anger du "SQL Server" som filter. Välj den SQL-utlösare som du vill använda från listan utlösare. 

   I det här exemplet väljer du den här utlösaren: **SQL Server – när ett objekt skapas**

   ![Välj "SQL Server – när ett objekt skapas" utlösare](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Om du uppmanas att ange anslutnings information [skapar du din SQL-anslutning nu](#create-connection). 
   Eller, om anslutningen redan finns, väljer du det **tabell namn** som du vill använda i listan.

   ![Välj tabell](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Ange egenskaper för **intervall** och **frekvens** , som anger hur ofta din Logic App kontrollerar tabellen.

   I det här exemplet kontrol leras bara den valda tabellen, ingen annan. 
   Om du vill göra något mer intressant lägger du till åtgärder som utför de uppgifter du vill. 
   
   Om du till exempel vill visa det nya objektet i tabellen kan du lägga till andra åtgärder, till exempel skapa en fil som innehåller fält från tabellen, och sedan skicka e-postaviseringar. 
   Mer information om andra åtgärder för den här anslutningen eller andra anslutningar finns i [Logic Apps kopplingar](../connectors/apis-list.md).

5. När du är klar väljer du **Spara**i verktygsfältet designer. 

   I det här steget kan du automatiskt publicera din Logic app Live i Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Lägg till SQL-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar Logic-appen med [upprepnings](../connectors/connectors-native-recurrence.md)utlösaren och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. Öppna din Logic app i Logic Apps designer i Azure Portal eller Visual Studio. I det här exemplet används Azure Portal.

2. I Logic App Designer går du till utlösaren eller åtgärden och väljer **nytt steg** > **Lägg till en åtgärd**.

   ![Välj "nytt steg", "Lägg till en åtgärd"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

2. I rutan Sök anger du "SQL Server" som filter. I listan åtgärder väljer du valfri SQL-åtgärd som du vill använda. 

   I det här exemplet väljer du den här åtgärden, som hämtar en enda post: **SQL Server-Hämta rad**

   ![Ange "SQL Server" och välj "SQL Server-get Row"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Om du uppmanas att ange anslutnings information [skapar du din SQL-anslutning nu](#create-connection). 
   Om anslutningen finns väljer du ett **tabell namn**och anger **rad-ID** : t för den post som du vill använda.

   ![Ange tabell namn och rad-ID](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   I det här exemplet returneras bara en rad från den valda tabellen, inget annat. 
   Om du vill visa data i den här raden kan du lägga till andra åtgärder som skapar en fil med fält från raden för senare granskning och lagra filen i ett moln lagrings konto. Mer information om andra åtgärder i denna anslutning eller andra anslutningar finns i [Logic Apps kopplingar](../connectors/apis-list.md).

4. När du är klar väljer du **Spara**i verktygsfältet designer. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Hantera Mass data

Ibland kan du behöva arbeta med resultat uppsättningar så att kopplingen inte returnerar alla resultat samtidigt, eller om du vill ha bättre kontroll över storlek och struktur för dina resultat uppsättningar. Här är några exempel på hur du kan hantera sådana stora resultat uppsättningar:

* För att hjälpa dig att hantera resultat som mindre mängder, aktiverar du *sid brytning*. Mer information finns i [Hämta Mass data, poster och objekt med hjälp av sid brytning](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Skapa en lagrad procedur som ordnar resultatet på det sätt som du vill.

  När du hämtar eller infogar flera rader kan din Logi Kap par iterera genom de här raderna genom att använda en [*loop till*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) i dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). 
  Men när din Logic-app måste arbeta med post uppsättningar så stor, till exempel tusentals eller miljon tals rader, vill du minimera kostnaderna som uppstår vid anrop till databasen.

  För att ordna resultaten på det sätt som du vill ha, kan du skapa en [*lagrad procedur*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) som körs i SQL-instansen och som använder instruktionen **Select-order by** . 
  Den här lösningen ger dig större kontroll över storleken och strukturen på resultaten. 
  Din Logi Kap par anropar den lagrade proceduren med hjälp av åtgärden SQL Server Connector: s **Kör lagrad procedur** .

  Information om lösningar finns i följande artiklar:

  * [SQL-sid brytning för Mass data överföring med Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY-sats](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Teknisk information om den här anslutningens utlösare, åtgärder och begränsningar finns i [kopplingens referens information](/connectors/sql/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

