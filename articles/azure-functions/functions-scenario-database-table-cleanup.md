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
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 024958d8a548313b53fc24ade5805de036a89afb
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351923"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Använd Azure Functions för att ansluta till en Azure SQL Database
Det här avsnittet visar hur du använder Azure Functions för att skapa ett schemalagt jobb som rensar upp rader i en tabell i en Azure SQL Database. Den nya C#-skriptfunktion skapas baserat på en fördefinierad timer utlösarmallen i Azure-portalen. För det här scenariot måste ange du också en anslutningssträng för databasen som en appinställning i funktionsappen. Det här scenariot använder en bulkåtgärd mot databasen. 

Ha din funktion processen person skapa, läsa, uppdatera och ta bort (CRUD)-åtgärder i en tabell med Mobile Apps, bör du i stället använda [Mobile Apps-bindningar](functions-bindings-mobile-apps.md).

> [!IMPORTANT]
> Exemplen i det här dokumentet avser 1.x-körningen. Information om hur du skapar en 1.x-funktionsapp [hittar du här](./functions-versions.md#creating-1x-apps).

## <a name="prerequisites"></a>Förutsättningar

+ Det här avsnittet använder en timerutlöst funktion. Utför stegen i avsnittet [skapa en funktion i Azure som utlöses av en timer](functions-create-scheduled-function.md) att skapa en C#-version av den här funktionen.   

+ Det här avsnittet visas ett Transact-SQL-kommando som kör en bulkåtgärd rensning i den **SalesOrderHeader** tabell i AdventureWorksLT-exempeldatabasen. Skapa AdventureWorksLT-exempeldatabasen genom att slutföra stegen i avsnittet [skapa en Azure SQL database i Azure-portalen](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Du måste hämta anslutningssträngen för databasen som du skapade när du slutfört [skapa en Azure SQL database i Azure-portalen](../sql-database/sql-database-get-started-portal.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).
 
3. Välj **SQL-databaser** på den vänstra menyn och välj din databas på den **SQL-databaser** sidan.

4. Välj **visa databasanslutningssträngar** och kopiera den fullständiga **ADO.NET** anslutningssträngen. 

    ![Kopiera ADO.NET-anslutningssträngen.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ange anslutningssträngen 

En funktionsapp är värd för körningen av dina funktioner i Azure. Det är en bra idé att lagra anslutningssträngar och andra hemligheter i dina funktionsappinställningarna. Med hjälp av programinställningar förhindrar oavsiktlig utlämnande av anslutningssträngen med din kod. 

1. Gå till din funktionsapp som du skapade [skapa en funktion i Azure som utlöses av en timer](functions-create-scheduled-function.md).

2. Välj **plattformsfunktioner** > **programinställningar**.
   
    ![Programinställningar för funktionsappen.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Rulla ned till **anslutningssträngar** och lägga till en anslutningssträng med inställningarna som anges i tabellen.
   
    ![Lägga till en anslutningssträng till funktionsappinställningarna.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Inställning       | Föreslaget värde | Beskrivning             | 
    | ------------ | ------------------ | --------------------- | 
    | **Namn**  |  sqldb_connection  | Används för att få åtkomst till lagrade anslutningssträngen i funktionskoden.    |
    | **Värde** | Kopierade sträng  | Klistra in anslutningssträngen som du kopierade i föregående avsnitt och Ersätt `{your_username}` och `{your_password}` platshållarna med riktiga värden. |
    | **Typ** | SQL Database | Använd standard SQL Database-anslutningar. |   

3. Klicka på **Spara**.

Nu kan du lägga till C#-Funktionskoden som ansluter till din SQL-databas.

## <a name="update-your-function-code"></a>Uppdatera Funktionskoden

1. Välj timerutlöst funktion i din funktionsapp i portalen.
 
3. Lägg till följande sammansättningsreferenser överst i den befintliga C#-skript Funktionskoden:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Koden i de här exemplen är C#-skript från portalen. När du utvecklar en förkompilerade C#-funktion lokalt, måste du i stället lägga till referenser till dessa monterar i projektet lokalt.  

3. Lägg till följande `using` instruktioner till funktionen:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Ersätt de befintliga `Run` funktionen med följande kod:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Det här exemplet kommandot uppdaterar den `Status` kolumnen baserat på leverera datum. Det bör uppdatera 32 rader med data.

5. Klicka på **spara**, titta på den **loggar** för nästa fungera körning och Observera hur många rader som uppdateras i den **SalesOrderHeader** tabell.

    ![Visa funktionsloggarna.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Nästa steg

Därefter lär du dig hur du använder Functions med Logic Apps för att integrera med andra tjänster.

> [!div class="nextstepaction"] 
> [Skapa en funktion som kan integreras med Logic Apps](functions-twitter-email.md)

Mer information om funktioner finns i följande avsnitt:

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.  
