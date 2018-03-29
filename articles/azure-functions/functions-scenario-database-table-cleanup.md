---
title: Använd Azure Functions för att utföra en databas uppgift | Microsoft Docs
description: Använd Azure Functions för att schemalägga en aktivitet som ansluter till Azure SQL Database för att regelbundet rensa rader.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 2947fc6da0c4559e81cf97255b8375b020e0b657
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Använd Azure Functions för att ansluta till en Azure SQL Database
Det här avsnittet visar hur du använder Azure Functions för att skapa ett schemalagt jobb som rensar upp rader i en tabell i Azure SQL-databas. Nytt C# skript funktionen skapas baserat på en fördefinierad timer utlösaren mall i Azure-portalen. För att stödja det här scenariot måste du också ange en anslutningssträng för databasen som en appinställning i funktionen appen. Det här scenariot använder en massåtgärd mot databasen. 

Har din funktion processen individuella skapa, Läs-, update- och borttagningsåtgärder (CRUD) i en tabell för Mobile Apps ska du i stället använda [Mobile Apps bindningar](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Förutsättningar

+ Det här avsnittet använder en som utlöste-timerfunktion. Utför stegen i avsnittet [skapa en funktion i Azure som utlöses av en timer](functions-create-scheduled-function.md) att skapa en C#-version av den här funktionen.   

+ Det här avsnittet visar ett Transact-SQL-kommando som kör en massåtgärd rensning i den **SalesOrderHeader** tabell i exempeldatabasen AdventureWorksLT. Utför stegen i avsnittet om du vill skapa exempeldatabasen AdventureWorksLT [skapa en Azure SQL database i Azure portal](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Du måste hämta anslutningssträngen för databasen du skapade när du slutfört [skapa en Azure SQL database i Azure portal](../sql-database/sql-database-get-started-portal.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).
 
3. Välj **SQL-databaser** i den vänstra menyn och väljer din databas på den **SQL-databaser** sidan.

4. Välj **visa databasanslutningssträngar** och kopiera hela **ADO.NET** anslutningssträngen. 

    ![Kopiera ADO.NET-anslutningssträngen.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ange anslutningssträngen 

En funktionsapp är värd för körningen av dina funktioner i Azure. Det är en bra idé att lagra anslutningssträngar och andra hemligheter i funktionen app-inställningarna. Med hjälp av programinställningar förhindrar oavsiktlig avslöjande av anslutningssträngen med din kod. 

1. Navigera till din funktionsapp som du skapade [skapa en funktion i Azure som utlöses av en timer](functions-create-scheduled-function.md).

2. Välj **plattformsfunktioner** > **programinställningar**.
   
    ![Programinställningar för funktionen appen.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Rulla ned till **anslutningssträngar** och lägga till en anslutningssträng med inställningar som anges i tabellen.
   
    ![Lägga till en anslutningssträng i funktionen app-inställningar.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Inställning       | Föreslaget värde | Beskrivning             | 
    | ------------ | ------------------ | --------------------- | 
    | **Namn**  |  sqldb_connection  | Används för att få åtkomst till lagrade anslutningssträngen i funktionskoden.    |
    | **Värde** | Kopierade sträng  | Klistra in anslutningssträngen som du kopierade i föregående avsnitt och Ersätt `{your_username}` och `{your_password}` platshållarna med verkliga värden. |
    | **Typ** | SQL Database | Använd SQL databasanslutningen. |   

3. Klicka på **Spara**.

Nu kan du lägga till C#-Funktionskoden som ansluter till SQL-databasen.

## <a name="update-your-function-code"></a>Uppdatera din funktionskod

1. Välj funktionen timer-utlösta i appen funktionen i portalen.
 
3. Lägg till följande sammansättningsreferenser överst i den befintliga C# skript Funktionskoden:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Koden i de här exemplen är C# skript från portalen. När du utvecklar en förkompilerade C#-funktion lokalt, måste du i stället lägga till referenser till dessa monterar i projektet lokalt.  

3. Lägg till följande `using` instruktioner till funktionen:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Ersätta den befintliga `Run` funktionen med följande kod:
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

    Det här exemplet kommandot uppdaterar den `Status` kolumnen baserat på leverera datumet. Det bör uppdatera 32 datarader.

5. Klicka på **spara**, titta på den **loggar** windows för nästa fungera körning och notera antalet rader som uppdateras med den **SalesOrderHeader** tabell.

    ![Visa funktionsloggar.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Nästa steg

Därefter lär du dig hur du använder funktioner med Logic Apps för att integrera med andra tjänster.

> [!div class="nextstepaction"] 
> [Skapa en funktion som kan integreras med Logic Apps](functions-twitter-email.md)

Mer information om funktioner finns i följande avsnitt:

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.  
