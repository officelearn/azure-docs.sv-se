---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 355d89b1fd7d7cc8c9db58122d51c6ff7dff5f3b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622594"
---
Öppna projektet i i Unity, den `Unity` mapp.

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Android**. Ändra den **skapa System** till **Gradle** och välj **exportera projekt**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Android**. Unity bli du ombedd att installera stöd för Android-komponenter om de saknas.

![Unity Versionsinställningar fönster](./media/spatial-anchors-unity/unity-android-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Ladda ned och importera ARCore SDK för Unity

Ladda ned den `unitypackage` fil från den [ARCore SDK för Unity 1.5 släpper](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Tillbaka i Unity-projekt väljer **tillgångar** > **Importera paket** > **anpassade paketet** och välj sedan den `unitypackage` filen som du tidigare hämtade. I den **importera Unity-paketet** dialogrutan, kontrollera att alla filer har valts och välj sedan **Import**.
