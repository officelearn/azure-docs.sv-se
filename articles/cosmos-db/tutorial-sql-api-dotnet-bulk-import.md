---
title: Massimportdata till Azure Cosmos DB SQL API-konto med hjälp av .Net SDK
description: Lär dig hur du importerar eller intar data till Azure Cosmos DB genom att skapa ett .NET-konsolprogram som optimerar etablerat dataflöde (RU/s) som krävs för att importera data
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587574"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Massimportdata till Azure Cosmos DB SQL API-konto med hjälp av .NET SDK

Den här självstudien visar hur du skapar ett .NET-konsolprogram som optimerar etablerat dataflöde (RU/s) som krävs för att importera data till Azure Cosmos DB. I den här artikeln läser du data från en exempeldatakälla och importerar dem till en Azure Cosmos-behållare.
Den här självstudien använder [version 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) av Azure Cosmos DB .NET SDK, som kan riktas till .NET Framework eller .NET Core.

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos-konto
> * Konfigurera projektet
> * Ansluta till ett Azure Cosmos-konto med masssupport aktiverad
> * Utföra en dataimport genom samtidiga skapa åtgärder

## <a name="prerequisites"></a>Krav

Innan du följer instruktionerna i den här artikeln bör du kontrollera att du har följande resurser:

* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Du kan kontrollera vilken version som `dotnet --version`är tillgänglig i din miljö genom att köra .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto

[Skapa ett Azure Cosmos DB SQL API-konto](create-cosmosdb-resources-portal.md) från Azure-portalen eller så kan du skapa kontot med hjälp av [Azure Cosmos DB Emulator](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Steg 2: Konfigurera .NET-projektet

Öppna kommandotolken i Windows eller ett terminalfönster från den lokala datorn. Du kör alla kommandon i nästa avsnitt från kommandotolken eller terminalen. Kör följande dotnet nytt kommando för att skapa en ny app med namnet *bulk-import-demo*. Parametern `--langVersion` anger egenskapen *LangVersion* i den skapade projektfilen.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Den förväntade produktionen från bygga bör se ut ungefär så här:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Steg 3: Lägg till Azure Cosmos DB-paketet

Installera Azure Cosmos DB-klientbiblioteket för .NET Core medan du fortfarande är i programkatalogen med hjälp av kommandot dotnet add package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Steg 4: Hämta dina Azure Cosmos-kontouppgifter

Exempelprogrammet måste autentiseras till ditt Azure Cosmos-konto. För att autentisera bör du skicka Azure Cosmos-kontoautentiseringsuppgifterna till programmet. Hämta dina Azure Cosmos-kontouppgifter efter så här:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
1.  Navigera till ditt Azure Cosmos-konto.
1.  Öppna fönstret **Nycklar** och kopiera **URI:n** och **PRIMÄRNYCKELn** för ditt konto.

Om du använder Azure Cosmos DB Emulator kan du hämta [emulatorautentiseringsuppgifterna från](local-emulator.md#authenticating-requests)den här artikeln .

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Steg 5: Initiera CosmosClient-objektet med stöd för masskörning

Öppna den `Program.cs` genererade filen i en kodredigerare. Du skapar en ny instans av CosmosClient med masskörning aktiverad och använder den för att utföra åtgärder mot Azure Cosmos DB. 

Låt oss börja med att `Main` skriva över standardmetoden och definiera de globala variablerna. Dessa globala variabler kommer att innehålla slutpunkten och auktoriseringsnycklar, namnet på databasen, behållare som du ska skapa och antalet objekt som du kommer att infoga i bulk. Se till att ersätta värdena för slutpunktURL och auktoriseringsnyckel enligt din miljö. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Lägg `Main` till följande kod i metoden för att initiera CosmosClient-objektet:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

När masskörningen har aktiverats grupperar CosmosClient internt samtidiga åtgärder i enskilda tjänstanrop. På så sätt optimeras dataflödesanvändningen genom att distribuera tjänstanrop över partitioner och slutligen tilldela enskilda resultat till de ursprungliga anroparna.

Du kan sedan skapa en behållare för att lagra alla våra artiklar.  Definiera `/pk` som partitionsnyckel, 50000 RU/s som etablerat dataflöde och en anpassad indexeringsprincip som utesluter alla fält för att optimera skrivflödet. Lägg till följande kod efter CosmosClient-initieringssatsen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Steg 6: Fylla i en lista över samtidiga uppgifter

Om du vill dra nytta av stöd för masskörning skapar du en lista över asynkrona uppgifter `Task.WhenAll` baserat på datakällan och de åtgärder du vill utföra och använder för att köra dem samtidigt.
Låt oss börja med att använda "Bogus" data för att generera en lista över objekt från vår datamodell. I ett verkligt program, skulle objekten kommer från önskad datakälla.

Lägg först till det falska paketet i lösningen med hjälp av kommandot dotnet add package.

   ```bash
   dotnet add package Bogus
   ```

Definiera definitionen av de objekt som du vill spara. Du måste definiera `Item` klassen `Program.cs` i filen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Skapa sedan en hjälpfunktion `Program` i klassen. Den här hjälpfunktionen får antalet objekt som du har definierat för att infoga och genererar slumpmässiga data:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Läs objekten och serialisera dem i `System.Text.Json` strömmande instanser med hjälp av klassen. På grund av den typ av autogenererade data, du serialisera data som strömmar. Du kan också använda objektinstansen direkt, men genom att konvertera dem till strömmar kan du utnyttja prestanda för strömma API:er i CosmosClient. Vanligtvis kan du använda data direkt så länge du känner till partitionsnyckeln. 


Om du vill konvertera data till `Main` strömmarinstanser lägger du till följande kod direkt efter att behållaren har skapats:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Använd sedan dataströmmarna för att skapa samtidiga aktiviteter och fylla i uppgiftslistan för att infoga objekten i behållaren. Om du vill utföra den här `Program` åtgärden lägger du till följande kod i klassen:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Alla dessa samtidiga punktåtgärder kommer att utföras tillsammans (det är i bulk) enligt beskrivningen i introduktionsavsnittet.

## <a name="step-7-run-the-sample"></a>Steg 7: Kör provet

För att köra exemplet kan du göra `dotnet` det helt enkelt med kommandot:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Hämta hela exemplet

Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned kodexemplen kan du hämta den från [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

När du har klonat projektet ska du uppdatera önskade autentiseringsuppgifter i [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

Exemplet kan köras genom att ändra `dotnet`till databaskatalogen och använda:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos-konto
> * Konfigurera projektet
> * Ansluta till ett Azure Cosmos-konto med masssupport aktiverad
> * Utföra en dataimport genom samtidiga skapa åtgärder

Du kan nu gå vidare till nästa självstudiekurs:

> [!div class="nextstepaction"]
>[Köra frågor mot Azure Cosmos DB med hjälp av SQL API](tutorial-query-sql-api.md)