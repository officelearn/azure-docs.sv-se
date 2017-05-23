---
title: Skapa ett Azure Cosmos DB-konto | Microsoft Docs
description: "Skapa en NoSQL-databas med Azure Cosmos DB. Skapa ett Azure Cosmos DB-konto genom att följa instruktionerna nedan och börja utveckla din blixtsnabba, globala NoSQL-databas."
keywords: skapa en databas
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 0e7f8488-7bb7-463e-b6fd-3ae91a02c03a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/17/2017
ms.author: mimig
redirect_url: https://aka.ms/acdbnetqa
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 13621d942f2880f4dd1523315f43099eca2607d8
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-nosql-account-using-the-azure-portal"></a>Skapa ett Azure Cosmos DB NoSQL-konto med Azure portal
> [!div class="op_single_selector"]
> * [Azure Portal](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

För att skapa en databas med Microsoft Azure Cosmos DB måste du:

* Ha ett Azure-konto. Om du inte redan har ett kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free).
* Skapa ett Azure Cosmos DB-konto.  

Du kan skapa ett Azure Cosmos DB-konto via Azure Portal, Azure Resource Manager-mallar eller kommandoradsgränssnittet (CLI) i Azure. Den här artikeln beskriver hur du skapar ett Azure Cosmos DB-konto med hjälp av Azure Portal. Om du vill skapa ett konto med Azure Resource Manager eller Azure CLI läser du [Automate Azure Cosmos DB database account creation](documentdb-automation-resource-manager-cli.md) (Automatisera genereringen av ett Azure Cosmos DB-databaskonto).

Har du inte använt Azure Cosmos DB tidigare? Titta på [den här](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) fyra minuter långa videon av Scott Hanselman, så får du se hur du utför de vanligaste uppgifterna i onlineportalen.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Nytt** i det vänstra navigeringsfältet, klicka på **Databaser** och sedan på **Azure Cosmos DB**.

   ![Skärmbild av Azure Portal med fokus på Fler tjänster och NoSQL (Azure Cosmos DB)](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  
3. På bladet **Nytt konto** anger du önskad konfiguration för Azure Cosmos DB-kontot.

    ![Skärmbild av bladet Ny Azure Cosmos DB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

   * Ange namnet som ska användas för att identifiera Azure Cosmos DB-kontot i rutan **ID**.  När **ID**:n har verifierats, visas en grön kryssmarkering i **ID**-rutan. **ID**-värdet blir värdnamnet inom URI:n. **ID**:n får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken. Observera att *documents.azure.com* läggs till i slutpunktsnamnet du väljer, och resultatet blir slutpunkten för ditt Azure Cosmos DB-konto.
   * I rutan **NoSQL API** markerar du den programmeringsmodell som ska användas:

     * **DocumentDB**: API:t för DocumentDB är tillgängligt via [SDK:er](documentdb-sdk-dotnet.md) för .NET, Java, Node.js, Python och JavaScript, samt HTTP [REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), och omfattar programmeringsåtkomst till alla DocumentDB-funktioner.
     * **MongoDB**: DocumentDB omfattar också [support på protokollnivå](documentdb-protocol-mongodb.md) för API:er för **MongoDB**. När du väljer MongoDB API-alternativet kan du använda befintliga SDK:er och [verktyg](documentdb-mongodb-mongochef.md) för MongoDB för att kommunicera med DocumentDB. Du kan [flytta](documentdb-import-data.md) dina befintliga MongoDB-appar för att använda DocumentDB [utan att kodändringar krävs](documentdb-connect-mongodb-account.md), och utnyttja en fullständigt hanterad databas som en tjänst, med obegränsad skala, global replikering och andra funktioner.
   * För **Prenumeration** väljer du den Azure-prenumeration som du vill använda för Azure Cosmos DB-kontot. Om ditt konto bara har en prenumeration så väljs det kontot som standard.
   * I **Resursgrupp** väljer du eller skapar en resursgrupp för Azure Cosmos DB-kontot.  Som standard skapas en ny resursgrupp. Mer information finns i [Using the Azure portal to manage your Azure resources](../azure-portal/resource-group-portal.md) (Hantera Azure-resurser med hjälp av Azure Portal).
   * Använd **Plats** för att ange den geografiska platsen där du vill att Azure Cosmos DB-kontots värd ska finnas.
4. När de nya alternativen för Azure Cosmos DB-kontot har konfigurerats klickar du på **Skapa**. Kontrollera meddelandehubben för att se statusen för distributionen.  

   ![Skapa databaser snabbt – Skärmbild av navet Meddelanden som visar att Azure Cosmos DB-kontot håller på att skapas](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

   ![Skärmbild av navet Meddelanden som visar att Azure Cosmos DB-kontot har skapats och distribuerats till en resursgrupp – Meddelande från online-databasskaparen](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)
5. När Azure Cosmos DB-kontot är det redo för användning med standardinställningarna. Standardkonsekvensnivån för Azure Cosmos DB-kontot är **Session**.  Du kan justera standardkonsekvensen genom att klicka på **Standardkonsekvens** på resursmenyn. Mer information om de tillgängliga konsekvensnivåerna för Azure Cosmos DB finns i avsnittet om [konsekvensnivåer i Azure Cosmos DB](documentdb-consistency-levels.md).

   ![Skärmbild av Resursgrupp-bladet – starta apputveckling](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

   ![Skärmbild av Konsekvensnivå-bladet – sessionskonsekvens](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create an Azure Cosmos DB account]: #Howto
[Next steps]: #NextSteps


## <a name="next-steps"></a>Nästa steg
Nu när du har ett Azure Cosmos DB-konto är nästa steg att skapa en Azure Cosmos DB-samling och en Azure Cosmos DB-databas.

Du kan skapa en ny samling och databas genom att använda något av följande:

* Azure Portal. Mer information finns i avsnittet [Create an Azure Cosmos DB collection using the Azure portal](documentdb-create-collection.md) (Skapa en Azure Cosmos DB-samling med hjälp av Azure Portal).
* Omfattande självstudier, inklusive exempeldata: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md), eller [Python](documentdb-python-application.md).
* Exempelkoden för [.NET](documentdb-dotnet-samples.md#database-examples), [Node.js](documentdb-nodejs-samples.md#database-examples) eller [Python](documentdb-python-samples.md#database-examples) som är tillgänglig i GitHub.
* SDK:er för [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md) och [REST](https://msdn.microsoft.com/library/azure/mt489072.aspx).

När du har skapat databasen och samlingen behöver du [lägga till dokument ](documentdb-view-json-document-explorer.md) i samlingarna.

När du har dokument i en samling kan du använda [DocumentDB SQL](documentdb-sql-query.md) för att [köra frågor](documentdb-sql-query.md#ExecutingSqlQueries) mot dokumenten. Du kan köra frågor genom att använda [Frågeutforskaren](documentdb-query-collections-query-explorer.md) i portalen, [REST API:t](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller en av [SDK:erna](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>Läs mer
Mer information om Azure Cosmos DB finns här:

* [Introduktion till Azure Cosmos DB](../cosmos-db/introduction.md)

