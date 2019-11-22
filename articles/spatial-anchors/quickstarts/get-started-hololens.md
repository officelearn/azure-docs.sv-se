---
title: 'Snabb start: skapa en HoloLens-app med DirectX'
description: I den här snabbstarten får du lära dig hur du skapar en HoloLens-app med Spatial Anchors.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3814a12657b2634ede02eda83c7e7bcdf78cd7e2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277101"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Snabb start: skapa en HoloLens-app med Azures spatiala C++ankare, i/WinRT och DirectX

I den här snabbstarten beskrivs hur du skapar en HoloLens-app med [Azure Spatial Anchors](../overview.md) i C++/WinRT och DirectX. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en HoloLens-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Distribuera och köra på en HoloLens-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande för genomföra den här snabbstarten:
- En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> installerad med arbets belastningen **universell Windows-plattform utveckling** och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)** och <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a>.
- [C++/WinRT Visual Studio-tillägget (VSIX)](https://aka.ms/cppwinrt/vsix) för Visual Studio bör installeras från [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- En HoloLens-enhet med [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat. Den här artikeln kräver en HoloLens-enhet med [Windows-uppdateringen från 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (kallas även RS5). Uppdatera till den senaste versionen på HoloLens genom att öppna appen **Inställningar**, gå till **Uppdatering och säkerhet** och välja **Sök efter uppdateringar**.
- Appen måste ange funktionen **spatialPerception** i dess AppX-manifest.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Öppna `HoloLens\DirectX\SampleHoloLens.sln` i Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurera konto-ID och -nyckel

Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

Öppna `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med konto-ID.

## <a name="deploy-the-app-to-your-hololens"></a>Distribuera appen till din HoloLens

Ändra **Lösningskonfiguration** till **Version**, ändra **Lösningsplattform** till **x86** och välj **Enhet** bland alternativen för distributionsmål.

Om du använder HoloLens 2 använder du **arm64** som **lösnings plattform**i stället för **x86**.

![Visual Studio-konfiguration](./media/get-started-hololens/visual-studio-configuration.png)

Slå på HoloLens-enheten, logga in och anslut den till datorn via en USB-kabel.

Välj **Felsök** > **Starta felsökning** för att distribuera appen och börja felsöka.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen i Visual Studio genom att välja **Stoppa felsökning** eller trycka på **Skift+F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)
