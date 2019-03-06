---
title: Självstudie – Dela mellan sessioner och enheter med Azure Spatial Anchors | Microsoft Docs
description: I den här självstudien får du lära dig hur du delar Azure Spatial Anchors-ID mellan enheter i Unity med en serverdelstjänst.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: df7f8aa5b49e3fe17be3b17a6e0f5d8861b26253
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753512"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Självstudier: Dela mellan sessioner och enheter med Azure Spatial Anchors

Den här självstudien visar hur du använder [Azure Spatial Anchors](../overview.md) för att:

1. Skapa fästpunkter i en session och sedan leta upp dem i en annan session på samma eller en annan enhet. Till exempel en annan dag.
2. Skapa fästpunkter som kan hittas av flera enheter på samma plats samtidigt.

![Bevarande](./media/persistence.gif)

Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en app som kan distribueras till två eller fler enheter. Azure Spatial Anchors som skapats av en instans kan delas till de andra.

Du lär dig följande:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core-webbapp i Azure som kan användas för att dela fästpunkter, och lagra dem i minnet under en tid.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo-scenen i Unity-exemplet från våra snabbstarter för att dra nytta av webbappen för delningsfästpunkter.
> * Distribuera och köra på en eller flera enheter.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Det är värt att nämna att även om du använder Unity och en ASP.NET Core-webbapp i den här självstudien, är det bara för att visa ett exempel på hur du delar Azure Spatial Anchors-ID mellan andra enheter. Du kan använda andra språk och serverdelstekniker för att uppnå samma resultat. ASP.NET Core-webbappen som används i den här självstudien är dessutom beroende av .NET Core 2.2 SDK. Den fungerar bra på vanliga Azure Web Apps (för Windows), men fungerar för närvarande inte på Azure Web Apps för Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Distribuera tjänsten för delningsfästpunkter

Öppna Visual Studio och öppna projektet i mappen `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en ASP.NET Core-webbapp i Azure, och sedan konfigurerat och distribuerat en Unity-app. Du har skapat spatiala fästpunkter med appen och delat dem med andra enheter med hjälp av ASP.NET Core-webbappen. Om du vill veta mer om hur du kan förbättra ASP.NET Core-webbappen så att den använder Cosmos DB för att lagra de delade spatiala fästpunkterna fortsätter du till nästa självstudie.

> [!div class="nextstepaction"]
> [Självstudier: Använda Cosmos DB för att lagra fästpunkter](./tutorial-use-cosmos-db-to-store-anchors.md)
