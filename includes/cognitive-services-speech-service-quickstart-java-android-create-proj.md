---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: e1f9b561efb1fd88fe2dd00f8c175f1ebae848d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391793"
---
1. Starta Android Studio och välj alternativet för att **starta ett nytt Android Studio-projekt** i välkomstfönstret.

    ![Skärmbild av Android Studio-välkomstfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Guiden **Välj projekt** visas, Välj **telefon och surfplatta** och **Tom aktivitet** i rutan aktivitets val. Välj **Nästa**.

   ![Skärm bild av guiden Välj projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. På skärmen **Konfigurera ditt projekt anger du** **snabb start** som **namn**, **samples.Speech.cognitiveservices.Microsoft.com** som **paket namn**och väljer en projekt katalog. För **lägsta API-nivå** väljer du **API 23: Android 6,0 (Marshmallow)** , låt alla andra kryss rutor vara avmarkerade och välj **Slutför**.

   ![Skärm bild av guiden Konfigurera ditt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Det tar en stund för Android Studio att förbereda det nya projektet för Android. Konfigurera projektet att veta om Speech SDK och använda Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.7.0`.

Speech SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs.
Den finns i en maven-lagringsplats på https: \//csspeechstorage. blob. Core. Windows. net/Maven/.

Konfigurera ditt projekt för att använda Speech SDK. Öppna projektstrukturfönstret genom att välja **File** (Fil)  > **Project Structure** (Projektstruktur) på menyraden i Android Studio. I projektstrukturfönstret gör du följande ändringar:

1. I listan till vänster i fönstret väljer du **Projekt**. Redigera inställningarna för **standardbiblioteksdatabasen** genom att lägga till ett kommatecken och vår URL till Maven-lagringsplatsen inom enkla citattecken. "https: \//csspeechstorage. blob. Core. Windows. net/maven/"

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. På samma skärm till vänster väljer du **app**. Välj sedan fliken **Beroenden** högst upp i fönstret. Markera det gröna plustecknet (+) och välj **Library dependency** (Biblioteksberoenden) på den nedrullningsbara menyn.

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namnet och versionen av vårt Speech SDK för Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`. Välj sedan **OK**.
   Speech SDK:n ska läggas till i listan över beroenden nu enligt nedan:

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Välj fliken **Egenskaper** . För både **käll** -och **mål**-kompatibilitet väljer du **1,8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Välj **OK** för att stänga fönstret Project Structure (Projektstruktur) och tillämpa ändringarna i projektet.
