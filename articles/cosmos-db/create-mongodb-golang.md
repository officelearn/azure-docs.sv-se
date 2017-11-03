---
title: 'Azure Cosmos DB: Skapa en MongoDB API-konsolapp med Golang och Azure Portal | Microsoft Docs'
description: "Anger ett Golang-kodexempel som du kan använda för att ansluta till och fråga en Azure Cosmos DB"
services: cosmos-db
author: Durgaprasad-Budhwani
manager: jhubbard
editor: mimig1
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 07/21/2017
ms.author: mimig
ms.openlocfilehash: cb123107178f5e7c0207524c19331a6fa4658739
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-golang-and-the-azure-portal"></a>Azure Cosmos DB: Skapa en MongoDB API-konsolapp med Golang och Azure Portal

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

Den här snabbstarten visar hur du använder en befintlig [MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)-app som skrivits i [Golang](https://golang.org/) och ansluter den till din Azure Cosmos DB-databas, som stöder MongoDB-klientanslutningar.

Med andra ord vet ditt Golang-program bara att det ansluter till en databas som använder MongoDB-API:er. Det är transparent för programmet att data lagras i Azure Cosmos DB.

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) och grundläggande kunskaper i språket [Go](https://golang.org/).
- En IDE — [Gogland](https://www.jetbrains.com/go/) från Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) från Microsoft eller [Atom](https://atom.io/). Jag använder Goglang i den här självstudiekursen.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Klona exempelprogrammet och installera de nödvändiga paketen.

1. Skapa en mapp med namnet CosmosDBSample i mappen GOROOT\src. Mappen finns i C:\Go\ som standard.
2. Kör följande kommando i ett git-terminalfönster, till exempel git bash, för att klona exempelkatalogen till mappen CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Kör följande kommando för att hämta mgo-paketet. 

    ```
    go get gopkg.in/mgo.v2
    ```

Drivrutinen [mgo](http://labix.org/mgo) (uttalas *mango*) är en [MongoDB](http://www.mongodb.org/)-drivrutin för [språket Go](http://golang.org/) som implementerar ett omfattande och noggrant testat urval funktioner i ett mycket enkelt API som följer Go-standard.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Klicka på **Snabbstart** på den vänstra navigeringsmenyn och klicka sedan på **Other** (Övriga) för att visa den information för anslutningssträngen som behövs för Go-programmet.

2. I Goglang öppnar du filen main.go i katalogen GOROOT\CosmosDBSample och uppdaterar följande kodrad med information för anslutningssträngen från Azure Portal enligt följande skärmbild. 

    Databasnamnet är prefixet för värdet **Host** (Värd) i rutan med anslutningssträngen i Azure Portal. Databasnamnet för kontot som visas på bilden nedan är golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Fliken Other (Övrigt) i rutan Snabbstart i Azure Portal som visar information för anslutningssträngen](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Spara filen main.go.

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i filen main.go. 

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Ansluta Go-appen till Azure Cosmos DB

Azure Cosmos DB stöder SSL-aktiverad MongoDB. Om du vill ansluta till en SSL-aktiverad MongoDB måste du definiera funktionen **DialServer** i [mgo.DialInfo](http://gopkg.in/mgo.v2#DialInfo) och utföra anslutningen med funktionen [tls.*Dial*](http://golang.org/pkg/crypto/tls#Dial).

Följande Golang-kodfragment ansluter Go-appen till Azure Cosmos DB MongoDB-API:t. Klassen *DialInfo* innehåller alternativ för att upprätta en session med ett MongoDB-kluster.

```go
// DialInfo holds options for establishing a session with a MongoDB cluster.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to our Azure Cosmos DB MongoDB database.
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect to mongo, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

Metoden **mgo. Dial()** används när det inte finns någon SSL-anslutning. För en SSL-anslutning krävs metoden **mgo.DialWithInfo()**.

En instans av objektet **DialWIthInfo{}** används för att skapa sessionsobjektet. När sessionen har upprättats kan du få åtkomst till samlingen med hjälp av följande kodfragment:

```go
collection := session.DB(“database”).C(“package”)
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Skapa ett dokument

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Fråga eller läsa ett dokument

Azure Cosmos DB stöder komplexa frågor mot JSON-dokument som lagras i varje samling. Följande exempelkod visar en fråga som du kan köra mot dokumenten i samlingen.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Uppdatera ett dokument

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Ta bort ett dokument

Azure Cosmos DB har stöd för borttagning av JSON-dokument.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Kör appen

1. I Goglang ser du till att din GOPATH (finns under **File** (Arkiv), **Settings** (Inställningar), **Go**, **GOPATH**) innehåller den plats där gopkg har installerats. Platsen är USERPROFILE\go som standard. 
2. Kommentera ut de rader som tar bort dokumentet (rad 91-96), så att du kan se dokumentet när appen har körts.
3. Klicka på **Run** (Kör) i Goglang och sedan på **Run 'Build main.go and run'** (Bygg och kör main.go).

    Appen avslutas och en beskrivning av dokumentet som skapats i [Skapa ett dokument](#create-document) visas.
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Utdata för appen i Goglang](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Granska dokumentet i Datautforskaren

Gå tillbaka till Azure Portal för att visa dokumentet i Datautforskaren.

1. Klicka på **Data Explorer (förhandsgranskning)** på den vänstra navigeringsmenyn, expandera **golang-coach** och **paketet** och klicka sedan på **Dokument**. Klicka på \_-ID:t på fliken **Dokument** för att visa dokumentet i den högra rutan. 

    ![Det nyligen skapade dokumentet visas i Datautforskaren](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Du kan sedan arbeta med dokumentet och spara det genom att klicka på **Uppdatera**. Du kan också ta bort dokumentet eller skapa nya dokument eller frågor.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto och kör en Golang-app via API:t för MongoDB. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med MongoDB-API:t](mongodb-migrate.md)
