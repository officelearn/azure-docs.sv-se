---
title: 'Snabbstart: Cassandra API med Python – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder Apache Cassandra API i Azure Cosmos DB för att skapa ett profilprogram med Python.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 0b432653c452b6763e746f61b86e881c9cee62cc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134677"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Snabb start: Bygg en Cassandra-app med python SDK och Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

I den här snabb starten skapar du ett Azure Cosmos DB API för Cassandra konto och använder en Cassandra python-app som klonas från GitHub för att skapa en Cassandra-databas och-behållare. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
- [Python 2.7.14 + eller 3.4 +](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Python-drivrutin för Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Cassandra API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk. Skapa en ny mapp med namnet `git-samples`. Stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Alla kod avsnitt hämtas från *pyquickstart.py* -filen. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

* Användarnamnet och lösenordet angavs med hjälp av anslutningssträngsidan i Azure-portalen. `path\to\cert` tillhandahåller en sökväg till X509-certifikatet. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* `cluster` har initierats med contactPoint-information. contactPoint hämtas från Azure-portalen.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* `cluster` ansluter till Azure Cosmos DB Cassandra-API.

    ```python
    session = cluster.connect()
    ```

* Ett nytt keyspace skapas.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* En ny tabell skapas.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Nyckel/värde-entiteter infogas.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Fråga för att hämta alla nyckelvärden.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Fråga för att hämta ett nyckelvärde.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen. Anslutningssträngen gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. Välj **anslutnings sträng**i Azure Cosmos DB-kontot i [Azure Portal](https://portal.azure.com/). 

    Använd ![Knappen Kopiera](./media/create-cassandra-python/copy.png) knappen på höger sida av skärmen för att kopiera det övre värdet, KONTAKTPUNKT.

    ![Visa och kopiera åtkomstanvändarnamn, lösenord och kontaktpunkt i Azure-portalen, på bladet för anslutningssträng](./media/create-cassandra-python/keys.png)

2. Öppna filen *config.py* . 

3. Klistra in KONTAKTPUNKT-värdet från portalen över `<FILLME>` på rad 10.

    Rad 10 bör nu se ut ungefär som 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Kopiera ANVÄNDARNAMN från portalen och klistra in det över `<FILLME>` på rad 6.

    Rad 6 bör nu se ut ungefär som 

    `'username': 'cosmos-db-quickstart',`
    
5. Kopiera LÖSENORD från portalen och klistra in det över `<FILLME>` på rad 8.

    Rad 8 bör nu se ut ungefär som

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Spara filen *config.py* .
    
## <a name="use-the-x509-certificate"></a>Använd X509-certifikatet

1. Ladda ned Baltimore CyberTrust-rotcertifikatet lokalt från [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Byt namn på filen med fil namns tillägget *. cer*.

   Certifikatet har serienummer `02:00:00:b9` och SHA1-fingeravtryck `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Öppna *pyquickstart.py* och ändra `path\to\cert` så att den pekar på det nya certifikatet.

3. Spara *pyquickstart.py*.

## <a name="run-the-python-app"></a>Köra Python-appen

1. Använd cd-kommandot i git-terminalen för att ändra till den `azure-cosmos-db-cassandra-python-getting-started`-mappen. 

2. Installera modulerna som krävs med hjälp av följande kommandon:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Kör följande kommando för att starta python-programmet:

    ```
    python pyquickstart.py
    ```

3. Kontrollera att resultatet är som förväntat från kommandoraden.

    Tryck på CTRL + C för att stoppa körningen av programmet och stänga konsol fönstret. 

    ![Granska och verifiera utdata.](./media/create-cassandra-python/output.png)
    
4. I **Datautforskaren** på Azure-portalen kan du fråga, ändra och arbeta med dessa nya data. 

    ![Visa data i Datautforskaren](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB konto med API för Cassandra och kör en Cassandra python-app som skapar en Cassandra-databas och-behållare. Nu kan du importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)

