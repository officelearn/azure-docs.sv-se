---
title: Lär dig hur du skyddar åtkomsten till data i Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om access control begrepp i Azure Cosmos-databasen, inklusive huvudnycklar, skrivskyddade nycklar, användare och behörigheter.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: sngun
ms.openlocfilehash: 7a53dda7d6b49187d77ca44bcb55db5f9c305f64
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>Skydda åtkomst till Azure Cosmos DB data
Den här artikeln innehåller en översikt över skydda åtkomsten till data som lagras i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos-DB använder två typer av nycklar för att autentisera användare och ge tillgång till dess data och resurser. 

|Nyckeltyp|Resurser|
|---|---|
|[Huvudnycklar](#master-keys) |Används för administrativa resurser: databasen konton, databaser, användare och behörigheter|
|[Resurs-token](#resource-tokens)|Används för programresurser: samlingar, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er|

<a id="master-keys"></a>

## <a name="master-keys"></a>Huvudnycklar 

Huvudnycklar ger åtkomst till alla de administrativa resurserna för databaskontot. Huvudnycklar:  
- Ger åtkomst till konton, databaser, användare och behörigheter. 
- Kan inte användas för att ge detaljerade åtkomst till samlingar och dokument.
- Skapas när du skapar ett konto.
- Genereras när som helst.

Varje konto består av två huvudnycklar: en primärnyckel och sekundärnyckel. Syftet med dubbla nycklar är så att du kan återskapa eller återställa nycklar, ger kontinuerlig tillgång till ditt konto och data. 

Förutom de två huvudnycklarna för Cosmos-DB-kontot finns två skrivskyddade nycklar. Nycklarna skrivskyddat tillåter endast läsåtgärder på kontot. Skrivskyddade nycklar ger inte behörighet att läsa behörigheter resurser.

Primära, sekundära, skrivskyddad och skrivskyddad huvudnycklar kan hämtas och återskapas med hjälp av Azure portal. Instruktioner finns i [visa, kopiera och generera åtkomstnycklar](manage-account.md#keys).

![Åtkomstkontroll (IAM) i Azure portal – visar NoSQL database-säkerhet](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Processen för att rotera din huvudnyckeln är enkelt. Navigera till den Azure-portalen att hämta den sekundära nyckeln, Ersätt primärnyckel med din sekundärnyckeln i ditt program och rotera primärnyckeln i Azure-portalen.

![Huvudnyckeln rotation i Azure portal – visar NoSQL database-säkerhet](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Kodexempel för att använda en huvudnyckel

Följande kodexempel illustrerar hur du använder en Cosmos-DB konto slutpunkt och huvudnyckeln för att skapa en instans av en DocumentClient och skapa en databas. 

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

## <a name="resource-tokens"></a>Resurs-token

Resursen token ger åtkomst till resursen i en databas. Resurs-token:
- Ger åtkomst till specifika samlingar, partitionsnycklar, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er.
- Skapas när en [användare](#users) beviljas [behörigheter](#permissions) för en viss resurs.
- Återskapas när en resurs behörighet är efterlevts på av POST, hämta eller skicka anrop.
- Använda en hash-resurs-token som skapats specifikt för användare, resurser och behörighet.
- Är Tidsbundna med en anpassningsbara giltighetsperiod. Standard giltigt timespan är en timme. Livslängd för token, men kan uttryckligen anges, upp till högst fem timmar.
- Ange en säker alternativ till att du lämnar ut huvudnyckeln. 
- Att klienter ska kunna läsa, skriva och ta bort resurser i kontot Cosmos DB enligt de behörigheter som de har beviljats.

Du kan använda en resurs-token (genom att skapa Cosmos-DB-användare och behörigheter) när du vill ge åtkomst till resurser i ditt Cosmos-DB-konto till en klient som inte är tillförlitliga med huvudnyckel.  

Cosmos DB resurs token är en säker alternativ som gör att klienter kan läsa, skriva och ta bort resurser i Cosmos-DB-kontot enligt de behörigheter som du har beviljat och utan krävs en master eller Läs bara viktiga.

Här är en typisk designmönstret där resursen token kan begärde, skapas och levereras till klienter:

1. En tjänst halva nivå ställs in för att hantera ett mobilt program att dela Användarfoton. 
2. Tjänsten halva nivå har huvudnyckeln för Cosmos-DB-konto.
3. Foto-app är installerad på slutanvändarens mobila enheter. 
4. Du loggar in upprättar foto-app till identiteten på användaren med tjänsten halva nivå. Den här mekanismen för identitet etablering är rent upp till programmet.
5. När identiteten har upprättats halva nivå-tjänstbegäranden behörigheter baserat på användarens identitet.
6. Tjänsten halva nivån skickar en resurs token tillbaka till phone-app.
7. Phone-app kan fortsätta att använda resursen token direkt åtkomst till Cosmos-DB-resurser med de behörigheter som definierats av resurs-token och för intervallet som tillåts av resurs-token. 
8. När resource-token upphör att gälla, får ett 401 obehörig undantag efterföljande förfrågningar.  Nu phone app återupprättar identitet och begär en ny resurs-token.

    ![Azure DB Cosmos-resurs tokens arbetsflöde](./media/secure-access-to-data/resourcekeyworkflow.png)

Resurs-token generering och hantering hanteras av inbyggda Cosmos-DB-klientbibliotek; Om du använder REST måste du skapa huvuden för begäran-autentisering. Mer information om hur du skapar autentiseringshuvuden för REST finns [åtkomstkontroll på Cosmos DB resurser](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) eller [källkoden för våra SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Ett exempel på en mellannivå-tjänst som används för att generera eller token för resursen för koordinatortjänst finns i [ResourceTokenBroker app](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Användare
Cosmos DB användare har associerats med en Cosmos-DB-databas.  Varje databas kan innehålla noll eller flera Cosmos-DB-användare.  Följande kodexempel visar hur du skapar en Cosmos-DB användarresurs.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Varje Cosmos-DB-användare har en PermissionsLink-egenskap som kan användas för att hämta listan över [behörigheter](#permissions) som är associerade med användaren.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Behörigheter
En Cosmos-DB behörighet resurs är associerad med en Cosmos-DB-användare.  Varje användare kan innehålla noll eller flera Cosmos-DB-behörigheter.  En behörighet för resursen ger åtkomst till en säkerhetstoken som användaren måste när du försöker komma åt en resurs för specifika program.
Det finns två tillgängliga åtkomstnivåer som kan tillhandahållas av en behörighet:

* Alla: Användaren har fullständig behörighet på resursen.
* Läs: Användaren endast kan läsa innehållet i resursen men kan inte utföra skrivåtgärder-, update- eller delete-åtgärder på resursen.

> [!NOTE]
> För att kunna köra Cosmos-DB-lagrade procedurer som användaren måste ha behörigheten All i den samling där den lagrade proceduren körs.
> 
> 

### <a name="code-sample-to-create-permission"></a>Kodexempel för att skapa behörighet

Följande kodexempel visar hur du skapar en resurs för behörighet, läsa resurs token av resursen behörighet och associera behörigheter med den [användaren](#users) skapade ovan.

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

Om du har angett en partitionsnyckel för samlingen, måste behörigheten för insamling, dokument och resurser för bifogad fil också att inkludera ResourcePartitionKey utöver ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Kodexemplet läsa behörigheter för användare

För att enkelt få alla resurser som är associerade med en viss användare, Cosmos DB tillgängliggör en behörighet för varje användarobjekt.  Följande kodavsnitt visar hur du hämtar behörigheten som är associerade med användaren som skapade ovan, skapa en behörighetslista och skapa en instans av en ny Dokumentklient för användarens räkning.

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

## <a name="next-steps"></a>Nästa steg
* Läs mer om Cosmos DB databassäkerhet i [Cosmos DB: databasen säkerhet](database-security.md).
* Läs om hur du hanterar master och skrivskyddade nycklar i [så här hanterar du ett konto i Azure Cosmos DB](manage-account.md#keys).
* Information om hur du skapar Azure Cosmos DB auktorisering token finns [åtkomstkontroll på Azure Cosmos DB resurser](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
