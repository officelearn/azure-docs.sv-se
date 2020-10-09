---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89536283"
---
Välj **build**. I dialog rutan som öppnas väljer du en mapp att exportera Xcode-projektet till.

När exporten är klar visas en mapp som innehåller det exporterade Xcode-projektet.

> [!NOTE]
> Om ett fönster frågar dig om du vill ersätta eller lägga till visas, rekommenderar vi att du väljer **Lägg till** eftersom det är snabbare. Du behöver bara välja **Ersätt** om du byter till gångar i din scen. (Om du till exempel lägger till, tar bort eller ändrar överordnade/underordnade relationer eller om du lägger till, tar bort eller ändrar egenskaper.) Om du bara gör käll kods ändringar bör **tillägg** vara tillräckligt.

## <a name="open-the-xcode-project"></a>Öppna Xcode-projektet

Nu kan du öppna `Unity-iPhone.xcodeproj` i Xcode. Du kan antingen starta Xcode och öppna det exporterade `Unity-iPhone.xcodeproj` projektet eller starta projektet i Xcode genom att köra följande kommando från den plats där du exporterade projektet:

```bash
open ./Unity-iPhone.xcodeproj
```

Välj den rot **Uppunions-iPhone-** noden för att visa projekt inställningarna och välj sedan fliken **Allmänt** .

Under **signering**ser du till att **Hantera signering** är aktiverat automatiskt. Om det inte är det aktiverar du det och väljer sedan **Aktivera automatiskt** i dialog rutan som visas för att återställa build-inställningarna.

Under **Deployment Info** (Distributionsinformation) ser du till att **Deployment Target** (Distributionsmål) är angett till `11.0`.

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/spatial-anchors-unity/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/spatial-anchors-unity/deploy-run.png)
