---
title: Ansluta till SQL Server eller Azure SQL Database
description: Automatisera uppgifter för SQL-databaser lokalt eller i molnet med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789211"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatisera arbetsflöden för SQL Server eller Azure SQL Database med hjälp av Azure Logic Apps

Den här artikeln visar hur du kan komma åt data i SQL-databasen inifrån en logikapp med SQL Server-anslutningen. På så sätt kan du automatisera uppgifter, processer eller arbetsflöden som hanterar dina SQL-data och resurser genom att skapa logikappar. SQL [Server-anslutningen](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) fungerar både för lokala SQL Server och för [molnbaserad Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Du kan skapa logikappar som körs när de utlöses av händelser i SQL-databasen eller i andra system, till exempel Dynamics CRM Online. Dina logikappar kan också hämta, infoga och ta bort data tillsammans med att köra SQL-frågor och lagrade procedurer. Du kan till exempel skapa en logikapp som automatiskt söker efter nya poster i Dynamics CRM Online, lägger till objekt i SQL-databasen för nya poster och skickar sedan e-postaviseringar om de tillagda objekten.

Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Anslutningsspecifik teknisk information, begränsningar och kända problem finns på [referenssidan för SQL Server-anslutning](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En [SQL Server-databas](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) eller [Azure SQL-databas](../sql-database/sql-database-get-started-portal.md)

  Tabellerna måste ha data så att logikappen kan ge resultat när du ringer. Om du skapar en Azure SQL-databas kan du använda exempeldatabaser som ingår.

* Ditt SQL-servernamn, databasnamn, ditt användarnamn och ditt lösenord. Du behöver dessa autentiseringsuppgifter så att du kan auktorisera din logik för att komma åt din SQL-server.

  * För SQL Server hittar du dessa uppgifter i anslutningssträngen:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * För Azure SQL Database hittar du dessa information i anslutningssträngen eller i Azure-portalen under SQL Database-egenskaperna:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Den [lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) som är installerad på en lokal dator och en [Azure-datagatewayresurs som skapats i Azure-portalen](../logic-apps/logic-apps-gateway-connection.md) för dessa scenarier:

  * Dina logikappar körs inte i en [integrationstjänstmiljö (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * *Logikapparna* körs i en integrationstjänstmiljö, men du måste använda Windows-autentisering för SQL Server-anslutningen. I det här scenariot använder du SQL Server-anslutningens icke-ISE-version tillsammans med datagatewayen eftersom ISE-versionen inte stöder Windows-autentisering.

* Logikappen där du behöver åtkomst till din SQL-databas. Om du vill starta logikappen med en SQL-utlösare behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Lägga till en SQL-utlösare

I Azure Logic Apps måste varje logikapp starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans och börjar köra logikappens arbetsflöde.

1. Skapa en tom logikapp i Azure-portalen eller Visual Studio som öppnar Logic Apps Designer. I det här exemplet används Azure-portalen.

1. På designern, i sökrutan, ange "SQL Server" som filter. Välj den SQL-utlösare som du vill använda i listan utlösare.

   I det här exemplet används **utlösaren När ett objekt skapas.**

   ![Välj utlösaren "När ett objekt skapas"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Om du uppmanas att skapa en anslutning [skapar du SQL-anslutningen nu](#create-connection). Om anslutningen finns väljer du ett **tabellnamn**.

   ![Markera den tabell du vill använda](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Ange egenskaperna **För intervall** och **frekvens,** som anger hur ofta logikappen ska kontrollera tabellen.

   Den här utlösaren returnerar bara en rad från den valda tabellen, inget annat. Om du vill utföra andra uppgifter lägger du till andra åtgärder som utför de uppgifter du vill ha. Om du till exempel vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och sedan skicka e-postaviseringar. Mer information om andra tillgängliga åtgärder för den här kopplingen finns i [kopplingens referenssida](https://docs.microsoft.com/connectors/sql/).

1. När du är klar väljer du **Spara**i designerverktygsfältet .

   Det här steget aktiverar och publicerar automatiskt din logikapp live i Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Lägga till en SQL-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar logikappen med [upprepningsutlösaren](../connectors/connectors-native-recurrence.md)och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. Öppna logikappen i Logic Apps Designer i Azure-portalen eller Visual Studio. I det här exemplet används Azure-portalen.

1. Under utlösaren eller åtgärden där du vill lägga till SQL-åtgärden väljer du **Nytt steg**.

   ![Lägg till nytt steg i logikappen](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över anslutningspilen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**anger du "SQL Server" som filter i sökrutan. Välj den SQL-åtgärd som du vill använda i åtgärdslistan.

   I det här exemplet används åtgärden **Hämta rad,** som får en enda post.

   ![Sök efter och välj SQL "Hämta rad"-åtgärd](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Den här åtgärden returnerar bara en rad från den markerade tabellen, inget annat. Om du vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och lagra filen i ett molnlagringskonto. Mer information om andra tillgängliga åtgärder för den här kopplingen finns i [kopplingens referenssida](https://docs.microsoft.com/connectors/sql/).

1. Om du uppmanas att skapa en anslutning [skapar du SQL-anslutningen nu](#create-connection). Om anslutningen finns väljer du ett **tabellnamn**och anger **rad-ID:t** för den post du vill använda.

   ![Ange tabellnamn och rad-ID](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

   Det här steget aktiverar och publicerar automatiskt din logikapp live i Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Hantera massdata

Ibland måste du arbeta med resultatuppsättningar så stora att kopplingen inte returnerar alla resultat samtidigt, eller om du vill ha bättre kontroll över storlek och struktur för dina resultatuppsättningar. Här är några sätt som du kan hantera så stora resultatuppsättningar:

* Aktivera *sidnumrering*för att hjälpa dig att hantera resultat som mindre uppsättningar. Mer information finns i [Hämta massdata, poster och objekt med hjälp av sidnumrering](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Skapa en lagrad procedur som ordnar resultaten som du vill.

  När du hämtar eller infogar flera rader kan logikappen iterera genom dessa rader med hjälp av en [*tillslinga*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) inom dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). Men när logikappen måste arbeta med postuppsättningar så stora, till exempel tusentals eller miljontals rader, att du vill minimera kostnaderna till följd av anrop till databasen.

  Om du vill ordna resultaten på det sätt du vill kan du skapa en [*lagrad procedur*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) som körs i DIN SQL-instans och använder SELECT - ORDER BY-satsen. **SELECT - ORDER BY** Denna lösning ger dig mer kontroll över storleken och strukturen på dina resultat. Logikappen anropar den lagrade proceduren med hjälp av SQL Server-anslutningens **åtgärden Kör lagrad procedur.**

  Mer information om lösningar finns i följande artiklar:

  * [SQL-sidnumrering för massdataöverföring med Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [VÄLJ - ORDER EFTER SATS](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Anslutningsspecifik information

Teknisk information om den här kopplingens utlösare, åtgärder och begränsningar finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [kopplingar för Azure Logic Apps](../connectors/apis-list.md)
