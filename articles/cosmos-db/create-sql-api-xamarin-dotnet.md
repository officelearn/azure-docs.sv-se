---
title: 'Azure Cosmos DB: Skapa en webbapp med Xamarin- och Facebook-autentisering | Microsoft Docs'
description: Anger ett .NET kodexempel som du kan använda för att ansluta till och fråga en Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: mimig
ms.openlocfilehash: 593c55951479a3cdebfe8bdc08ca0443738269ef
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB: Skapa en webbapp med Xamarin- och Facebook-autentisering

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

> [!NOTE]
> Exempelkod för ett helt kanonisk Xamarin-exempelapp som visar flera Azure-erbjudanden, däribland CosmosDB, finns på GitHub [här](https://github.com/xamarinhq/app-geocontacts). Den här appen demonstrerar visning av geografiskt spridda kontakter, och gör att dessa kontakter kan uppdatera sin plats.

Den här snabbstarten demonstrerar hur man skapar ett Azure Cosmos DB-konto, en dokumentdatabas och en samling med hjälp av Azure-portalen. Sedan bygger du och distribuerar en webbapp (att göra-lista) som är byggd på [SQL .NET API-](sql-api-sdk-dotnet.md), [Xamarin-](https://www.xamarin.com/) och Azure Cosmos DB-auktoriseringsmotorn. Webbappen för att göra-listan implementerar ett datamönster per användare som gör att användare kan logga in med Facebook-auktorisering och hantera sina egna att göra-objekt.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Lägga till en samling

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en SQL API-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. Öppna sedan DocumentDBTodo.sln-filen från mappen samples/xamarin/UserItems/xamarin.forms i Visual Studio.

## <a name="review-the-code"></a>Granska koden

Koden i Xamarin-mappen innehåller:

* Xamarin-app. Appen lagrar användarens att göra-objekt i en partitionerad samling som heter UserItems.
* API för resurstokenutjämning. En enkel ASP.NET-webb-API för att mäkla Azure Cosmos DB-resurstokens till appens inloggade användare. Resurstokens är kortlivade åtkomsttokens som ger appen åtkomst till den inloggade användarens data.

Auktoriseringen och dataflödet visas i följande diagram.

* UserItems-samlingen skapas med partitionsnyckeln ”/userid”. Om du anger en partitionsnyckel för en samling kan Azure Cosmos DB skala oändligt när antalet användare och objekt växer.
* Med appen Xamarin kan användare logga in med Facebook-auktoriseringsuppgifter.
* Xamarin-appen använder Facebook-åtkomsttoken för att autentisera med ResourceTokenBroker API
* API:n för resurstokenutjämningen autentiserar begäran med funktionen för App Service-autentisering och begär en Azure Cosmos DB-resurstoken med läs-/skrivåtkomst till alla dokument som delar den autentiserade användarens partitionsnyckel.
* Resurstokenutjämningen returnerar resurstoken till klientappen.
* Appen får åtkomst till användarens att göra-objekt med hjälp av resurstoken.

![Att göra-app med exempeldata](./media/create-sql-api-xamarin-dotnet/tokenbroker.png)

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](http://portal.azure.com/) går du till ditt Azure Cosmos DB-konto. Klicka på **Nycklar** i det västra navigeringsfönstret och sedan på **läs- och skrivnycklar**. Du använder kopiera-knapparna till höger på skärmen för att kopiera URI:n och primärnyckeln i Web.config-filen i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-sql-api-xamarin-dotnet/keys.png)

2. I Visual Studio 2017 öppnar du Web.config-filen i mappen azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker. 

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för accountUrl i Web.config. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. Kopiera sedan värdet för primärnyckeln från portalen och gör det till värdet för accountKey i Web.config.

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

## <a name="build-and-deploy-the-web-app"></a>Skapa och distribuera webbappen

1. I Azure Portal skapar du en App Service-webbplats som är värd för API:n för resurstokenutjämningen.
2. I Azure Portal öppnar du bladet Appinställningar på webbplatsen för API för resurstokenutjämning. Fyll i följande inställningar för appen:

    * accountUrl – URL:en för Azure Cosmos DB-kontot från fliken Nycklar i ditt Azure Cosmos DB-konto.
    * accountKey – Huvudnyckeln för Azure Cosmos DB-kontot från fliken Nycklar i ditt Azure Cosmos DB-konto.
    * databaseId och collectionId för din skapade databas och samling

3. Publicera ResourceTokenBroker-lösningen på webbplatsen du har skapat.

4. Öppna Xamarin-projektet och navigera till TodoItemManager.cs. Fyll i värdena för accountURL, collectionId, databaseId och resourceTokenBrokerURL som grund-https-url för webbplatsen för resurstokenutjämningen.

5. Slutför självstudien [Så här konfigurerar du App Service-programmet för att använda Facebook-inloggning](../app-service/app-service-mobile-how-to-configure-facebook-authentication.md) för att konfigurera Facebook-autentisering och konfigurera ResourceTokenBroker-webbplatsen.

    Kör Xamarin-appen.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade.
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto, skapar en samling med datautforskaren och skapar och distribuerar en Xamarin-app. Du kan nu importera ytterligare data till ditt Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
