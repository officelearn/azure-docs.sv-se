---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681397"
---
Välj **skapa**. I dialogrutan som öppnas väljer du en mapp som du vill exportera Xcode-projektet till.

När exporten är klar visas en mapp som innehåller det exporterade Xcode-projektet.

> [!NOTE]
> Om det visas ett fönster som frågar om du vill ersätta eller lägga till, rekommenderar vi att du väljer **Append** eftersom det är snabbare. Du behöver bara välja **Ersätt** om du ändrar tillgångar i din scen. (Till exempel om du vill lägga till, ta bort, ändra överordnade/underordnade relationer eller om du vill lägga till, ta bort eller ändra egenskaperna.) Om du endast gör ändringar av datakällan kod **Append** borde räcka.

### <a name="open-the-xcode-project"></a>Öppna Xcode-projektet

Kör följande kommando i terminalen för att installera nödvändiga CocoaPods för projektet i Xcode exporterade projektmappen:

```bash
pod install --repo-update
```

Nu kan du öppna `Unity-iPhone.xcworkspace` att öppna projektet i Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Om du ser en `library not found for -lPods-Unity-iPhone` fel du förmodligen har öppnat den `.xcodeproj` fil i stället för den `.xcworkspace` filen. 

Välj roten **Unity-iPhone** noden för att visa projektinställningarna för och välj sedan den **Allmänt** fliken.

Under **signering**, se till att **automatiskt hantera signering** är aktiverad. Om den inte aktivera den och välj sedan **aktivera automatisk** i dialogrutan som visas för build-inställningar återställs.

Under **Deployment Info** (Distributionsinformation) ser du till att **Deployment Target** (Distributionsmål) är angett till `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/spatial-anchors-unity/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/spatial-anchors-unity/deploy-run.png)