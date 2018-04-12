---
title: Använda Mongoose-ramverket med Azure Cosmos DB | Microsoft Docs
description: Läs hur du ansluter en Node.js Mongoose-app till Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: romitgirdhar
manager: kfile
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: 4ce86c6a3fb3d191f101329025d0aedf266ce79e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB: Använda Mongoose-ramverket med Azure Cosmos DB

I den här självstudien visas hur du använder [Mongoose-ramverket](http://mongoosejs.com/) när du lagrar data i Azure Cosmos DB. I den här genomgången använder vi MongoDB-API:et för Azure Cosmos DB. För dem som inte vet är Mongoose ett ramverk för modellering av objekt för MongoDB i Node.js och tillhandahåller en enkel och schemabaserad lösning för att modellera dina programdata.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) version 0.10.29 eller högre.

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera Node.js-programmet](#SetupNode). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [konfigurationen av Node.js-programmet](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Konfigurera Node.js-programmet

>[!Note]
> Om du bara vill gå igenom exempelkoden istället för konfigurationen av själva programmet kan du klona [exemplet](https://github.com/Azure-Samples/Mongoose_CosmosDB) som används för den här självstudien och skapa ett Node.js Mongoose-program på Azure Cosmos DB.

1. Om du vill skapa ett Node.js-program i en valfri mapp kör du följande kommando i en nods kommandotolk.

    ```npm init```

    När du svarar på frågorna är projektet klart.

1. Lägg till en ny fil och ge den namnet ```index.js```.
1. Installera de paket som behövs med något av ```npm install```-alternativen:
    * Mongoose: ```npm install mongoose --save```
    * Dotenv (om du vill läsa in dina hemligheter från en .env-fil): ```npm install dotenv --save```

    >[!Note]
    > Flaggan ```--save``` lägger till beroendet till package.json-filen.

1. Importera beroendena i din index.js-fil.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Lägg till Cosmos DB-anslutningssträngen och Cosmos DB-namnet till filen ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Anslut till Azure Cosmos DB med Mongoose-ramverket genom att lägga till följande kod till slutet av index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > Här läses miljövariablerna in med process.env.{variableName} med hjälp av ”dotenv”-npm-paketet.

    När du är ansluten till Azure Cosmos DB kan du börja konfigurera objektmodeller i Mongoose.

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Varningar för att använda Mongoose med Azure Cosmos DB

För varje modell du skapar, skapar Mongoose en ny MongoDB-samling på ett och samma ställe. Men med tanke på faktureringsmodellen per samling för Azure Cosmos DB kanske det inte är det mest kostnadseffektiva sättet om du har flera objektmodeller som är sparsamt ifyllda.

Den här genomgången täcker båda modellerna. Först går vi igenom lagring av en typ av data per samling. Det är Mongooses faktiska beteende.

Mongoose har också ett begrepp som kallas [Diskriminatorer](http://mongoosejs.com/docs/discriminators.html). Diskriminatorer är en mekanism för schemaarv. De gör att du kan ha flera modeller med överlappande scheman ovanpå samma underliggande MongoDB-samling.

Du kan lagra olika datamodeller i samma samling och sedan använda en filtersats vid frågetiden för att endast hämta de data som krävs.

### <a name="one-collection-per-object-model"></a>En samling per objektmodell

Standardbeteendet i Mongoose är att skapa en MongoDB-samling varje gång du skapar en objektmodell. I det här avsnittet förklaras hur du gör det med MongoDB för Azure Cosmos DB. Den här metoden rekommenderas med Azure Cosmos DB när du har objektmodeller med större datamängder. Det är standarddriftsmodellen för Mongoose, så du kanske är bekant med det om du är bekant med Mongoose.

1. Öppna ```index.js``` igen.

1. Skapa schemadefinitionen för ”Familj”.

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

1. Skapa ett objekt för ”Familj”.

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

1. Först sparar vi objektet i Azure Cosmos DB. Det skapar en samling på ett och samma ställe.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Nu ska vi skapa ytterligare ett schema och ett objekt. Den här gången skapar vi ett för de ”semesterdestinationer” familjerna kan ha intresse av.
    1. Precis som förra gången skapar vi schemat
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Skapa ett exempelobjekt (Du kan lägga till flera objekt till det här schemat) och spara det.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Gå nu till Azure-portalen. Du ser två samlingar som har skapats i Azure Cosmos DB.

    ![Node.js-självstudie – Skärmbild av Azure-portalen som visar ett Azure Cosmos DB-konto, med flera samlingsnamn markerade – Node-databas][mutiple-coll]

1. Läs till sist data från Azure Cosmos DB. Eftersom vi använder standarddriftmodellen i Mongoose är läsningarna desamma som alla andra läsningar med Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Använda Mongoose-diskriminatorer för att lagra data i en enda samling

I den här metoden använder vi [Mongoose-diskriminatorer](http://mongoosejs.com/docs/discriminators.html) för att optimera för kostnaden för varje Azure Cosmos DB-samling. Diskriminatorer gör att du kan definiera en särskiljande ”nyckel”, som gör att du kan lagra, särskilja och filtrera på olika objektmodeller.

Här skapar vi en basobjektmodell med en särskiljande nyckel och lägger till ”Familj” och ”VacationDestinations” som tillägg till basmodellen.

1. Vi konfigurerar baskonfigurationen och definierar diskriminatornyckeln.

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

1. Nu definierar vi modellen ”Familj”. Lägg märke till att vi här använder ```commonModel.discriminator``` istället för ```mongoose.model```. Vi lägger dessutom till grundkonfigurationen till mongoose-schemat. Så här är discriminatorKey ```FamilyType```.

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

1. På samma sätt lägger vi till ytterligare ett schema, denna gång för ”VacationDestinations”. Här är DiscriminatorKey ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Slutligen skapar vi objekt för modellen och sparar den.
    1. Nu ska vi lägga till objekt till modellen ”Familj”.
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

    1. Därefter lägger vi till objekt till modellen ”VacationDestinations” och sparar den.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Om du nu går tillbaka till Azure-portalen lägger du märke till att du enda samling som heter ```alldata``` med både ”Familj”- och ”VacationDestinations”-data.

    ![Node.js-självstudie – Skärmbild av Azure-portalen som visar ett Azure Cosmos DB-konto, med samlingsnamnet markerat – Node-databas][alldata]

1. Lägg också märke till att varje objekt har ytterligare ett attribut som heter ```__type```, som hjälper till att skilja mellan de två olika objektmodellerna.

1. Läs till sist data som är lagrade i Azure Cosmos DB. Mongoose tar hand om filtrering av data baserat på modell. Så du gör inget annorlunda när du läser data. Ange bara din modell (i det här fallet ```Family_common```) så hanterar Mongoose filtreringen av ”DiscriminatorKey”.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Som du ser är det enkelt att arbeta med diskriminatorer i Mongoose. Så om du har en app som använder Mongoose-ramverket är den här självstudien ett sätt för dig att komma igång och köra programmet på MongoDB-API:n på Azure Cosmos DB utan att det krävs så många förändringar.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om MongoDB-åtgärder, -operatorer, -nivåer och -kommandon, samt alternativ som stöds av API:et för Azure Cosmos DB MongoDB i informationen om [stöd för MongoDB-API:et för MongoDB-funktioner och syntax](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png