---
title: Använda Azure Cosmos DB:s API för MongoDB till att skapa en webbapp | Microsoft Docs
description: En självstudie om Azure Cosmos DB som skapar en webbapp i en onlinedatabas som använder API:n för MongoDB.
keywords: mongodb examples
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 76a8e19bacdbde938758bf41ed7f209521f513aa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2018
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB: Ansluta till en MongoDB-app med hjälp av .NET

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här självstudien visar hur du skapar ett Azure Cosmos DB-konto med Azure Portal, samt hur du skapar en databas och en samling för att lagra data med hjälp av [MongoDB API](mongodb-introduction.md). 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto 
> * Uppdatera din anslutningssträng
> * Skapa en MongoDB-app på en virtuell dator 


## <a name="create-a-database-account"></a>Skapa ett databaskonto

Vi ska börja med att skapa ett Azure Cosmos DB-konto i Azure-portalen.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Har du redan ett Azure Cosmos DB-konto? I sådana fall kan du hoppa vidare till [Konfigurera din Visual Studio-lösning](#SetupVS)
> * Om du använder Azure Cosmos DB-emulatorn följer du stegen i [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och hoppa vidare till [Konfigurera din Visual Studio-lösning](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

1. I Azure Portal på sidan **Azure Cosmos DB** väljer du API:n för MongoDB-kontot. 
2. I fältet till vänster på kontobladet klickar du på **Snabbstart**. 
3. Välj din plattform (*.NET-drivrutin*, *Node.js-drivrutin*, *MongoDB-gränssnitt*, *Java-drivrutin*, *Python-drivrutin*). Om du inte ser din drivrutin eller ditt verktyg i listan behöver du inte oroa dig. Vi dokumenterar kontinuerligt fler kodfragment för anslutningar. 
4. Kopiera och klistra in kodfragmentet i MongoDB-appen så är du redo att sätta igång.

## <a name="set-up-your-mongodb-app"></a>Konfigurera din MongoDB-app

Du kan använda självstudien [Skapa en webbapp i Azure som ansluter till MongoDB som körs på en virtuell dator](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md) med minimala ändringar, för att snabbt konfigurera ett MongoDB-program (antingen lokalt eller publicerat till en Azure-webbapp) som ansluter till en API för MongoDB-kontot.  

1. Följ självstudien och gör bara en enda ändring.  Ersätt koden Dal.cs med följande:

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

2. Ändra nedanstående variabler i filen Dal.cs enligt dina kontoinställningar från sidan Nycklar i Azure Portal:

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

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto 
> * Uppdatera din anslutningssträng
> * Skapa en MongoDB-app på en virtuell dator

Fortsätt med nästa självstudie och importera dina MongoDB-data till Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)

