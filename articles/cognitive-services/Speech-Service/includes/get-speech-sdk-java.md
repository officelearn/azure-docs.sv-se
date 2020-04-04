---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656591"
---
:::row:::
    :::column span="3":::
        Java SDK för Android är paketerad som ett <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android-bibliotek), <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i ett Maven-arkiv `https://csspeechstorage.blob.core.windows.net/maven/` `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`på som paket .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Så här använder du paketet från ditt Android Studio-projekt:

1. Lägg till följande i avsnittet `repository` *build.gradle* på projektnivåsnivå:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Lägg till följande i avsnittet `dependencies` *build.gradle* på modulnivå:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Java SDK är också en del av [Speech Devices SDK](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-specifik Java-snabbstarts källkod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Snabbstartskod för Java Runtime (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>