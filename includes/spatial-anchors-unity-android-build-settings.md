---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342508"
---
Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Android**. Ändra den **skapa System** till **Gradle** och välj **exportera projekt**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Android**. Unity bli du ombedd att installera stöd för Android-komponenter om de saknas.

![Unity Versionsinställningar fönster](./media/spatial-anchors-unity/unity-android-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Ladda ned och importera ARCore SDK för Unity

Ladda ned den `unitypackage` fil från den [ARCore SDK för Unity 1.7 släpper](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Tillbaka i Unity-projekt väljer **tillgångar** > **Importera paket** > **anpassade paketet** och välj sedan den `unitypackage` filen som du tidigare hämtade. I den **importera Unity-paketet** dialogrutan, kontrollera att alla filer har valts och välj sedan **Import**.
