---
title: 'Snabbstart: Mata in data från Event Hub i Azure Data Explorer'
description: I den här snabbstarten får du lära dig hur du matar in (load) data i Azure Data Explorer från Event Hub.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: acc18b6414e62a1f8239562158b4b80219c5d7c1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976830"
---
# <a name="quickstart-ingest-data-from-event-hub-into-azure-data-explorer"></a>Snabbstart: Mata in data från Event Hub i Azure Data Explorer

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från Event Hubs, en dataströmningsplattform och händelseinmatningstjänst för stordata. Event Hubs kan bearbeta flera miljoner händelser per sekund i nära realtid. I den här snabbstarten ska du skapa en händelsehubb, ansluta till den från Azure Data Explorer och se hur data flödar genom systemet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Förutom en Azure-prenumeration behöver du följande för att slutföra den här snabbstarten:

* [Ett testkluster och en databas](create-cluster-database-portal.md)

* [En exempelapp](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) som genererar data

* [Visual Studio 2017 version 15.3.2 eller senare](https://www.visualstudio.com/vs/) för att köra exempelappen

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

I den här snabbstarten ska du skapa exempeldata och skicka dem till en händelsehubb. Det första steget är att skapa en händelsehubb. Du kan göra detta med hjälp av en Azure Resource Manager-mall (ARM) på Azure-portalen.

1. Välj följande knapp för att starta distributionen.

    [![Distribuera till Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Knappen **Distribuera till Azure** tar dig till Azure-portalen där du kan fylla i ett distributionsformulär.

    ![Distribuera till Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Välj den prenumeration där du vill skapa händelsehubben och skapa en resursgrupp med namnet *test-hub-rg*.

    ![Skapa en resursgrupp](media/ingest-data-event-hub/create-resource-group.png)

1. Fyll i formuläret med följande information.

    ![Distributionsformulär](media/ingest-data-event-hub/deployment-form.png)

    Använd standardinställningarna för alla inställningar som inte visas i följande tabell.

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för händelsehubben.|
    | Resursgrupp | *test-hub-rg* | Skapa en ny resursgrupp. |
    | Plats | *USA, västra* | Välj *USA, västra* för den här snabbstarten. Välj den region som bäst uppfyller dina behov för ett produktionssystem.
    | Namn på namnområde | Ett unikt namnområdesnamn | Välj ett unikt namn som identifierar namnområdet. Till exempel *mytestnamespace*. Domännamnet *servicebus.windows.net* läggs till i namnet som du anger. Namnet får endast innehålla bokstäver, siffror och bindestreck. Namnet måste börja med en bokstav och sluta med en bokstav eller siffra. Värdet måste innehålla mellan 6 och 50 tecken.
    | Namn på händelsehubb | *test-hub* | Händelsehubben finns under namnområdet, som tillhandahåller en unik omfångscontainer. Namnet på händelsehubben måste vara unikt inom namnområdet. |
    | Konsumentgruppens namn | *test-group* | Konsumentgrupper gör att flera konsumerande program kan ha en separat vy över händelseströmmen. |
    | | |

1. Välj **Köp**, vilket bekräftar att du skapar resurser i din prenumeration.

1. Välj **Meddelanden** (klockikonen) i verktygsfältet för att övervaka etableringsprocessen. Det kan ta flera minuter innan distributionen slutförs, men du kan gå vidare till nästa steg nu.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Skapa en måltabell i Azure Data Explorer

Nu ska du skapa en tabell i Azure Data Explorer som Event Hubs skickar data till. Du skapar tabellen i klustret och databasen som etablerades i avsnittet **Förutsättningar**.

1. Välj **Fråga** under klustret på Azure-portalen.

    ![Frågeprogramlänk](media/ingest-data-event-hub/query-explorer-link.png)

1. Kopiera följande kommando till fönstret och välj **Kör**.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Kör genereringsfråga](media/ingest-data-event-hub/run-create-query.png)

1. Kopiera följande kommando till fönstret och välj **Kör**.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```
    Det här kommandot mappar inkommande JSON-data med kolumnnamnen och datatyperna som användes när tabellen skapades.

## <a name="connect-to-the-event-hub"></a>Ansluta till händelsehubben

Nu ska du ansluta till händelsehubben från Azure Data Explorer så att data som flödar till händelsehubben strömmas till testtabellen.

1. Välj **Meddelanden** i verktygsfältet för att kontrollera att distributionen av händelsehubben lyckades.

1. Under klustret som du skapade väljer du **Databaser** och sedan **TestDatabase**.

    ![Välj testdatabas](media/ingest-data-event-hub/select-test-database.png)

1. Välj **Datainmatning** och sedan **Lägg till dataanslutning**.

    ![Datainhämtning](media/ingest-data-event-hub/data-ingestion-create.png)

1. Fyll i formuläret med följande information och välj sedan **Skapa**.

    ![Händelsehubbanslutning](media/ingest-data-event-hub/event-hub-connection.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn på dataanslutning | *test-hub-connection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | Namnområde för händelsehubb | Ett unikt namnområdesnamn | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | Händelsehubb | *test-hub* | Händelsehubben som du skapade. |
    | Konsumentgrupp | *test-group* | Konsumentgruppen som definierades i hubben som du skapade. |
    | Tabell | *TestTable* | Tabellen som du skapade i **TestDatabase**. |
    | Dataformat | *JSON* | JSON- och CSV-format stöds. |
    | Kolumnmappning | *TestMapping* | Mappningen som du skapade i **TestDatabase**. |

    I den här snabbstartsguiden ska du använda *statisk routning* från händelsehubben, där du anger tabellnamnet, filformatet och mappningen. Du kan också använda dynamisk routning, där dessa egenskaper anges av programmet.

## <a name="copy-the-connection-string"></a>Kopiera anslutningssträngen

När du kör appen för att generera exempeldata behöver du anslutningssträngen för händelsehubbens namnområde.

1. Under händelsehubbens namnområde som du skapade väljer du **Policyer för delad åtkomst** och sedan **RootManageSharedAccessKey**.

    ![Principer för delad åtkomst](media/ingest-data-event-hub/shared-access-policies.png)

1. Kopiera **Anslutningssträng – primär nyckel**.

    ![Anslutningssträng](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generera exempeldata

Nu när du har anslutit Azure Data Explorer och händelsehubben använder du exempelappen som du laddade ned för att generera data.

1. Öppna exempelapplösningen i Visual Studio.

1. I filen *program.cs* uppdaterar du konstanten `connectionString` till anslutningssträngen som du kopierade från händelsehubbens namnområde.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Skapa och kör appen. Appen skickar meddelanden till händelsehubben, och den skriver ut status var tionde sekund.

1. När appen har skickat några meddelanden går du vidare till nästa steg där du ska granska dataflödet till händelsehubben och testtabellen.

## <a name="review-the-data-flow"></a>Granska dataflödet

1. Under händelsehubben på Azure-portalen ser du en aktivitetstopp när appen körs.

    ![Graf för händelsehubb](media/ingest-data-event-hub/event-hub-graph.png)

1. Gå tillbaka till appen och stoppa den när den når meddelande 99.

1. Kör följande fråga i testdatabasen för att kontrollera hur många meddelanden som har kommit fram till databasen hittills.

    ```Kusto
    TestTable
    | count
    ```

1. Kör följande fråga för att se innehållet i meddelandena.

    ```Kusto
    TestTable
    ```

    Resultatet bör se ut så här.

    ![Meddelanderesultat](media/ingest-data-event-hub/message-result-set.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda händelsehubben igen rensar du **test-hub-rg** för att undvika kostnader.

1. Välj **Resursgrupper** i Azure Portal längst till vänster och välj sedan den resursgrupp du skapat.  

    Om den vänstra menyn är dold väljer du ![Knappen Expandera](media/ingest-data-event-hub/expand.png) för att expandera den.

   ![Välj den resursgrupp som ska tas bort](media/ingest-data-event-hub/delete-resources-select.png)

1. Under **test-resource-group** väljer du **Ta bort resursgrupp**.

1. I det nya fönstret skriver du namnet på resursgruppen som ska tas bort (*test-hub-rg*) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Fråga efter data i Azure Data Explorer](web-query-data.md)
