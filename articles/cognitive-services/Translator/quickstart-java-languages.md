---
title: 'Snabbstart: Hämta lista över språk som stöds- Java – Translator Text-API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten hämtar du en lista över språk som stöds för översättning, translitterering och ordlistesökningar med hjälp av Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 4b5c89d6d2066af3978845676a42a6c39ba3659e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705564"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-java"></a>Snabbstart: Använd Translator Text API för att hämta en lista över språk som stöds med Java

I den här snabbstarten hämtar du en lista över språk som stöds för översättning, translitterering och ordlistesökningar med hjälp av Translator Text API.

>[!TIP]
> Om du vill att all kod på samma gång källkoden för det här exemplet är tillgänglig i [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Java).

## <a name="prerequisites"></a>Förutsättningar

* [JDK 7 eller senare](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)

## <a name="initialize-a-project-with-gradle"></a>Initiera ett projekt med Gradle

Vi börjar med att skapa en arbetskatalog för projektet. Kör följande kommando på kommandoraden (eller i terminalen):

```console
mkdir get-languages-sample
cd get-languages-sample
```

Sedan initierar du ett Gradle-projekt. Det här kommandot skapar viktiga build-filer för Gradle, framför allt `build.gradle.kts`, som används vid körning för att skapa och konfigurera programmet. Kör kommandot från arbetskatalogen:

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurera build-filen

Leta upp `build.gradle.kts` och öppna den med valfri IDE eller textredigerare. Kopiera sedan i den här build-konfigurationen:

```java
plugins {
    java
    application
}
application {
    mainClassName = "GetLanguages"
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
mkdir -p src/main/java
```

Skapa sedan en fil med namnet `GetLanguages.java` i den här mappen.

## <a name="import-required-libraries"></a>Importera obligatoriska bibliotek

Öppna `GetLanguages.java` och lägg till följande importinstruktioner:

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
public class GetLanguages {
  // All project code goes here...
}
```

Lägg till följande rader i klassen `GetLanguages`:

```java
String url = "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0";
```

Om du använder en flera tjänster Cognitive Services-prenumeration, måste du också inkludera den `Ocp-Apim-Subscription-Region` i dina begäranparametrar. [Mer information om autentisering med flera tjänster prenumerationen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Skapa en klient och en begäran

Lägg till följande rad i klassen `GetLanguages` för att instansiera `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Nu ska vi skapa `GET`-begäran.

```java
// This function performs a GET request.
public String Get() throws IOException {
    Request request = new Request.Builder()
            .url(url).get()
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
        GetLanguages getLanguagesRequest = new GetLanguages();
        String response = getLanguagesRequest.Get();
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

Hitta en förkortning på land/region i den här [Språklista](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referens för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Se också

* [Översätta text](quickstart-java-translate.md)
* [Translitterera text](quickstart-java-transliterate.md)
* [Identifiera språket efter indata](quickstart-java-detect.md)
* [Hämta alternativa översättningar](quickstart-java-dictionary.md)
* [Fastställa meningslängd utifrån indata](quickstart-java-sentences.md)
