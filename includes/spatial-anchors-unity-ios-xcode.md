---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "72933480"
---
Välj **Bygg**. I dialogrutan som öppnas väljer du en mapp som Xcode-projektet ska exporteras till.

När exporten är klar visas en mapp som innehåller det exporterade Xcode-projektet.

> [!NOTE]
> Om ett fönster som frågar dig om du vill ersätta eller lägga till visas rekommenderar vi att du väljer **Lägg till** eftersom det går snabbare. Du bör bara behöva välja **Ersätt** om du ändrar tillgångar i scenen. (Om du till exempel lägger till, tar bort eller ändrar överordnade/underordnade relationer, eller om du lägger till, tar bort eller ändrar egenskaper.) Om du bara gör källkodsändringar bör **Lägg till** vara tillräckligt.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Konvertera Xcode-projektet till xcworkspace som innehåller Azure Spatial Anchors-referenser

I den exporterade Xcode-projektmappen kör du det här kommandot i terminalen för att installera nödvändiga CocoaPods för projektet:

```bash
pod install --repo-update
```

Nu kan `Unity-iPhone.xcworkspace` du öppna för att öppna projektet i Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se felsökningsstegen [här](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) om du har Problem med CocoaPod efter uppgradering till macOS Catalina (10.15).

Välj roten **Unity-iPhone-nod** för att visa projektinställningarna och välj sedan fliken **Allmänt.**

Under **Signering**kontrollerar du att **automatisk hantering av signering** är aktiverat. Om den inte är det aktiverar du den och väljer sedan **Aktivera automatisk** i dialogrutan som visas för att återställa bygginställningarna.

Under **Deployment Info** (Distributionsinformation) ser du till att **Deployment Target** (Distributionsmål) är angett till `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/spatial-anchors-unity/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Om ett `library not found for -lPods-Unity-iPhone` fel visas har `.xcodeproj` du förmodligen `.xcworkspace` öppnat filen i stället för filen.
