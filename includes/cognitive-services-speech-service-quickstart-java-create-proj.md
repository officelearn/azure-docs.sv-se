---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: e79442483fc419e73caa38ea7e088f8a389173e5
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963353"
---
1. Starta Eclipse.

1. I fältet **Arbetsyta** i Eclipse-startfönstret anger du namnet på en ny katalog för arbetsytan. Välj sedan **Starta**.

   ![Skärmbild av Eclipse-startfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. På Eclipse-menyraden skapar du ett nytt projekt genom att välja **Arkiv** > **Nytt** > **Projekt**.

1. Dialogrutan **Nytt projekt** visas. Välj **Java-projekt** och välj **Nästa**.

   ![Skärmbild av dialogrutan Nytt projekt, med Java-projekt markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Guiden Nytt Java-projekt startar. I fältet **Projektnamn** anger du **quickstart** (snabbstart) och väljer **JavaSE 1.8** som körningsmiljö. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Om fönstret **Open Associated Perspective?** (Vill du öppna associerat perspektiv?) visas väljer du **Open Perspective** (Öppna perspektiv).

1. I **Paketutforskaren** högerklickar du på projektet **quickstart** (snabbstart). Välj **Konfigurera** > **Convert to Maven Project** (Konvertera till Maven-projekt) från snabbmenyn.

   ![Skärmbild av Paketutforskaren](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Fönstret **Create new POM** (Skapa ny POM) visas. I fältet **Grupp-ID** anger du **com.microsoft.cognitiveservices.speech.samples** och i fältet **Artifact Id** (Artefakt-ID) anger du **quickstart**. Välj sedan **Slutför**.

   ![Skärmbild av fönstret Create new POM (Skapa ny POM)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Öppna filen **pom.xml** och redigera den.

   * I slutet av filen, före sluttaggen `</project>`, skapar du ett `repositories`-element med en referens till Maven-databasen för Speech SDK, som du ser här:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Lägg även till ett `dependencies`-element med Speech SDK-version 1.3.1 som ett beroende:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Spara ändringarna.
