---
title: "Använda Azure Cosmos DB API för MongoDB för att skapa en webbapp | Microsoft Docs"
description: "En självstudiekurs om Azure Cosmos DB som skapar en onlinedatabas webbprogram som använder API: et för MongoDB."
keywords: mongodb-exempel
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: a0598d32b5bad240c0a5d77a6e19285115a9f6b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB: Ansluta till en MongoDB-app med hjälp av .NET

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här kursen visar hur du skapar ett Azure DB som Cosmos-konto med Azure-portalen och hur du skapar en databas och samling för att lagra data med hjälp av den [MongoDB API](mongodb-introduction.md). 

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto 
> * Uppdatera din anslutningssträng
> * Skapa en MongoDB-app på en virtuell dator 


## <a name="create-a-database-account"></a>Skapa ett databaskonto

Börja med att skapa ett Azure DB som Cosmos-konto i Azure-portalen.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Redan har ett Azure DB som Cosmos-konto? I så fall, gå vidare till [konfigurera Visual Studio-lösning](#SetupVS)
> * Hade du ett Azure DocumentDB-konto? Om ditt konto är nu ett Azure DB som Cosmos-konto och du kan gå vidare till så [konfigurera Visual Studio-lösning](#SetupVS).  
> * Om du använder Azure Cosmos DB-emulatorn, följer du stegen i [Azure Cosmos DB emulatorn](local-emulator.md) konfigurera emulatorn och gå vidare till [ställa in din Visual Studio-lösning](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

1. I Azure-portalen i den **Azure Cosmos DB** väljer API för MongoDB-kontot. 
2. I fältet till vänster på konto-bladet, klickar du på **Snabbstart**. 
3. Välj din plattform (*.NET drivrutinen*, *Node.js-drivrutinen*, *MongoDB Shell*, *Java-drivrutinen*, *Python-drivrutinen*). Om du inte ser ditt drivrutin eller verktyg, oroa dig inte, vi dokumentera kontinuerligt mer kodfragment för anslutningen. 
4. Kopiera och klistra in kodfragmentet till MongoDB-app, och du är redo att sätta igång.

## <a name="set-up-your-mongodb-app"></a>Konfigurera din app för MongoDB

Du kan använda den [skapa en webbapp i Azure som ansluter till MongoDB som körs på en virtuell dator](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md) självstudiekurs med minimala ändringar, att snabbt konfigurera en MongoDB-programmet (antingen lokalt eller publiceras på en Azure-app) som ansluter till en API för MongoDB-kontot.  

1. Följ självstudierna med en enda ändring.  Ersätt Koden Dal.cs med detta:

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. Ändra följande variabler i filen Dal.cs per inställningarna för ditt konto från sidan nycklar i Azure-portalen:

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Använd appen!

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den här appen mer följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure-portalen. 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto 
> * Uppdatera din anslutningssträng
> * Skapa en MongoDB-app på en virtuell dator

Du kan gå vidare till nästa kurs och importera MongoDB-data till Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)

