---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0e81a9b6a91d2a9ed1af922dbbc53720022ff598
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657882"
---
## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

* Läs igenom [översikten över Azures spatiala ankare](../articles/spatial-anchors/overview.md).
* Slutfört ett av [snabb starterna på fem minuter](../articles/spatial-anchors/index.yml).
* Grundläggande kunskaper om C# och behållning.
* Grundläggande kunskaper om <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> om du vill använda Android eller <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> om du vill använda iOS.
* En Windows-dator där <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> eller senare installeras med arbets belastningen **ASP.net och webb utveckling** .
* [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download).
* En eller flera enheter (iOS eller Android) som du använder för att distribuera och köra en app.
  * Om du använder Android måste du ha:
    * <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3,3</a> eller senare, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,1</a> eller senare och <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> installerat på Windows-datorn.
    * En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklare som är aktive rad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kompatibel Android-</a> enhet.
  * Om du använder iOS måste du ha:
    * En macOS-dator där <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> eller senare, <a href="https://cocoapods.org" target="_blank">CocoaPods</a>och <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,1</a> eller senare är installerade.
    * En <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel iOS-</a> enhet som är kompatibel med en utvecklare.
    * Git installerat via homebrew. Ange följande kommando på en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`Kör `brew install git`sedan.


