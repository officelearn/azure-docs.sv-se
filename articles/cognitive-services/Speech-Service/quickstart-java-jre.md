---
title: 'Snabbstart: Identifiera tal, Java – Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i Java (Windows eller Linux)
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: 7d1f26a43866025c3b542fc10a3f316ad0d1dc37
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103130"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>Snabbstart: Känna igen tal i Java i Windows eller Linux med hjälp av Speech Service SDK:n

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln ska du skapa ett Java-konsolprogram med hjälp av [Speech Service SDK](speech-sdk.md). Du transkriberar tal till text i realtid från datorns mikrofon. Programmet baseras på Maven-paketet för Speech SDK och Eclipse Java IDE (v4.8) i 64-bitars Windows eller Ubuntu Linux 16.04. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en prenumerationsnyckel för Speech-tjänsten för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Mer information finns i [Prova Speech-tjänsten kostnadsfritt](get-started.md).


## <a name="create-and-configure-project"></a>Skapa och konfigurera projektet

Om du använder Ubuntu 16.04 kör du följande kommandon innan du startar Eclipse för att se till att nödvändiga paket är installerade.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Starta Eclipse.

1. I fältet **Arbetsyta** i Eclipse-startfönstret anger du namnet på en ny katalog för arbetsytan. Välj sedan **Starta**.

   ![Skärmbild av Eclipse-startfönstret](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. På Eclipse-menyraden skapar du ett nytt projekt genom att välja **Arkiv** > **Nytt** > **Projekt**.

1. Dialogrutan **Nytt projekt** visas. Välj **Java-projekt** och välj **Nästa**.

   ![Skärmbild av dialogrutan Nytt projekt, med Java-projekt markerat](media/sdk/qs-java-jre-02-select-wizard.png)

1. Guiden Nytt Java-projekt startar. I fältet **Projektnamn** anger du **quickstart** (snabbstart) och väljer **JavaSE 1.8** som körningsmiljö. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](media/sdk/qs-java-jre-03-create-java-project.png)

1. Om fönstret **Open Associated Perspective?** (Vill du öppna associerat perspektiv?) visas väljer du **Open Perspective** (Öppna perspektiv).

1. I **Paketutforskaren** högerklickar du på projektet **quickstart** (snabbstart). Välj **Konfigurera** > **Convert to Maven Project** (Konvertera till Maven-projekt) från snabbmenyn.

   ![Skärmbild av Paketutforskaren](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Fönstret **Create new POM** (Skapa ny POM) visas. I fältet **Grupp-ID** anger du **com.microsoft.cognitiveservices.speech.samples** och i fältet **Artifact Id** (Artefakt-ID) anger du **quickstart**. Välj sedan **Slutför**.

   ![Skärmbild av fönstret Create new POM (Skapa ny POM)](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Öppna filen **pom.xml** och redigera den.

   * I slutet av filen, före sluttaggen `</project>`, skapar du ett `repositories`-element med en referens till Maven-databasen för Speech SDK, som du ser här:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Lägg även till ett `dependencies`-element med Speech SDK-version 1.1.0 som ett beroende:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Spara ändringarna.

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/java-jre`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter från tal med hjälp av Speech SDK för Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
