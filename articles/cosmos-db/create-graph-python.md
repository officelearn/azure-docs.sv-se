---
title: 'Snabbstart: Gremlin-API med Python – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder Gremlin-API i Azure Cosmos DB för att skapa ett konsolprogram med Azure Portal och Python
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: lbosq
ms.openlocfilehash: b1286daaa76c71f88d44ea387a92876a8676783c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062255"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Snabbstart: Skapa en diagramdatabas i Azure Cosmos DB med Python och Azure-portalen

> [!div class="op_single_selector"]
> * [Gremlin-konsol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [Php](create-graph-php.md)
>  

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB Gremlin -API-konto (graf) från Azure-portalen och lägger till data med hjälp av en Python-app som klonats från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="prerequisites"></a>Krav
- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
- [Python 3.5+](https://www.python.org/downloads/) inklusive [pip](https://pip.pypa.io/en/stable/installing/) paketinstallationsprogram.
- [Python Driver för Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python).
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Den här snabbstarten kräver ett databaskonto för diagram som skapats efter den 20 december 2017. Befintliga konton stöder Python när de är migrerade till allmän tillgänglighet.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en grafdatabas måste du skapa ett Gremlin-databaskonto (Graph) med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Nu ska vi klona en Gremlin-API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt.  

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till en mapp där du vill installera exempelappen.  

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Kodavsnitten tas alla från *connect.py-filen* i mappen *C:\git-samples\azure-cosmos-db-graph-python-getting-started.\\ * Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-information). 

* Gremlin `client` initieras i linje 104 i *connect.py:*

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* En serie Gremlin-steg deklareras i början av *connect.py* filen. De körs sedan med metoden `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Uppdatera din anslutningsinformation

Gå nu tillbaka till Azure Portal för att hämta anslutningsinformation och kopiera den till appen. De här inställningarna gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. Välj **Nycklar**i ditt Azure Cosmos DB-konto i [Azure-portalen](https://portal.azure.com/). 

    Kopiera den första delen av URI-värdet.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, sidan Nycklar](./media/create-graph-python/keys.png)

2. Öppna *connect.py-filen* och klistra in URI-värdet `<YOUR_ENDPOINT>` på rad 104 här:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    URI-delen av klientobjektet bör nu likna den här koden:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Ändra den andra parametern för objektet `client` för att ersätta strängarna `<YOUR_DATABASE>` och `<YOUR_COLLECTION_OR_GRAPH>`. Om du använde de föreslagna värdena bör parametern se ut som den här koden:

    `username="/dbs/sample-database/colls/sample-graph"`

    Hela objektet `client` bör nu se ut som den här koden:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. På sidan **Nycklar** använder du knappen Kopiera för att `<YOUR_PASSWORD>` kopiera `password=<YOUR_PASSWORD>` primärnyckeln och klistra in den i parametern.

    Hela objektdefinitionen `client` bör nu se ut som den här koden:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Spara *connect.py-filen.*

## <a name="run-the-console-app"></a>Kör konsolappen

1. I git-terminalfönstret `cd` till mappen azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Python-paket som krävs.

   ```
   pip install -r requirements.txt
   ```

3. I git-terminalfönstret använder du följande kommando för att starta Python-programmet.
    
    ```
    python connect.py
    ```

    I terminalfönstret visas de hörn och gränser som läggs till i tabellen. 
    
    Om du får timeout-fel kontrollerar du att du har uppdaterat anslutningsinformationen på rätt sätt i [Uppdatera din anslutningsinformation](#update-your-connection-information). Prova även att köra det senaste kommandot igen. 
    
    När programmet har slutat arbeta trycker du på Retur och växlar sedan tillbaka till Azure Portal i webbläsaren.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Granska och lägg till exempeldata

När hörnen och kanterna har infogats kan du nu gå tillbaka till Data Explorer och se de hörn som lagts till i diagrammet och lägga till ytterligare datapunkter.

1. I ditt Azure Cosmos DB-konto i Azure-portalen väljer du **Data Explorer,** expanderar **exempeldiagram,** väljer **Diagram**och väljer sedan **Använd filter**. 

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. I **resultatlistan** läggs meddelande om att tre nya användare läggs till i diagrammet. Du kan flytta hörnen genom att dra och släppa, zooma in och ut genom att bläddra med mushjulet, och utöka diagrammets storlek med hjälp av dubbelpilen. 

   ![Nya hörn i grafen i datautforskaren på Azure Portal](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Vi ska nu lägga till några nya användare. Välj knappen **Nytt hörn** om du vill lägga till data i diagrammet.

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Ange en etikett för *person*.

5. Välj **Lägg till egenskap** om du vill lägga till var och en av följande egenskaper. Tänk på att du kan skapa unika egenskaper för varje person i grafen. Endast id-nyckeln krävs.

    key|värde|Anteckningar
    ----|----|----
    Pk|/pk (pk)| 
    id|ashley|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|kvinna| 
    teknik | Java | 

    > [!NOTE]
    > I den här snabbstartsguiden skapar vi en icke-partitionerad samling. Men om du skapar en partitionerad samling genom att ange en partitionsnyckel när samlingen skapas, måste du lägga till partitionsnyckeln som nyckel i varje nytt hörn. 

6. Välj **OK**. Du kan behöva expandera skärmen för att se **OK** längst ned på skärmen.

7. Välj **Nytt hörn** igen och lägg till ytterligare en ny användare. 

8. Ange en etikett för *person*.

9. Välj **Lägg till egenskap** om du vill lägga till var och en av följande egenskaper:

    key|värde|Anteckningar
    ----|----|----
    Pk|/pk (pk)| 
    id|rakesh|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|man| 
    skola|MIT| 

10. Välj **OK**. 

11. Välj knappen **Använd filter** `g.V()` med standardfiltret om du vill visa alla värden i diagrammet. Nu visas alla användare i listan **Resultat**. 

    Allteftersom du lägger till data kan du använda filter för att begränsa resultaten. Som standard använder Datautforskaren `g.V()` för att hämta alla hörnen i ett diagram. Du kan ändra till en annan [diagramfråga](tutorial-query-graph.md), t.ex. `g.V().count()`, för att returnera en uppräkning av alla hörn i diagrammet i JSON-format. Om du har ändrat filtret `g.V()` ändrar du tillbaka filtret och väljer **Använd filter** för att visa alla resultat igen.

12. Nu kan vi koppla ihop Rakesh och Ashley. Se **till att Ashley** är markerat i **resultatlistan** och välj sedan redigeringsknappen bredvid **Mål** längst ned till höger. Du kan behöva bredda fönstret för att se området **Egenskaper**.

    ![Ändra mål för ett hörn i en graf](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. I rutan **Mål** skriver du *rakesh*och i rutan **Kantetikett** *vet du*och markerar sedan kryssrutan.

    ![Lägg till en anslutning mellan Ashley och Rakesh i datautforskaren](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Markera nu **rakesh** i resultatlistan och se att Ashley och Rakesh är anslutna. 

    ![Två hörn anslutna i datautforskaren](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

Då är delen om att skapa resurser slutförd i den här självstudien. Du kan fortsätta att lägga till hörn i diagrammet, ändra befintliga hörn eller ändra frågorna. Vi ska nu titta på de mått som Azure Cosmos DB tillhandahåller, och sedan ska vi rensa resurserna. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Azure Cosmos DB-konto, skapar ett diagram med Data Explorer och kör en Python-app för att lägga till data i diagrammet. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)

