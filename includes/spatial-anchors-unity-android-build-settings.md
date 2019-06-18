---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 9798e5f76881be38fb27e1f428565caba6e50bf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135118"
---
Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Android**. Ändra den **skapa System** till **Gradle** och se till att den **exportera projekt** kryssrutan inte är markerade.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Android**. Unity bli du ombedd att installera stöd för Android-komponenter om de saknas.

![Unity Versionsinställningar fönster](./media/spatial-anchors-unity/unity-android-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Ladda ned och importera ARCore SDK för Unity

Ladda ned den `unitypackage` fil från den [ARCore SDK för Unity 1.7 släpper](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Tillbaka i Unity-projekt väljer **tillgångar** > **Importera paket** > **anpassade paketet** och välj sedan den `unitypackage` filen som du tidigare hämtade. I den **importera Unity-paketet** dialogrutan, kontrollera att alla filer har valts och välj sedan **Import**.
