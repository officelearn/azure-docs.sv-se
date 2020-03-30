---
title: 'Snabbstart: Skapa en HoloLens-app med DirectX'
description: I den här snabbstarten får du lära dig hur du skapar en HoloLens-app med Spatial Anchors.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 01a9a8239e2997335f5123d63cfc664027cffa02
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376400"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Snabbstart: Skapa en HoloLens-app med Azure Spatial Anchors, i C++/WinRT och DirectX

I den här snabbstarten beskrivs hur du skapar en HoloLens-app med [Azure Spatial Anchors](../overview.md) i C++/WinRT och DirectX. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en HoloLens-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en HoloLens-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande så att du kan utföra den här snabbstarten:
- En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> installerad med **arbetsbelastningen för universell windows-plattformsutveckling** och **Komponenten Windows 10 SDK (10.0.18362.0 eller nyare).** Du måste också installera <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a> och <a href="https://git-lfs.github.com/">Git LFS</a>.
- [C++/WinRT Visual Studio-tillägget (VSIX)](https://aka.ms/cppwinrt/vsix) för Visual Studio bör installeras från [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- En HoloLens-enhet med [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat. Den här artikeln kräver en HoloLens-enhet med [Windows-uppdateringen från 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (kallas även RS5). Uppdatera till den senaste versionen på HoloLens genom att öppna appen **Inställningar**, gå till **Uppdatering och säkerhet** och välja **Sök efter uppdateringar**.
- Appen måste ange funktionen **spatialPerception** i dess AppX-manifest.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `HoloLens\DirectX\SampleHoloLens.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder kontoidentifieraren och kontonyckeln. Du kopierade dem till en textredigerare när [du konfigurerade resursen Spatial Anchors](#create-a-spatial-anchors-resource).

Öppna `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

## <a name="deploy-the-app-to-your-hololens"></a>Distribuera appen till din HoloLens

Ändra **Lösningskonfiguration** till **Version**, ändra **Lösningsplattform** till **x86** och välj **Enhet** bland alternativen för distributionsmål.

Om du använder HoloLens 2 använder du **ARM64** som **lösningsplattform**i stället för **x86**.

![Visual Studio-konfiguration](./media/get-started-hololens/visual-studio-configuration.png)

Slå på HoloLens-enheten, logga in och anslut den till datorn via en USB-kabel.

Välj **Felsökning** > **Starta felsökning** för att distribuera din app och börja felsöka.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen i Visual Studio genom att välja **Stoppa felsökning** eller trycka på **Skift+F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudiekurs: Dela rumsliga ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
