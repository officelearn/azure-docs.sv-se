---
title: "Med Azure Cosmos DB MongoDB API MongoDB Läs inställningar | Microsoft Docs"
description: "Lär dig hur du använder MongoDB Läs inställningar med Azure Cosmos DB MongoDB-API"
services: cosmos-db
documentationcenter: 
author: vidhoonv
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: 
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 02/26/2018
ms.author: viviswan
ms.openlocfilehash: b28285695f52d6dbcc33d9fb0efe06b43cdf1cab
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Hur du distribuerar globalt läser med Azure Cosmos DB MongoDB API Läs inställningar 

Den här artikeln visar hur du distribuerar globalt läsåtgärder med [MongoDB Läs inställningar](https://docs.mongodb.com/manual/core/read-preference/) med Azure Cosmos DB MongoDB-API. 

## <a name="prerequisites"></a>Förutsättningar 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Referera till den här [Quickstart](tutorial-global-distribution-mongodb.md) artikel anvisningar om hur du använder Azure-portalen att ställa in Azure DB som Cosmos-konto med global distributionsplatsen och ansluter sedan med MongoDB-API.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna ett git-terminalfönster, till exempel git bash och `cd` till en arbetskatalog.  

Kör följande kommandon för att klona exempellagringsplatsen. Baserat på din plattform intressanta, Använd följande exempel databaser:

1. [Exempelprogram för .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS exempelprogrammet]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose exempelprogrammet](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java exempelprogrammet](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot exempelprogrammet](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Köra programmet

Beroende på plattform som används för att installera de nödvändiga paketen och starta programmet. Installera beroenden så viktigt som ingår i exemplet programmet databasen. Till exempel i exempelprogrammet NodeJS använda följande kommandon för att installera de nödvändiga paketen och starta programmet.

```bash
cd mean
npm install
node index.js
```
Programmet försöker ansluta till en MongoDB-källa och misslyckas eftersom anslutningssträngen är ogiltig. Följ stegen i filen viktigt att uppdatera anslutningssträngen `url`. Uppdatera också den `readFromRegion` till en skrivskyddad region i Azure DB som Cosmos-konto. Följande instruktioner är från NodeJS-exemplet:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

När du har följt de här stegen exempelprogrammet kör och i följande utdata:

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

## <a name="read-using-read-preference-mode"></a>Läsa med Läs läge

MongoDB innehåller följande Läs inställningar lägen för klienter att använda:

1. PRIMÄRT TELEFONNR
2. PRIMARY_PREFERRED
3. SEKUNDÄR
4. SECONDARY_PREFERRED
5. NÄRMASTE

Referera till den detaljerade [MongoDB Läs inställningar beteende](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) dokumentation för information om hur dessa läsa inställningar lägen. I Azure Cosmos DB primära mappar till SKRIVÅTGÄRDER region och sekundära mappar till Läs-region.

Baserat på vanliga scenarier, bör du använda följande inställningar:

1. Om **låg latens läser** är krävs, använda den **NEAREST** läsa läge. Den här inställningen dirigeras läsåtgärder till den närmaste tillgängliga region. Observera att om den närmaste regionen är SKRIVÅTGÄRDER region, sedan dessa åtgärder riktas till den regionen.
2. Om **hög tillgänglighet och geo distribution läsningar** krävs (svarstiden är inte ett villkor), sedan använda den **sekundära ÖNSKADE** läsa läge. Den här inställningen dirigeras läsåtgärder till en tillgänglig Läs region. Om det finns ingen region har läs dirigeras begäranden till WRITE-region.

Följande kodavsnitt från exempelprogrammet visar hur du konfigurerar den NÄRMASTE Läs inställningar i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

På liknande sätt visar kodfragmentet nedan hur du konfigurerar SECONDARY_PREFERRED Läs inställningar i NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Referera till motsvarande exempel programmet repor för andra plattformar som [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Läsa med hjälp av taggar

Förutom Läs läge kan MongoDB du använda taggar för att dirigera läsåtgärder. I Azure Cosmos DB för MongoDB-API: T i `region` taggen ingår som standard som en del av den `isMaster` svar:

```json
"tags": {
         "region": "West US"
      }
```

Därför MongoClient kan använda den `region` tagga tillsammans med regionnamn att dirigera läsåtgärder till vissa områden. För Azure Cosmos DB konton regionnamn hittar du i Azure-portalen till vänster under **Inställningar -> replikdata globalt**. Den här inställningen är användbar för att uppnå **läsa isolering** -fall i vilka klientprogrammet vill dirigera läsåtgärder till en viss region. Den här inställningen är idealisk för icke-produktion/analytics skriver scenarier som körs i bakgrunden och som inte produktion kritiska tjänster.

Följande kodavsnitt från exempelprogrammet visar hur du konfigurerar inställningen Läs med taggar i NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Referera till motsvarande exempel programmet repor för andra plattformar som [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) och [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Du har lärt dig hur du distribuerar globalt läsåtgärder med Azure Cosmos DB MongoDB API Läs inställningar i den här artikeln.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda den här appen, tar du bort alla resurser som skapats av den här artikeln i Azure-portalen med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
* [Konfigurera ett globalt replikerade Azure DB som Cosmos-konto och använda den med MongoDB-API](tutorial-global-distribution-mongodb.md)
* [Utveckla lokalt med emulatorn](local-emulator.md)
