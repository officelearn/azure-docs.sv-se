---
title: 'Snabbstart: Cassandra API med .NET - Cosmos Azure DB | Microsoft Docs'
description: "Den här snabbstarten visar hur du använder Azure Cosmos DB Cassandra API för att skapa en profil program med Azure-portalen och .NET"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2503e7e6025e6f064574f14855468ae9b1b97fa0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-build-a-cassandra-app-with-net-and-azure-cosmos-db"></a>Snabbstart: Skapa en Cassandra app med .NET och Azure Cosmos DB

Den här snabbstarten visar hur du använder .NET och Azure Cosmos DB [Cassandra API](cassandra-introduction.md) att skapa en app för profilen genom att klona ett exempel från GitHub. Denna Snabbstart också vägleder dig genom att skapa ett Azure DB som Cosmos-konto med hjälp av den webbaserade Azure-portalen.   

Azure Cosmos-DB är Microsofts globalt distribuerade flera modellen database-tjänsten. Du kan snabbt skapa och fråga dokumentet, tabell, nyckel / värde- och graph-databaser som dra nytta av de globala distribution och skala horisontellt kärnan i Azure Cosmos DB. 

## <a name="prerequisites"></a>Krav

Åtkomst till förhandsgranskningsprogrammet Azure Cosmos DB Cassandra API. Om du inte använder för åtkomst till ännu, [registrera nu](https://aka.ms/cosmosdb-cassandra-signup).

Om du inte redan har Visual Studio 2017 installerat, du kan hämta och använda den **ledigt** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Du kan också [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, helt kostnadsfritt och åtaganden.

Installera [Git](https://www.git-scm.com/) att klona exemplet.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en Cassandra API-app från GitHub, ange anslutningssträngen och kör den. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en git-terminalfönster, till exempel git bash och använda den `cd` kommando för att ändra till en mapp att installera sample-appen. 

    ```bash
    cd "C:\git-samples"
    ```

2. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av sample-appen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-getting-started.git
    ```

3. Öppna sedan lösningsfilen CassandraQuickStartSample i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du är intresserad av att lära sig hur databasresurser skapas i koden kan granska du följande kodavsnitt. Fragmenten hämtas från den `Program.cs` fil i mappen C:\git-samples\azure-cosmos-db-cassandra-dotnet-getting-started\CassandraQuickStartSample. Annars kan du gå vidare till [uppdatera anslutningssträngen](#update-your-connection-string).

* Initiera sessionen genom att ansluta till en Cassandra klusterslutpunkten. Cassandra-API: et på Azure Cosmos DB stöder endast TLSv1.2. 

  ```csharp
   var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
   options.SetHostNameResolver((ipAddress) => CassandraContactPoint);
   Cluster cluster = Cluster.Builder().WithCredentials(UserName, Password).WithPort(CassandraPort).AddContactPoint(CassandraContactPoint).WithSSL(options).Build();
   ISession session = cluster.Connect();
   ```

* Skapa en ny keyspace.

    ```csharp
    session.Execute("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"); 
    ```

* Skapa en ny tabell.

   ```csharp
  session.Execute("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)");
   ```

* Infoga användaren entiteter med hjälp av IMapper-objektet med en ny session som ansluter till uprofile keyspace.

    ```csharp
    mapper.Insert<User>(new User(1, "LyubovK", "Dubai"));
    ```
    
* Frågan att hämta information om alla användare.

    ```csharp
   foreach (User user in mapper.Fetch<User>("Select * from user"))
   {
      Console.WriteLine(user);
   }
    ```
    
 * Fråga om du vill hämta information om en enskild användare.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Informationen i anslutningssträngen kan din app för att kommunicera med databasen värdbaserade.

1. I den [Azure-portalen](http://portal.azure.com/), klickar du på **anslutningssträngen**. 

    Använd den ![Kopieringsknappen](./media/create-cassandra-dotnet/copy.png) knappen på höger sida av skärmen för att kopiera värdet för användarnamn.

    ![Visa och kopiera snabbtangent i Azure portal, anslutningssträngen-sida](./media/create-cassandra-dotnet/keys.png)

2. Öppna filen Program.cs i Visual Studio 2017. 

3. Klistra in USERNAME värdet från portalen över `<FILLME>` på rad 13.

    Raden 13 i Program.cs bör nu se ut 

    `private const string UserName = "cosmos-db-quickstart";`

3. Gå tillbaka till portalen och kopiera värdet lösenord. Klistra in lösenordsvärdet från portalen över `<FILLME>` på rad 14.

    Raden 14 i Program.cs bör nu se ut 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

4. Gå tillbaka till portalen och kopiera KONTAKTA punktvärdet. Klistra in KONTAKTA punktvärdet från portalen över `<FILLME>` på rad 15.

    Raden 15 i Program.cs bör nu se ut 

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.documents.azure.com"; //  DnsName`

5. Spara filen Program.cs.
    
## <a name="run-the-app"></a>Kör appen

1. I Visual Studio klickar du på **verktyg** > **NuGet Package Manager** > **Pakethanterarkonsolen**.

2. I Kommandotolken, använder du följande kommando för att installera drivrutinen .NET NuGet-paketet. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Tryck på Ctrl + F5 för att köra programmet. Appen visas i din konsolfönstret. 

    ![Visa och kontrollera utdata](./media/create-cassandra-dotnet/output.png)

    Tryck på CTRL + C om du vill stoppa exection av programmet och Stäng fönstret. 
    
    Nu kan du öppna Data Explorer i Azure portal för att se fråga, ändra och arbeta med dessa nya data. 

    ![Visa data i Data Explorer](./media/create-cassandra-dotnet/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto, skapar en samling med datautforskaren och kör en webbapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra data till Azure Cosmos DB](cassandra-import-data.md)
