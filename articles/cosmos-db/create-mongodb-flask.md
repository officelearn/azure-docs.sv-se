---
title: Skapa en Python Flask-webbapp med Azure Cosmos DB:s API för MongoDB
description: Presenterar ett Python Flask-kodexempel som du kan använda för att ansluta till och ställa frågor men hjälp av Azure Cosmos DB:s API för MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 43f4cf7e4008aa01a26c48a8e99f7465eeeb234b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77061751"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Snabbstart: Skapa en Python-app med Azure Cosmos DB:s API för MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

I den här snabbstarten använder du ett Azure Cosmos DB för Mongo DB API-konto eller Azure Cosmos DB Emulator för att köra en Python Flask To-Do-webbapp som klonats från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB Emulator](local-emulator.md). 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio-kod](https://code.visualstudio.com/Download) med [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Flask-MongoDB-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt.

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Kör följande kommando för att installera python-modulerna.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Öppna mappen i Visual Studio Code.

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Kör webbappen](#run-the-web-app). 

Följande utdrag hämtas alla från *app.py-filen* och använder anslutningssträngen för den lokala Azure Cosmos DB Emulator. Lösenordet måste delas upp på det sätt som visas nedan för att möjliggöra de snedstreck som annars inte kan parsas.

* Initiera MongoDB-klienten, hämta databasen och autentisera.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Hämta samlingen eller skapa den om den inte redan finns.

    ```python
    todos = db.todo #Select the collection
    ```

* Skapa appen

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Kör webbappen

1. Kontrollera att Azure Cosmos DB-emulatorn körs.

2. Öppna ett terminalfönster och `cd` till den katalog där appen sparas.

3. Ställ sedan in miljövariabeln `set FLASK_APP=app.py`för `$env:FLASK_APP = app.py` Flask-appen med `export FLASK_APP=app.py` , för PowerShell-redigerare eller om du använder en Mac. 

4. Kör appen `flask run` med och bläddra till *http:\//127.0.0.1:5000/*.

5. Lägg till och ta bort uppgifter och se hur de läggs till och ändras i samlingen.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Om du vill testa koden mot ett live Azure Cosmos DB-konto går du till Azure-portalen för att skapa ett konto.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Om du vill testa koden mot det aktiva Azure Cosmos DB-kontot hämtar du din information om anslutningssträngen. Kopiera den till appen.

1. I ditt Azure Cosmos DB-konto i Azure-portalen väljer du **Anslutningssträng**i den vänstra navigeringen och väljer sedan **Läs-skrivnycklar**. Du använder kopieringsknapparna till höger på skärmen för att kopiera användarnamn, anslutningssträng och lösenord. 

2. Öppna filen *app.py* i rotkatalogen.

3. Kopiera ditt **användarnamn** från portalen (med kopieringsknappen) och gör det till värdet för **name** i filen *app.py*.

4. Kopiera sedan **anslutningssträngvärdet** från portalen och gör det till värdet för **MongoClient** i *din app.py* fil.

5. Kopiera slutligen **lösenordet** från portalen och gör det till värdet för **password** i filen *app.py*.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. Du kan köra den på samma sätt som innan.

## <a name="deploy-to-azure"></a>Distribuera till Azure

Om du vill distribuera den här appen kan du skapa en ny webbapp i Azure och aktivera kontinuerlig distribution med en förgrening av den här GitHub-lagringsplatsen. Följ den här [självstudien](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) för att konfigurera kontinuerlig distribution med GitHub i Azure.

När du distribuerar till Azure bör du ta bort dina programnycklar och kontrollera att avsnittet nedan inte har kommenterats bort:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Du måste sedan lägga till MONGOURL, MONGO_PASSWORD och MONGO_USERNAME i programinställningarna. Följ den här [självstudien](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) om du vill veta mer om programinställningar i Azure Web Apps.

Om du inte vill skapa en gaffel av den här reporäntan kan du också välja knappen **Distribuera till Azure** nedan. Du bör sedan gå in i Azure och konfigurera programinställningarna med din Azure Cosmos DB-kontoinformation.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Om du planerar att lagra din kod i GitHub eller något annat alternativ för källåtkomstkontroll måste du tänka på att ta bort anslutningssträngarna från koden. De kan anges med programinställningar för webbappen i stället.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Azure Cosmos DB för Mongo DB API-konto och använder Azure Cosmos DB Emulator för att köra en Python Flask To-Do-webbapp som klonats från GitHub. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
