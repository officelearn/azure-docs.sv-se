---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 2ee6b12923bfd0e06343e8f185226cb72280d806
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78384069"
---
I den här snabbstarten får du lära dig hur du använder Talenheterna SDK för Android för att skapa en talaktiverad produkt eller använda den som en [konversationsavskriptionsenhet.](../conversation-transcription-service.md)

Den här guiden kräver ett [Azure Cognitive Services-konto](../get-started.md) med en taltjänstresurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

Källkoden för exempelprogrammet ingår i Speech Devices SDK. Den finns även [på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Krav

Innan du börjar använda SDK-talenheterna måste du:

- Följ instruktionerna som medföljer [utvecklingssatsen](../get-speech-devices-sdk.md) för att slå på enheten.

- Hämta den senaste versionen av [Speech Devices SDK](https://aka.ms/sdsdk-download)och extrahera .zip till arbetskatalogen.

  > [!NOTE]
  > Den här snabbstarten förutsätter att appen extraheras till C:\SDSDK\Android-Sample-Release

- Så här hämtar du en [Azure-prenumerationsnyckel för Tal-tjänsten](../get-started.md)

- Om du planerar att använda konversationsavskriptionen måste du använda en [cirkulär mikrofonenhet](../get-speech-devices-sdk.md) och den här funktionen är för närvarande endast tillgänglig för "en-US" och "zh-CN" i regioner, "centralus" och "eastasia". Du måste ha en talnyckel i något av dessa regioner för att kunna använda konversationsavskrift.

- Om du planerar att använda taltjänsten för att identifiera avsikter (eller åtgärder) från användaryttranden behöver du en [LUIS-prenumeration (Language Understanding Service).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Mer information om LUIS och avsiktsigenkänning finns i [Identifiera talavsikter med LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Du kan [skapa en enkel LUIS-modell](https://docs.microsoft.com/azure/cognitive-services/luis/) eller använda exempel luismodellen LUIS-exempel.json. Exempelet LUIS-modellen är tillgänglig från [hämtningsplatsen för Talenheter SDK](https://aka.ms/sdsdk-luis). Om du vill ladda upp modellens JSON-fil till [LUIS-portalen](https://www.luis.ai/home)väljer du **Importera ny app**och väljer sedan JSON-filen.

- Installera [Android Studio](https://developer.android.com/studio/) och [Vysor](https://vysor.io/download/) på datorn.

## <a name="set-up-the-device"></a>Konfigurera enheten

1. Starta Vysor på datorn.

   ![Vysor (på andra)](../media/speech-devices-sdk/qsg-3.png)

1. Enheten ska visas under **Välj en enhet**. Välj knappen **Visa** bredvid enheten.

1. Anslut till det trådlösa nätverket genom att välja mappikonen och välj sedan **Inställningar** > **WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Om ditt företag har policyer för att ansluta enheter till sitt Wi-Fi-system måste du skaffa MAC-adressen och kontakta IT-avdelningen om hur du ansluter den till företagets Wi-Fi.
   >
   > Om du vill hitta MAC-adressen för utvecklingssatsen markerar du filmappsikonen på skrivbordet i dev-paketet.
   >
   > ![Vysor-filmapp](../media/speech-devices-sdk/qsg-10.png)
   >
   > Välj **Inställningar**. Sök efter "mac-adress" och välj sedan **Mac-adress** > **Advanced WLAN**. Skriv ned MAC-adressen som visas längst ned i dialogrutan.
   >
   > ![Vysor MAC-adress](../media/speech-devices-sdk/qsg-11.png)
   >
   > Vissa företag kan ha en tidsgräns för hur länge en enhet kan anslutas till sitt Wi-Fi-system. Du kan behöva utöka dev-kitets registrering med ditt Wi-Fi-system efter ett visst antal dagar.

## <a name="run-the-sample-application"></a>Köra exempelprogrammet

Så här validerar du inställningarna för utvecklingspaketet genom att skapa och installera exempelprogrammet:

1. Starta Android Studio.

1. Välja **Öppna ett befintligt Android Studio-projekt**.

   ![Android Studio - Öppna ett befintligt projekt](../media/speech-devices-sdk/qsg-5.png)

1. Gå till exemplet C:\SDSDK\Android-Sample-Release\. Välj **OK** för att öppna exempelprojektet.

1. Konfigurera gradle för att referera till Tal-SDK. Följande filer finns under **Gradle Skript** i Android Studio.

    Uppdatera **build.gradle(Project:example)** bör alla projektblock matcha nedan genom att lägga till maven-raderna.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Uppdatera **build.gradle(Module:app)** genom att lägga till den här raden i avsnittet beroenden. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.10.0'
    ```
    
1. Lägg till din talprenumerationsnyckel i källkoden. Om du vill prova avsiktskännedom lägger du även till din prenumerationsnyckel för [språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) och program-ID.

   För tal och LUIS går din information in i MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Om du använder konversationsranskription behövs även taltangenten och regioninformationen i conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Standardnyckelordet är "Dator". Du kan också prova ett av de andra nyckelorden, till exempel "Maskin" eller Assistent. Resursfilerna för dessa alternativa nyckelord finns i SDK för talenheter i nyckelordsmappen. C:\SDSDK\Android-Sample-Release\keyword\Computer innehåller till exempel de filer som används för nyckelordet "Dator".

   > [!TIP]
   > Du kan också [skapa ett anpassat nyckelord](../speech-devices-sdk-create-kws.md).

   Om du vill använda ett nytt `MainActivity.java`nyckelord uppdaterar du följande två rader i och kopierar nyckelordspaketet till appen. Om du till exempel vill använda nyckelordet "Maskin" från nyckelordspaketet kws-machine.zip:

   - Kopiera nyckelordspaketet till mappen "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Uppdatera `MainActivity.java` med nyckelordet och paketnamnet:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Uppdatera följande rader, som innehåller geometriinställningarna för mikrofonmatrisen:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   I den här tabellen visas värden som stöds:

   | Variabel | Betydelse | Tillgängliga värden |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfiguration av fysisk mikrofon | För ett cirkulärt utvecklingspaket:`Circular6+1` |
   |          |         | För en linjär dev kit:`Linear4` |
   | `SelectedGeometry` | Konfiguration av programvarumikrofon | För ett cirkulärt utvecklingspaket som använder alla mikrofoner:`Circular6+1` |
   |          |         | För ett cirkulärt utvecklingspaket som använder fyra mikrofoner:`Circular3+1` |
   |          |         | För ett linjärt utvecklingspaket som använder alla mikrofoner:`Linear4` |
   |          |         | För ett linjärt utvecklingspaket som använder två mikrofoner:`Linear2` |

1. Om du vill skapa programmet väljer du **Kör "app"** på **Kör-menyn.** Dialogrutan **Välj distributionsmål** visas.

1. Välj enheten och välj sedan **OK** för att distribuera programmet till enheten.

   ![Dialogrutan Välj distributionsmål](../media/speech-devices-sdk/qsg-7.png)

1. Exempelprogrammet Talenheter SDK startar och visar följande alternativ:

   ![Exempel på SDK-exempelprogram och alternativ för talenheter](../media/speech-devices-sdk/qsg-8.png)

1. Prova den nya demonstrationen för konversationsavskrifter. Börja transkribera med "Starta session". Som standard är alla gäst. Men om du har deltagarens röstsignaturer kan `/video/participants.properties` de placeras i en fil på enheten. Om du vill generera röstsignaturen tittar du på [Transkribera konversationer (SDK).](../how-to-use-conversation-transcription-service.md)

   ![Demo Konversation Transkription ansökan](../media/speech-devices-sdk/qsg-15.png)

1. Experiment!

## <a name="troubleshooting"></a>Felsökning

Om du inte kan ansluta till talenheten. Skriv följande kommando i ett kommandotolksfönster. Det kommer att returnera en lista över enheter:

```powershell
 adb devices
```

> [!NOTE]
> Det här kommandot använder Android `adb.exe`Debug Bridge, som är en del av Android Studio-installationen. Det här verktyget finns i\[C:\Användares användarnamn]\AppData\Local\Android\Sdk\platform-tools. Du kan lägga till den här katalogen i `adb`sökvägen så att det blir bekvämare att anropa . Annars måste du ange den fullständiga sökvägen till installationen av adb.exe i varje kommando som anropar `adb`.
>
> Om du ser `no devices/emulators found` ett fel kontrollerar du att USB-kabeln är ansluten och se till att en kabel av hög kvalitet används.
