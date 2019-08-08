---
title: Snabb start – skapa en enhets-Android-app med moln rums ankare | Microsoft Docs
description: I den här snabbstarten lär du dig att skapa en Android-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ba50b8317e85d09aeaf32c1acc62342ee2683bc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847716"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en enhets-Android-app med avstånds ankare för Azure

Den här snabb starten beskriver hur du skapar en Unity Android-app med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Exportera Android Studio-projektet
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows-eller macOS-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 +</a> inklusive stöd för Android-build och android SDK & NDK tools.
  - Om du kör på Windows, behöver du även <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a>.
  - Om du kör på macOS ska du Hämta git installerat via HomeBrew. Ange följande kommando i en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör `brew install git`sedan.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare enhets driv rutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer information och anvisningar finns [här](https://developer.android.com/studio/run/device.html) .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempel projektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchors.Examples/Scenes` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="export-the-android-studio-project"></a>Exportera Android Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj enheten i **Kör enhet** och klicka på **skapa och kör**. Du uppmanas att spara en `.apk` fil som du kan välja namn på.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

## <a name="troubleshooting"></a>Felsökning

Om du inte ser kameran som bakgrund när du kör appen (om du till exempel ser en tom, blå eller annan textur) behöver du förmodligen återimportera tillgångarna i Unity. Stoppa appen. Från den översta menyn i Unity väljer du **Assets -> Reimport all** (Tillgångar -> Återimportera alla). Kör sedan appen igen.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudier: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
