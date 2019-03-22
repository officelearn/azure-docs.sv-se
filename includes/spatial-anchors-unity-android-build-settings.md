---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 53f480b8858e2bbe7d4699d8637ecaa5ab0c08a3
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305126"
---
Öppna projektet i i Unity, den `Unity` mapp.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Android**. Ändra den **skapa System** till **Gradle** och välj **exportera projekt**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Android**. Unity bli du ombedd att installera stöd för Android-komponenter om de saknas.

![Unity Versionsinställningar fönster](./media/spatial-anchors-unity/unity-android-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Ladda ned och importera ARCore SDK för Unity

Ladda ned filen `unitypackage` från [ARCore SDK för Unity-versioner](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Tillbaka i Unity-projekt väljer **tillgångar** > **Importera paket** > **anpassade paketet** och välj sedan den `unitypackage` filen som du tidigare hämtade. I den **importera Unity-paketet** dialogrutan, kontrollera att alla filer har valts och välj sedan **Import**.
