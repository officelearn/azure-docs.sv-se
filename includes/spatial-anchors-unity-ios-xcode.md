---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890932"
---
Välj **Build** (Skapa) för att öppna en dialogruta. Välj sedan en mapp för att exportera Xcode-projektet.

När exporten är klar visas en mapp som innehåller det exporterade Xcode-projektet.

### <a name="open-the-xcode-project"></a>Öppna Xcode-projektet

I mappen för det exporterade Xcode-projektet kör du följande kommando för att installera nödvändiga CocoaPods för projektet:

```bash
pod install --repo-update
```

Nu kan du öppna `Unity-iPhone.xcworkspace` för att öppna projektet i Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Om du ser felet `library not found for -lPods-Unity-iPhone` har du sannolikt öppnat filen `.xcodeproj` i stället för `.xcworkspace`. Öppna `.xcworkspace` och försök igen.

Välj rotnoden för **Unity-iPhone** för att visa projektinställningarna, och välj fliken **Allmänt**.

Under **Signing** (Signering) väljer du **Automatically manage signing** (Hantera signering automatiskt). Välj **Enable Automatic** (Aktivera automatisk) i den dialogruta som visas för att återställa build-inställningarna.

Under **Deployment Info** (Distributionsinformation) ser du till att **Deployment Target** (Distributionsmål) är angett till `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/spatial-anchors-unity/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/spatial-anchors-unity/deploy-run.png)