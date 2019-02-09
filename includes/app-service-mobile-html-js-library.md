---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55904981"
---
## <a name="create-client"></a>Skapa en klientanslutning
Skapa en klientanslutning genom att skapa ett `WindowsAzure.MobileServiceClient`-objekt.  Ersätt `appUrl` med URL-adressen till din mobilapp.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>Arbeta med tabeller
För att få åtkomst till eller uppdatera data skapar du en referens till serverdelstabellen. Ersätt `tableName` med namnet på tabellen

```javascript
var table = client.getTable(tableName);
```

När du har en tabellreferens kan du arbeta mer med din tabell:

* [Fråga en tabell](#querying)
  * [Filtrera data](#table-filter)
  * [Växla genom data](#table-paging)
  * [Sortera data](#sorting-data)
* [Infoga data](#inserting)
* [Ändra data](#modifying)
* [Ta bort data](#deleting)

### <a name="querying"></a>Hur: Fråga en tabellreferens
När du har en tabellreferens kan använda du den för att fråga efter data på servern.  Frågor görs på ett "LINQ-liknande" språk.
Använd följande kod för att returnera alla data från tabellen:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Klarfunktionen anropas med resultatet.  Använd inte `for (var i in results)` i klarfunktionen eftersom det upprepas över informationen som ingår i resultatet när andra frågefunktioner (som `.includeTotalCount()`) används.

Mer information om frågesyntaxen finns i [dokumentationen för frågeobjekt].

#### <a name="table-filter"></a>Filtrera data på servern
Du kan använda en `where`-sats på tabellreferensen:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Du kan även använda en funktion som filtrerar objektet.  I det här fallet tilldelas variabeln `this` till det aktuella objektet som filtreras.  Följande kod har samma funktioner som i föregående exempel:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>Växla genom data
Utnyttja metoderna `take()` och `skip()`.  Om du till exempel vill dela upp tabellen i 100-radsposter:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Metoden `.includeTotalCount()` används för att lägga till ett totalCount-fält till resultatobjekten.  Fältet totalCount fylls i med det totala antalet poster som skulle returneras om ingen sidindelning används.

Du kan sedan använda sidvariablerna och vissa UI-knappar för att få en sidlista. Läs in de nya posterna för varje sida med hjälp av `loadPage()`.  Implementera cachelagring för att påskynda åtkomst till poster som redan har lästs in.

#### <a name="sorting-data"></a>Hur: Returnera sorterade data
Använd frågemetoden `.orderBy()` eller `.orderByDescending()`:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Mer information om frågeobjektet finns i [dokumentationen för frågeobjekt].

### <a name="inserting"></a>Hur: Infoga data
Skapa ett JavaScript-objekt med rätt datum och anropa `table.insert()` asynkront:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

När infogningen har fungerat returneras den infogade posten med de ytterligare fält som krävs för synkroniseringsåtgärder.  Uppdatera ditt eget cacheminne med den här informationen för senare uppdateringar.

Azure Mobile Apps Node.js Server SDK har funktioner för dynamiskt schema i utvecklingssyften.  Med dynamiskt schema kan du lägga till kolumner i tabellen genom att ange dem i en infognings- eller uppdateringsåtgärd.  Vi rekommenderar att du stänger av dynamiskt schema innan du flyttar programmet till produktionen.

### <a name="modifying"></a>Hur: Ändra data
Precis som för metoden `.insert()` ska du skapa ett objekt för uppdateringen och sedan anropa `.update()`.  Uppdateringsobjektet måste innehålla ID:t för posten som ska uppdateras – ID:t hämtas vid läsning av posten eller när `.insert()` anropas.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>Hur: Ta bort data
Om du vill ta bort en post anropar du `.del()`-metoden.  Skicka ID i en objektreferens:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
