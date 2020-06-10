---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 64a6bf1fec61871d2787966a5b0af24d4f012032
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637415"
---
:::row:::
    :::column span="3":::
        Java SDK för Android paketeras som ett <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android-bibliotek) <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i en maven-lagringsplats på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.12.1` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Om du vill använda paketet från Android Studio-projektet gör du följande ändringar:

1. I filen *build. gradle* för projekt nivå lägger du till följande i `repository` avsnittet:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Lägg till följande i avsnittet i filen *build. gradle* för modulnivå `dependencies` :
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.12.1'
  ```

Java SDK är också en del av [tal enhets-SDK: n](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-Specific källkod för Java snabb start<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) snabb starts käll kod<span class="docon docon-navigate-external x-hidden-focus"></span></a>