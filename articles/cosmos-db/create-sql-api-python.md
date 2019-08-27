---
title: 'Azure Cosmos DB: Skapa en app med Python och SQL API'
description: Presenterar ett Python-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API:t
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: f085ca6de2c04caa90c34bc1293da1210d2883eb
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020164"
---
# <a name="azure-cosmos-db-build-a-python-application-using-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB: Skapa ett Python-program med hjälp av Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och fråga dokument, nyckel/värde-, bred kolumn-och Graf-databaser. Alla dessa åtgärder drar nytta av distribution och skalning av Azure Cosmos DB.

Den här snabbstarten visar hur du skapar ett [SQL API](sql-api-introduction.md)-konto i Azure Cosmos DB, en dokumentdatabas och en container med Azure-portalen. Sedan skapar du och kör en konsolapp som är byggd med Python SDK för [SQL API](sql-api-sdk-python.md). I den här snabb starten används version 3,0 av [python SDK](https://pypi.org/project/azure-cosmos).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Python 3,6](https://www.python.org/downloads/)med den `python` `PATH`körbara filen som är tillgänglig i.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägg till en behållare

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona vi en SQL API-app från GitHub, ange anslutningssträngen och köra appen.

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```cmd
    md "git-samples"
    ```
   Om du använder en bash-prompt bör du i stället använda följande kommando:

   ```bash
   mkdir "git-samples"
   ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](https://portal.azure.com/), i ditt Azure Cosmos-konto, i det vänstra navigeringset väljer du **nycklar**. Använd kopieringsknapparna till höger för att kopiera **URI** och **Primärnyckel** till filen `CosmosGetStarted.py` i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)

2. `CosmosGetStarted.py` Öppna filen i \git-samples\azure-Cosmos-DB-python-Getting-Started i Visual Studio Code.

3. Kopiera ditt **URI**-värde från portalen (med kopieringsknappen) och gör det till värdet för **slutpunktsnyckeln** i ``CosmosGetStarted.py``. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Kopiera sedan värdet för **PRIMÄRNYCKEL** från portalen och gör det till värdet för **config.PRIMARYKEY** i ``CosmosGetStarted.py``. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

    `'PRIMARYKEY': 'FILLME',`

5. Spara filen ``CosmosGetStarted.py``.

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Lär dig mer om databas resurserna som skapats i kod eller gå vidare till [Uppdatera anslutnings strängen](#update-your-connection-string).

Observera att om du är bekant med den tidigare versionen av Python SDK så är du kanske van vid att se termerna ”samling” och ”dokument”. Eftersom Azure Cosmos DB stöder flera API-modeller använder version 3.0+ av Python SDK de allmänna termerna ”container”, vilket kan vara en samling, ett diagram eller en tabell, och ”objekt” för att beskriva innehållet i containern.

Följande kodavsnitt är alla hämtade från filen `CosmosGetStarted.py`.

* CosmosClient initieras. Se till att uppdatera värdena ”Endpoint” (Slutpunkt) och ”master key” (huvudnyckel) enligt beskrivningen i avsnittet [Uppdatera din anslutningssträng](#update-your-connection-string). 

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* En ny databas skapas.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* En ny behållare skapas.

    ```python
    # Create container options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* Vissa objekt läggs till i containern.

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* En fråga utförs med hjälp av SQL

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```
   
## <a name="run-the-app"></a>Kör appen

1. Gå till Visual Studio Code och välj **Visa** > **Kommandopalett**. 

2. Vid kommandotolken anger du **Python: Välj tolk** och välj sedan den version av Python som ska användas.

    Sidfoten i Visual Studio Code uppdateras för att ange vilken tolk som valts. 

3. Välj **Visa** > **Integrerad terminal** så att den integrerade Visual Studio Code-terminalen öppnas.

4. I fönstret för den integrerade terminalen kontrollerar du att du befinner dig i mappen azure-cosmos-db-python-getting-started. Om du inte är det kör du följande kommando för att växla till exempelmappen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Kör följande kommando för att installera paketet azure-cosmos. 

    ```python
    pip3 install azure-cosmos
    ```

    Om du får ett felmeddelande om nekad åtkomst när du försöker installera azure-cosmos måste du [köra VS Code som administratör](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Kör följande kommando för att köra exemplet och skapa och lagra nya dokument i Azure Cosmos dB.

    ```python
    python CosmosGetStarted.py
    ```

7. Bekräfta att de nya objekten har skapats och sparats i Azure-portalen: välj **Datautforskaren**, expandera **coll**, expandera **Dokument** och välj sedan dokumentet **server1**. Innehållet i dokumentet server1 matchar det innehåll som returneras i fönstret för den integrerade terminalen. 

    ![Visa de nya dokument i Azure-portalen](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en behållare med hjälp av Datautforskaren och kör en app. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med SQL API:t](import-data.md)


