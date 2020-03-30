---
title: Använda Azure Functions för att utföra en databasrensningsuppgift
description: Använd Azure Functions för att schemalägga en aktivitet som ansluter till Azure SQL Database för att regelbundet rensa rader.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366825"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Använda Azure-funktioner för att ansluta till en Azure SQL-databas

Den här artikeln visar hur du använder Azure Functions för att skapa ett schemalagt jobb som ansluter till en Azure SQL-databas eller Azure SQL-hanterad instans. Funktionskoden rensar rader i en tabell i databasen. Den nya C#-funktionen skapas baserat på en fördefinierad timerutlösarmall i Visual Studio 2019. För att stödja det här scenariot måste du också ange en databasanslutningssträng som en appinställning i funktionsappen. För Azure SQL Managed Instance måste du [aktivera offentlig slutpunkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) för att kunna ansluta från Azure Functions. I det här scenariot används en massåtgärd mot databasen. 

Om det här är din första erfarenhet av att arbeta med C#-funktioner bör du läsa [utvecklarreferensen för Azure Functions C#.](functions-dotnet-class-library.md)

## <a name="prerequisites"></a>Krav

+ Slutför stegen i artikeln [Skapa din första funktion med Visual Studio](functions-create-your-first-function-visual-studio.md) för att skapa en lokal funktionsapp som riktar sig till version 2.x eller en senare version av körningen. Du måste också ha publicerat projektet i en funktionsapp i Azure.

+ Den här artikeln visar ett Transact-SQL-kommando som kör en massrensningsåtgärd i tabellen **SalesOrderHeader** i exempeldatabasen AdventureWorksLT. Om du vill skapa exempeldatabasen AdventureWorksLT slutför du stegen i artikeln [Skapa en Azure SQL-databas i Azure-portalen](../sql-database/sql-database-get-started-portal.md).

+ Du måste lägga till en [brandväggsregel på servernivå](../sql-database/sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen för den dator som du använder för den här snabbstarten. Den här regeln krävs för att kunna komma åt SQL-databasinstansen från den lokala datorn.  

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Du måste hämta anslutningssträngen för databasen som du skapade när du slutförde [Skapa en Azure SQL-databas i Azure-portalen](../sql-database/sql-database-get-started-portal.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **SQL-databaser** på menyn till vänster och välj databasen på **sidan SQL-databaser.**

1. Välj **Anslutningssträngar** under **Inställningar** och kopiera hela **ADO.NET** anslutningssträngen. För Azure SQL-hanterad instanskopieringsanslutningssträng för offentlig slutpunkt.

    ![Exempel på ADO.NET-anslutningssträng.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ange anslutningssträngen

En funktionsapp är värd för körningen av dina funktioner i Azure. Som bästa säkerhetspraxis kan du lagra anslutningssträngar och andra hemligheter i funktionsappinställningarna. Om du använder programinställningar förhindras oavsiktligt utlämnande av anslutningssträngen med din kod. Du kan komma åt appinställningar för din funktionsapp direkt från Visual Studio.

Du måste ha publicerat din app tidigare i Azure. Om du inte redan har gjort det [publicerar du funktionsappen i Azure](functions-develop-vs.md#publish-to-azure).

1. Högerklicka på funktionsappprojektet i Solution Explorer och välj Inställningar **för Publicera** > **redigera Azure App Service**. Välj **Lägg till- inställning**, i Nytt **appinställningsnamn**, skriv `sqldb_connection`och välj **OK**.

    ![Programinställningar för funktionsappen.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. I den nya **inställningen sqldb_connection** klistrar du in anslutningssträngen som du kopierade i föregående avsnitt i fältet **Lokalt** och ersätter `{your_username}` och `{your_password}` placerar innehavare med verkliga värden. Välj **Infoga värde från lokal** om du vill kopiera det uppdaterade värdet till fältet **Fjärr** och välj sedan **OK**.

    ![Lägg till sql-anslutningsstränginställning.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Anslutningssträngarna lagras krypterade i Azure (**Remote**). För att förhindra läckande hemligheter bör den local.settings.json-projektfilen (**Lokal**) uteslutas från källkontrollen, till exempel genom att använda en .gitignore-fil.

## <a name="add-the-sqlclient-package-to-the-project"></a>Lägga till SqlClient-paketet i projektet

Du måste lägga till NuGet-paketet som innehåller SqlClient-biblioteket. Det här dataåtkomstbiblioteket behövs för att ansluta till en SQL-databas.

1. Öppna ditt lokala funktionsappprojekt i Visual Studio 2019.

1. Högerklicka på funktionsappprojektet i Solution Explorer och välj **Hantera NuGet-paket**.

1. På fliken **Bläddra** söker du efter ```System.Data.SqlClient``` och markerar det när du hittar det.

1. På sidan **System.Data.SqlClient** väljer `4.5.1` du version och klickar sedan på **Installera**.

1. När installationen har slutförts granskar du ändringarna och klickar på **OK** för att stänga fönstret **Förhandsgranska**.

1. Om ett fönster för **Godkännande av licens** visas klickar du på **Jag accepterar**.

Nu kan du lägga till C#-funktionskoden som ansluter till DIN SQL-databas.

## <a name="add-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. Högerklicka på funktionsappprojektet i Solution Explorer och välj **Lägg till** > **ny Azure-funktion**.

1. När mallen **Azure Functions** har valts `DatabaseCleanup.cs` namnger du det nya objektet ungefär och väljer **Lägg till**.

1. I dialogrutan **Ny Azure-funktion** väljer du **Timer-utlösare** och sedan **OK**. I den här dialogrutan skapas en kodfil för den utlösta tidsutlösande funktionen.

1. Öppna den nya kodfilen och lägg till följande med hjälp av satser högst upp i filen:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Ersätt den `Run` befintliga funktionen med följande kod:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Den här funktionen körs var 15:e sekund för att uppdatera `Status` kolumnen baserat på leveransdatumet. Mer information om timerutlösaren finns i [Timer-utlösaren för Azure Functions](functions-bindings-timer.md).

1. Tryck på **F5** för att starta funktionsappen. Körningsfönstret för [Azure Functions Core Tools](functions-develop-local.md) öppnas bakom Visual Studio.

1. Vid 15 sekunder efter start körs funktionen. Titta på utdata och notera antalet rader som uppdaterats i tabellen **SalesOrderHeader.**

    ![Visa funktionsloggarna.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Vid den första körningen bör du uppdatera 32 rader med data. Efter körningar uppdatera inga datarader, om du inte gör ändringar i SalesOrderHeader `UPDATE` tabelldata så att fler rader väljs av utdraget.

Om du planerar att publicera `TimerTrigger` den [här funktionen,](functions-develop-vs.md#publish-to-azure)kom ihåg att ändra attributet till ett mer rimligt [cron-schema](functions-bindings-timer.md#ncrontab-expressions) än var 15:e sekund.

## <a name="next-steps"></a>Nästa steg

Läs sedan om hur du använder. Fungerar med Logic Apps för att integrera med andra tjänster.

> [!div class="nextstepaction"]
> [Skapa en funktion som integreras med Logic Apps](functions-twitter-email.md)

Mer information om Funktioner finns i följande artiklar:

+ [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
+ [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.  
