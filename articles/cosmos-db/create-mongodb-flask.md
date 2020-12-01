---
title: Bygg en python flaska-webbapp med Azure Cosmos DB s API för MongoDB
description: Presenterar ett Python Flask-kodexempel som du kan använda för att ansluta till och ställa frågor men hjälp av Azure Cosmos DB:s API för MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 3d066005ac18bf42f913885558bf6f0ee5fc50c4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349171"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Snabb start: bygga en python-app med Azure Cosmos DB s API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

I den här snabb starten använder du en Azure Cosmos DB för mongo DB API-konto eller Azure Cosmos DB-emulatorn för att köra en python-flaska To-Do-webbapp som klonas från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](local-emulator.md). 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) med [python-tillägget](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

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

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Kör webbappen](#run-the-web-app). 

Följande kodfragment är alla hämtade från *app.py* -filen och använder anslutnings strängen för den lokala Azure Cosmos DB-emulatorn. Lösenordet måste delas upp på det sätt som visas nedan för att möjliggöra de snedstreck som annars inte kan parsas.

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

3. Ange sedan miljövariabeln för kolv-appen med `set FLASK_APP=app.py` , `$env:FLASK_APP = app.py` för PowerShell-redigerare eller `export FLASK_APP=app.py` om du använder en Mac-dator. 

4. Kör appen med `flask run` och bläddra till *http: \/ /127.0.0.1:5000/*.

5. Lägg till och ta bort uppgifter och se hur de läggs till och ändras i samlingen.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Om du vill testa koden mot ett Live Azure Cosmos DB-konto går du till Azure Portal för att skapa ett konto.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Om du vill testa koden mot Live Azure Cosmos DB-kontot hämtar du information om anslutnings strängen. Kopiera den till appen.

1. I ditt Azure Cosmos DB konto i Azure Portal klickar du på **anslutnings sträng** i den vänstra navigerings rutan och väljer sedan **Läs-och skriv nycklar**. Du använder kopierings knapparna till höger på skärmen för att kopiera användar namnet, anslutnings strängen och lösen ordet. 

2. Öppna filen *app.py* i rotkatalogen.

3. Kopiera ditt **användarnamn** från portalen (med kopieringsknappen) och gör det till värdet för **name** i filen *app.py*.

4. Kopiera sedan värdet för **anslutnings strängen** från portalen och gör det till värdet för **MongoClient** i *app.py* -filen.

5. Kopiera slutligen **lösenordet** från portalen och gör det till värdet för **password** i filen *app.py*.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. Du kan köra den på samma sätt som innan.

## <a name="deploy-to-azure"></a>Distribuera till Azure

Om du vill distribuera den här appen kan du skapa en ny webbapp i Azure och aktivera kontinuerlig distribution med en förgrening av den här GitHub-lagringsplatsen. Följ den här [självstudien](../app-service/deploy-continuous-deployment.md) för att konfigurera kontinuerlig distribution med GitHub i Azure.

När du distribuerar till Azure bör du ta bort dina programnycklar och kontrollera att avsnittet nedan inte har kommenterats bort:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Du måste sedan lägga till MONGOURL, MONGO_PASSWORD och MONGO_USERNAME i programinställningarna. Följ den här [självstudien](../app-service/configure-common.md#configure-app-settings) om du vill veta mer om programinställningar i Azure Web Apps.

Om du inte vill skapa en förgrening av den här lagrings platsen kan du också välja knappen **distribuera till Azure** nedan. Du bör sedan gå till Azure och konfigurera program inställningarna med din Azure Cosmos DB konto information.

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

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB för mongo DB API-konto och använder Azure Cosmos DB-emulatorn för att köra en python-flaska To-Do webbapp som klonas från GitHub. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)