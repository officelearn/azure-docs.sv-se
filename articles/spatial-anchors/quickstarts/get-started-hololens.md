---
title: 'Snabb start: skapa en HoloLens-app med DirectX'
description: I den här snabbstarten får du lära dig hur du skapar en HoloLens-app med Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c96c45869ee1c9c96cd77d0b3eb10c733199666e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95993524"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Snabb start: skapa en HoloLens-app med Azures spatiala ankare, i C++/WinRT och DirectX

I den här snabbstarten beskrivs hur du skapar en HoloLens-app med [Azure Spatial Anchors](../overview.md) i C++/WinRT och DirectX. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en HoloLens-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en HoloLens-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:
- En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> installerad med arbets belastningen **universell Windows-plattform utveckling** och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)** . Du måste också installera <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> och <a href="https://git-lfs.github.com/">git LF</a>.
- [C++/WinRT Visual Studio-tillägget (VSIX)](https://aka.ms/cppwinrt/vsix) för Visual Studio bör installeras från [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- En HoloLens-enhet med [utvecklarläge](/windows/mixed-reality/using-visual-studio) aktiverat. Den här artikeln kräver en HoloLens-enhet med [Windows 10 maj 2020 Update](/windows/mixed-reality/whats-new/release-notes-may-2020). Uppdatera till den senaste versionen på HoloLens genom att öppna appen **Inställningar**, gå till **Uppdatering och säkerhet** och välja **Sök efter uppdateringar**.
- Appen måste ange funktionen **spatialPerception** i dess AppX-manifest.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `HoloLens\DirectX\SampleHoloLens.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

Öppna `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

Leta upp `SpatialAnchorsAccountDomain` fältet och Ersätt `Set me` med konto domänen.

## <a name="deploy-the-app-to-your-hololens"></a>Distribuera appen till din HoloLens

Ändra **Lösningskonfiguration** till **Version**, ändra **Lösningsplattform** till **x86** och välj **Enhet** bland alternativen för distributionsmål.

Om du använder HoloLens 2 använder du **arm64** som **lösnings plattform** i stället för **x86**.

![Visual Studio-konfiguration](./media/get-started-hololens/visual-studio-configuration.png)

Slå på HoloLens-enheten, logga in och anslut den till datorn via en USB-kabel.

Välj **Felsök**  >  **Starta fel sökning** för att distribuera din app och starta fel sökning.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen i Visual Studio genom att välja **Stoppa felsökning** eller trycka på **Skift+F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)