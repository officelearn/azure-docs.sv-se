---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187858"
---
På grund av pågående utveckling kanske Android SDK-versionen som är installerad i Android Studio inte matchar versionen i koden. Android SDK refereras i den här guiden är version 26, den senaste i skrivande stund. Versionsnumret kan öka när nya versioner av SDK visas, och vi rekommenderar att du använder den senaste tillgängliga versionen.

Två symptom på version obalans är:

- När du skapar eller återskapar projektet kan du `Gradle sync failed: Failed to find target with hash string 'android-XX'`få Gradle-felmeddelanden som .
- Standard-Android-objekt i kod `import` som ska matchas baserat på satser kan generera felmeddelanden.

Om något av dessa visas kanske versionen av Android SDK som är installerad i Android Studio inte matchar SDK-målet för det nedladdade projektet. Så här verifierar du versionen:

1. I Android Studio klickar du på **Verktyg** > **Android** > **SDK Manager**. Om du inte har installerat den senaste versionen av SDK-plattformen klickar du för att installera den. Anteckna versionsnumret.

2. Öppna filen **build.gradle (Modul: app)** under **Gradle-skript**på fliken **Projektutforskaren** . Kontrollera att **kompileringenSdkVersion** och **targetSdkVersion** är inställda på den senaste SDK-versionen installerad. Den `build.gradle` kan se ut så här:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
