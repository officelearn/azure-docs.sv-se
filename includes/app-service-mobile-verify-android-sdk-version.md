---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133334"
---
På grund av pågående utveckling kanske Android SDK-versionen som installerats i Android Studio inte matchar versionen i koden. Android SDK refereras till i den här självstudien är version 26, senast vid tidpunkten för skrivning. Öka versionsnumret som visas av nya versioner av SDK, och vi rekommenderar att du använder den senast tillgängliga versionen.

Två symptom av versionskonflikt:

- När du skapar eller återskapa projektet kan du få Gradle felmeddelanden som `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Android Standardobjekt i koden som ska matcha utifrån `import` instruktioner kan generera felmeddelanden.

Om något av dessa visas kanske versionen av Android SDK: N installeras i Android Studio inte överensstämmer med SDK-målet för det hämta projektet. Kontrollera versionen genom att göra följande ändringar:

1. I Android Studio klickar du på **verktyg** > **Android** > **SDK Manager**. Klicka för att installera den om du inte har installerat den senaste versionen av SDK-plattformen. Anteckna det lägre versionsnumret.

2. På den **Projektutforskaren** fliken, under **Gradle-skripten**, öppna filen **build.gradle (modul: app)**. Se till att den **compileSdkVersion** och **targetSdkVersion** är inställda på SDK-version som är installerad. Den `build.gradle` se ut så här:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
