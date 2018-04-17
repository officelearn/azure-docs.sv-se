---
title: Flera huvuddatabasen arkitekturer med Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur du utformar programarkitekturer med lokala läsningar och skrivningar över flera geografiska områden med Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e8853d521173a9a8d3c925361e43ce469471918
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Flera master replikerad globalt databasarkitekturer med Azure Cosmos DB
Azure Cosmos-DB stöder NYCKELFÄRDIGT [globala replikering](distribute-data-globally.md), där du kan distribuera data till flera regioner med låg latens åtkomst var som helst i arbetsbelastningen. Den här modellen används ofta för utgivaren/konsumenten arbetsbelastningar där det finns en skrivare i en geografisk region och globalt distribuerade läsare i andra (skrivskyddad) regioner. 

Du kan också använda Azure Cosmos DB globala replication stöd för att bygga program som skrivare och läsare globalt distribueras. Det här dokumentet beskrivs ett mönster som gör det möjligt att uppnå lokala Skriv- och lokala läsbehörighet för distribuerade skrivare med hjälp av Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publiceringen av innehållet - exempel
Nu ska vi titta på ett verkligt scenario som beskriver hur du kan använda global flera-region/flera-master läsa skriva mönster med Azure Cosmos DB. Överväg att en innehåll publishing plattform som bygger på Azure Cosmos DB. Här följer några krav som den här plattformen måste uppfylla för en bra användarupplevelse för både utgivare och konsumenter.

* Både författare och prenumeranter kan sträcka sig över hela världen 
* Författare måste publicera (skriva) artiklar på sin lokala (närmaste) region
* Författare har läsare-/ prenumeranter sina artiklar som distribueras över hela världen. 
* Prenumeranter bör få ett meddelande när nya artiklar publiceras.
* Prenumeranter måste kunna läsa artiklar från sin lokala region. De bör också kunna lägga till granskningar till dessa artiklar. 
* Alla inklusive författaren av artiklarna ska kunna visa alla omdömen anslutna till artiklar från region med en lokal. 

Under förutsättning att miljontals användare och utgivare med miljarder artiklar, snart vi behöver ta itu med problem för skala tillsammans med garanterar ort åtkomst. Precis som med de flesta skalbarhetsproblem med, är lösningen en bra strategi för partitionering. Nu ska vi titta på hur modellen artiklar, granska och meddelanden som dokument, konfigurera Azure Cosmos DB konton och implementera en dataåtkomstnivå. 

Om du vill veta mer om partitionering och partitionsnycklar [partitionering och skalning i Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modeling meddelanden
Meddelanden är datafeeds specifika för en användare. Därför är åtkomstmönster för meddelanden dokument alltid i samband med en enskild användare. Du kan till exempel ”skicka ett meddelande till en användare” eller ”hämta alla meddelanden för en viss användare”. Därför föredra partitionering nyckel för den här typen är `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modeling prenumerationer
Prenumerationer kan skapas för olika kriterier som en viss kategori med artiklar för specifika intresseområden eller en specifik utgivare. Därför det `SubscriptionFilter` är ett bra alternativ för partitionsnyckel.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Modeling artiklar
När en artikel identifieras via meddelanden efterföljande frågor vanligtvis baserat på den `Article.Id`. Att välja `Article.Id` som partition nyckeln därför ger bästa distributionen för att lagra artiklar i en samling Azure Cosmos DB. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Modellering granskar
Som artiklar, granskningar främst skrivs och läses i kontexten för artikeln. Att välja `ArticleId` som en partition nyckel ger bästa distribution och effektiv åtkomst av granskningar som är kopplad till artikeln. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Metoder för dataåtkomst lager
Nu ska vi titta på viktigaste data åtkomstmetoder vi behöver implementera. Här är listan över metoder som den `ContentPublishDatabase` måste:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Konfigurationen av Azure DB Cosmos-konto
Om du vill garantera lokala läser och skriver vi måste partitionera bara data inte på partition nyckel, men även baserat på geografisk åtkomstmönstret i regioner. Modellen är beroende av att ha ett geo-replikerade Azure Cosmos DB databaskonto för varje region. Till exempel med två regioner är här en konfiguration för flera regioner skrivningar:

| Kontonamn | Skrivregion | Läsregion |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Följande diagram visar hur läsning och skrivning utförs i en typisk program med den här installationen:

![Azure multimaster Cosmos-DB-arkitektur](./media/multi-region-writers/multi-master.png)

Här är ett kodfragment som visar hur du initierar klienter i en DAL som körs i den `West US` region.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Med den föregående installationen kan dataåtkomstnivå vidarebefordra alla skrivningar till det lokala kontot utifrån där den distribueras. Läser utförs av läsning från båda kontona att hämta globala vyn av data. Den här metoden kan utökas till så många regioner som krävs. Här är till exempel en installation med tre geografiska områden:

| Kontonamn | Skrivregion | Läs Region 1 | Läs Region 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Data access layer implementering
Nu ska vi titta på implementering av dataåtkomstlagret (DAL) för ett program med två skrivbar regioner. DAL måste implementera följande steg:

* Skapa flera instanser av `DocumentClient` för varje konto. Med två regioner varje DAL-instans har en `writeClient` och ett `readClient`. 
* Baserat på den distribuerade regionen för programmet, konfigurera slutpunkter för `writeclient` och `readClient`. Till exempel DAL distribuerats i `West US` använder `contentpubdatabase-usa.documents.azure.com` för att utföra skrivningar. DAL distribuerats i `NorthEurope` använder `contentpubdatabase-europ.documents.azure.com` för skrivningar.

Metoder för dataåtkomst kan implementeras med föregående installation. Skriva operations vidarebefordra skriva till motsvarande `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

För att läsa meddelanden och granskningar, måste du läsa från både regioner och union resultaten som visas i följande utdrag:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Genom att välja en bra partitionsnyckel och statiska konto-baserade partitionering, kan du därför uppnå flera regioner lokala skrivningar och läsningar med hjälp av Azure Cosmos DB.

## <a id="NextSteps"></a>Nästa steg
I den här artikeln beskrivs hur du kan använda global flera regioner läsa skriva mönster med Azure Cosmos-databasen med publiceringen av innehållet som ett exempelscenario.

* Lär dig mer om hur Azure Cosmos DB stöder [global distributionsplatsen](distribute-data-globally.md)
* Lär dig mer om [automatisk och manuell växling vid fel i Azure Cosmos DB](regional-failover.md)
* Lär dig mer om [global konsekvens med Azure Cosmos DB](consistency-levels.md)
* Utveckla med flera regioner med hjälp av den [Azure Cosmos DB - SQL-API](tutorial-global-distribution-sql-api.md)
* Utveckla med flera regioner med hjälp av den [Azure DB Cosmos - MongoDB-API](tutorial-global-distribution-MongoDB.md)
* Utveckla med flera regioner med hjälp av den [Azure DB Cosmos - tabellen API](tutorial-global-distribution-table.md)
