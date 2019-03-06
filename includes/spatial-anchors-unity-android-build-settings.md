---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752904"
---
Öppna Unity och öppna projektet i mappen `Unity`.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** -> **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Android**. Ändra sedan **Build System** (Build-system) till **Gradle** och markera alternativet **Exportera projekt**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Android**. Unity kan be dig att installera Android-stödkomponenter om de saknas.

![Unity-bygginställningar](./media/spatial-anchors-unity/unity-android-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Ladda ned och importera ARCore SDK för Unity

Ladda ned filen `unitypackage` från [ARCore SDK för Unity-versioner](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). När du är tillbaka i Unity-projektet väljer du **Tillgångar** -> **Importera paket** -> **Custom Package...** (Anpassat paket...) och väljer sedan den `unitypackage`-fil som du har laddat ned tidigare. I dialogrutan **Import Unity Package** (Importera Unity-paket) kontrollerar du att alla filer har valts och väljer sedan **Importera**.
