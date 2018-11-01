---
title: Unika nycklar i Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du använder unika nycklar i Azure Cosmos DB-databasen.
services: cosmos-db
keywords: Unik nyckel begränsning, överträdelse av unika nyckelvillkor
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: rafats
ms.openlocfilehash: ff432de59e5a5fdfeaad4c3a5361554ee32e21b0
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740016"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Unika nycklar i Azure Cosmos DB

Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till sin databas. Genom att skapa en unik nyckel princip när en behållare har skapats kan du se till att en eller flera värden för varje unikt [partitionsnyckel](partition-data.md). När du har skapat en behållare med en unik nyckel princip förhindrar skapande av alla nya eller uppdaterade objekt med värden att duplicerade värden som anges av det unika nyckelvillkoret.   

> [!NOTE]
> Unika nycklar stöds av de senaste versionerna av den [.NET](sql-api-sdk-dotnet.md) och [.NET Core](sql-api-sdk-dotnet-core.md) SQL SDK: er, och [MongoDB API](mongodb-feature-support.md#unique-indexes). Tabell-API och Gremlin API stöder inte unika nycklar just nu. 
> 
>

## <a name="use-case"></a>Användningsfall

Exempelvis kan vi titta på hur en användardatabas som är associerade med en [sociala program](use-cases.md#web-and-mobile-applications) kan dra nytta av att ha en unik nyckel princip på e-postadresser. Genom att göra användarens e-postadressen en unik nyckel, kontrollera att varje post har en unik e-postadress och inga nya poster kan skapas med dubbla e-postadresser. 

Om du vill använda att användarna ska kunna skapa flera poster med samma e-postadress, men inte samma förnamn, efternamn och e-postadress, du kan lägga till andra sökvägar till principen för unik nyckel. Istället för att skapa en unik nyckel som baseras på en e-postadress, kan så du skapa en unik nyckel som är en kombination av de förnamn, efternamn och e-post. I det här fallet varje unik kombination av de tre sökvägarna tillåts, så att databasen kan innehålla objekt som har följande sökväg-värden. Var och en av dessa poster skulle skicka Unik nyckel princip.  

**Tillåtna värden för unik nyckel för förnamn, efternamn och e-post**

|Förnamn|Efternamn|E-postadress|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Om du har försökt att infoga en annan post med någon av de kombinationer som anges i tabellen ovan ska du få ett felmeddelande om att unika nyckelvillkor inte var uppfyllt. Felet som returnerades av Azure Cosmos DB är ”resursen med angivet id eller namn finns redan”. eller ”resurs med angivet id, namn eller unikt index finns redan”. 

## <a name="using-unique-keys"></a>Med hjälp av unika nycklar

Unika nycklar måste anges när behållaren har skapats och den unika nyckeln är begränsad till Partitionsnyckeln. För att bygga på det tidigare exemplet, om du partitionerar utifrån postnummer, kan du ha poster från tabellen dupliceras i varje partition.

Du kan inte uppdatera befintlig behållare för att använda unika nycklar.

När en behållare har skapats med en unik nyckel princip kan inte principen ändras, såvida inte du återskapa behållaren. Om du har befintliga data som du vill implementera unika nycklar på Skapa ny behållare och sedan använda lämpliga datamigreringsverktyget för att flytta data till den nya behållaren. SQL-behållare, använda den [Datamigreringsverktyget](import-data.md). MongoDB-behållare, använda [mongoimport.exe eller mongorestore.exe](mongodb-migrate.md).

Högst 16 sökväg-värden (till exempel /firstName, /lastName, /address/zipCode osv.) kan ingå i varje unik nyckel. 

Varje unik nyckel princip kan ha högst 10 unika nyckelvillkor eller kombinationer och de kombinerade sökvägarna för alla egenskaper för unikt index får innehålla högst 60 tecken. Så det tidigare exemplet som använder förnamn, efternamn, e-postadressen är bara en begränsning och den använder tre av de 16 sökvägarna som tillgängliga. 

Begära enhet kostnaden för att skapa, uppdatera, och tar bort ett objekt är något högre om det finns en unik nyckel princip i behållaren. 

Null-optimerade unika nycklar stöds inte. Om det saknas värden för vissa unika sökvägar, hanteras de som en särskild null-värde, som ingår i unikhetsbegränsningen.

## <a name="sql-api-sample"></a>SQL API-exempel

Följande kodexempel visar hur du skapar en ny SQL-behållare med två unika nyckelvillkor. Det första villkoret är förnamn, efternamn, e-begränsningen som beskrivs i det tidigare exemplet. Det andra villkoret är användare adress/postnummer. En JSON-fil som använder sökvägarna i den här unika viktiga principen följer kodexemplet. 

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

Exempel-JSON-dokument.

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
> . Obs unika nyckelnamnet är skiftlägeskänsligt. Som ovan i exemplet, har unikt namn angetts för /address/zipcode. Om dina data har postnummer ska sedan infogas ”null” i Unik nyckel som postnummer inte är lika postnummer. Och på grund av den här skiftlägeskänslighet alla poster med postnummer kommer inte kunna infogas eftersom duplicerade ”null” kommer bryter mot villkoret för unikt.

## <a name="mongodb-api-sample"></a>MongoDB API-exempel

I följande kommando exempel visas hur du skapar ett unikt index på förnamn, efternamn och e-postfält i mängden användare för MongoDB-API. Detta garanterar unikhet för en kombination av alla tre fält i alla dokument i samlingen. För MongoDB API-samlingar skapas unikt index när samlingen har skapats, men innan du fylla i samlingen.

> [!NOTE]
> Format för unik nyckel för MongoDB API-konton är skiljer sig från den SQL-API-konton, där du inte behöver ange snedstreck (/)-tecken före fältnamnet. 

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Konfigurera unika nycklar med hjälp av Azure Portal

I avsnitten ovan kan du hittar exempel som visar hur du kan definiera unika nyckelvillkor när en samling har skapats med SQL API eller MongoDB API. Men det är också möjligt att definiera unika nycklar när du skapar en samling via webbgränssnittet i Azure-portalen. 

- Navigera till den **Datautforskaren** i ditt Cosmos DB-konto
- Klicka på **ny samling**
- I avsnittet unika nycklar, ** du kan lägga till de önskade unika nyckelvillkor genom att klicka på **Lägg till unik nyckel**

![Definiera unika nycklar i Datautforskaren](./media/unique-keys/unique-keys-azure-portal.png)

- Om du vill skapa en unik nyckel begränsning på sökvägen lastName, du lägger till `/lastName`.
- Om du vill skapa en unik begränsning som nyckel för lastName firstName kombination, du lägger till `/lastName,/firstName`

När klar klickar du på **OK** att skapa samlingen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar unika nycklar för objekt i en databas. Om du skapar en behållare för första gången, granska [partitionera data i Azure Cosmos DB](partition-data.md) som unika nycklar och partitionsnycklar är beroende av varandra. 


