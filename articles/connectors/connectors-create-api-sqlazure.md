---
title: Ansluta till SQL Server, Azure SQL Database eller Azure SQL-hanterad instans
description: Automatisera aktiviteter för SQL-databaser lokalt eller i molnet med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/06/2020
tags: connectors
ms.openlocfilehash: ba8a6e5b53634850670a7d6b2fb55ef0e7b18d09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255524"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatisera arbets flöden för en SQL-databas med hjälp av Azure Logic Apps

Den här artikeln visar hur du kan komma åt data i din SQL-databas inifrån en Logic-app med SQL Server-anslutningen. På så sätt kan du automatisera aktiviteter, processer eller arbets flöden som hanterar dina SQL-data och-resurser genom att skapa Logic Apps. SQL Server Connector fungerar för [SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) såväl som [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) och [Azure SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Du kan skapa Logi Kap par som körs när de utlöses av händelser i din SQL-databas eller i andra system, till exempel Dynamics CRM Online. Dina Logic Apps kan också hämta, infoga och ta bort data tillsammans med SQL-frågor och lagrade procedurer. Du kan till exempel bygga en Logi Kap par som automatiskt söker efter nya poster i Dynamics CRM Online, lägger till objekt i din SQL-databas efter nya poster och skickar sedan e-postaviseringar om de tillagda objekten.

Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Information om anslutningsspecifika teknisk information, begränsningar och kända problem finns på [referens sidan för SQL Server koppling](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En [SQL Server databas](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)eller [Azure SQL-hanterad instans](../azure-sql/managed-instance/instance-create-quickstart.md).

  Dina tabeller måste innehålla data så att din Logi Kap par kan returnera resultat vid anrop av åtgärder. Om du använder Azure SQL Database kan du använda exempel databaser, som ingår.

* SQL Server-namnet, databas namnet, ditt användar namn och ditt lösen ord. Du behöver dessa autentiseringsuppgifter så att du kan auktorisera din logik för att få åtkomst till din SQL Server.

  * För lokala SQL Server kan du hitta informationen i anslutnings strängen:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * För Azure SQL Database kan du hitta informationen i anslutnings strängen.
  
    Om du till exempel vill hitta den här strängen i Azure Portal öppnar du databasen. På menyn databas väljer du antingen **anslutnings strängar** eller **Egenskaper**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Baserat på om dina Logi Kap par ska köras i global, multi-Tenant Azure eller en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), finns det andra krav för att ansluta till lokala SQL Server:

  * För logi Kap par i globala Azure-klienter som ansluter till lokala SQL Server måste du ha en lokal [datagateway](../logic-apps/logic-apps-gateway-install.md) installerad på en lokal dator och en [data gateway-resurs som redan har skapats i Azure](../logic-apps/logic-apps-gateway-connection.md).

  * För logi Kap par i en ISE som ansluter till lokala SQL Server och använder Windows-autentisering, stöder inte Windows-autentisering ISE-versionen SQL Server-anslutaren. Så du behöver fortfarande använda datagatewayen och den icke-ISE SQL Server-anslutningen. För andra typer av autentisering behöver du inte använda datagatewayen och kan använda ISE-versions koppling.

* Den Logic-app där du behöver åtkomst till din SQL-databas. Om du vill starta din Logic-app med en SQL-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Fortsätt nu med följande steg:

* [Ansluta till molnbaserad Azure SQL Database eller hanterad instans](#connect-azure-sql-db)
* [Ansluta till lokala SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Anslut till Azure SQL Database eller hanterad instans

Första gången du lägger till en SQL- [utlösare](#add-sql-trigger) eller [SQL-åtgärd](#add-sql-action), och du inte redan har skapat en anslutning till databasen, uppmanas du att slutföra de här stegen:

1. För **Autentiseringstyp**väljer du den autentisering som krävs och är aktive rad på databasen i Azure SQL Database eller Azure SQL-hanterad instans:

   | Autentisering | Beskrivning |
   |----------------|-------------|
   | [**Azure AD-integrerad**](../azure-sql/database/authentication-aad-overview.md) | – Stöder både non-ISE-och ISE SQL Server-anslutningen. <p><p>-Kräver en giltig identitet i Azure Active Directory (Azure AD) som har åtkomst till din databas. <p>Mer information finns i de här ämnena: <p>- [Översikt över Azure SQL-säkerhet – autentisering](../azure-sql/database/security-overview.md#authentication) <br>- [Auktorisera databas åtkomst till Azure SQL – autentisering och auktorisering](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL – integrerad Azure AD-autentisering](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server autentisering**](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Stöder både non-ISE-och ISE SQL Server-anslutningen. <p><p>-Kräver ett giltigt användar namn och ett starkt lösen ord som skapas och lagras i databasen. <p>Mer information finns i de här ämnena: <p>- [Översikt över Azure SQL-säkerhet – autentisering](../azure-sql/database/security-overview.md#authentication) <br>- [Auktorisera databas åtkomst till Azure SQL – autentisering och auktorisering](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Det här exemplet fortsätter med **Azure AD Integrated**:

   ![Välj autentiseringstyp som ska användas](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. När du har valt **Azure AD Integrated**väljer du **Logga**in. Välj dina användarautentiseringsuppgifter för autentisering baserat på om du använder Azure SQL Database eller Azure SQL-hanterad instans.

1. Välj följande värden för databasen:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Servernamn** | Ja | Adressen till din SQL-Server, till exempel`Fabrikam-Azure-SQL.database.windows.net` |
   | **Databasnamn** | Ja | Namnet på din SQL-databas, till exempel`Fabrikam-Azure-SQL-DB` |
   | **Tabellnamn** | Ja | Den tabell som du vill använda, till exempel`SalesLT.Customer` |
   ||||

   > [!TIP]
   > Du hittar den här informationen i databasens anslutnings sträng. I Azure Portal kan du till exempel söka efter och öppna databasen. På menyn databas väljer du antingen **anslutnings strängar** eller **Egenskaper** där du hittar den här strängen:
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   Det här exemplet visar hur dessa värden kan se ut:

   ![Skapa anslutning till SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Fortsätt nu med de steg som du inte har slutfört än i antingen [Lägg till en SQL-utlösare](#add-sql-trigger) eller [Lägg till en SQL-åtgärd](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Ansluta till lokala SQL Server

Första gången du lägger till en SQL- [utlösare](#add-sql-trigger) eller [SQL-åtgärd](#add-sql-action), och du inte redan har skapat en anslutning till databasen, uppmanas du att slutföra de här stegen:

1. Kontrol lera att du har [slutfört dessa krav](#multi-tenant-or-ise)för anslutningar till din lokala SQL Server som kräver den lokala datagatewayen.

   Annars visas inte din data gateway-resurs i listan över **anslutnings-Gateway** när du skapar anslutningen.

1. För **Autentiseringstyp**väljer du den autentisering som krävs och är aktive rad på SQL Server:

   | Autentisering | Beskrivning |
   |----------------|-------------|
   | [**Windows-autentisering**](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -Stöder endast non-ISE SQL Server-anslutningen, som kräver en data gateway-resurs som tidigare har skapats i Azure för anslutningen, oavsett om du använder Azure med flera innehavare eller en ISE. <p><p>-Kräver ett giltigt Windows-användarnamn och-lösen ord för att bekräfta din identitet via ditt Windows-konto. <p>Mer information finns i [Windows-autentisering](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server autentisering**](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Stöder både non-ISE-och ISE SQL Server-anslutningen. <p><p>-Kräver ett giltigt användar namn och ett starkt lösen ord som skapas och lagras i SQL Server. <p>Mer information finns i [SQL Server autentisering](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Det här exemplet fortsätter med **Windows-autentisering**:

   ![Välj autentiseringstyp som ska användas](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Välj eller ange följande värden för din SQL-databas:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **SQL Server-namn** | Ja | Adressen till din SQL-Server, till exempel`Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL Database-namn** | Ja | Namnet på SQL Server databasen, till exempel`Fabrikam-Azure-SQL-DB` |
   | **Användar** | Ja | Ditt användar namn för SQL Server och databasen |
   | **Lösenord** | Ja | Ditt lösen ord för SQL Server och databasen |
   | **Prenumeration** |  Ja, för Windows-autentisering | Azure-prenumerationen för den data gateway-resurs som du tidigare skapade i Azure |
   | **Gateway för anslutning** | Ja, för Windows-autentisering | Namnet på den data gateway-resurs som du tidigare skapade i Azure <p><p>**Tips**: om din Gateway inte visas i listan kontrollerar du att du har konfigurerat [din gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)korrekt. |
   |||

   > [!TIP]
   > Du hittar den här informationen i databasens anslutnings sträng:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Det här exemplet visar hur dessa värden kan se ut:

   ![Skapande av SQL Server anslutning slutförd](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. När du är klar väljer du **skapa**.

1. Fortsätt nu med de steg som du inte har slutfört än i antingen [Lägg till en SQL-utlösare](#add-sql-trigger) eller [Lägg till en SQL-åtgärd](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Lägg till en SQL-utlösare

1. I [Azure Portal](https://portal.azure.com) eller i Visual Studio skapar du en tom Logic-app som öppnar Logic Apps designer. Det här exemplet fortsätter med Azure Portal.

1. Skriv i rutan Sök i designern `sql server` . Välj den SQL-utlösare som du vill använda från listan utlösare. I det här exemplet används utlösaren **när ett objekt skapas** .

   ![Välj när ett objekt skapas "utlösare](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Om du ansluter till SQL-databasen för första gången uppmanas du att [skapa din SQL Database-anslutning nu](#create-connection). När du har skapat den här anslutningen kan du fortsätta med nästa steg.

1. I utlösaren anger du intervall och frekvens för hur ofta utlösaren kontrollerar tabellen.

1. Om du vill lägga till andra tillgängliga egenskaper för den här utlösaren öppnar du listan **Lägg till ny parameter** .

   Den här utlösaren returnerar bara en rad från den valda tabellen och inget annat. Om du vill utföra andra uppgifter kan du fortsätta genom att lägga till en [SQL-kopplings åtgärd](#add-sql-action) eller [en annan åtgärd](../connectors/apis-list.md) som utför nästa uppgift som du vill ha i ditt Logic app-arbetsflöde.
   
   Om du till exempel vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och sedan skicka e-postaviseringar. Information om andra tillgängliga åtgärder för den här anslutningen finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sql/).

1. I verktygsfältet designer väljer du **Spara**.

   Även om det här steget automatiskt aktiverar och publicerar din Logic app Live i Azure, är den enda åtgärden som din Logic app tar för närvarande att kontrol lera din databas baserat på angivet intervall och frekvens.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Lägg till en SQL-åtgärd

I det här exemplet börjar Logic-appen med [upprepnings utlösaren](../connectors/connectors-native-recurrence.md)och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller i Visual Studio. I det här exemplet fortsätter Azure Portal.

1. Under utlösaren eller åtgärden där du vill lägga till SQL-åtgärden väljer du **nytt steg**.

   ![Lägg till en åtgärd i din Logic app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Eller om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**i rutan Sök anger du `sql server` . I listan åtgärder väljer du den SQL-åtgärd som du vill använda. I det här exemplet används åtgärden **Hämta rad** som hämtar en enda post.

   ![Välj SQL-åtgärd för att hämta rad](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Om du ansluter till SQL-databasen för första gången uppmanas du att [skapa din SQL Database-anslutning nu](#create-connection). När du har skapat den här anslutningen kan du fortsätta med nästa steg.

1. Välj **tabell namnet**, som är `SalesLT.Customer` i det här exemplet. Ange **rad-ID: t** för den post som du vill använda.

   ![Välj Tabell namn och ange rad-ID](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Den här åtgärden returnerar bara en rad från den valda tabellen, inget annat. Så om du vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och lagra filen i ett moln lagrings konto. Information om andra tillgängliga åtgärder för den här anslutningen finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sql/).

1. När du är klar väljer du **Spara**i verktygsfältet designer.

   I det här steget kan du automatiskt publicera din Logic app Live i Azure.

## <a name="handle-bulk-data"></a>Hantera Mass data

Ibland måste du arbeta med resultat uppsättningar så att kopplingen inte returnerar alla resultat samtidigt, eller om du vill ha bättre kontroll över storleken och strukturen för dina resultat uppsättningar. Här är några exempel på hur du kan hantera sådana stora resultat uppsättningar:

* För att hjälpa dig att hantera resultat som mindre mängder, aktiverar du *sid brytning*. Mer information finns i [Hämta Mass data, poster och objekt med hjälp av sid brytning](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Skapa en lagrad procedur som ordnar resultatet på det sätt som du vill.

  När du hämtar eller infogar flera rader kan din Logi Kap par iterera genom de här raderna genom att använda en [*loop till*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) i dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). Men när din Logic-app måste arbeta med post uppsättningar så stor, till exempel tusentals eller miljon tals rader, vill du minimera kostnaderna som uppstår vid anrop till databasen.

  För att ordna resultaten på det sätt som du vill ha, kan du skapa en [*lagrad procedur*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) som körs i SQL-instansen och som använder instruktionen **Select-order by** . Den här lösningen ger dig större kontroll över storleken och strukturen på resultaten. Din Logi Kap par anropar den lagrade proceduren med hjälp av åtgärden SQL Server Connector: s **Kör lagrad procedur** .

  Mer information om lösningar finns i följande artiklar:

  * [SQL-sid brytning för Mass data överföring med Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY-sats](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Hantera dynamiska Mass data

När du anropar en lagrad procedur med hjälp av SQL Server Connector är den returnerade utdatan ibland dynamisk. I det här scenariot följer du dessa steg:

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Visa utdataformatet genom att utföra en testkörning. Kopiera och spara exempel på utdata.

1. I designern, under den åtgärd där du anropar den lagrade proceduren, väljer du **nytt steg**.

1. Under **Välj en åtgärd**söker du efter och väljer åtgärden [**parsa JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .

1. I åtgärden **parsa JSON** väljer **du Använd exempel nytto last för att generera schemat**.

1. I rutan **Ange eller klistra in ett exempel på JSON-nyttolast** klistrar du in exempel på utdata och väljer sedan **Slutför**.

   > [!NOTE]
   > Om du får ett fel meddelande som Logic Apps inte kan generera ett schema, kontrollerar du att syntaxen för exempel på utdata är korrekt formaterad. Om du fortfarande inte kan skapa schemat går du till rutan **schema** och anger schemat manuellt.

1. I verktygsfältet designer väljer du **Spara**.

1. Om du vill referera till egenskaperna för JSON-innehåll klickar du i redigerings rutorna där du vill referera till egenskaperna så att listan med dynamiskt innehåll visas. I listan, under rubriken [**parsa JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) , väljer du de datatoken som du vill använda för de egenskaper för JSON-innehåll som du vill ha.

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Teknisk information om den här anslutningens utlösare, åtgärder och gränser finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/sql/), som genereras från Swagger-beskrivningen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [anslutningar för Azure Logic Apps](../connectors/apis-list.md)
