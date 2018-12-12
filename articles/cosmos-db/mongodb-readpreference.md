---
title: Använda läsinställningar för MongoDB med Azure Cosmos DB MongoDB API
description: Lär dig hur du använder MongoDB läsinställningar med Azure Cosmos DB MongoDB API
services: cosmos-db
author: vidhoonv
ms.author: sclyon
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.openlocfilehash: 4f40a2dac04102a19be7b3209c6bf37be9252cde
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875356"
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Hur du distribuerar globalt läser använda läsinställningar med Azure Cosmos DB MongoDB API 

Den här artikeln visar hur du distribuerar globalt läsåtgärder med [MongoDB läsinställningar](https://docs.mongodb.com/manual/core/read-preference/) inställningar med Azure Cosmos DB MongoDB API. 

## <a name="prerequisites"></a>Förutsättningar 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Referera till denna [snabbstarten](tutorial-global-distribution-mongodb.md) artikeln anvisningar om hur du använder Azure-portalen för att ställa in Azure Cosmos DB-konto med global distribution och sedan ansluta med hjälp av MongoDB API.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna ett git-terminalfönster, till exempel git bash och `cd` till en arbetskatalog.  

Kör följande kommandon för att klona exempellagringsplatsen. Baserat på din plattform intressanta, Använd någon av följande exempel-databaser:

1. [.NET-exempelprogram](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS-exempelprogram]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose-exempelprogram](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-exempelprogram](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot-exempelprogram](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Köra programmet

Beroende på vilken plattform som används för att installera de nödvändiga paketen och starta programmet. Om du vill installera beroenden, följer du Viktigt-filen som ingår i programmet exempellagringsplatsen. Till exempel i exempelprogrammet NodeJS använder du följande kommandon för att installera de nödvändiga paketen och starta programmet.

```bash
cd mean
npm install
node index.js
```
Programmet försöker ansluta till en MongoDB-källa och misslyckas eftersom anslutningssträngen är ogiltig. Följ stegen i filen viktigt att uppdatera anslutningssträngen `url`. Uppdatera också den `readFromRegion` till en läsregion i ditt Azure Cosmos DB-konto. Följande instruktioner används från NodeJS-exempel:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

När du har följt de här stegen exempelprogrammet körs och i följande utdata:

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

## <a name="read-using-read-preference-mode"></a>Läsa med läsinställningar läge

MongoDB innehåller följande läsinställningar lägen för klienter att använda:

1. PRIMÄRT TELEFONNR
2. PRIMARY_PREFERRED
3. SEKUNDÄR
4. SECONDARY_PREFERRED
5. NÄRMASTE

Referera till detaljerade [MongoDB läsinställningar beteende](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) dokumentationen för mer information på beteenden som dessa läsa inställningar lägen. I Azure Cosmos DB mappar primära skrivregionen och sekundära mappar till skrivregion.

Baserat på vanliga scenarier, rekommenderar vi att du använder följande inställningar:

1. Om **med låg svarstid för läsning** är krävs, Använd den **NEAREST** läsa läge. Den här inställningen dirigeras läsåtgärder till den närmast tillgängliga regionen. Observera att om den närmaste regionen är skrivbar, sedan dessa åtgärder dirigeras till den regionen.
2. Om **hög tillgänglighet och geo-distribution av läsningar** krävs (svarstiden är inte en begränsning), sedan använda den **sekundära ÖNSKADE** läsa läge. Den här inställningen dirigeras läsåtgärder till en tillgänglig läsregion. Om det finns inga läsregion dirigeras begäranden till skrivregionen.

Följande kodavsnitt från exempelprogrammet visar hur du konfigurerar NÄRMASTE läsinställningar i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

På samma sätt visar fragmentet nedan hur du konfigurerar läsinställningar SECONDARY_PREFERRED i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Den läsinställningar kan också anges genom att skicka `readPreference` som en parameter i anslutningsalternativ för sträng-URI:

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

Referera till motsvarande exemplet program-lagringsplatser för andra plattformar, till exempel [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Läsa med hjälp av taggar

Förutom läsinställningar-läge kan MongoDB du använda taggar för att dirigera läsåtgärder. I Azure Cosmos DB MongoDB API: t, den `region` taggen ingår som standard som en del av den `isMaster` svaret:

```json
"tags": {
         "region": "West US"
      }
```

Därför MongoClient kan använda den `region` tagga tillsammans med regionnamn att dirigera läsåtgärder till specifika regioner. För Azure Cosmos DB-konton regionnamn finns i Azure-portalen till vänster under **Inställningar -> replikdata globalt**. Den här inställningen är användbar för att uppnå **läsa isolering** -fall i vilka klientprogram vill dirigera läsåtgärder i en specifik region. Den här inställningen är perfekt för icke-produktion/analytics skriver scenarier som kan köras i bakgrunden och inte är kritiska tjänster i produktion.

Följande kodavsnitt från exempelprogrammet visar hur du konfigurerar den läsinställningar med taggar i NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Referera till motsvarande exemplet program-lagringsplatser för andra plattformar, till exempel [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

I den här artikeln har du lärt dig hur du distribuerar globalt läsåtgärder använda läsinställningar med Azure Cosmos DB MongoDB API.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen, tar du bort alla resurser som skapades i den här artikeln i Azure-portalen med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
* [Konfigurera ett globalt replikerade Azure Cosmos DB-konto och Använd det med MongoDB API](tutorial-global-distribution-mongodb.md)
* [Utveckla lokalt med emulatorn](local-emulator.md)
