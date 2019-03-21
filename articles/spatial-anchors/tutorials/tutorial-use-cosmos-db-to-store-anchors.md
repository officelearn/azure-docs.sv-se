---
title: Självstudie – delning över sessioner och enheter med Azure Spatial ankare och en serverdel för Azure Cosmos DB | Microsoft Docs
description: I den här självstudien får lära du att dela Azure Spatial ankare identifierare i Android/iOS-enheter i Unity med en backend tjänst och Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286405"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Självstudier: Dela mellan sessioner och enheter med Azure Spatial ankare och en serverdel för Azure Cosmos DB

I den här självstudien får du lära dig hur du använder [Azure Spatial ankare](../overview.md) till:

- Skapa fästpunkter under en session och leta sedan upp dem i en annan session, på samma enhet eller på ett annat. Den andra sessionen kan exempelvis vara en annan dag.
- Skapa fästpunkter som är tillgängliga för flera enheter på samma plats och på samma gång.

![GIF som illustrerar objektet persistence](./media/persistence.gif)

[Azure Spatial ankare](../overview.md) är en plattformsoberoende developer-tjänst som du kan använda för att skapa Mixad verklighet upplevelser med objekt som kvarstår befann i enheter över tid. När du är klar har du en app som kan distribueras till två eller fler enheter. Spatial ankare som skapats av en instans dela sina identifierare med andra med hjälp av Azure Cosmos DB.

Du lär dig följande:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core-webbapp i Azure som kan användas för att dela ankare, lagra dem i Azure Cosmos DB.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo scenen i Unity-exempel från Azure snabbstarter för att dra nytta av delning ankare Web App.
> * Distribuera en app till en eller flera enheter och kör den.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Det är värt att, även om du ska använda Unity och Azure Cosmos DB i den här självstudien, det är bara för att ge dig ett exempel på hur du delar Spatial ankare identifierare på enheter. Du kan använda andra språk och serverdelstekniker för att uppnå samma resultat. ASP.NET Core-webbapp som används i den här självstudien kräver också .NET Core 2.2 SDK. Den fungerar på rätt sätt på Web Apps för Windows, men det för närvarande körs inte i Web Apps för Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopiera den `Connection String` eftersom du behöver den.

## <a name="open-the-sample-project-in-unity"></a>Öppna exempelprojektet i Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Distribuera delningsapplikationen fästpunkter tjänsten

Öppna Visual Studio och öppna projektet i den `Sharing\SharingServiceSample` mapp.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Konfigurera tjänsten så att din Azure Cosmos DB-databas

I **Solution Explorer**öppnar `SharingService\Startup.cs`.

Leta upp `#define INMEMORY_DEMO` överst i filen och kommentera som linje ut. Spara filen.

I **Solution Explorer**öppnar `SharingService\appsettings.json`.

Leta upp den `StorageConnectionString` egenskap och ange det värde som ska vara samma som den `Connection String` värde som du kopierade i den [skapa en databas konto steg](#create-a-database-account). Spara filen.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Om du vill veta mer om Azure Spatial Anchors-biblioteket fortsätter du till vår guide om hur du skapar och hittar fästpunkter.

> [!div class="nextstepaction"]
> [Skapa och hitta fästpunkter med hjälp av Azure Spatial Anchors](../create-locate-anchors-overview.md)
