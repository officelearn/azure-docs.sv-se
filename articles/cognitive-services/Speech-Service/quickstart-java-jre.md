---
title: 'Snabbstart: Känna igen tal i Java (Windows eller Linux)'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i Java (Windows eller Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234352"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Snabbstart: Känna igen tal i Java (Windows eller Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Det här dokumentet beskriver hur du skapar ett Java-baserade konsolprogram för körning miljö JRE (Java) som använder SDK: N för tal.
Programmet är baserad på Microsoft Cognitive Services SDK Maven-paketet.
Vi använder Eclipse som ett Integrated Development Environment (IDE).

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En dator (Windows x64, Ubuntu 16.04 x64) kapacitet att köra Eclipse, med en fungerande mikrofon.
* 64-bitars JRE/JDK för Java 8.
* 4.8-versionen av [Eclipse](https://www.eclipse.org), 64-bitarsversionen.
* Kör följande kommandon för att installera nödvändiga paket på Ubuntu 16.04:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>Skapa och konfigurera ditt projekt

1. Starta Eclipse.

1. Ange namnet på en ny katalog i i Eclipse-starta den **arbetsytan** fält.
   Klicka sedan på **starta**.

   ![Skapa Eclipse-arbetsyta](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Efter ett tag visas huvudfönstret i Eclipse IDE.
   Om det finns en välkomstskärm i den, stänga den.

1. Välj **filen** \> **nya** \> **projekt**.

1. I den **nytt projekt** guiden som visas väljer **Java-projekt**, och klicka på **nästa**.

   ![Välj en guide](media/sdk/qs-java-jre-02-select-wizard.png)

1. I fönstret nästa anger **snabbstarten** som ett projekt servernamnet och **JavaSE 1.8** (eller upp) som körningsmiljö.
   Klicka på **Slutför**.

   ![Välj en guide](media/sdk/qs-java-jre-03-create-java-project.png)

1. Om ett fönster med rubriken **öppna associerat perspektiv?** POP upp väljer **öppna perspektiv**.

1. I den **Package explorer**, högerklicka på den **snabbstarten** projektet och välj **konfigurera** \> **konvertera till Maven-projekt**.

   ![Konvertera till Maven-projekt](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. I fönstret som visas, ange **com.microsoft.cognitiveservices.speech.samples** som **grupp-Id** och **snabbstarten** som **artefakt-Id**. Välj **Slutför**.

   ![Konfigurera Maven POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Redigera den **pom.xml** fil.

  * I slutet av filen, före sluttaggen `</project>`, skapa ett avsnitt för databaser med en referens till Maven-centrallagret för tal-SDK:

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Lägg även till därefter ett beroenden avsnitt med tal SDK version 0.6.0 som ett beroende:

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Spara ändringarna.

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. Välj **filen** \> **New** \> **klass** att lägga till en ny tom klass i Java-projekt.

1. I fönstret **ny Java-klass** ange **speechsdk.quickstart** till den **paketet** fält, och **Main** till den **namn**  fält.

   ![Skapa en Main-klass](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringar i projektet.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Tryck på F11 eller välj **kör** \> **felsöka**.
Nästa 15 sekunder av talindata från mikrofonen ska identifieras och loggas i konsolfönstret.

![Konsolens utdata efter lyckad taligenkänning](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/java-jre` mapp.

## <a name="next-steps"></a>Nästa steg

* [Hämta våra exempel](speech-sdk.md#get-the-samples)
