---
title: Självstudie – Dela mellan sessioner och enheter med Azure Spatial Anchors och en Azure Cosmos DB-serverdel | Microsoft Docs
description: 'I den här självstudien får du lära dig hur du delar Azure Spatial ankar-ID: n mellan Android/iOS-enheter i Unity med en backend-tjänst och Azure Cosmos DB.'
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: b585b13f40be447a5c5a4b348efc28bf5171e210
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863193"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Självstudier: Dela mellan sessioner och enheter med Azure Spatial Anchors och en Azure Cosmos DB-serverdel

Den här självstudien visar hur du använder [Azure Spatial Anchors](../overview.md) för att:

1. Skapa fästpunkter i en session och sedan leta upp dem i en annan session på samma eller en annan enhet. Det kan till exempel vara en annan dag.
2. Skapa fästpunkter som kan hittas av flera enheter på samma plats samtidigt.

![Bevarande](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) är en plattformsoberoende utvecklartjänst som du kan använda för att skapa Mixed Reality-upplevelser med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en app som kan distribueras till två eller fler enheter. Azure Spatial Anchors som skapas av en instans delar sina identifierare med de andra som använder Cosmos DB.

Du lär dig följande:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core-webbapp i Azure som kan användas för att dela fästpunkter, och lagra dem i Cosmos DB.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo-scenen i Unity-exemplet från våra snabbstarter för att dra nytta av webbappen för delningsfästpunkter.
> * Distribuera och köra på en eller flera enheter.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Det är värt att nämna att även om du använder Unity och Azure Cosmos DB i den här självstudien så är det bara för att visa ett exempel på hur du delar Azure Spatial Anchors-identifierare mellan andra enheter. Du kan använda andra språk och backend-tekniker för att uppnå samma resultat. Den ASP.NET Core-webbapp som används i den här självstudien har dessutom ett beroende för .NET Core 2.2 SDK. Den fungerar bra på vanliga Azure Web Apps (för Windows), men fungerar för närvarande inte på Azure Web Apps för Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Anteckna `Connection String`, eftersom den används senare.

## <a name="open-the-sample-project-in-unity"></a>Öppna exempelprojektet i Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Distribuera tjänsten för delningsfästpunkter

Öppna Visual Studio och öppna projektet i mappen `Sharing\SharingServiceSample`.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Konfigurera tjänsten så att den använder din Cosmos DB

I **Solution Explorer** öppnar du `SharingService\Startup.cs`.

Leta upp raden `#define INMEMORY_DEMO` överst i filen och kommentera ut den. Spara filen.

I **Solution Explorer** öppnar du `SharingService\appsettings.json`.

Leta upp egenskapen `StorageConnectionString` och ange värdet till den `Connection String` som du antecknade i [steget för att skapa ett databaskonto](#create-a-database-account). Spara filen.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Om du vill veta mer om Azure Spatial Anchors-biblioteket fortsätter du till vår guide om hur du skapar och hittar fästpunkter.

> [!div class="nextstepaction"]
> [Skapa och hitta fästpunkter med hjälp av Azure Spatial Anchors](../create-locate-anchors-overview.md)
