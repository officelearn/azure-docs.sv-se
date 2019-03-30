---
title: Självstudie – dela Azure Spatial ankare flera sessioner och enheter | Microsoft Docs
description: 'I den här självstudien får du lära dig hur du delar Azure Spatial ankar-ID: n mellan Android/iOS-enheter i Unity med en backend-tjänst.'
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff9868dd7347812eb6ef566288ec364bc89b6955
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629312"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Självstudier: Dela Azure Spatial ankare flera sessioner och enheter

I den här självstudien får du lära dig hur du använder [Azure Spatial ankare](../overview.md) att skapa fästpunkter under en session och leta sedan upp dem, på samma enhet eller på ett annat. Dessa samma ankare kan också finnas av flera enheter på samma plats och på samma gång.

![Bevarande](./media/persistence.gif)

Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en app som kan distribueras till två eller fler enheter. Azure Spatial Anchors som skapats av en instans kan delas till de andra.

Du lär dig följande:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core-webbapp i Azure som kan användas för att dela fästpunkter, och lagra dem i minnet under en tid.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo-scenen i Unity-exemplet från våra snabbstarter för att dra nytta av webbappen för delningsfästpunkter.
> * Distribuera och köra på en eller flera enheter.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Det är värt att nämna att även om du använder Unity och en ASP.NET Core-webbapp i den här självstudien, är det bara för att visa ett exempel på hur du delar Azure Spatial Anchors-ID mellan andra enheter. Du kan använda andra språk och backend-tekniker för att uppnå samma resultat. Den ASP.NET Core-webbapp som används i den här självstudien har dessutom ett beroende för .NET Core 2.2 SDK. Den fungerar bra på vanliga Azure Web Apps (för Windows), men fungerar för närvarande inte på Azure Web Apps för Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Öppna exempelprojektet i Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Distribuera tjänsten för delningsfästpunkter

Öppna Visual Studio och öppna projektet i mappen `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en ASP.NET Core-webbapp i Azure, och sedan konfigurerat och distribuerat en Unity-app. Du har skapat spatiala fästpunkter med appen och delat dem med andra enheter med hjälp av ASP.NET Core-webbappen.

Om du vill veta mer om hur du kan förbättra din ASP.NET Core-Webbapp så att den använder Azure Cosmos DB för att lagra dina delade Spatial ankar-ID: n kan du fortsätta till nästa självstudie. Azure Cosmos DB ger persistence ASP.NET Core-Webbappen. Detta så kan din app för att skapa en fästpunkt i dag och gå tillbaka dagar senare för att kunna hitta den igen, med hjälp av ankar-ID lagras i din webbapp.

> [!div class="nextstepaction"]
> [Självstudier: Använda Azure Cosmos DB till Store fästpunkter](./tutorial-use-cosmos-db-to-store-anchors.md)
