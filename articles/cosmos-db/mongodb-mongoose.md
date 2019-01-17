---
title: Ansluta en Node.js Mongoose-program till Azure Cosmos DB
description: Lär dig hur du använder Mongoose-ramverket för att lagra och hantera data i Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 6cdfa7492a9916cc3e2300ad71e21acfb68e3d30
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352248"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Ansluta en Node.js Mongoose-program till Azure Cosmos DB

Den här självstudien visar hur du använder den [Mongoose-ramverket](https://mongoosejs.com/) när du lagrar data i Cosmos DB. Vi använder Azure Cosmos DB: s API för MongoDB i den här genomgången. För dem som inte vet är Mongoose ett ramverk för modellering av objekt för MongoDB i Node.js och tillhandahåller en enkel och schemabaserad lösning för att modellera dina programdata.

Cosmos DB är Microsofts globalt distribuerade databas för flera modeller tjänst. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) version 0.10.29 eller högre.

## <a name="create-a-cosmos-account"></a>Skapa ett Cosmos-konto

Nu ska vi skapa ett Cosmos-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera Node.js-programmet](#SetupNode). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [konfigurationen av Node.js-programmet](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Konfigurera Node.js-programmet

>[!Note]
> Om du bara vill gå igenom exempelkoden istället för konfigurationen av själva programmet kan du klona [exemplet](https://github.com/Azure-Samples/Mongoose_CosmosDB) som används för den här självstudien och skapa ett Node.js Mongoose-program på Azure Cosmos DB.

1. Om du vill skapa ett Node.js-program i en valfri mapp kör du följande kommando i en nods kommandotolk.

    ```npm init```

    När du svarar på frågorna är projektet klart.

1. Lägg till en ny fil och ge den namnet ```index.js```.
1. Installera de paket som behövs med något av ```npm install```-alternativen:
    * Mongoose: ```npm install mongoose@5 --save```

    > [!Note]
    > Mongoose exempel anslutningen nedan baseras på Mongoose 5 +, som har ändrats sedan tidigare versioner.
    
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
    COSMOSDB_CONNSTR=mongodb://{cosmos-user}.documents.azure.com:10255/{dbname}
    COSMODDB_USER=cosmos-user
    COSMOSDB_PASSWORD=cosmos-secret
    ```

1. Anslut till Cosmos DB med Mongoose-ramverket genom att lägga till följande kod i slutet av index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Här läses miljövariablerna in med process.env.{variableName} med hjälp av ”dotenv”-npm-paketet.

    När du är ansluten till Azure Cosmos DB kan du börja konfigurera objektmodeller i Mongoose.

## <a name="caveats-to-using-mongoose-with-cosmos-db"></a>Varningar för att använda Mongoose med Cosmos DB

För varje modell du skapar, skapar Mongoose en ny samling. Men kanske med tanke på faktureringsmodellen per samling för Cosmos DB bör det inte det mest kostnadseffektiva sättet att gå, om du har flera objektmodeller som är sparsamt ifyllda.

Den här genomgången täcker båda modellerna. Först går vi igenom lagring av en typ av data per samling. Det är Mongooses faktiska beteende.

Mongoose har också ett begrepp som kallas [Diskriminatorer](https://mongoosejs.com/docs/discriminators.html). Diskriminatorer är en mekanism för schemaarv. De gör att du kan ha flera modeller med överlappande scheman ovanpå samma underliggande MongoDB-samling.

Du kan lagra olika datamodeller i samma samling och sedan använda en filtersats vid frågetiden för att endast hämta de data som krävs.

### <a name="one-collection-per-object-model"></a>En samling per objektmodell

Standardbeteendet i Mongoose är att skapa en MongoDB-samling varje gång du skapar en objektmodell. Det här avsnittet förklaras hur du gör detta med Azure Cosmos DB API för MongoDB. Den här metoden rekommenderas när du har objektmodeller med stora mängder data. Det är standarddriftsmodellen för Mongoose, så du kanske är bekant med det om du är bekant med Mongoose.

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

1. Först sparar vi objektet till Cosmos DB. Det skapar en samling på ett och samma ställe.

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

1. Nu ser går in på Azure portal, du två samlingar som skapats i Cosmos DB.

    ![Node.js-självstudie – Skärmbild av Azure-portalen som visar ett Azure Cosmos DB-konto, med flera samlingsnamn markerade – Node-databas][multiple-coll]

1. Slutligen ska vi läsa data från Cosmos DB. Eftersom vi använder standarddriftmodellen i Mongoose är läsningarna desamma som alla andra läsningar med Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Använda Mongoose-diskriminatorer för att lagra data i en enda samling

I den här metoden använder vi [Mongoose-Diskriminatorer](https://mongoosejs.com/docs/discriminators.html) för att optimera för kostnaden för varje samling. Diskriminatorer gör att du kan definiera en särskiljande ”nyckel”, som gör att du kan lagra, särskilja och filtrera på olika objektmodeller.

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

Som du ser är det enkelt att arbeta med diskriminatorer i Mongoose. Så om du har en app som använder Mongoose-ramverket är ett sätt att komma upp ditt program med hjälp av Azure Cosmos-API för MongoDB utan att för många ändringar i den här självstudien.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
