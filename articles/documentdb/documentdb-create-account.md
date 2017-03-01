---
title: "Så här skapar du ett DocumentDB-konto | Microsoft Docs"
description: "Bygg en NoSQL-databas med Azure DocumentDB. Skapa ett DocumentDB-konto genom att följa instruktionerna och börja utveckla din blixtsnabba, globala NoSQL-databas."
keywords: skapa en databas
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 0e7f8488-7bb7-463e-b6fd-3ae91a02c03a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/17/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 655f501f920e3169450831f501f7183ae46a4a60
ms.openlocfilehash: 086c87c52adf972307003ff78fd0c07f3035321c
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-create-a-documentdb-nosql-account-using-the-azure-portal"></a>Så här skapar du ett DocumentDB NoSQL-konto med Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

Om du vill skapa en databas med Microsoft Azure DocumentDB måste du:

* Ha ett Azure-konto. Om du inte redan har ett kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free).
* Skapa ett DocumentDB-konto.  

Du kan skapa ett DocumentDB-konto via Azure Portal, Azure Resource Manager-mallar eller kommandoradsgränssnittet (CLI) i Azure. I den här artikeln beskrivs hur man skapar ett DocumentDB-konto i Azure Portal. Om du vill skapa ett konto med Azure Resource Manager eller Azure CLI finns det mer information i [Skapa DocumentDB-databaskonton automatiskt](documentdb-automation-resource-manager-cli.md).

Har du inte använt DocumentDB förut? Titta på [den här](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) fyra minuter långa videon av Scott Hanselman, så får du se hur du utför de vanligaste uppgifterna i onlineportalen.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I det vänstra navigeringsfönstret klickar du på **Ny**, **Databaser** och sedan på **NoSQL (DocumentDB)**.

   ![Skärmbild av Azure-portalen, med fokus på Fler tjänster och NoSQL (DocumentDB).](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  
3. På **Nytt konto**-bladet anger du önskad konfiguration för DocumentDB-kontot.

    ![Skärmbild av Nytt DocumentDB-bladet](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

   * I **ID**-rutan, anger du ett namn för att identifiera DocumentDB-kontot.  När **ID**:n har verifierats, visas en grön kryssmarkering i **ID**-rutan. **ID**-värdet blir värdnamnet inom URI:n. **ID**:n får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken. Observera att *documents.azure.com* läggs till i slutpunktsnamnet du väljer, och resultatet blir slutpunkten för ditt DocumentDB-konto.
   * I rutan **NoSQL API** markerar du den programmeringsmodell som ska användas:

     * **DocumentDB**: API:t för DocumentDB är tillgängligt via [SDK:er](documentdb-sdk-dotnet.md) för .NET, Java, Node.js, Python och JavaScript, samt HTTP [REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), och omfattar programmeringsåtkomst till alla DocumentDB-funktioner.
     * **MongoDB**: DocumentDB omfattar också [support på protokollnivå](documentdb-protocol-mongodb.md) för API:er för **MongoDB**. När du väljer MongoDB API-alternativet kan du använda befintliga SDK:er och [verktyg](documentdb-mongodb-mongochef.md) för MongoDB för att kommunicera med DocumentDB. Du kan [flytta](documentdb-import-data.md) dina befintliga MongoDB-appar för att använda DocumentDB [utan att kodändringar krävs](documentdb-connect-mongodb-account.md), och utnyttja en fullständigt hanterad databas som en tjänst, med obegränsad skala, global replikering och andra funktioner.
   * För **Prenumeration** väljer du den Azure-prenumeration som du vill ange för DocumentDB-kontot. Om ditt konto bara har en prenumeration så väljs det kontot som standard.
   * I **Resursgrupp** väljer eller skapar du en resursgrupp för ditt DocumentDB-konto.  Som standard skapas en ny resursgrupp. Mer information finns i [Using the Azure portal to manage your Azure resources](../azure-portal/resource-group-portal.md) (Hantera Azure-resurser med hjälp av Azure Portal).
   * Använd **Plats** för att ange den geografiska platsen där du vill att DocumentDB-kontots värd ska finnas.
4. När de nya DocumentDB-kontoalternativen har konfigurerats, klickar du på **Skapa**. Kontrollera meddelandehubben för att se statusen för distributionen.  

   ![Skapa databaser snabbt – Skärmbild av Meddelandehubben som visar att DocumentDB-kontot håller på att skapas](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

   ![Skärmbild av Meddelandehubben som visar att DocumentDB-kontot har skapats och distribuerats till en resursgrupp – Meddelande från online-databasskaparen](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)
5. När DocumentDB-kontot har skapats är det redo att användas med standardinställningarna. Standardkonsekvensen för DocumentDB-kontot har angetts till **Session**.  Du kan justera standardkonsekvensen genom att klicka på **Standardkonsekvens** på resursmenyn. Mer information om de konsekvensnivåer som finns för DocumentDB finns i [Konsekvensnivåer i DocumentDB](documentdb-consistency-levels.md).

   ![Skärmbild av Resursgrupp-bladet – starta apputveckling](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

   ![Skärmbild av Konsekvensnivå-bladet – sessionskonsekvens](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps


## <a name="next-steps"></a>Nästa steg
Nu när du har ett DocumentDB-konto är nästa steg att skapa en DocumentDB-samling och -databas.

Du kan skapa en ny samling och databas genom att använda något av följande:

* Azure Portal, enligt beskrivningen i [Skapa en DocumentDB-samling med Azure Portal](documentdb-create-collection.md).
* Omfattande självstudier, inklusive exempeldata: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md), eller [Python](documentdb-python-application.md).
* Exempelkoden för [.NET](documentdb-dotnet-samples.md#database-examples), [Node.js](documentdb-nodejs-samples.md#database-examples) eller [Python](documentdb-python-samples.md#database-examples) som är tillgänglig i GitHub.
* SDK:er för [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md) och [REST](https://msdn.microsoft.com/library/azure/mt489072.aspx).

När du har skapat databasen och samlingen behöver du [lägga till dokument ](documentdb-view-json-document-explorer.md) i samlingarna.

När du har dokument i en samling kan du använda [DocumentDB SQL](documentdb-sql-query.md) för att [köra frågor](documentdb-sql-query.md#ExecutingSqlQueries) mot dokumenten. Du kan köra frågor genom att använda [Frågeutforskaren](documentdb-query-collections-query-explorer.md) i portalen, [REST API:t](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller en av [SDK:erna](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>Läs mer
Mer information om DocumentDB finns i följande resurser:

* [Utbildningsväg för DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Hierarkisk resursmodell och begrepp i DocumentDB](documentdb-resources.md)

