---
title: Anslut till SQL Server eller Azure SQL Database-Azure Logic Apps
description: Automatisera aktiviteter för SQL-databaser lokalt eller i molnet med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 10/14/2019
ms.openlocfilehash: 880ae4b661d247889815fc5b9ad08a759fe0aa5b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161608"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatisera arbets flöden för SQL Server eller Azure SQL Database med Azure Logic Apps

Den här artikeln visar hur du kan komma åt data i din SQL-databas inifrån en Logic-app med SQL Server-anslutningen. På så sätt kan du automatisera aktiviteter, processer eller arbets flöden som hanterar dina SQL-data och-resurser genom att skapa Logic Apps. SQL Server Connector fungerar både [lokalt SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) och för [molnbaserad Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Du kan skapa Logi Kap par som körs när de utlöses av händelser i din SQL-databas eller i andra system, till exempel Dynamics CRM Online. Dina Logic Apps kan också hämta, infoga och ta bort data tillsammans med SQL-frågor och lagrade procedurer. Du kan till exempel bygga en Logi Kap par som automatiskt söker efter nya poster i Dynamics CRM Online, lägger till objekt i din SQL-databas efter nya poster och skickar sedan e-postaviseringar om de tillagda objekten.

Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Information om anslutningsspecifika teknisk information, begränsningar och kända problem finns på [referens sidan för SQL Server koppling](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En [SQL Server databas](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) eller [Azure SQL-databas](../sql-database/sql-database-get-started-portal.md)

  Dina tabeller måste innehålla data så att din Logi Kap par kan returnera resultat vid anrop av åtgärder. Om du skapar en Azure SQL Database kan du använda exempel databaser, som ingår.

* SQL Server-namnet, databas namnet, ditt användar namn och ditt lösen ord. Du behöver dessa autentiseringsuppgifter så att du kan auktorisera din logik för att få åtkomst till din SQL Server.

  * För SQL Server kan du hitta informationen i anslutnings strängen:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * För Azure SQL Database kan du hitta informationen i anslutnings strängen eller i Azure Portal under SQL Database egenskaper:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Innan du kan ansluta Logi Kap par till lokala system som SQL Server måste du [Konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du välja gatewayen när du skapar SQL-anslutningen för din Logic app.

* Den Logic-app där du behöver åtkomst till din SQL-databas. Om du vill starta din Logic-app med en SQL-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Lägg till en SQL-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts)som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång som utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra ditt Logic Apps-arbetsflöde.

1. I Azure Portal eller Visual Studio skapar du en tom Logic-app som öppnas Logic Apps designer. I det här exemplet används Azure Portal.

1. Skriv "SQL Server" som filter i rutan Sök i designern. Välj den SQL-utlösare som du vill använda från listan utlösare.

   I det här exemplet används utlösaren **när ett objekt skapas** .

   ![Välj när ett objekt skapas "utlösare](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Om du uppmanas att skapa en anslutning [skapar du din SQL-anslutning nu](#create-connection). Om anslutningen finns väljer du ett **tabell namn**.

   ![Välj den tabell som du vill använda](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Ange egenskaper för **intervall** och **frekvens** , som anger hur ofta din Logic App kontrollerar tabellen.

   Den här utlösaren returnerar bara en rad från den valda tabellen, ingen annan. Om du vill utföra andra uppgifter lägger du till andra åtgärder som utför de uppgifter du vill. Om du till exempel vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och sedan skicka e-postaviseringar. Information om andra tillgängliga åtgärder för den här anslutningen finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sql/).

1. När du är klar väljer du **Spara**i verktygsfältet designer.

   I det här steget kan du automatiskt publicera din Logic app Live i Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Lägg till en SQL-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar Logic-appen med [upprepnings utlösaren](../connectors/connectors-native-recurrence.md)och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. Öppna din Logic app i Logic Apps designer i Azure Portal eller Visual Studio. I det här exemplet används Azure Portal.

1. Under utlösaren eller åtgärden där du vill lägga till SQL-åtgärden väljer du **nytt steg**.

   ![Lägg till nytt steg i din Logic app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**i rutan Sök anger du "SQL Server" som filter. I listan åtgärder väljer du den SQL-åtgärd som du vill använda.

   I det här exemplet används åtgärden **Hämta rad** som hämtar en enda post.

   ![Sök efter och välj SQL-åtgärden Hämta rad](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Den här åtgärden returnerar bara en rad från den valda tabellen, inget annat. Om du vill visa data i den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och lagra filen i ett moln lagrings konto. Information om andra tillgängliga åtgärder för den här anslutningen finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sql/).

1. Om du uppmanas att skapa en anslutning [skapar du din SQL-anslutning nu](#create-connection). Om anslutningen finns väljer du ett **tabell namn**och anger **rad-ID** : t för den post som du vill använda.

   ![Ange tabell namn och rad-ID](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. När du är klar väljer du **Spara**i verktygsfältet designer.

   I det här steget kan du automatiskt publicera din Logic app Live i Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Hantera Mass data

Ibland måste du arbeta med resultat uppsättningar så att kopplingen inte returnerar alla resultat samtidigt, eller om du vill ha bättre kontroll över storleken och strukturen för dina resultat uppsättningar. Här är några exempel på hur du kan hantera sådana stora resultat uppsättningar:

* För att hjälpa dig att hantera resultat som mindre mängder, aktiverar du *sid brytning*. Mer information finns i [Hämta Mass data, poster och objekt med hjälp av sid brytning](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Skapa en lagrad procedur som ordnar resultatet på det sätt som du vill.

  När du hämtar eller infogar flera rader kan din Logi Kap par iterera genom de här raderna genom att använda en [*loop till*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) i dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). Men när din Logic-app måste arbeta med post uppsättningar så stor, till exempel tusentals eller miljon tals rader, vill du minimera kostnaderna som uppstår vid anrop till databasen.

  För att ordna resultaten på det sätt som du vill ha, kan du skapa en [*lagrad procedur*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) som körs i SQL-instansen och som använder instruktionen **Select-order by** . Den här lösningen ger dig större kontroll över storleken och strukturen på resultaten. Din Logi Kap par anropar den lagrade proceduren med hjälp av åtgärden SQL Server Connector: s **Kör lagrad procedur** .

  Mer information om lösningar finns i följande artiklar:

  * [SQL-sid brytning för Mass data överföring med Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY-sats](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Teknisk information om den här anslutningens utlösare, åtgärder och gränser finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [anslutningar för Azure Logic Apps](../connectors/apis-list.md)