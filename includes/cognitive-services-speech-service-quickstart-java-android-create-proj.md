---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485035"
---
1. Starta Android Studio och välj alternativet för att **starta ett nytt Android Studio-projekt** i välkomstfönstret.

    ![Skärmbild av Android Studio-välkomstfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Den **väljer du projektet** guiden visas, väljer **Telefoner och surfplattor** och **tom aktivitet** i aktivitetsrutan. Välj **Nästa**.

   ![Skärmbild av Välj ditt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. I den **konfigurera ditt projekt** anger **snabbstarten** som **namn**, **samples.speech.cognitiveservices.microsoft.com** som **Paketnamn**, och välj en projektkatalogen. För **minsta API-nivå** Välj **API-23: Android 6.0 (Marshmallow)** , lämnar du alla andra kryssrutorna avmarkerad och välj **Slutför**.

   ![Skärmbild av konfigurera ditt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Det tar en stund för Android Studio att förbereda det nya projektet för Android. Konfigurera projektet att veta om Speech SDK och använda Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.6.0`.

Speech SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs.
Den finns i ett Maven-centrallager på https:\//csspeechstorage.blob.core.windows.net/maven/.

Konfigurera ditt projekt för att använda Speech SDK. Öppna projektstrukturfönstret genom att välja **File** (Fil)  > **Project Structure** (Projektstruktur) på menyraden i Android Studio. I projektstrukturfönstret gör du följande ändringar:

1. I listan till vänster i fönstret väljer du **Projekt**. Redigera inställningarna för **standardbiblioteksdatabasen** genom att lägga till ett kommatecken och vår URL till Maven-lagringsplatsen inom enkla citattecken. ”https:\//csspeechstorage.blob.core.windows.net/maven/”

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. På samma skärm till vänster väljer du **app**. Välj sedan fliken **Beroenden** högst upp i fönstret. Markera det gröna plustecknet (+) och välj **Library dependency** (Biblioteksberoenden) på den nedrullningsbara menyn.

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namnet och versionen av vårt Speech SDK för Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Välj sedan **OK**.
   Speech SDK:n ska läggas till i listan över beroenden nu enligt nedan:

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Välj fliken **Egenskaper**. För både **Källkompatibilitet** och **Målkompatibilitet** väljer du **1.8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Välj **OK** för att stänga fönstret Project Structure (Projektstruktur) och tillämpa ändringarna i projektet.
