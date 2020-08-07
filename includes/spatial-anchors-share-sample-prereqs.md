---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 07/31/2020
ms.author: rgarcia
ms.openlocfilehash: 310c0f547ee11a3243589c364755a30a84be1a25
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810196"
---
## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

* Läs igenom [översikten över Azures spatiala ankare](../articles/spatial-anchors/overview.md).
* Slutfört ett av [snabb starterna på fem minuter](../articles/spatial-anchors/index.yml).
* Grundläggande kunskaper om C# och Unity.
* Grundläggande kunskaper om <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> om du vill använda Android eller <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> om du vill använda iOS.
* En Windows-dator där <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> eller senare installeras med arbets belastningen **ASP.net och webb utveckling** .
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download).
* En eller flera enheter (iOS eller Android) som du använder för att distribuera och köra en app.
  * Om du använder Android måste du ha:
    * <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3,3</a> eller senare, <a href="https://unity3d.com/get-unity/download" target="_blank">Unit 2019,4 (LTS)</a>och <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> installerat på Windows-datorn.
    * En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklare som är aktive rad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kompatibel Android-</a> enhet.
  * Om du använder iOS måste du ha:
    * En macOS-dator där <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> eller senare, <a href="https://cocoapods.org" target="_blank">CocoaPods</a>och <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,4 (LTS)</a> installeras.
    * En <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel iOS-</a> enhet som är kompatibel med en utvecklare.
    * Git installerat via homebrew. Ange följande kommando på en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` kör sedan `brew install git` .
