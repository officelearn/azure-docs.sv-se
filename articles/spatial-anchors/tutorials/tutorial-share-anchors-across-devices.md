---
title: 'Självstudiekurs: Dela fästpunkter mellan sessioner och enheter'
description: I den här självstudien får du lära dig hur du delar Azure Spatial Anchor-identifierare mellan Android/iOS-enheter i Unity med en backend-tjänst.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615470"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Självstudiekurs: Dela Azure Spatial Anchors över sessioner och enheter

I den här självstudien får du lära dig hur du använder [Azure Spatial Anchors](../overview.md) för att skapa ankare under en session och sedan hitta dem, på samma enhet eller på en annan. Samma ankare kan också placeras av flera enheter på samma plats och samtidigt.

![Bevarande](./media/persistence.gif)

Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en app som kan distribueras till två eller fler enheter. Azure Spatial Anchors som skapats av en instans kan delas till de andra.

Du lär dig följande:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core-webbapp i Azure som kan användas för att dela fästpunkter, och lagra dem i minnet under en tid.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo-scenen i Unity-exemplet från våra snabbstarter för att dra nytta av webbappen för delningsfästpunkter.
> * Distribuera och köra på en eller flera enheter.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Det är värt att nämna att även om du använder Unity och en ASP.NET Core-webbapp i den här självstudien, är det bara för att visa ett exempel på hur du delar Azure Spatial Anchors-ID mellan andra enheter. Du kan använda andra språk och backend-tekniker för att uppnå samma mål. Den ASP.NET Core-webbapp som används i den här självstudien har dessutom ett beroende för .NET Core 2.2 SDK. Den fungerar bra på vanliga Azure Web Apps (för Windows), men fungerar för närvarande inte på Azure Web Apps för Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Ladda ner exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Distribuera tjänsten för delningsfästpunkter

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Öppna Visual Studio och öppna projektet i mappen `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio-kod](#tab/VSC)

Du måste skapa en resursgrupp och en appserviceplan innan du distribuerar tjänsten i VS-kod.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Navigera till <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a> och logga in på din Azure-prenumeration.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Ge resursgruppen namnet **myResourceGroup** och välj **OK**.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Bredvid **Värdplan** väljer du **Ny**.

Använd följande inställningar i dialogrutan **Konfigurera värdplan:**

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service-planen. |
| Location | USA, västra | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) som bestämmer värdfunktioner. |

Välj **OK**.

Öppna Visual Studio-kod och öppna `Sharing\SharingServiceSample` projektet i mappen. Följ <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">den här självstudien</a> om du vill distribuera delningstjänsten via Visual Studio Code. Du kan följa stegen från avsnittet "Öppna den med Visual Studio-kod". Skapa inte ett annat mvc-projekt som förklaras i steget ovan eftersom du redan har projektet som behöver distribueras och publiceras- SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Distribuera exempelappen

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="unity-20193"></a>Enhet 2019.3

På grund av att förändringar bryts stöds för närvarande inte Unity 2019.3. Använd Unity 2019.1 eller 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en ASP.NET Core-webbapp i Azure, och sedan konfigurerat och distribuerat en Unity-app. Du har skapat spatiala fästpunkter med appen och delat dem med andra enheter med hjälp av ASP.NET Core-webbappen.

Du kan förbättra din ASP.NET Core Web App så att den använder Azure Cosmos DB för att spara lagringen av dina delade Spatial Anchor-identifierare. Genom att lägga till Azure Cosmos DB-stöd kan din ASP.NET Core Web App skapa ett ankare idag och komma tillbaka några dagar senare för att kunna hitta det igen med hjälp av ankaridentifieraren som lagras i webbappen.

> [!div class="nextstepaction"]
> [Använda Azure Cosmo DB för att lagra fästpunkter](./tutorial-use-cosmos-db-to-store-anchors.md)

