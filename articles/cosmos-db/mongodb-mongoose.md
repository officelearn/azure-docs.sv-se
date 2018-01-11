---
title: Med Azure Cosmos DB Mongoose framework | Microsoft Docs
description: "Lär dig hur du ansluter en Node.js Mongoose-app till Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: romitgirdhar
manager: jhubbard
editor: 
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: 9878936b5dd76730633dec16b1c3a3eaac78e95a
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB: Med Azure Cosmos DB Mongoose framework

Den här kursen visar hur du använder den [Mongoose Framework](http://mongoosejs.com/) när du lagrar data i Azure Cosmos-databasen. Vi använder MongoDB-API: et för Azure Cosmos-databasen för den här genomgången. För de som du vet, Mongoose är ett ramverk för modellering av objekt för MongoDB i Node.js och ger en enkel och schema-baserad lösning för att modellera dina programdata.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) version 0.10.29 eller högre.

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [konfigurera Node.js-programmet](#SetupNode). Om du använder Azure Cosmos DB-emulatorn, Följ stegen i [Azure Cosmos DB emulatorn](local-emulator.md) att ställa in emulatorn och gå vidare till [konfigurera Node.js-programmet](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Konfigurera Node.js-programmet

>[!Note]
> Om du vill bara genomgången exempelkoden i stället för installationsprogrammet för programmet, klona den [exempel](https://github.com/Azure-Samples/Mongoose_CosmosDB) i den här självstudien och skapa din Node.js Mongoose App på Azure Cosmos DB.

1. Kör följande kommando i Kommandotolken nod om du vill skapa ett Node.js-program i valfri mapp.

    ```npm init```

    Svara på frågorna och projektet kommer att gå vidare.

1. Lägga till en ny fil i mappen och ge den namnet ```index.js```.
1. Installera de nödvändiga paketen med någon av de ```npm install``` alternativ:
    * Mongoose:```npm install mongoose --save```
    * Dotenv (om du vill läsa in din hemliga från en .env-fil):```npm install dotenv --save```
    
    >[!Note]
    > Den ```--save``` flaggan lägger till beroendet i package.json-filen.

1. Importera beroenden i filen index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Lägg till anslutningssträng Cosmos DB och Cosmos-databasnamn för den ```.env``` filen.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Ansluta till Azure Cosmos-databasen med Mongoose framework genom att lägga till följande kod i slutet av index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > Här kan laddas miljövariablerna med hjälp av process.env. {variableName} 'dotenv' npm paketera.

    När du är ansluten till Azure Cosmos DB kan börjar du nu konfigurera nätverksobjektmodellerna i Mongoose.
    
## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Varningar att använda Mongoose med Azure Cosmos DB

Mongoose skapar för varje modell som du skapar en ny samling MongoDB djupare. Dock kanske anges per samling fakturering modell för Azure Cosmos DB det inte mest kostnadseffektivt sätt att gå, om du har flera objektmodeller som fylls sparsely.

Den här genomgången täcker båda modellerna. Vi först rapporterar genomgång av lagring av en typ av data per samling. Detta görs defacto för Mongoose.

Mongoose har också ett begrepp som kallas [Discriminators](http://mongoosejs.com/docs/discriminators.html). Discriminators är en mekanism för arv av schemat. De gör att du kan ha flera modeller med överlappande scheman ovanpå samma underliggande MongoDB-samling.

Du kan lagra olika datamodeller i samma samling och sedan använda en filtersats när databasfrågan för att hämtar de data som krävs.

### <a name="one-collection-per-object-model"></a>En samling per objektmodellen

Mongoose standardbeteendet är att skapa en samling MongoDB varje gång du skapar en objektmodell. Det här avsnittet behandlar så att göra detta med MongoDB för Azure Cosmos DB. Den här metoden bör med Azure Cosmos DB när du har nätverksobjektmodellerna med stora mängder data. Detta är standard kör modellen för Mongoose, så kanske du känner till, om du är bekant med Mongoose.

1. Öppna din ```index.js``` igen.

1. Skapa schemadefinitionen för' '.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Skapa ett objekt för' '.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Slutligen ska vi spara objektet i Azure Cosmos DB. Detta skapar en samling djupare.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Nu ska vi skapa en annan schemat och objektformatet. Den här gången, skapar vi en för 'semester mål, som kan ha intresse av.
    1. Precis som senast, nu ska vi skapa schemat
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Skapa en exempel-objekt (du kan lägga till flera objekt till det här schemat) och spara den.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Nu kan du gå in på Azure-portalen, se två samlingar som skapats i Azure Cosmos DB.

    ![Självstudie om node.js – skärmdump av Azure-portalen visar ett Azure DB som Cosmos-konto med namnet på samlingen markerat – Node-databas][alldata]

1. Slutligen ska vi läsa data från Azure Cosmos-databasen. Eftersom vi använder operativsystemet standardmodell Mongoose är samma som andra läsningar med Mongoose läsningar.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Använda Mongoose discriminators för att lagra data i en enda samling

I den här metoden kan vi använda [Mongoose Discriminators](http://mongoosejs.com/docs/discriminators.html) för att optimera kostnader för varje Azure DB som Cosmos-samling. Discriminators kan du definiera en olika 'nyckel', där du kan lagra, skilja och filtrera på olika objektmodeller.

Här kan vi skapa en basobjektet modell, definiera olika nyckel och lägga till 'Familj' och 'VacationDestinations' som ett tillägg till grundläggande modellen.

1. Vi ställa in den grundläggande konfigurationen och definiera diskriminator nyckeln.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Nu ska vi definiera den gemensamma objektmodellen

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Vi nu definiera-familjen-modellen. Observera att vi använder ```commonModel.discriminator``` i stället för ```mongoose.model```. Dessutom kan vi också lägger till den grundläggande konfigurationen mongoose schemat. Så här, discriminatorKey är ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. På liknande sätt, Lägg till ett annat schema nu för VacationDestinations. Här är DiscriminatorKey ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Slutligen ska vi skapa objekt för modellen och spara den.
    1. Lägg till objekt-familjen-modellen.
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. Nu ska vi lägga till objekt i 'VacationDestinations'-modell och spara den.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Nu om du går tillbaka till Azure-portalen kan du se att du har endast en samling som heter ```alldata``` med både '' och 'VacationDestinations' data.

    ![Självstudie om node.js – skärmdump av Azure-portalen visar ett Azure DB som Cosmos-konto med namnet på samlingen markerat – Node-databas][mutiple-coll]

1. Observera också att varje objekt har ett annat attribut som kallas ```__type```, som bidrar du skilja mellan två olika objekt.

1. Slutligen ska vi läsa data som lagras i Azure Cosmos DB. Mongoose hand tar om filtrering av data baserat på modellen. Så behöver du göra något annat vid läsning av data. Ange bara din modell (i det här fallet ```Family_common```) och Mongoose handtag filtrering på DiscriminatorKey.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Du kan se är det enkelt att arbeta med Mongoose discriminators. Så om du har en app som använder Mongoose framework är ett sätt för dig att komma ditt program och körs på MongoDB-API på Azure Cosmos DB utan att för många ändringar i den här kursen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om MongoDB åtgärder, operatorer, steg, kommandon och alternativ som stöds av Azure Cosmos DB MongoDB API i [MongoDB-API-stöd för MongoDB-funktioner och syntax](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png