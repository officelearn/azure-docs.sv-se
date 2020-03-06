---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 81c77b2f6ae0c4f8497716c168a937657ceb57dd
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384033"
---
I den här snabb starten lär du dig att använda tal enheter SDK för Linux för att bygga en tal aktive rad produkt eller använda den som en [Avskrifts](../conversation-transcription-service.md) enhet för konversation. För närvarande stöds endast [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Den här guiden kräver ett [Azure Cognitive Services](../get-started.md) -konto med en tjänst resurs för tal. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

Käll koden för [exempel programmet](https://aka.ms/sdsdk-download-JRE) ingår i tal enheter SDK. Det finns också [på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Operativ system: 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Endast [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* En Azure-prenumerationsnyckel för tjänsten Speech. [Skaffa en utan kostnad](../get-started.md).
* Ladda ned den senaste versionen av [tal enheter SDK](https://aka.ms/sdsdk-download-JRE) för Java och extrahera zip-filen till din arbets katalog.
   > [!NOTE]
   > Den här snabb starten förutsätter att appen extraheras till/home/wcaltest/JRE-Sample-Release

Se till att dessa beroenden är installerade innan du startar Sol förmörkelse.

* I Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* På Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

På RHEL/CentOS 8:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Konversations avskrift är för närvarande endast tillgängligt för "en-US" och "zh-CN" i regionerna "Central" och "asienöstra". Du måste ha en tal nyckel i någon av dessa regioner för att kunna använda konversations avskrifter.

Om du planerar att använda de avsikter behöver du en LUIS-prenumeration [(Language Understanding service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Mer information om LUIS och avsikts igenkänning finns i [känna igen tal C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)insikter med Luis. En [exempel modell för Luis](https://aka.ms/sdsdk-luis) är tillgänglig för den här appen.

## <a name="create-and-configure-the-project"></a>Skapa och konfigurera projektet

1. Starta Eclipse.

1. I fältet **arbets yta** i **sol förmörkelses IDE-start**, anger du namnet på en ny arbets ytans katalog. Välj sedan **Starta**.

   ![Skärmbild av Eclipse-startfönstret](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. Skapa ett nytt projekt från meny raden för Sol förmörkelse genom att välja **arkiv** > **nytt** > **Java-projekt**. Om det inte är tillgängligt väljer du **projekt** och sedan **Java-projekt**.

1. Guiden **nytt Java-projekt** startar. **Bläddra** efter exempel projektets plats. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Konfigurera** > **Convert to Maven Project** (Konvertera till Maven-projekt) från snabbmenyn. Välj **Slutför**.

   ![Skärmbild av Paketutforskaren](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Öppna filen Pom. xml och redigera den.

    I slutet av filen, innan den avslutande taggen `</project>`, skapar du `repositories` och `dependencies` element, som du ser här, och kontrollerar att `version` matchar din aktuella version:
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
             <version>1.10.0</version>
        </dependency>
    </dependencies>
   ```

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Egenskaper**, **Kör/Felsök inställningar** > **ny...** > **Java-program**. 

1. Fönstret **Redigera konfiguration** visas. I fältet **namn** anger du **main**och använder **Sök** efter **huvud klassen** för att hitta och välja **com. Microsoft. cognitiveservices. Speech. Samples. FunctionsList**.

   ![Skärm bild av redigera start konfiguration](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Kopiera ljud binärfilerna för mål arkitekturen från **Linux-arm** eller **linux-x64**till Java-projektets plats, t. ex. **/Home/wcaltest/JRE-Sample-release**

1. I fönstret **Redigera konfiguration** väljer du också sidan **miljö** och **ny**. Fönstret **ny miljö variabel** visas. I fältet **namn** anger du **LD_LIBRARY_PATH** och i fältet **värde** anger du mappen som innehåller *. so-filer, till exempel **/Home/wcaltest/JRE-Sample-release**

1. Kopiera `kws.table` och `participants.properties` till projektmappen **/-klasserna** i Project Folder


## <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Lägg till din tal prenumerations nyckel i käll koden. Om du vill prova avsikts igenkänning lägger du också till din [language Understanding tjänst](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerations nyckel och program-ID.

   För tal-och LUIS hamnar informationen i `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Om du använder en konversations avskrift behövs även din information om din nyckel och region i `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Standard nyckelordet (Keyword) är "Computer". Du kan också prova något av de andra angivna nyckelorden, t. ex. "Machine" eller "Assistant". Resursfiler för dessa alternativa nyckelord finns i avsnittet om tal enheter SDK i mappen nyckelord. `/home/wcaltest/JRE-Sample-Release/keyword/Computer` innehåller till exempel de filer som används för nyckelordet "dator".

   > [!TIP]
   > Du kan också [skapa ett anpassat nyckelord](../speech-devices-sdk-create-kws.md).

    Om du vill använda ett nytt nyckelord uppdaterar du följande rad i `FunctionsList.java`och kopierar nyckelordet till din app. Om du till exempel vill använda nyckelordet "dator" från nyckelords paketet `machine.zip`:

   * Kopiera `kws.table`-filen från zip-paketet till projektmappen **/-klasserna**i Project-mappen.

   * Uppdatera `FunctionsList.java` med nyckelords namnet:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Köra exempel programmet från Sol förmörkelse

1. I meny raden för Sol förmörkelse **kör** > **Kör** 

1. Exempelprogram för tal Devices SDK startar och välja mellan följande alternativ:

   ![Exempelprogrammet tal Devices SDK exempel och alternativ](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Prova den nya demonstrationen av **konversations avskrift** . Börja skriva med **sessionen** > **Starta**. Som standard är alla gäst. Men om du har deltagares röst-signaturer kan de placeras i `participants.properties` i projektmappen i Project **-** mappen. Om du vill generera röst signaturen tittar du på Skicka [konversationer (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Avskrifts program för demo konversation](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Skapa och kör fristående program

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Exportera**. 
1. **Export** fönstret visas. Expandera **Java** och välj **körbara jar-fil** och välj sedan **Nästa**.

   ![Skärm bild av export fönstret](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. **KÖRBARA jar File export** -fönstret visas. Välj ett **export mål** för programmet och välj sedan **Slutför**.
 
   ![Skärm bild av körbara JAR File export](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Lägg `kws.table` och `participants.properties` i målmappen som väljs ovan eftersom de här filerna behövs av programmet.

1. Ange LD_LIBRARY_LIB till den mapp som innehåller *. so-filer

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Köra det fristående programmet

     ```bash
     java -jar SpeechDemo.jar
     ```
