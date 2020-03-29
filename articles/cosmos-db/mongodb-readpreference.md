---
title: Använd läsinställningar med Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du använder MongoDB Läsinställningar med Azure Cosmos DB:s API för MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 10e6ed556abe8f8c438e5436fbb93c1b70b85d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445167"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Så här distribuerar du läsningar globalt med Azure Cosmos DB:s API för MongoDB

Den här artikeln visar hur du globalt distribuerar läsåtgärder med Inställningar för [MongoDB läsinställningar](https://docs.mongodb.com/manual/core/read-preference/) med Hjälp av Azure Cosmos DB:s API för MongoDB.

## <a name="prerequisites"></a>Krav 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

I den här [snabbstartsartikeln](tutorial-global-distribution-mongodb.md) finns instruktioner om hur du använder Azure-portalen för att konfigurera ett Cosmos-konto med global distribution och sedan ansluta till den.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna ett git-terminalfönster, till exempel git bash och `cd` till en arbetskatalog.  

Kör följande kommandon för att klona exempellagringsplatsen. Använd något av följande exempeldatabaser baserat på din intresseplattform:

1. [EXEMPELprogram för .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS-exempelprogram]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Exempelprogram för mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-exempelprogram](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot prov ansökan](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Köra appen

Beroende på vilken plattform som används, installera de nödvändiga paketen och starta programmet. Om du vill installera beroenden följer du den README som ingår i exempelprogramdatabasen. I exempelvis nodeJS-exempelprogrammet använder du följande kommandon för att installera de nödvändiga paketen och starta programmet.

```bash
cd mean
npm install
node index.js
```
Programmet försöker ansluta till en MongoDB-källa och misslyckas eftersom anslutningssträngen är ogiltig. Följ stegen i README för att `url`uppdatera anslutningssträngen . Uppdatera också `readFromRegion` till en läsregion i ditt Cosmos-konto. Följande instruktioner kommer från nodeJS-exemplet:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

När du har följt dessa steg körs och ger exempelprogrammet följande utdata:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Läsa med läsinställningsläge

MongoDB-protokollet innehåller följande läsinställningslägen för klienter som ska använda:

1. Primära
2. PRIMARY_PREFERRED
3. Sekundära
4. SECONDARY_PREFERRED
5. Närmaste

Mer information om hur du aktiverar var och en av dessa läsinställningslägen finns i dokumentationen för den detaljerade [mongoDb-läsinställningen.](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) I Cosmos DB, primära kartor till SKRIV-region och sekundära kartor till LÄS-regionen.

Baserat på vanliga scenarier rekommenderar vi att du använder följande inställningar:

1. Om det krävs **läsningar med låg latens** använder du **läget NÄRMAST** läsinställning. Den här inställningen dirigerar avläsningsåtgärderna till närmaste tillgängliga region. Observera att om den närmaste regionen är SKRIV-regionen dirigeras dessa åtgärder till den regionen.
2. Om **hög tillgänglighet och geofördelning av läsningar** krävs (svarstid är inte en begränsning) använder du läsinställningsläget **SEKUNDÄRT.** Den här inställningen dirigerar läsåtgärder till en tillgänglig READ-region. Om det inte finns någon LÄS-region dirigeras begäranden till SKRIV-regionen.

Följande utdrag från exempelprogrammet visar hur du konfigurerar NEAREST Läsinställningar i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

På samma sätt visar kodavsnittet nedan hur du konfigurerar SECONDARY_PREFERRED läsinställning i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Läsinställningen kan också ställas `readPreference` in genom att skickas som en parameter i URI-alternativen för anslutningssträngen:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Se motsvarande exempelprogramrepor för andra plattformar, till exempel [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Läsa med taggar

Förutom läsinställningsläget tillåter MongoDB-protokollet användning av taggar för direktläsning. I Cosmos DB:s API för MongoDB inkluderas taggen `region` `isMaster` som standard som en del av svaret:

```json
"tags": {
         "region": "West US"
      }
```

Därför kan MongoClient `region` använda taggen tillsammans med regionnamnet för att dirigera läsåtgärder till specifika regioner. För Cosmos-konton finns regionnamn i Azure-portalen till vänster under **Inställningar->Replikdata globalt**. Den här inställningen är användbar för att uppnå **läsisolering** - fall där klientprogram endast vill dirigera läsåtgärder till en viss region. Den här inställningen är idealisk för scenarier av icke-produktions-/analystyp, som körs i bakgrunden och inte är produktionskritiska tjänster.

Följande utdrag från exempelprogrammet visar hur du konfigurerar läsinställningarna med taggar i NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Se motsvarande exempelprogramrepor för andra plattformar, till exempel [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

I den här artikeln har du lärt dig hur du distribuerar läsåtgärder globalt med Läsinställningar med Azure Cosmos DB:s API för MongoDB.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda den här appen tar du bort alla resurser som skapas av den här artikeln i Azure-portalen med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
* [Konfigurera en globalt distribuerad databas med Azure Cosmos DB:s API för MongoDB](tutorial-global-distribution-mongodb.md)
* [Utveckla lokalt med Azure Cosmos DB-emulatorn](local-emulator.md)
