---
title: 'Azure Cosmos DB: Skapa en Flask-webbapp med Python och Azure Cosmos DB MongoDB-API:et | Microsoft Docs'
description: Visar ett Python Flask-kodexempel som du kan använda för att ansluta till och ställa frågor via Azure Cosmos DB MongoDB-API:t
services: cosmos-db
documentationcenter: ''
author: heatherbshapiro
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: 095cc724beb9f35896bd02e299523839a9f43f4b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB: Skapa en Flask-app med MongoDB-API:et

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

Den här snabbstartsguiden använder följande [Flask-exempel](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) och visar hur du kan bygga en enkel att göra-app i Flask med [Microsoft Azure Cosmos DB-emulatorn](local-emulator.md) och Microsoft Azure Cosmos DB [MongoDB API](mongodb-introduction.md) i stället för MongoDB.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Hämta [Azure Cosmos DB-emulatorn](local-emulator.md). Emulatorn stöds för tillfället endast på Windows. Exemplet visar hur du kan använda exemplet med en produktionsnyckel från Azure, vilket går att göra på valfri plattform.

- Om du inte redan har Visual Studio Code installerat kan du snabbt installera [VS-kod](https://code.visualstudio.com/Download) för din plattform (Windows, Mac, Linux).

- Tänk på att lägga till Python-språkstöd genom att installera ett av de populära Python-tilläggen.
    1. Välj ett tillägg.
    2. Installera tillägget genom att skriva in `ext install` i kommandopaletten `Ctrl+Shift+P`.

    Exemplen i det här dokumentet använder Don Jayamannes populära [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) med fullständiga funktioner.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Flask-MongoDB API-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt.

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

Följande kodavsnitt hämtas från filen app.py och använder anslutningssträngen för den lokala Azure Cosmos DB-emulatorn. Lösenordet måste delas upp på det sätt som visas nedan för att möjliggöra de snedstreck som annars inte kan parsas.

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

3. Ange sedan miljövariabeln för Flask-appen med `set FLASK_APP=app.py` eller `export FLASK_APP=app.py` om du använder en Mac.

4. Kör appen med `flask run` och bläddra till [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Lägg till och ta bort uppgifter och se hur de läggs till och ändras i samlingen.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Om du vill testa koden mot ett aktivt Azure Cosmos DB-konto går du till Azure-portalen för att skapa ett konto och hämta information om anslutningssträngen. Kopiera den till appen.

1. Öppna ditt Azure Cosmos DB-konto i [Azure Portal](http://portal.azure.com/), klicka på **Anslutningssträng** och därefter på **Läs- och skrivnycklar**. Använd kopieringsknapparna till höger på skärmen och kopiera Användarnamn, Lösenord och Värd till filen Dal.cs i nästa steg.

2. Öppna filen **app.py** i rotkatalogen.

3. Kopiera ditt **användarnamn** från portalen (med kopieringsknappen) och gör det till värdet för **name** i filen **app.py**.

4. Kopiera sedan **anslutningssträngen** från portalen och gör den till värdet för MongoClient i filen **app.py**.

5. Kopiera slutligen **lösenordet** från portalen och gör det till värdet för **password** i filen **app.py**.

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. Du kan köra den på samma sätt som innan.

## <a name="deploy-to-azure"></a>Distribuera till Azure

Om du vill distribuera den här appen kan du skapa en ny webbapp i Azure och aktivera kontinuerlig distribution med en förgrening av den här github-lagringsplatsen. Följ den här [självstudien](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment) om du vill konfigurera kontinuerlig distribution med Github i Azure.

När du distribuerar till Azure bör du ta bort dina programnycklar och kontrollera att avsnittet nedan inte har kommenterats bort:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Du måste sedan lägga till MONGOURL, MONGO_PASSWORD och MONGO_USERNAME i programinställningarna. Följ den här [självstudien](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) om du vill veta mer om programinställningar i Azure Web Apps.

Om du inte vill skapa en förgrening av den här lagringsplatsen kan du även klicka på knappen nedan för att distribuera till Azure. Du bör sedan gå till Azure och konfigurera programinställningarna med dina uppgifter för Cosmos DB-kontot.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> Om du planerar att lagra din kod i Github eller något annat alternativ för källåtkomstkontroll måste du tänka på att ta bort anslutningssträngarna från koden. De kan anges med programinställningar för webbappen i stället.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto och kör en Flask-app via API:t för MongoDB. Du kan nu importera ytterligare data till Cosmos DB-kontot.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med MongoDB-API:t](mongodb-migrate.md)
