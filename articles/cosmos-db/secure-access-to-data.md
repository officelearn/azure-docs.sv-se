---
title: Lär dig hur du skyddar åtkomsten till data i Azure Cosmos DB
description: Lär dig mer om åtkomstkontrollbegrepp i Azure Cosmos DB, inklusive huvudnycklar, skrivskyddade nycklar, användare och behörigheter.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251822"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Säker åtkomst till data i Azure Cosmos DB

Den här artikeln innehåller en översikt över hur du skyddar åtkomsten till data som lagras i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB använder två typer av nycklar för att autentisera användare och ge åtkomst till dess data och resurser. 

|Nyckeltyp|Resurser|
|---|---|
|[Huvudnycklar](#master-keys) |Används för administrativa resurser: databaskonton, databaser, användare och behörigheter|
|[Resurstoken](#resource-tokens)|Används för programresurser: behållare, dokument, bilagor, lagrade procedurer, utlösare och UDF-filer|

<a id="master-keys"></a>

## <a name="master-keys"></a>Huvudnycklar

Huvudnycklar ger åtkomst till alla administrativa resurser för databaskontot. Huvudnycklar:

- Ge åtkomst till konton, databaser, användare och behörigheter. 
- Det går inte att använda för att ge detaljerad åtkomst till behållare och dokument.
- Skapas när ett konto skapas.
- Kan återskapas när som helst.

Varje konto består av två huvudnycklar: en primärnyckel och sekundärnyckel. Syftet med dubbla nycklar är att du ska kunna återskapa eller rulla nycklar, vilket ger kontinuerlig åtkomst till ditt konto och data.

Förutom de två huvudnycklarna för Cosmos DB-kontot finns det två skrivskyddade nycklar. Dessa skrivskyddade nycklar tillåter endast läsåtgärder på kontot. Skrivskyddade nycklar ger inte åtkomst till läsbehörighetsresurser.

Primära, sekundära, skrivskyddade och skrivskyddade huvudnycklar kan hämtas och återskapas med hjälp av Azure-portalen. Instruktioner finns i [Visa, kopiera och återskapa åtkomstnycklar](manage-with-cli.md#regenerate-account-key).

![Åtkomstkontroll (IAM) i Azure-portalen – visar nosql-databassäkerhet](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Processen att rotera din huvudnyckel är enkel. Navigera till Azure-portalen för att hämta din sekundära nyckel och ersätt sedan din primära nyckel med din sekundära nyckel i ditt program och rotera sedan den primära nyckeln i Azure-portalen.

![Huvudnyckelrotation i Azure-portalen – demonstrerar nosql-databassäkerhet](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Kodexempel för att använda en huvudnyckel

Följande kodexempel illustrerar hur du använder en Cosmos DB-kontoslutpunkt och huvudnyckel för att instansiera en DocumentClient och skapa en databas.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## <a name="resource-tokens"></a>Resurstoken<a id="resource-tokens"></a>

Resurstoken ger åtkomst till programresurserna i en databas. Resurstoken:

- Ge åtkomst till specifika behållare, partitionsnycklar, dokument, bilagor, lagrade procedurer, utlösare och UDF:er.
- Skapas när en [användare](#users) beviljas [behörigheter](#permissions) till en viss resurs.
- Återskapas när en behörighetsresurs aktiveras av POST,GET eller PUT-anrop.
- Använd en hash-resurstoken som är särskilt utformad för användaren, resursen och behörigheten.
- Är tiden bunden med en anpassningsbar giltighetsperiod. Det giltiga standardintervallet är en timme. Token livstid, dock, kan uttryckligen anges, upp till högst fem timmar.
- Ge ett säkert alternativ till att ge ut huvudnyckeln.
- Gör det möjligt för klienter att läsa, skriva och ta bort resurser i Cosmos DB-kontot enligt de behörigheter de har beviljats.

Du kan använda en resurstoken (genom att skapa Cosmos DB-användare och behörigheter) när du vill ge åtkomst till resurser i ditt Cosmos DB-konto till en klient som inte kan lita på huvudnyckeln.  

Cosmos DB-resurstokens är ett säkert alternativ som gör det möjligt för klienter att läsa, skriva och ta bort resurser i ditt Cosmos DB-konto enligt de behörigheter du har beviljat, och utan behov av antingen en huvud- eller skrivskyddad nyckel.

Här är ett typiskt designmönster där resurstoken kan begäras, genereras och levereras till klienter:

1. En tjänst på mellannivå har konfigurerats för att betjäna ett mobilprogram för att dela användarfoton.
2. Tjänsten mellannivå har huvudnyckeln för Cosmos DB-kontot.
3. Fotoappen är installerad på slutanvändarens mobila enheter.
4. Vid inloggning fastställer fotoappen användarens identitet med tjänsten på mellannivå. Denna mekanism för identitetsinrättning är helt upp till tillämpningen.
5. När identiteten har upprättats begär tjänsten mellannivå behörigheter baserat på identiteten.
6. Tjänsten på mellannivå skickar tillbaka en resurstoken till telefonappen.
7. Telefonappen kan fortsätta att använda resurstoken för att direkt komma åt Cosmos DB-resurser med de behörigheter som definierats av resurstoken och för det intervall som tillåts av resurstoken.
8. När resurstoken upphör att gälla får efterföljande begäranden ett 401 obehörigt undantag.  Nu återupprättar telefonappen identiteten och begär en ny resurstoken.

    ![Azure Cosmos DB-resurstokensarbetsflöde](./media/secure-access-to-data/resourcekeyworkflow.png)

Generering och hantering av resurstoken hanteras av de inbyggda Cosmos DB-klientbiblioteken. Om du använder REST måste du dock konstruera förfrå behåll/autentiseringshuvudena. Mer information om hur du skapar autentiseringshuvuden för REST finns i [Åtkomstkontroll på Cosmos DB-resurser](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) eller källkoden för vår [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) eller [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Ett exempel på en tjänst på mellannivå som används för att generera eller förmedla resurstokens finns i [appen ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## <a name="users"></a>Användare<a id="users"></a>

Azure Cosmos DB-användare är associerade med en Cosmos-databas.  Varje databas kan innehålla noll eller fler Cosmos DB-användare. Följande kodexempel visar hur du skapar en Cosmos DB-användare med [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Varje Cosmos DB-användare har en ReadAsync()-metod som kan användas för att hämta listan över [behörigheter](#permissions) som är associerade med användaren.

## <a name="permissions"></a>Behörigheter<a id="permissions"></a>

En behörighetsresurs associeras med en användare och tilldelas på behållaren samt partitionsnyckelnivå. Varje användare kan innehålla noll eller fler behörigheter. En behörighetsresurs ger åtkomst till en säkerhetstoken som användaren behöver när han eller hon försöker komma åt en viss behållare eller data i en viss partitionsnyckel. Det finns två tillgängliga åtkomstnivåer som kan tillhandahållas av en behörighetsresurs:

- Alla: Användaren har fullständig behörighet för resursen.
- Läs: Användaren kan bara läsa innehållet i resursen men kan inte utföra skriv-, uppdaterings- eller borttagningsåtgärder på resursen.

> [!NOTE]
> För att kunna köra lagrade procedurer måste användaren ha behörigheten Alla för behållaren där den lagrade proceduren ska köras.

### <a name="code-sample-to-create-permission"></a>Kodexempel för att skapa behörighet

Följande kodexempel visar hur du skapar en behörighetsresurs, läser resurstoken för behörighetsresursen och associerar behörigheterna med [användaren](#users) som skapats ovan.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Kodexempel för att läsa behörighet för användare

Följande kodavsnitt visar hur du hämtar behörigheten som är associerad med användaren som skapats ovan och instansierar en ny CosmosClient för användarens räkning, begränsad till en enda partitionsnyckel.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Lägga till användare och tilldela roller

Om du vill lägga till Azure Cosmos DB-kontoläsares åtkomst till ditt användarkonto har du en prenumerationsägare utföra följande steg i Azure-portalen.

1. Öppna Azure-portalen och välj ditt Azure Cosmos DB-konto.
2. Klicka på fliken **Access-kontroll (IAM)** och klicka sedan på **+ Lägg till rolltilldelning**.
3. Välj **Cosmos DB-kontoläsarroll**i rutan **Lägg** **till rolltilldelning** i rutan Lägg till roll.
4. Välj **Azure AD-användare, grupp eller program**i rutan Tilldela åtkomst **till**.
5. Välj den användare, grupp eller det program i katalogen som du vill bevilja åtkomst till.  Du kan söka i katalogen efter visningsnamn, e-postadress eller objektidentifierare.
    Den markerade användaren, gruppen eller programmet visas i den valda medlemslistan.
6. Klicka på **Spara**.

Entiteten kan nu läsa Azure Cosmos DB-resurser.

## <a name="delete-or-export-user-data"></a>Ta bort eller exportera användardata

Med Azure Cosmos DB kan du söka, välja, ändra och ta bort alla personuppgifter som finns i databasen eller samlingarna. Azure Cosmos DB tillhandahåller API:er för att hitta och ta bort personuppgifter, men det är ditt ansvar att använda API:erna och definiera logik som krävs för att radera personuppgifterna. Varje API med flera modeller (SQL, MongoDB, Gremlin, Cassandra, Table) tillhandahåller olika språk-SDK:er som innehåller metoder för att söka och ta bort personuppgifter. Du kan också aktivera [tiden för att live (TTL)](time-to-live.md) funktionen för att ta bort data automatiskt efter en angiven period, utan att ådra sig någon extra kostnad.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- Mer information om Cosmos-databassäkerhet finns i [Cosmos DB Database security](database-security.md).
- Mer information om hur du skapar Azure Cosmos DB-auktoriseringstoken finns [i Åtkomstkontroll på Azure Cosmos DB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Exempel på användarhantering med användare och behörigheter, exempel på användarhantering på [.NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
