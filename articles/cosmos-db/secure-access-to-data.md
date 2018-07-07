---
title: Lär dig att skydda åtkomsten till data i Azure Cosmos DB | Microsoft Docs
description: Läs mer om åtkomstkontrollkoncept i Azure Cosmos DB, såsom huvudnycklar, skrivskyddade nycklar, användare och behörigheter.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: sngun
ms.openlocfilehash: c51d399b646e7914ba85048c0928837caac7c15b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901127"
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Skydda åtkomst till Azure Cosmos DB-data
Den här artikeln innehåller en översikt över att skydda åtkomsten till data som lagras i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB använder två typer av nycklar för att autentisera användare och ge åtkomst till dess data och resurser. 

|Nyckeltyp|Resurser|
|---|---|
|[Huvudnycklar](#master-keys) |Används för administrativa resurser: databasen konton, databaser, användare och behörigheter|
|[Resurstokens](#resource-tokens)|Används för programresurser: behållare, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er|

<a id="master-keys"></a>

## <a name="master-keys"></a>Huvudnycklar 

Huvudnycklar ger åtkomst till alla de administrativa resurserna för databaskontot. Huvudnycklar:  
- Ger åtkomst till konton, databaser, användare och behörigheter. 
- Kan inte användas för att ge detaljerad åtkomst till behållare och dokument.
- Skapas när du skapar ett konto.
- Kan återskapas när som helst.

Varje konto består av två huvudnycklar: en primärnyckel och sekundärnyckel. Syftet med dubbla nycklar är så att du kan återskapa eller återställa nycklar, vilket ger kontinuerlig åtkomst till ditt konto och dina data. 

Förutom de två huvudnycklarna för Cosmos DB-kontot finns två skrivskyddade nycklar. Dessa skrivskyddade nycklar endast tillåta läsåtgärder på kontot. Skrivskyddade nycklar ger inte tillgång för att läsa behörigheter resurser.

Primär, sekundär, skrivskyddad och läs-och huvudnycklar kan hämtas och återskapas med hjälp av Azure portal. Anvisningar finns i [visa, kopiera och återskapa åtkomstnycklar](manage-account.md#keys).

![Åtkomstkontroll (IAM) i Azure portal – visar NoSQL database-säkerhet](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Processen för att rotera din huvudnyckel är enkelt. Navigera till Azure portal för att hämta den sekundära nyckeln och Ersätt den primära nyckeln med din sekundära nyckel i ditt program och rotera den primära nyckeln i Azure-portalen.

![Huvudnyckel rotation i Azure portal – visar NoSQL database-säkerhet](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Kodexempel för att använda en huvudnyckel

Följande kodexempel visar hur du använder en slutpunkten för Cosmos DB-konto och huvudnyckeln för att skapa en instans av en DocumentClient och skapa en databas. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Resurstokens

Resurstokens ger åtkomst till resursen i en databas. Resurstokens:
- Ger åtkomst till specifika behållare, partitionsnycklar, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er.
- Skapas när en [användaren](#users) beviljas [behörigheter](#permissions) till en viss resurs.
- Återskapas när en resurs med behörighet är efterlevts på av POST eller GET PUT-anrop.
- Använd en hash-Resurstoken särskilt konstruerade för användaren, resurs och behörighet.
- Är Tidsbundna med en anpassningsbar giltighetsperiod. Standard giltiga timespan är en timme. Livslängd för token, men kan anges explicit, upp till fem timmar.
- Ange en säker alternativ till att lämna ut huvudnyckeln. 
- Att klienter ska kunna läsa, skriva och ta bort resurser i Cosmos DB-kontot enligt de behörigheter som de har beviljats.

Du kan använda en Resurstoken (genom att skapa Cosmos DB-användare och behörigheter) när du vill ge åtkomst till resurser i ditt Cosmos DB-konto till en klient som inte är betrott med huvudnyckeln.  

Cosmos DB-resurstokens är en säker alternativ som gör att klienter kan läsa, skriva och ta bort resurser i Cosmos DB-kontot enligt de behörigheter som du har beviljat och utan behov av en master eller Läs bara viktiga.

Här är ett vanliga designmönster där resurstokens kan begäras, genereras och levereras till klienter:

1. En medelnivån tjänst har ställts in för att hantera en hanteringsprincip för mobilprogram att dela Användarfoton. 
2. Tjänsten medelnivån har huvudnyckeln för Cosmos DB-kontot.
3. Foto-appen är installerad på slutanvändarens mobila enheter. 
4. På inloggning etablerar foto-appen identiteten hos användaren med tjänsten medelnivån. Den här mekanismen för identitet upprätta är helt och hållet upp till programmet.
5. När identiteten har upprättats kan begär tjänsten medelnivån behörighet baserat på användarens identitet.
6. Tjänsten medelnivån skickar en Resurstoken tillbaka till appen.
7. Appen kan fortsätta att använda Resurstoken för direkt åtkomst till Cosmos DB-resurser med de behörigheter som definierats av Resurstoken och för intervallet som tillåts av Resurstoken. 
8. När Resurstoken upphör att gälla får efterföljande förfrågningar en 401, obehörig undantag.  Då appen återupprättar identiteten och begär en ny Resurstoken.

    ![Azure Cosmos DB-arbetsflöde för resurs-token](./media/secure-access-to-data/resourcekeyworkflow.png)

Generering av resursen och hantering hanteras av de inbyggda Cosmos DB-klientbibliotek; Om du använder REST måste du skapa begäran-/ autentiseringshuvuden. Läs mer om hur du skapar autentiseringshuvuden resten [åtkomstkontroll på Cosmos DB-resurser](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) eller [källkoden för våra SDK: er](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Ett exempel på en mellannivå-tjänst som används för att generera eller mäkla resurstokens finns i den [ResourceTokenBroker app](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Användare
Cosmos DB-användare är kopplade till en Cosmos DB-databas.  Varje databas kan innehålla noll eller flera Cosmos DB-användare.  Följande kodexempel visar hur du skapar en Cosmos DB-användarresurs.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Varje Cosmos DB-användare har en PermissionsLink-egenskap som kan användas för att hämta listan över [behörigheter](#permissions) som är associerade med användaren.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Behörigheter
En behörighet Cosmos DB-resurs är associerad med en Cosmos DB-användare.  Varje användare kan innehålla noll eller flera Cosmos DB-behörigheter.  En behörighet resurs ger åtkomst till en säkerhetstoken som användaren behöver vid åtkomst till en specifik resurs.
Det finns två tillgängliga åtkomstnivåer som kan utföras av en resurs för behörighet:

* Alla: Användaren har fullständig behörighet på resursen.
* Läs: Användaren endast kan läsa innehållet i resursen men det går inte att utföra skrivåtgärder, update eller delete-åtgärder på resursen.

> [!NOTE]
> Lagrade procedurer som användaren måste ha alla behörigheter för behållaren där den lagrade proceduren körs för att köra Cosmos DB.
> 
> 

### <a name="code-sample-to-create-permission"></a>Kodexempel för att skapa behörigheten

Följande kodexempel visar hur du skapar en resurs med behörighet läser Resurstoken för resursen behörighet och koppla behörigheter med den [användaren](#users) skapade ovan.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Om du har angett en partitionsnyckel för din samling, måste behörigheten för samlingen, dokument och bilaga resurser även att inkludera ResourcePartitionKey utöver ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Kodexempel för att läsa behörigheter för användare

För att enkelt få behörighet för alla resurser som är associerade med en viss användare, Cosmos DB gör tillgängliga en behörighet för varje användarobjekt-flöde.  Följande kodfragment visar hur du hämtar den behörighet som är associerad med användaren som skapades ovan, konstruera en behörighetslistan och skapa en instans av en ny Dokumentklient användarens räkning.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="delete-or-export-user-data"></a>Ta bort eller exportera användardata
Azure Cosmos DB kan du söka efter, Välj, ändra och ta bort personliga data i databasen eller samlingar. Azure Cosmos DB tillhandahåller API: er för att hitta och ta bort personliga data men, är det ditt ansvar att använda API: er och definiera logik som krävs för att radera alla personliga data. Varje flermodells-API (SQL-API, MongoDB-API, Gremlin-API, API för Cassandra, tabell-API) tillhandahåller olika språk i SDK: er som innehåller metoder för att söka och ta bort personliga data. Du kan också aktivera den [till live (TTL)](time-to-live.md) att ta bort data automatiskt efter en angiven tidsperiod, utan några ytterligare kostnader.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om Cosmos DB database-säkerhet i [Cosmos DB: databasen security](database-security.md).
* Läs om hur du hanterar huvudnycklar och skrivskyddade nycklar i [så här hanterar du ett Azure Cosmos DB-konto](manage-account.md#keys).
* Läs hur du skapar Azure Cosmos DB-auktoriseringstoken i [åtkomstkontroll på Azure Cosmos DB-resurser](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
