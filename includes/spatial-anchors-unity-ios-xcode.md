---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "72933480"
---
Välj **build**. I dialog rutan som öppnas väljer du en mapp att exportera Xcode-projektet till.

När exporten är klar visas en mapp som innehåller det exporterade Xcode-projektet.

> [!NOTE]
> Om ett fönster frågar dig om du vill ersätta eller lägga till visas, rekommenderar vi att du väljer **Lägg till** eftersom det är snabbare. Du behöver bara välja **Ersätt** om du byter till gångar i din scen. (Om du till exempel lägger till, tar bort eller ändrar överordnade/underordnade relationer eller om du lägger till, tar bort eller ändrar egenskaper.) Om du bara gör käll kods ändringar bör **tillägg** vara tillräckligt.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Konvertera Xcode-projektet till xcworkspace som innehåller referenser till Azure spatiala ankare

I den exporterade Xcode-projektmappen kör du det här kommandot i terminalen för att installera de nödvändiga CocoaPods för projektet:

```bash
pod install --repo-update
```

Nu kan du öppna `Unity-iPhone.xcworkspace` projektet i Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se fel söknings stegen [här](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) om du har CocoaPod problem efter uppgraderingen till MacOS Catalina (10,15).

Välj den rot **Uppunions-iPhone-** noden för att visa projekt inställningarna och välj sedan fliken **Allmänt** .

Under **signering**ser du till att **Hantera signering** är aktiverat automatiskt. Om det inte är det aktiverar du det och väljer sedan **Aktivera automatiskt** i dialog rutan som visas för att återställa build-inställningarna.

Under **Deployment Info** (Distributionsinformation) ser du till att **Deployment Target** (Distributionsmål) är angett till `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/spatial-anchors-unity/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Om du ser ett `library not found for -lPods-Unity-iPhone` fel har du förmodligen öppnat `.xcodeproj` filen i stället för `.xcworkspace` filen.
