---
title: Hantera Azure Cosmos DB SQL API-resurser med .Net V4 SDK
description: Snabbstart för att skapa en konsolapp med .Net V4 SDK för att hantera Azure Cosmos DB SQL API-kontoresurser.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77585942"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Snabbstart: Skapa en konsolapp med .Net V4 SDK för att hantera Azure Cosmos DB SQL API-kontoresurser.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Kom igång med Azure Cosmos DB SQL API-klientbiblioteket för .NET. Följ stegen i det här dokumentet för att installera .NET V4-paketet (Azure.Cosmos), skapa en app och prova exempelkoden för grundläggande CRUD-åtgärder på data som lagras i Azure Cosmos DB. 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga efter nyckel-/värde-, dokument- och diagramdatabaser. Använd Azure Cosmos DB SQL API-klientbiblioteket för .NET för att:

* Skapa en Azure Cosmos-databas och en behållare
* Lägga till exempeldata i behållaren
* Fråga efter data 
* Ta bort databasen

[Bibliotekets](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en gratis](https://azure.microsoft.com/free/) eller prova Azure [Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, kostnadsfritt och åtaganden. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Du kan kontrollera vilken version som `dotnet --version`är tillgänglig i din miljö genom att köra .

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att skapa ett Azure Cosmos-konto och konfigurera ett projekt som använder Azure Cosmos DB SQL API-klientbibliotek för .NET för att hantera resurser. Exempelkoden som beskrivs i `FamilyDatabase` den här artikeln skapar en databas och familjemedlemmar (varje familjemedlem är ett objekt) i databasen. Varje familjemedlem har egenskaper `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`som . Egenskapen `LastName` används som partitionsnyckel för behållaren. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Skapa ett Azure Cosmos-konto

Om du använder [alternativet Prova Azure Cosmos DB utan kostnad för](https://azure.microsoft.com/try/cosmosdb/) att skapa ett Azure Cosmos-konto måste du skapa ett Azure Cosmos DB-konto av typen SQL **API**. Ett Azure Cosmos DB-testkonto har redan skapats åt dig. Du behöver inte skapa kontot explicit, så du kan hoppa över det här avsnittet och gå vidare till nästa avsnitt.

Om du har en egen Azure-prenumeration eller skapat en prenumeration gratis bör du uttryckligen skapa ett Azure Cosmos-konto. Följande kod skapar ett Azure Cosmos-konto med sessionskonsekvens. Kontot replikeras i `South Central US` `North Central US`och .  

Du kan använda Azure Cloud Shell för att skapa Azure Cosmos-kontot. Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare. Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell. För den här **Bash** snabbstarten väljer du Bash-läge. Azure Cloud Shell kräver också ett lagringskonto, du kan skapa ett när du uppmanas.

Välj **knappen Prova** bredvid följande kod, välj **Bash-läge** välj **skapa ett lagringskonto** och logga in på Cloud Shell. Kopiera och klistra in följande kod nästa gång du gör i Azure-molngränssnittet och kör det. Azure Cosmos-kontonamnet måste vara globalt unikt och `mysqlapicosmosdb` se till att uppdatera värdet innan du kör kommandot.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Skapandet av Azure Cosmos-kontot tar ett tag, när åtgärden har slutförts kan du se bekräftelseutdata. När kommandot har slutförts loggar du in på [Azure-portalen](https://portal.azure.com/) och kontrollerar att Azure Cosmos-kontot med det angivna namnet finns. Du kan stänga Azure Cloud Shell-fönstret när resursen har skapats. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Skapa en ny .NET-app

Skapa ett nytt .NET-program i önskad redigerare eller IDE. Öppna kommandotolken i Windows eller ett terminalfönster från den lokala datorn. Du kör alla kommandon i nästa avsnitt från kommandotolken eller terminalen.  Kör följande dotnet nytt kommando för att `todo`skapa en ny app med namnet . Parametern --langVersion anger egenskapen LangVersion i den skapade projektfilen.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

   ```bash
   cd todo
   dotnet build
   ```

Den förväntade produktionen från bygga bör se ut ungefär så här:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installera Azure Cosmos DB-paketet

Installera Azure Cosmos DB-klientbiblioteket för .NET Core medan du fortfarande är i programkatalogen med hjälp av kommandot dotnet add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiera dina Azure Cosmos-kontouppgifter från Azure-portalen

Exempelprogrammet måste autentiseras till ditt Azure Cosmos-konto. För att autentisera bör du skicka Azure Cosmos-kontoautentiseringsuppgifterna till programmet. Hämta dina Azure Cosmos-kontouppgifter efter så här:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Cosmos-konto.

1. Öppna fönstret **Nycklar** och kopiera **URI:n** och **PRIMÄRNYCKELn** för ditt konto. Du lägger till URI- och nycklarvärdena i en miljövariabel i nästa steg.

## <a name="object-model"></a><a id="object-model"></a>Objektmodell

Innan du börjar skapa programmet ska vi titta i hierarkin av resurser i Azure Cosmos DB och den objektmodell som används för att skapa och komma åt dessa resurser. Azure Cosmos DB skapar resurser i följande ordning:

* Azure Cosmos-konto 
* Databaser 
* Containrar 
* Objekt

Mer information om hierarkin för olika entiteter finns i [hur du arbetar med databaser, behållare och objekt i azure cosmos](databases-containers-items.md) DB-artikeln. Du kommer att använda följande .NET-klasser för att interagera med dessa resurser:

* CosmosClient - Den här klassen ger en logisk representation på klientsidan för Azure Cosmos DB-tjänsten. Klientobjektet används för att konfigurera och köra begäranden mot tjänsten.
* CreateDatabaseIfNotNotexistsAsync - Den här metoden skapar (om den inte finns) eller hämtar (om det redan finns) en databasresurs som en asynkron åtgärd. 
* CreateContainerIfNotExistsAsync - Den här metoden skapar (om den inte finns) eller hämtar (om den redan finns) en behållare som en asynkron åtgärd. Du kan kontrollera statuskoden från svaret för att avgöra om behållaren har skapats nyligen (201) eller om en befintlig behållare returnerades (200). 
* CreateItemAsync - Den här metoden skapar en artikel i behållaren.
* UpsertItemAsync - Den här metoden skapar ett objekt i behållaren om det inte redan finns eller ersätter objektet om det redan finns. 
* GetItemQueryIterator - Den här metoden skapar en fråga för objekt under en behållare i en Azure Cosmos-databas med hjälp av ett SQL-uttryck med parameteriserade värden. 
* DeleteAsync - Tar bort den angivna databasen från ditt Azure Cosmos-konto. `DeleteAsync`tar endast bort databasen.

 ## <a name="code-examples"></a><a id="code-examples"></a>Kodexempel

Exempelkoden som beskrivs i den här artikeln skapar en familjedatabas i Azure Cosmos DB. Familjedatabasen innehåller familjedetaljer som namn, adress, plats, associerade föräldrar, barn och husdjur. Innan du fyller i data på ditt Azure Cosmos-konto definierar du egenskaperna för ett familjeobjekt. Skapa en ny `Family.cs` klass som heter på rotnivån i exempelprogrammet och lägg till följande kod i det:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Lägga till med hjälpdirektiv & definiera klientobjektet

Öppna `Program.cs` filen i redigeraren i redigeraren från projektkatalogen och lägg till följande med hjälp av direktiv högst upp i programmet:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Lägg till följande globala `Program` variabler i klassen. Dessa kommer att innehålla slutpunkten och auktoriseringsnycklar, namnet på databasen och behållare som du ska skapa. Se till att ersätta slutpunkts- och auktoriseringsnycklarnas värden enligt din miljö. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Slutligen, ersätta `Main` metoden:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Skapa en databas 

Definiera `CreateDatabaseAsync` metoden i `program.cs` klassen. Den här metoden `FamilyDatabase` skapar om den inte redan finns.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Skapa en container

Definiera `CreateContainerAsync` metoden i `Program` klassen. Den här metoden `FamilyContainer` skapar om den inte redan finns. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Skapa ett objekt

Skapa ett familjeobjekt `AddItemsToContainerAsync` genom att lägga till metoden med följande kod. Du kan `CreateItemAsync` använda `UpsertItemAsync` metoderna eller för att skapa ett objekt:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Fråga objekten

När du har infogat ett objekt kan du köra en fråga för att få information om "Andersen"-familjen. Följande kod visar hur du kör frågan direkt med SQL-frågan. SQL-frågan för att få "Anderson" `SELECT * FROM c WHERE c.LastName = 'Andersen'`familjeinformation är: . Definiera `QueryItemsAsync` metoden i `Program` klassen och lägg till följande kod i den:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Ersätta ett objekt 

Läs ett familjeobjekt och uppdatera `ReplaceFamilyItemAsync` det sedan genom att lägga till metoden med följande kod.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Ta bort ett objekt 

Ta bort ett familjeobjekt genom att lägga till `DeleteFamilyItemAsync` metoden med följande kod.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Ta bort databasen 

Slutligen kan du ta `DeleteDatabaseAndCleanupAsync` bort databasen lägga till metoden med följande kod:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Spara `Program` filen när du har lagt till alla nödvändiga metoder. 

## <a name="run-the-code"></a>Kör koden

Nästa bygga och köra programmet för att skapa Azure Cosmos DB resurser.

   ```bash
   dotnet run
   ```

Följande utdata genereras när du kör programmet. Du kan också logga in på Azure-portalen och verifiera att resurserna skapas:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Du kan verifiera att data skapas genom att logga in på Azure-portalen och se de nödvändiga objekten i ditt Azure Cosmos-konto. 

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort Azure Cosmos-kontot och motsvarande resursgrupp. Följande kommando visar hur du tar bort resursgruppen med hjälp av Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Azure Cosmos-konto, skapar en databas och en behållare med hjälp av en .NET Core-app. Du kan nu importera ytterligare data till ditt Azure Cosmos-konto med instruktionerna int följande artikel. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
