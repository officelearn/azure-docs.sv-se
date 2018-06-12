---
title: Unika nycklar i Azure Cosmos DB | Microsoft Docs
description: Lär dig använda unika nycklar i Azure DB som Cosmos-databasen.
services: cosmos-db
keywords: Unik nyckel begränsning, överträdelse av unique key-begränsningen
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: rafats
ms.openlocfilehash: d12109efbb157b1e0c15b1a4c0d005fa98c44858
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261108"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Unika nycklar i Azure Cosmos DB

Unika nycklar ger utvecklare möjlighet att lägga till ett lager för data i sin databas. Genom att skapa en unik nyckel princip när en behållare har skapats kan du se till att en eller flera värden för varje unikt [partitionsnyckel](partition-data.md). När en behållare har skapats med en unik nyckel princip förhindrar skapande av alla nya eller uppdaterade objekt med värden att duplicerade värden som anges av begränsningen för unik nyckel.   

> [!NOTE]
> Unika nycklar som stöds av de senaste versionerna av den [.NET](sql-api-sdk-dotnet.md) och [.NET Core](sql-api-sdk-dotnet-core.md) SQL SDK och [MongoDB API](mongodb-feature-support.md#unique-indexes). Tabell-API och Graph API stöder inte unika nycklar just nu. 
> 
>

## <a name="use-case"></a>Användningsfall

Exempelvis ska vi titta på hur en användardatabas som är associerade med en [sociala programmet](use-cases.md#web-and-mobile-applications) kan dra nytta av att ha en unik nyckel princip för e-postadresser. Genom att göra användarens e-postadressen en unik nyckel måste du kontrollera att varje post har en unik e-postadress och inga nya poster kan inte skapas med dubbla e-postadresser. 

Om du vill använda att användarna ska kunna skapa flera poster med samma e-postadress, men inte samma förnamn, efternamn och e-postadress, kan du lägga till andra sökvägar till principen för unik nyckel. Istället för att skapa en unik nyckel baserat på en e-postadress, kan så du skapa en unik nyckel som är en kombination av förnamn, efternamn och e-post. Varje unik kombination av tre sökvägar tillåts i det här fallet, så att databasen kan innehålla objekt som har följande sökväg värden. Var och en av dessa poster skulle skicka den unika nyckeln principen.  

**Tillåtna värden för unik nyckel för förnamn, efternamn och e-post**

|Förnamn|Efternamn|E-postadress|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Om du försöker infoga en annan post med någon av de kombinationer som anges i tabellen ovan, skulle du får ett felmeddelande om att begränsningen för unik nyckel inte har uppfyllts. Felet som returnerades av Azure Cosmos DB är ”resursen med angivet id eller namn finns redan”. eller ”med angivet id, namn och unikt index resursen finns redan”. 

## <a name="using-unique-keys"></a>Med unika nycklar

Unika nycklar måste anges när behållaren har skapats och den unika nyckeln är begränsad till Partitionsnyckeln. Du kan ha poster från tabellen dupliceras i varje partition för att bygga vidare på det tidigare exemplet, om du partitionera baserat på postnummer.

Du kan inte uppdatera en befintlig behållare för att använda unika nycklar.

När du har skapat en behållare med en unik nyckel princip kan inte principen ändras om du återskapa behållaren. Om du har befintliga data som du vill implementera unika nycklar på Skapa nya behållare och sedan använda Migreringsverktyget lämpliga uppgifter för att flytta data till den nya behållaren. SQL-behållare, Använd den [Datamigreringsverktyg](import-data.md). MongoDB-behållare, Använd [mongoimport.exe eller mongorestore.exe](mongodb-migrate.md).

Högst 16 sökväg värden (till exempel /firstName, /lastName, /address/zipCode osv.) kan ingå i varje unik nyckel. 

Varje unik nyckel princip kan innehålla högst 10 unika nyckelvillkor eller kombinationer och de kombinerade sökvägarna för alla egenskaper i unikt index får inte överstiga 60 tecken. Så det tidigare exemplet som använder förnamn, efternamn och e-postadressen är en begränsning och den använder tre av de 16 sökvägarna som tillgängliga. 

Begära enhet avgifter för att skapa, uppdatera, och ta bort ett objekt är något högre när det finns en unik nyckel princip på behållaren. 

Sparse unika nycklar stöds inte. Om det saknas värden för vissa unika sökvägar, behandlas de som ett särskilt null-värde som ingår i unikhetsbegränsningen.

## <a name="sql-api-sample"></a>SQL-API-exemplet

Följande kodexempel visar hur du skapar en ny SQL-behållare med två unika viktiga begränsningar. Det första villkoret är förnamn, efternamn, e-begränsningen som beskrivs i det tidigare exemplet. Det andra villkoret är användare adress/postnummer. En JSON-fil som använder sökvägarna i denna unika nycklar princip följer kodexemplet. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
          }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Exempel på JSON-dokument.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> Kontrollera är Obs unika nyckelnamn skiftlägeskänsliga. Som visas i ovanstående exempel, har unikt namn angetts för /address/zipcode. Om dina data har postnumret, sedan infogas ”null” i Unik nyckel som postnumret inte är lika med postnumret. Och på grund av den här skiftlägeskänslighet alla poster med postnumret kommer inte att kunna infogas som dubbla ”null” bryter mot begränsningen för unik nyckel.

## <a name="mongodb-api-sample"></a>MongoDB-API-exemplet

I följande exempel kommando visar hur du skapar ett unikt index på fälten Förnamn, efternamn och e-post i mängden användare för MongoDB-API. Detta säkerställer är unikt för en kombination av alla tre fält i alla dokument i samlingen. För MongoDB API samlingar skapas det unika indexet när samlingen har skapats, men innan du fyller i samlingen.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Konfigurera unika nycklar med hjälp av Azure Portal

I avsnitten ovan du hittar kodexempel som visar hur du kan definiera unika nyckelvillkor när en samling har skapats med SQL API eller MongoDB-API. Men det är också möjligt att definiera unika nycklar när du skapar en samling via webbgränssnittet i Azure-portalen. 

- Navigera till den **Data Explorer** i Cosmos-DB-konto
- Klicka på **ny samling**
- I avsnittet unika nycklar, ** du kan lägga till önskade unika nyckelvillkor genom att klicka på **Lägg till unik nyckel**

![Definiera unika nycklar i Data Explorer](./media/unique-keys/unique-keys-azure-portal.png)

- Om du vill skapa en unik nyckel begränsning i sökvägen till lastName, du lägger till `/lastName`.
- Om du vill skapa en unik nyckel begränsning för lastName firstName kombination, du lägger till `/lastName,/firstName`

När du är klar klickar du på **OK** att skapa samlingen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar unika nycklar för objekt i en databas. Om du skapar en behållare för första gången, granska [partitionering data i Azure Cosmos DB](partition-data.md) som unika nycklar och partitionsnycklar är beroende av varandra. 


