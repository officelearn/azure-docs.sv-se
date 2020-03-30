---
title: Snabbstart- Använd nod.js för att fråga från Azure Cosmos DB SQL API-konto
description: Så här använder du Node.js för att skapa en app som ansluter till Azure Cosmos DB SQL API-konto och frågar efter data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 0b29f9c1f395e079c97d5877d08bd7bd73c7ea53
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240317"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snabbstart: Använd nod.js för att ansluta och fråga data från Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure-portalen och med hjälp av en Node.js-app som klonats från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="walkthrough-video"></a>Genomgång video

Titta på det här videoklippet för en fullständig genomgång av innehållet i den här artikeln.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) med en URI av `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Skapa ett Azure Cosmos-konto

För det här snabbstartssyftet kan du använda [alternativet prova Azure Cosmos DB gratis för](https://azure.microsoft.com/try/cosmosdb/) att skapa ett Azure Cosmos-konto.

1. Navigera till [sidan prova Azure Cosmos DB för kostnadsfritt.](https://azure.microsoft.com/try/cosmosdb/)

1. Välj **SQL API-konto** och välj **Skapa**. Logga in med ditt Microsoft-konto.

1. När inloggningen har framgångsrikt bör ditt Azure Cosmos-konto vara klart. Välj **Öppna i Azure-portalen** för att öppna det nyskapade kontot.

Alternativet "prova Azure Cosmos DB gratis" kräver inte en Azure-prenumeration och det erbjuder dig ett Azure Cosmos-konto under en begränsad period på 30 dagar. Om du vill använda Azure Cosmos-kontot under en längre period bör du i stället [skapa kontot](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) i din Azure-prenumeration.

## <a name="add-a-container"></a>Lägga till en container

Du kan nu använda datautforskaren verktyget i Azure-portalen för att skapa en databas och behållare.

1. Välj**ny behållare för** **datautforskaren** > .

   Området **Lägg till behållare** visas längst till höger, du kan behöva rulla åt höger för att se det.

   ![Datautforskaren på Azure-portalen, fönstret Lägg till container](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. På sidan **Lägg till behållar** anger du inställningarna för den nya behållaren.

   | Inställning           | Föreslaget värde | Beskrivning                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Databas-ID**   | Aktiviteter           | Ange _Uppgifter_ som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrollera **alternativet Etablera databasdataflöde,** det låter dig dela dataflödet som etablerats i databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnadsbesparingar. |
   | **Dataflöde**    | 400             | Lämna dataflödet på 400 begäranheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.                                                                                                                                                                                                                                                    |
   | **Container-ID**  | Objekt           | Ange _objekt_ som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.                                                                                                                                                                                                                                                               |
   | **Partitionsnyckeln** | /category       | I exemplet som beskrivs i den här artikeln används _/category_ som partitionsnyckel.                                                                                                                                                                                                                                                                                                           |

   Förutom föregående inställningar kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckelprincip när du skapar en behållare säkerställer du att ett eller flera värden per partitionsnyckel är unika. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).

   Välj **OK**. Datautforskaren visar den nya databasen och containern.

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Node.js-app från GitHub, ange anslutningssträngen och köra den.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du är intresserad av att lära dig hur Azure Cosmos-databasresurserna skapas i koden kan du granska följande utdrag. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string).

Om du är bekant med den tidigare versionen av SQL JavaScript SDK kan du vara van att se _termersamlingen_ och _dokumentet_. Eftersom Azure Cosmos DB stöder [flera API-modeller](introduction.md)använder [version 2.0+ av JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) _behållaren_för allmänna termer , som kan vara en samling, ett diagram eller en tabell, och _objektet_ för att beskriva innehållet i behållaren.

Cosmos DB JavaScript SDK@azure/cosmosheter " " och kan installeras från npm...

```bash
npm install @azure/cosmos
```

Följande kodfragment är alla hämtade från filen _app.js_.

- Importeras `CosmosClient` från `@azure/cosmos` npm-paketet.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Ett `CosmosClient` nytt objekt initieras.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Markera databasen "Uppgifter".

  ```javascript
  const database = client.database(databaseId);
  ```

- Välj behållaren "Objekt".Select the "Items" container/collection.

  ```javascript
  const container = database.container(containerId);
  ```

- Markera alla objekt i behållaren "Objekt".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Skapa ett nytt objekt

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Uppdatera ett objekt

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Ta bort ett objekt

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> I både metoderna "uppdatera" och "ta bort" måste objektet `container.item()`väljas från databasen genom att anropa . De två parametrar som skickas in är id för objektet och objektets partitionsnyckel. I det här fallet är paritionnyckeln värdet för fältet "kategori".

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att få information om anslutningssträngen för ditt Azure Cosmos-konto. Kopiera anslutningssträngen till appen så att den kan ansluta till databasen.

1. I ditt Azure Cosmos DB-konto i [Azure-portalen](https://portal.azure.com/)väljer du **Nycklar** från den vänstra navigeringen och väljer sedan **Läs-skrivnycklar**. Använd kopieringsknapparna till höger på skärmen för att kopiera URI-filen och primärnyckeln till _app.js-filen_ i nästa steg.

   ![Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)

2. I Öppna _config.js-filen._

3. Kopiera URI-värdet från portalen (med kopieringsknappen) och gör det till slutpunktsnyckelns värde i _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopiera sedan ditt primärnyckelvärde från portalen och `config.key` gör det till värdet för in _config.js_. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Kör appen

1. Kör `npm install` i en terminal@azure/cosmosför att installera " " npm-paketet

2. Kör `node app.js` i en terminal för att starta nodprogrammet.

3. De två objekt som du skapade tidigare i den här snabbstarten visas. Ett nytt objekt skapas. Flaggan "isComplete" på det objektet uppdateras till "true" och slutligen tas objektet bort.

Du kan fortsätta att experimentera med det här exempelprogrammet eller gå tillbaka till Data Explorer, ändra och arbeta med dina data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en behållare med datautforskaren och kör en Node.js-app. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [importera data till azure cosmos db](import-data.md)
