---
title: Utföra flera regioner skrivningar och läsningar genom att välja rätt Partitioneringsnyckel | Microsoft Docs
description: Läs mer om hur du utformar arkitekturer för program med lokala läsningar och skrivningar över flera geografiska områden med Azure Cosmos DB genom att välja en partitionsnyckel.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/6/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d38b7cd7d1f28f706e94782602926abc8fc11e3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060667"
---
# <a name="perform-multi-region-writes-and-reads-by-choosing-the-right-partitioning-key"></a>Utföra flera regioner skrivningar och läsningar genom att välja rätt Partitioneringsnyckel

Azure Cosmos DB stöder NYCKELFÄRDIGT [global replikering](distribute-data-globally.md), där du kan distribuera data till flera regioner med låg latens var som helst i arbetsbelastningen. Den här modellen används ofta för utgivare/konsument arbetsbelastningar där det finns en skrivare i en enda geografisk region och globalt distribuerade läsare i andra regioner för (skrivskyddad). 

Du kan också använda Azure Cosmos DB global Replikeringsstöd för att bygga program som skrivare och läsare distribueras globalt. Det här dokumentet beskriver ett mönster som gör det möjligt att uppnå lokala Skriv- och lokala läsbehörighet för distribuerade skrivare med hjälp av Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publiceringen av innehållet – ett exempelscenario
Låt oss titta på ett verkligt scenario som beskriver hur du kan använda globalt distribuerade Multi-Factor-region/Multi-Factor-master skrivskyddad mönster med Azure Cosmos DB. Överväg en publishing innehållsplattform som bygger på Azure Cosmos DB. Här följer några krav som den här plattformen måste uppfylla för en bättre användarupplevelse för både utgivare och konsumenter.

* Både redigerare och prenumeranter kan sträcka sig över hela världen 
* Författare måste publicera artiklar (skriva) till deras lokala (närmaste) region
* Författare har läsare-/ prenumeranter av artiklarna som distribueras över hela världen. 
* Prenumeranter bör få ett meddelande när nya artiklar publiceras.
* Prenumeranter måste kunna läsa artiklarna från deras lokala region. De bör också att kunna lägga till recensioner i dessa artiklar. 
* Vem som helst, inklusive författaren av artiklarna ska kunna visa alla granskningar som är anslutna till artiklar från en lokal region. 

Om vi antar att miljoner konsumenter och utgivare med flera miljarder artiklar, snart måste vi ta itu med problemen med skalas tillsammans, vilket ger ort av åtkomst. Precis som med de flesta skalbarhetsproblem ligger lösningen i en bra partitioneringsstrategi. Nu ska vi titta på hur du modellera artiklar, granskning och aviseringar som dokument, konfigurera Azure Cosmos DB-konton och implementera en dataåtkomstlagret. 

Om du vill ha mer information om partitionering och partitionsnycklar finns i [partitionering och skalning i Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modellering meddelanden
Meddelanden är data matas specifika till en användare. Åtkomstmönster för meddelanden dokument är därför alltid i samband med enskild användare. Du kan till exempel ”publicera ett meddelande till en användare” eller ”hämta alla meddelanden för en viss användare”. Så att föredra att partitionera nyckeln för den här typen skulle vara `UserId`.

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

## <a id="ModelingSubscriptions"></a>Modellering prenumerationer
Prenumerationer kan skapas för olika kriterier som en särskild kategori av artiklar olika områden eller en viss utgivare. Därför den `SubscriptionFilter` är ett bra val för partitionsnyckel.

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

## <a id="ModelingArticles"></a>Modellering artiklar
När en artikel har identifierats via meddelanden, efterföljande frågor baseras vanligtvis på den `Article.Id`. Välja `Article.Id` som partition nyckeln därför ger bästa distributionen för att lagra artiklar i en Azure Cosmos DB-samling. 

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

## <a id="ModelingReviews"></a>Modellering granskningar
Som artiklar, granskningar huvudsakligen skrivs och läsa i kontexten för artikeln. Välja `ArticleId` som en partition nyckel ger bästa distribution och effektiv åtkomst till granskningar som är associerade med artikeln. 

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

## <a id="DataAccessMethods"></a>Data access layer-metoder
Nu ska vi titta på de viktigaste data åtkomstmetoder som vi behöver för att implementera. Här är listan över metoder som de `ContentPublishDatabase` måste:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Azure Cosmos DB-kontokonfigurationen
För att garantera lokal läsningar och skrivningar, vi måste partitionera bara data inte på partitionen nyckel, men även baserat på geografisk åtkomstmönstret till regioner. Modellen är beroende av att ha en geo-replikerat Azure Cosmos DB-databaskonto för varje region. Till exempel med två regioner är här en konfiguration för flera regioner skrivningar:

| Kontonamn | Skrivregion | Läsregion |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Följande diagram visar hur läsningar och skrivningar utförs i ett typiskt program med de här inställningarna:

![Azure Cosmos DB multimaster-arkitektur](./media/multi-master-workaround/multi-master.png)

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

Dataåtkomstlagret kan vidarebefordra alla skrivningar till det lokala kontot utifrån där det har distribuerats med den föregående installationen. Läsningar som utförs av läsning från båda konton för att få global vy över data. Den här metoden kan utökas till så många regioner som krävs. Här är till exempel en installation med tre geografiska områden:

| Kontonamn | Skrivregion | Läsregion 1 | Läsregion 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Data access layer implementering
Nu ska vi titta på implementeringen av dataåtkomstlagret (DAL) för ett program med två skrivbar regioner. DAL måste implementera följande steg:

* Skapa flera instanser av `DocumentClient` för varje konto. Med två regioner varje DAL-instans har en `writeClient` och en `readClient`. 
* Baserat på den distribuerade regionen av programmet, konfigurera slutpunkter för `writeclient` och `readClient`. Till exempel DAL distribueras i `West US` använder `contentpubdatabase-usa.documents.azure.com` för att utföra skrivåtgärder. DAL distribueras i `NorthEurope` använder `contentpubdatabase-europ.documents.azure.com` för skrivningar.

Metoder för dataåtkomst kan implementeras med den föregående installationen. Skriva operations vidarebefordra skriva till motsvarande `writeClient`.

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

För att läsa meddelanden och granskningar, måste du läsa från både regioner och union resultaten som visas i följande kodavsnitt:

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

Du kan därför få flera regioner lokala skrivningar och läsningar med Azure Cosmos DB i genom att välja en bra partitionsnyckel och statiska baserade partitionering.

## <a id="NextSteps"></a>Nästa steg
I den här artikeln beskrivs hur du kan använda mönster för globalt distribuerade skrivskyddad i flera regioner med Azure Cosmos DB med publiceringen av innehållet som en Exempelscenario.

* Lär dig mer om hur Azure Cosmos DB stöder [global distribution](distribute-data-globally.md)
* Lär dig mer om [automatisk och manuell redundans i Azure Cosmos DB](regional-failover.md)
* Lär dig mer om [global konsekvens med Azure Cosmos DB](consistency-levels.md)
* Utveckla med flera regioner med den [Azure Cosmos DB - SQL-API](tutorial-global-distribution-sql-api.md)
* Utveckla med flera regioner med den [Azure Cosmos DB - MongoDB API](tutorial-global-distribution-MongoDB.md)
* Utveckla med flera regioner med den [Azure Cosmos DB - tabell-API](tutorial-global-distribution-table.md)
