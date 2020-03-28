---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 270a8316ee7c23ebb54c7d0633fb9159ef33acbd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383800"
---
1. Starta Android Studio och välj **Starta ett nytt Android Studio-projekt** i **välkomstfönstret.**

    ![Skärmbild av Android Studio-välkomstfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Guiden **Välj projekt** visas. Välj **Telefon och Surfplatta** och Tom **aktivitet** i rutan för aktivitetsval. Välj **Nästa**.

   ![Skärmbild av Guiden Välj projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. På **skärmen Konfigurera projekt** anger du *Snabbstart* som **namn** och anger *samples.speech.cognitiveservices.microsoft.com* som **paketnamn**. Välj sedan en projektkatalog. För **lägsta API-nivå**väljer du **API 23: Android 6.0 (Marshmallow).** Lämna alla andra kryssrutor avmarkerade och välj **Slutför**.

   ![Skärmbild av Konfigurera projektguiden](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Det tar en stund för Android Studio att förbereda det nya projektet för Android. Konfigurera sedan projektet så att det får reda på Azure Cognitive Services Speech SDK och att använda Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är 1.10.0.

Speech SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs.
Den finns i en Maven-databas\/på https: /csspeechstorage.blob.core.windows.net/maven/.

Konfigurera ditt projekt för att använda Speech SDK. Öppna fönstret **Projektstruktur** genom att välja > **Arkivprojektstruktur** på Menyraden i Android Studio. **File** Gör följande ändringar i fönstret **Projektstruktur:**

1. I listan till vänster i fönstret väljer du **Projekt**. Redigera **standardinställningarna för biblioteksdatabasen** genom att lägga till ett kommatecken och\/vår Maven-databas-URL innesluten i enkla citattecken: "https: /csspeechstorage.blob.core.windows.net/maven/"

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Välj **app**på samma skärm på vänster sida . Välj sedan fliken **Beroenden** högst upp i fönstret. Välj det gröna**+** plustecknet ( och välj **Biblioteksberoende** på den nedrullningsbara menyn.

   ![Skärmbild av biblioteksberoende](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namnet och versionen av Speech SDK för Android, *com.microsoft.cognitiveservices.speech:client-sdk:1.10.0*. Välj sedan **OK**.
   Tal-SDK ska läggas till i listan över beroenden nu, som visas:

   ![Skärmbild av Tal-SDK i listan över beroenden](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Välj fliken **Egenskaper.** För både **källkompatibilitet** och **målkompatibilitet**väljer du **1.9**.

   ![Skärmbild av källkompatibilitet och målkompatibilitet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Välj **OK** om du vill stänga fönstret **Projektstruktur** och tillämpa ändringarna i projektet.
