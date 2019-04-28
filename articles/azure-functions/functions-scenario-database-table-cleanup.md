---
title: Använd Azure Functions för att utföra en databas rensningsuppgiften | Microsoft Docs
description: Använd Azure Functions för att schemalägga en aktivitet som ansluter till Azure SQL Database att regelbundet rensa rader.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 4ec2e9b931e6405aca5b4237bc044647af3b8bb3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120678"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Använd Azure Functions för att ansluta till en Azure SQL Database

Den här artikeln visar hur du använder Azure Functions för att skapa ett schemalagt jobb som ansluter till en Azure SQL Database-instans. Funktionskoden rensar rader i en tabell i databasen. Den nya C# funktionen skapas baserat på en fördefinierad timer utlösarmallen i Visual Studio 2017. För det här scenariot måste ange du också en anslutningssträng för databasen som en appinställning i funktionsappen. Det här scenariot använder en bulkåtgärd mot databasen. 

Om det här är din första erfarenhet av att arbeta med C# funktioner, bör du läsa den [Azure Functions C# utvecklarreferens](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Utför stegen i artikeln [skapa din första funktion med Visual Studio](functions-create-your-first-function-visual-studio.md) att skapa en lokal funktionsapp som riktar sig till version 2.x-körningen. Du måste också har publicerat ditt projekt till en funktionsapp i Azure.

+ Den här artikeln visar ett Transact-SQL-kommando som kör en bulkåtgärd rensning i den **SalesOrderHeader** tabell i AdventureWorksLT-exempeldatabasen. Skapa AdventureWorksLT-exempeldatabasen genom att slutföra stegen i artikeln [skapa en Azure SQL database i Azure-portalen](../sql-database/sql-database-get-started-portal.md).

+ Du måste lägga till en [brandväggsregel på servernivå](../sql-database/sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen på den dator som du använder för den här snabbstarten. Den här regeln krävs att du kan få åtkomst till SQL database-instans från din lokala dator.  

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Du måste hämta anslutningssträngen för databasen som du skapade när du slutfört [skapa en Azure SQL database i Azure-portalen](../sql-database/sql-database-get-started-portal.md).

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **SQL-databaser** på den vänstra menyn och välj din databas på den **SQL-databaser** sidan.

1. Välj **anslutningssträngar** under **inställningar** och kopiera den fullständiga **ADO.NET** anslutningssträngen.

    ![Kopiera ADO.NET-anslutningssträngen.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ange anslutningssträngen

En funktionsapp är värd för körningen av dina funktioner i Azure. Som bästa säkerhetspraxis, lagra anslutningssträngar och andra hemligheter i dina funktionsappinställningarna. Med hjälp av programinställningar förhindrar oavsiktlig utlämnande av anslutningssträngen med din kod. Du kan komma åt appinställningar för din funktionsapp direkt från Visual Studio.

Du måste ha tidigare publicerat din app till Azure. Om du inte redan gjort det, [publicera din funktionsapp i Azure](functions-develop-vs.md#publish-to-azure).

1. I Solution Explorer högerklickar du på approjektet funktionen och välj **publicera** > **hantera programinställningar...** . Välj **Lägg till inställning**i **nya app inställningsnamn**, typ `sqldb_connection`, och välj **OK**.

    ![Programinställningar för funktionsappen.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. I den nya **sqldb_connection** inställningen, klistra in anslutningssträngen som du kopierade i föregående avsnitt i den **lokala** fältet och Ersätt `{your_username}` och `{your_password}` platshållare med real värden. Välj **Infoga värde från lokala** att kopiera det uppdaterade värdet till den **Remote** och välj sedan **OK**.

    ![Lägg till SQL inställning för anslutningssträngen.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Anslutningssträngar lagras krypterade i Azure (**Remote**). Att förhindra läckande hemligheter, filen local.settings.json projekt (**lokala**) bör undantas från källkontroll, till exempel med hjälp av en .gitignore-fil.

## <a name="add-the-sqlclient-package-to-the-project"></a>Lägg till SqlClient-paketet i projektet

Du måste lägga till NuGet-paket som innehåller SqlClient-biblioteket. Det här biblioteket för åtkomst av data krävs för att ansluta till en SQL-databas.

1. Öppna ditt lokala funktionsappsprojekt i Visual Studio 2017.

1. I Solution Explorer högerklickar du på approjektet funktionen och välj **hantera NuGet-paket**.

1. På fliken **Bläddra** söker du efter ```System.Data.SqlClient``` och markerar det när du hittar det.

1. I den **System.Data.SqlClient** sidan, väljer version `4.5.1` och klicka sedan på **installera**.

1. När installationen har slutförts granskar du ändringarna och klickar på **OK** för att stänga fönstret **Förhandsgranska**.

1. Om ett fönster för **Godkännande av licens** visas klickar du på **Jag accepterar**.

Nu kan du lägga till C#-Funktionskoden som ansluter till din SQL-databas.

## <a name="add-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. I Solution Explorer högerklickar du på approjektet funktionen och välj **Lägg till** > **nya Azure-funktion**.

1. Med den **Azure Functions** mall som har valts, namnge det nya objektet något som liknar `DatabaseCleanup.cs` och välj **Lägg till**.

1. I den **nya Azure-funktion** dialogrutan väljer du **timerutlösare** och sedan **OK**. Den här dialogrutan skapar en kodfil för timerutlöst funktion.

1. Öppna den nya kodfilen och Lägg till följande using-satser överst i filen:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Ersätt de befintliga `Run` funktionen med följande kod:

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

    Den här funktionen körs var 15: e sekund att uppdatera den `Status` kolumnen baserat på leverera datum. Läs mer om den Timer som utlösaren i [Timer som utlösare för Azure Functions](functions-bindings-timer.md).

1. Tryck på **F5** att starta funktionsappen. Den [Azure Functions Core Tools](functions-develop-local.md) körning öppnas bakom Visual Studio.

1. På 15 sekunder efter start körs funktionen. Titta på resultatet och Observera hur många rader som uppdateras i den **SalesOrderHeader** tabell.

    ![Visa funktionsloggarna.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Du bör uppdatera 32 rader med data på den första körningen. Följande körningar uppdatera inga rader med data om du gör ändringar i de SalesOrderHeader data så att fler rader är markerade som den `UPDATE` instruktionen.

Om du planerar att [publicera den här funktionen](functions-develop-vs.md#publish-to-azure), Kom ihåg att ändra den `TimerTrigger` attributet till ett mer rimligt [cron-schemat](functions-bindings-timer.md#cron-expressions) än var 15: e sekund.

## <a name="next-steps"></a>Nästa steg

Därefter lär du dig hur du använder. Funktioner med Logic Apps och integrera med andra tjänster.

> [!div class="nextstepaction"]
> [Skapa en funktion som kan integreras med Logic Apps](functions-twitter-email.md)

Mer information om funktioner finns i följande artiklar:

+ [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
+ [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.  
