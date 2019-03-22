---
title: 'Snabbstart: Konvertera textskript, Java – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig hur du translittererar (konverterar) text från ett skript till ett annat med hjälp av Java och Translator Text REST API. I det här exemplet translittereras japanska till det latinska alfabetet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 58821492c6972eeef622f8dc8a777f8da70acdf1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895931"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>Snabbstart: Använda Translator Text API till att transkribera text med hjälp av Java

I den här snabbstarten lär du dig hur du translittererar (konverterar) text från ett skript till ett annat med hjälp av Java och Translator Text REST API. I det angivna exemplet translittereras japanska till det latinska alfabetet.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [JDK 7 eller senare](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* En Azure-prenumerationsnyckel för Translator Text

## <a name="initialize-a-project-with-gradle"></a>Initiera ett projekt med Gradle

Vi börjar med att skapa en arbetskatalog för projektet. Kör följande kommando på kommandoraden (eller i terminalen):

```console
mkdir transliterate-sample
cd transliterate-sample
```

Sedan initierar du ett Gradle-projekt. Det här kommandot skapar viktiga build-filer för Gradle, framför allt `build.gradle.kts`, som används vid körning för att skapa och konfigurera programmet. Kör kommandot från arbetskatalogen:

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurera build-filen

Leta upp `build.gradle.kts` och öppna den med valfri IDE eller textredigerare. Kopiera sedan i den här build-konfigurationen:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Observera att det här exemplet har beroenden på OkHttp för HTTP-begäranden och Gson för att hantera och parsa JSON. Mer information om build-konfigurationer finns i avsnittet om att [skapa nya Gradle-byggen](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Skapa en Java-fil

Nu skapar vi en mapp för din exempelapp. Kör följande från arbetskatalogen:

```console
mkdir -p src\main\java
```

Skapa sedan en fil med namnet `Transliterate.java` i den här mappen.

## <a name="import-required-libraries"></a>Importera obligatoriska bibliotek

Öppna `Transliterate.java` och lägg till följande importinstruktioner:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definiera variabler

Först behöver du skapa en offentlig klass för projektet:

```java
public class Transliterate {
  // All project code goes here...
}
```

Lägg till följande rader i klassen `Transliterate`. Du ser då att `api-version` samt två ytterligare parametrar har lags till i `url`. De här parametrarna används för att ange indataspråket och skripten för translitteration. I det här exemplet är det inställt på japanska (`jpan`) och latin (`latn`). Kom ihåg att uppdatera prenumerationsnyckelvärdet.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```

## <a name="create-a-client-and-build-a-request"></a>Skapa en klient och en begäran

Lägg till följande rad i klassen `Transliterate` för att instansiera `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Nu skapar vi POST-begäran. Du kan ändra texten för translitteration om du vill.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Skapa en funktion för att parsa svaret

Den här enkla funktionen parsar och gör JSON-svaret prydligare från Translator Text-tjänsten.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att göra en begäran och få ett svar. Lägg till följande rader i projektet:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Navigera till roten för arbetskatalogen på kommandoraden (eller i terminalsessionen) och kör:

```console
gradle build
```

När bygget är klart kör du:

```console
gradle run
```

## <a name="sample-response"></a>Exempelsvar

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och språkidentifiering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Se även

* [Översätta text](quickstart-java-translate.md)
* [Identifiera språket efter indata](quickstart-java-detect.md)
* [Hämta alternativa översättningar](quickstart-java-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-java-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-java-sentences.md)
