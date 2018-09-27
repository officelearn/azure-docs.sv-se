---
title: Flera huvudservrar konfliktlösning i Azure Cosmos DB
description: Den här artikeln beskriver konflikt kategorier och konflikt upplösning lägen, till exempel senaste skrivare vinner (LWW), anpassade – användardefinierade proceduren anpassad – asynkront i Azure Comsos DB multimaster.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 5feefdb8fe6204bc8ef42a5e65bf1e30354e0cf9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393935"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Flera huvudservrar konfliktlösning i Azure Cosmos DB 

Azure Cosmos DB-multimaster har globala konflikt upplösning lägen för att säkerställa att data är konsekventa i alla regioner där de replikeras.

## <a name="conflict-categories"></a>Konflikt kategorier

Det finns tre kategorier av konflikter som kan uppstå när du arbetar med Azure Cosmos DB-data:

* **Infoga konflikter:** den här typen av konflikt händer när programmet infogar två eller flera dokument med samma unika index (t.ex, ID) från två eller fler regioner samtidigt. I det här fallet alla skrivningar kan lyckas inledningsvis, men baserat på principen för konfliktlösning du väljer kan bara ett dokument med den ursprungliga ID strävar.

* **Ersätt konflikter:** den här typen av konflikt händer när programmet uppdaterar ett enskilt dokument samtidigt från två eller fler regioner.

* **Ta bort konflikter:** den här typen av konflikt händer när programmet tar bort ett dokument från en region och uppdateringar från en eller flera regioner. 

## <a name="conflict-resolution-modes"></a>Lägen för lösning av konflikt

Det finns tre lägen för konflikt lösning erbjuds av Azure Cosmos DB. Lägen för lösning av konflikt har definierats för varje samling.

> [!NOTE]
> SQL API-användare kan välja mellan tre olika konflikt upplösning lägen. För alla andra API-modeller (MongoDB, Cassandra, diagram och tabell) löses konflikter med hjälp av senaste skrivaren Wins.

### <a name="last-writer-wins-lww"></a>Senaste skrivare vinner (LWW)

Senaste skrivare vinner är standardläget för lösning av konflikt. I det här läget principkonflilkter löser baserat på ett numeriskt värde som skickades i en egenskap på dokumentet.

Följande kodavsnitt är ett exempel på hur du konfigurerar principen för konfliktlösning senaste skrivare vinner när du använder .net SDK. ”ConflictResolutionPath” definierar sökvägen till den egenskap som används för att lösa konflikten. I det här exemplet `/userDefinedId` är konflikt upplösning sökvägen och dokumentet har det största `userDefinedId` värdet alltid att vinna konflikten. Registrera ett sista skrivare vinner Upplösningsläge genom att etablera samlingen med ConflictResolutionPolicy enligt nedan.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Senaste-skrivaren-Wins-Upplösningsläge tillämpas på olika konflikt kategorier på följande sätt:

* **Infognings- och står i konflikt:** om två eller flera dokument kolliderar i insert eller ersätt-åtgärder, blir det dokument som innehåller det största värdet för vägen med konflikt upplösning vinnaren (det vill säga userDefinedId). Om flera dokument har samma numeriskt värde för sökvägen till konflikt-lösning kan är den valda icke-deterministisk. Alla regioner kommer dock att Konvergera till vinnare.

* **Ta bort konflikt:** om det finns konflikter delete som ingår, ta bort alltid wins över andra ersätt konflikter oberoende av konflikt upplösning sökvägen värde.

### <a name="custom--user-defined-procedure"></a>Anpassad – användardefinierade proceduren

I detta läge gäller konfliktlösning användarkontrollerna genom att registrera en användare definieras procedur (UDP) i samlingen. Den här UDP har en specifik signatur. Om du väljer det här alternativet, men inte det gick att registrera en UDP, eller om UDP genererar ett undantag vid körning, skrivs konflikter till konflikter flöde där de kan lösas individuellt.

Om du vill registrera en anpassad – användardefinierade proceduren konflikt Upplösningsläge att etablera samlingen med ConflictResolutionPolicy enligt nedan.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Lägg sedan till den användardefinierade proceduren i samlingen som visas nedan.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

Den lagrade proceduren som registrerats med samlingen har en särskild signatur. I det här exemplet nedan används UDP en egenskap `UserDefinedId` att lösa konflikter. Dokumentet har det största värdet wins konflikten.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

Proceduren har fyra parametrar:

* **incomingDocument:** anger motstridiga versionen av dokumentet. Konflikten kan vara en insert-, ersätta, eller en delete-konflikt. Detta är en delete-avbildning (tombstone) med inget innehåll för en delete-konflikt.

* **existingDocument:** anger den dedicerade versionen av det dokument som har samma ”rid”-värde som incomingDocument. Den här parametern anges till null för en insert och ta bort konflikt.

* **isDeleteConflict:** boolesk flagga som indikerar om det inkommande dokumentet står i konflikt med ett tidigare borttagna dokument. När den här parametern anges till SANT, existingDocument också vara null.

* **conflictingDocuments:** anger en uppsättning den dedicerade versionen av alla dokument i databasen, som står i konflikt med incomingDocument på ID-kolumnen eller alla fält som unikt index. De här dokumenten har olika ”rid”-värdet i förhållande till incomingDocument.

Den användardefinierade proceduren har fullständig åtkomst till Cosmos DB-partitionsnyckel och kan utföra några store-åtgärder för att lösa konflikter. Om den användardefinierade proceduren inte checkar konflikt version systemet förlorar konflikten och existingDocument förblir allokerade. Om den användardefinierade proceduren misslyckas eller så finns inte, Azure Cosmos DB alla till konflikten i skrivskyddade konflikterna flöde där de kan bearbetas asynkront enligt den [asynkrona konflikt Upplösningsläge](). 

### <a name="custom--asynchronous"></a>Anpassade – asynkrona  

I det här läget kan Azure Cosmos DB utesluter alla konflikter (Infoga, Ersätt och ta bort) från att allokeras och registrerar dem i de skrivskyddade konflikter feed för uppskjutna matchning av användarens program. Programmet kan utföra konfliktlösning asynkront och använda någon logik eller referera till någon extern källa, programmet eller tjänsten att lösa konflikten.

Om du vill registrera en anpassad – asynkrona konflikt Upplösningsläge att etablera samlingen med ConflictResolutionPolicy enligt nedan.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Implementera koden som visas nedan för att läsa och bearbeta eventuella konflikter i feeden konflikter. Data som lagras i konflikter feed lägger till vissa kostnaden för lagring. Därför rekommenderar vi att ta bort data som lagras i konflikter feed när de bearbetas.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Konflikter flödet innehåller inte en lyssnare för att skicka meddelanden för nedströms bearbetning som ändringsflödet i Cosmos DB. Du måste implementera logik för att avsöka feeden konflikter och fastställa om det finns konflikter.

## <a name="code-samples"></a>Kodexempel

Nedan visas exempelprogram som visar konfliktlösning för API: er som visas. Varje exempel genererar konflikter i en behållare och sedan visar hur konflikter löses för varje lösning stöds konflikt-läge.

|API-modellen  | SDK |Exempel |
|---------|---------|---------|
|SQL-API    | .NET    |[Azure-cosmos-DB-SQL-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|SQL-API    | Node    |[Azure-cosmos-js/samples/MultiRegionWrite /](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|SQL-API    | Java    |[Azure-cosmos-DB-SQL-Java-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[Azure-cosmos-DB-mongodb-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|Tabell-API  | .NET    |[Azure-cosmos-DB-Table-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|Gremlin-API | .NET | [Azure-cosmos-DB-gremlin-dontnet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om konflikten kategorier och lägen för lösning av konflikt för Azure Cosmos DB. Nu ska ta en titt på följande resurser:

* [Använda MongoDB-klienter med Multi-Master](multi-master-oss-nosql.md)
