<properties
    pageTitle="Så här skapar du ett DocumentDB-konto | Microsoft Azure"
    description="Bygg en NoSQL-databas med Azure DocumentDB. Skapa ett DocumentDB-konto genom att följa instruktionerna och börja utveckla din blixtsnabba, globala NoSQL-databas." 
    keywords="build a database"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="mimig"/>

# Så här skapar du ett DocumentDB-konto med Azure Portal

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI och ARM](documentdb-automation-resource-manager-cli.md)

Om du vill skapa en databas med Microsoft Azure DocumentDB måste du:

- Ha ett Azure-konto. Om du inte redan har ett kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free). 
- Skapa ett DocumentDB-konto.  

Du kan skapa ett DocumentDB-konto via Azure Portal, Azure Resource Manager-mallar eller kommandoradsgränssnittet (CLI) i Azure. I den här artikeln beskriver vi hur du skapar ett konto i Azure Portal. Om du vill skapa ett konto med Azure Resource Manager eller Azure CLI finns det mer information i [Skapa DocumentDB-databaskonton automatiskt](documentdb-automation-resource-manager-cli.md).

Har du inte använt DocumentDB förut? Titta på [den här](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) fyra minuter långa videon av Scott Hanselman, så får du se hur du utför de vanligaste uppgifterna i onlineportalen.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Nästa steg

Nu när du har ett DocumentDB-konto är nästa steg att bygga DocumentDB-databasen. Du kan skapa en databas med hjälp av något av följande:

- Azure Portal, enligt beskrivningen i [Skapa en DocumentDB-databas med Azure Portal](documentdb-create-database.md).
- C# .NET-exemplen i projektet [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) i centrallagret [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) på GitHub.
- Heltäckande självstudiekurser: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) eller [Python](documentdb-python-application.md).
- [DocumentDB SDK:er](documentdb-sdk-dotnet.md). DocumentDB har SDK:er för .NET, Java, Python, Node.js och JavaScript API.


När du har skapat databasen behöver du [lägga till en eller flera samlingar](documentdb-create-collection.md) i databasen och sedan [lägga till dokument](documentdb-view-json-document-explorer.md) i samlingarna.

När du har dokument i en samling kan du använda [DocumentDB SQL](documentdb-sql-query.md) för att [köra frågor](documentdb-sql-query.md#executing-queries) mot dina dokument genom att använda [Frågeutforskaren](documentdb-query-collections-query-explorer.md) i portalen, [REST-API:et](https://msdn.microsoft.com/library/azure/dn781481.aspx) eller ett av [SDK:erna](documentdb-sdk-dotnet.md).

Mer information om DocumentDB finns i följande resurser:

-   [Utbildningsväg för DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [Resursmodell och begrepp i DocumentDB](documentdb-resources.md)



<!--HONumber=Jun16_HO2-->


