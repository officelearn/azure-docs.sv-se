---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: df29ecf0a4ddb30aea1c1ab9ceed44a93fc61eb8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400072"
---
I den här snabbstarten får du lära dig hur du använder Talenheterna SDK för Windows för att skapa en talaktiverad produkt eller använda den som en [konversationsavskriptionsenhet.](../conversation-transcription-service.md) För konversationsavskrift stöds endast [Azure Kinect DK.](https://azure.microsoft.com/services/kinect-dk/) För andra tal används linjära mikrofonmatriser som tillhandahåller en geometri för mikrofonmatris.

Programmet är byggt med Speech SDK-paketet och Eclipse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Den här guiden kräver ett [Azure Cognitive Services-konto](../get-started.md) med en taltjänstresurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

Källkoden för [exempelprogrammet](https://aka.ms/sdsdk-download-JRE) ingår i Speech Devices SDK. Den finns även [på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* Operativsystem: 64-bitars Windows
* En mikrofonmatris som [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Endast Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* En Azure-prenumerationsnyckel för tjänsten Speech. [Skaffa en kostnadsfritt](../get-started.md).
* Hämta den senaste versionen av [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) för Java och extrahera .zip till arbetskatalogen.
   > [!NOTE]
   > Den här snabbstarten förutsätter att appen extraheras till C:\SDSDK\JRE-Sample-Release

Konversation transkription är för närvarande endast tillgänglig för "en-US" och "zh-CN", i "centralus" och "eastasia" regioner. Du måste ha en talnyckel i något av dessa regioner för att kunna använda konversationsavskrift.

Om du planerar att använda avsikter behöver du en [LUIS-prenumeration (Language Understanding Service).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Mer information om LUIS och avsiktsigenkänning finns i [Identifiera talavsikter med LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Det finns ett [exempel på LUIS-modellen](https://aka.ms/sdsdk-luis) för den här appen.

## <a name="create-and-configure-the-project"></a>Skapa och konfigurera projektet

1. Starta Eclipse.

1. Ange namnet på en ny arbetsyta i fältet **Eclipse IDE Launcher**i fältet **Arbetsyta.** Välj sedan **Starta**.

   ![Skärmbild av Eclipse-startfönstret](../media/speech-devices-sdk/eclipse-launcher.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. Skapa ett nytt projekt på menyraden Eclipse genom att välja **Arkiv** > **Nytt** > **Java-projekt**. Om det inte är tillgängligt väljer du **Project** och sedan **Java-projekt**.

1. Guiden **Nytt Java-projekt** startar. **Sök efter** platsen för exempelprojektet. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. Högerklicka på projektet i **paketutforskaren.** Välj **Konfigurera** > **Konvertera till Maven-projekt** på snabbmenyn. Välj **Slutför**.

   ![Skärmbild av Paketutforskaren](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Öppna filen pom.xml och redigera den.

    I slutet av filen, före `</project>`den `repositories` avslutande `dependencies` taggen, skapa och `version` element, som visas här, och se till att matchar din nuvarande version:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. Kopiera innehållet i **Windows-x64** till Java-projektplatsen, **C:\SDSDK\JRE-Sample-Release** t.ex.

1. Kopiera `kws.table` `participants.properties` och `Microsoft.CognitiveServices.Speech.extension.pma.dll` in i projektmappens **mål\klasser**

## <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Lägg till din talprenumerationsnyckel i källkoden. Om du vill prova avsiktskännedom lägger du även till din prenumerationsnyckel för [språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) och program-ID.

   För tal och LUIS går `FunctionsList.java`din information till:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Om du använder konversationsranskription behövs även taltangenten och regioninformationen i: `Cts.java`

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Standardnyckelordet (nyckelordet) är "Dator". Du kan också prova ett av de andra nyckelorden, till exempel "Maskin" eller Assistent. Resursfilerna för dessa alternativa nyckelord finns i SDK för talenheter i nyckelordsmappen. Innehåller till `C:\SDSDK\JRE-Sample-Release\keyword\Computer` exempel de filer som används för nyckelordet "Dator".

    > [!TIP]
    > Du kan också [skapa ett anpassat nyckelord](../speech-devices-sdk-create-kws.md).

    Om du vill använda ett nytt `FunctionsList.java`nyckelord uppdaterar du följande rad i och kopierar nyckelordet till appen. Om du till exempel vill använda nyckelordet `machine.zip`"Maskin" från nyckelordspaketet:

   * Kopiera `kws.table` filen från zip-paketet till projektmappens **mål/klasser**.
   * Uppdatera `FunctionsList.java` med nyckelordsnamnet:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Kör exempelprogrammet från Eclipse

1. **Kör** > **Kör som** > **Java-program**på menyraden Eclipse . Välj sedan **FunctionsList** och **OK**.

   ![Skärmbild av Välj Java-program](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Exempelprogrammet Talenheter SDK startar och visar följande alternativ:

   ![Exempel på SDK-exempelprogram och alternativ för talenheter](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Prova den nya **demonstrationen för konversationsavskrifter.** Börja transkribera med **Session** > **Start**. Som standard är alla gäst. Men om du har deltagarens röstsignaturer kan `participants.properties` de placeras i en fil i projektmappens **mål/klasser**. Om du vill generera röstsignaturen tittar du på [Transkribera konversationer (SDK).](../how-to-use-conversation-transcription-service.md)

   ![Demo Konversation Transkription ansökan](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Skapa och kör ett fristående program

1. Högerklicka på projektet i **paketutforskaren.** Välj **Exportera**.

1. **Exportfönstret** visas. Expandera **Java** och välj **Körbar JAR-fil** och välj sedan **Nästa**.

   ![Skärmbild av exportfönstret](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Fönstret **Körningsbar JAR-filexport** visas. Välj ett **exportmål** för programmet och välj sedan **Slutför**.

   ![Skärmbild av körningsbar JAR-filexport](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Lägg `kws.table`, `participants.properties` `unimic_runtime.dll`, `pma.dll` `Microsoft.CognitiveServices.Speech.extension.pma.dll` och i målmappen som valts ovan eftersom dessa filer behövs av programmet.

1. Så här kör du det fristående programmet

   ```powershell
   java -jar SpeechDemo.jar
   ```
