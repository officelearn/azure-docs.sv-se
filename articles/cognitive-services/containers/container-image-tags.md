---
title: Taggar för Cognitive Services-containeravbildningar
titleSuffix: Azure Cognitive Services
description: En omfattande lista över alla avbildnings taggar för kognitiva tjänst behållare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412552"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Bild taggar för Azure Cognitive Services container och viktig information

Azure Cognitive Services erbjuder många behållar avbildningar. Behållar registren och motsvarande databaser varierar mellan behållar avbildningar. Varje behållar avbildnings namn innehåller flera taggar. En behållar avbildnings tag är en mekanism för version av behållar avbildningen. Den här artikeln är avsedd att användas som en omfattande referens för att visa alla Cognitive Services behållar avbildningar och de tillgängliga taggarna.

> [!TIP]
> När du använder ska du vara noga med att tänka på [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) SKIFT läge för behållar registret, databasen, behållar avbildningens namn och motsvarande tagg – eftersom de är **SKIFT läges känsliga**.

## <a name="anomaly-detector"></a>Avvikelseidentifiering

Behållar avbildningen för [avvikelse detektor][ad-containers] finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/decision` lagrings platsen och får namnet `anomaly-detector` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Läs OCR (optisk tecken läsning)

Du hittar [visuellt innehåll][cv-containers] Read OCR container-avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `read` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

Viktig information för `v2.0.013250001-amd64-preview` :

* Öka minnes användningen ytterligare för container.
* Extern cache krävs för installation av multi-poddar. Du kan till exempel konfigurera Redis för cachelagring.
* Korrigerade saknade resultat när Redis cache har kon figurer ATS och `ResultExpirationPeriod` är inställt på 0.
* Ta bort begär ande text storleks begränsning för 26MB. Container kan nu acceptera >26MB-filer.
* Lägg till en tidstämpel och build-version till konsol loggning.

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

Viktig information för `1.1.013050001-amd64-preview`

* `ReadEngineConfig:ResultExpirationPeriod`Initierings konfiguration för behållare har lagts till för att ange när systemet ska rensa igenkännings resultat. 
    * Inställningen är i timmar och standardvärdet är 48 timmar.
    * Inställningen kan minska minnes användningen för lagring av resultat, särskilt när behållare i minnet används.
    * Exempel 1. ReadEngineConfig: ResultExpirationPeriod = 1 rensar systemet igenkännings resultatet 1 tim efter processen.
    * Om den här konfigurationen är inställd på 0 rensas igenkännings resultatet av systemet när resultatet har hämtats.

* Ett internt Server fel 500 har åtgärd ATS när ett ogiltigt bild format skickas till systemet. Det kommer nu att returnera ett 400-fel:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Formigenkänning

[Formulär igenkännings][fr-containers] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/custom-form` lagrings platsen och får namnet `labeltool` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[Luis][lu-containers] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/language` lagrings platsen och får namnet `luis` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech till text

Du hittar en behållar avbildning från [Custom Speech till text][sp-cstt] i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `custom-speech-to-text` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `2.6.0-amd64` :

**Funktioner**
* Stöd för phraselist v2 
* Fras listorna stöds i följande språk:
    * en – au
    * en-ca
    * en-GB
    * en-in
    * en-NZ
    * sv-se
    * zh-cn
* Stöd för nedladdning av anpassad bas modell. 
    * Använd `BaseModelLocale=<locale>` med `docker run` kommandot
* Fullständigt migrerat till .NET 3,1

**Fixe**
* Åtgärdade ett problem där förtroende poängen var alltid 1 i Diarization-läge
* Migrerat till TextAnalytics 3,0-API: et

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat.

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `2.5.0-amd64` :

**Funktioner**
* Stöd för anpassat uttal av anpassade modeller
* Stöd för Azure och Azures Azures myndighets moln

**Fixe**
* Åtgärda kör som-problem som inte är rot användare i Diarization-läge

| Bildtaggar                    | Kommentarer               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   första GA-versionen    |

---

## <a name="custom-text-to-speech"></a>Anpassad text till tal

Du hittar den [anpassade text till tal-][sp-ctts] behållar avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `custom-text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `1.8.0-amd64` :

**Funktioner**
* Fullständigt migrerat till .NET 3,1

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `1.7.0-amd64` :

**Funktion**
* Delvis migrerad till .NET 3,1

| Bildtaggar                    | Kommentarer               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   första GA-versionen    |

---

## <a name="speech-to-text"></a>Tal till text

Du hittar en behållar avbildning från [tal till text][sp-stt] i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `speech-to-text` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Du kan hitta en fullständig lista över [taggar på MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Eftersom tal-till-text v-2.5.0 stöds stöds bilder i regionen *USA, amerikanska myndigheter* . Använd den *amerikanska regeringens* fakturerings slut punkt och API-nycklar när du använder den här regionen.

# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `2.6.0-amd64-<locale>` :

**Funktioner**
* Uppgraderas till de senaste modellerna och migreras fullständigt till .NET 3,1
* Stöd för phraselist v2
* Fras listorna stöds i följande språk:
    * en – au
    * en-ca
    * en-GB
    * en-in
    * en-NZ
    * sv-se
    * zh-cn
* Stöd för nya nationella inställningar `cs-CZ` 
    * Versaler och skiljetecken stöds inte för närvarande.

**Fixe**
* Åtgärdar ett problem där Tillförlitlighets poängen alltid är 1 i Diarization-läge
* Migrerad Använd TextAnalytics 3,0-API: et

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat. 

| Bildtaggar                    | Kommentarer                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Behållar avbildning med `en-US` språkvarianten.                                                             |
| `2.6.0-amd64-<locale>`        | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `2.6.0-amd64-en-us`. |

Den här behållaren har följande tillgängliga språk.

| Språk för v-2.6.0           | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Behållar avbildning med `ar-AE` språkvarianten. |
| `ar-eg`                     | Behållar avbildning med `ar-EG` språkvarianten. |
| `ar-kw`                     | Behållar avbildning med `ar-KW` språkvarianten. |
| `ar-qa`                     | Behållar avbildning med `ar-QA` språkvarianten. |
| `ar-sa`                     | Behållar avbildning med `ar-SA` språkvarianten. |
| `ca-es`                     | Behållar avbildning med `ca-ES` språkvarianten. |
| `cs-cz`                     | Behållar avbildning med `cs-CZ` språkvarianten. |
| `da-dk`                     | Behållar avbildning med `da-DK` språkvarianten. |
| `de-de`                     | Behållar avbildning med `de-DE` språkvarianten. |
| `en-au`                     | Behållar avbildning med `en-AU` språkvarianten. |
| `en-ca`                     | Behållar avbildning med `en-CA` språkvarianten. |
| `en-gb`                     | Behållar avbildning med `en-GB` språkvarianten. |
| `en-in`                     | Behållar avbildning med `en-IN` språkvarianten. |
| `en-nz`                     | Behållar avbildning med `en-NZ` språkvarianten. |
| `en-us`                     | Behållar avbildning med `en-US` språkvarianten. |
| `es-es`                     | Behållar avbildning med `es-ES` språkvarianten. |
| `es-mx`                     | Behållar avbildning med `es-MX` språkvarianten. |
| `fi-fi`                     | Behållar avbildning med `fi-FI` språkvarianten. |
| `fr-ca`                     | Behållar avbildning med `fr-CA` språkvarianten. |
| `fr-fr`                     | Behållar avbildning med `fr-FR` språkvarianten. |
| `gu-in`                     | Behållar avbildning med `gu-IN` språkvarianten. |
| `hi-in`                     | Behållar avbildning med `hi-IN` språkvarianten. |
| `it-it`                     | Behållar avbildning med `it-IT` språkvarianten. |
| `ja-jp`                     | Behållar avbildning med `ja-JP` språkvarianten. |
| `ko-kr`                     | Behållar avbildning med `ko-KR` språkvarianten. |
| `mr-in`                     | Behållar avbildning med `mr-IN` språkvarianten. |
| `nb-no`                     | Behållar avbildning med `nb-NO` språkvarianten. |
| `nl-nl`                     | Behållar avbildning med `nl-NL` språkvarianten. |
| `pl-pl`                     | Behållar avbildning med `pl-PL` språkvarianten. |
| `pt-br`                     | Behållar avbildning med `pt-BR` språkvarianten. |
| `pt-pt`                     | Behållar avbildning med `pt-PT` språkvarianten. |
| `ru-ru`                     | Behållar avbildning med `ru-RU` språkvarianten. |
| `sv-se`                     | Behållar avbildning med `sv-SE` språkvarianten. |
| `ta-in`                     | Behållar avbildning med `ta-IN` språkvarianten. |
| `te-in`                     | Behållar avbildning med `te-IN` språkvarianten. |
| `th-th`                     | Behållar avbildning med `th-TH` språkvarianten. |
| `tr-tr`                     | Behållar avbildning med `tr-TR` språkvarianten. |
| `zh-cn`                     | Behållar avbildning med `zh-CN` språkvarianten. |
| `zh-hk`                     | Behållar avbildning med `zh-HK` språkvarianten. |
| `zh-tw`                     | Behållar avbildning med `zh-TW` språkvarianten. |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `2.5.0-amd64-<locale>` :

**Funktioner**
* Stöd för Azures moln för amerikanska myndigheter

**Fixe**
* Åtgärdar ett problem med att köra som en icke-rot användare i Diarization-läge

| Bildtaggar                  | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `2.5.0-amd64-en-us`.  |

Den här behållaren har följande tillgängliga språk.

| Språk för v-2.5.0           | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Behållar avbildning med `ar-AE` språkvarianten. |
| `ar-eg`                     | Behållar avbildning med `ar-EG` språkvarianten. |
| `ar-kw`                     | Behållar avbildning med `ar-KW` språkvarianten. |
| `ar-qa`                     | Behållar avbildning med `ar-QA` språkvarianten. |
| `ar-sa`                     | Behållar avbildning med `ar-SA` språkvarianten. |
| `ca-es`                     | Behållar avbildning med `ca-ES` språkvarianten. |
| `da-dk`                     | Behållar avbildning med `da-DK` språkvarianten. |
| `de-de`                     | Behållar avbildning med `de-DE` språkvarianten. |
| `en-au`                     | Behållar avbildning med `en-AU` språkvarianten. |
| `en-ca`                     | Behållar avbildning med `en-CA` språkvarianten. |
| `en-gb`                     | Behållar avbildning med `en-GB` språkvarianten. |
| `en-in`                     | Behållar avbildning med `en-IN` språkvarianten. |
| `en-nz`                     | Behållar avbildning med `en-NZ` språkvarianten. |
| `en-us`                     | Behållar avbildning med `en-US` språkvarianten. |
| `es-es`                     | Behållar avbildning med `es-ES` språkvarianten. |
| `es-mx`                     | Behållar avbildning med `es-MX` språkvarianten. |
| `fi-fi`                     | Behållar avbildning med `fi-FI` språkvarianten. |
| `fr-ca`                     | Behållar avbildning med `fr-CA` språkvarianten. |
| `fr-fr`                     | Behållar avbildning med `fr-FR` språkvarianten. |
| `gu-in`                     | Behållar avbildning med `gu-IN` språkvarianten. |
| `hi-in`                     | Behållar avbildning med `hi-IN` språkvarianten. |
| `it-it`                     | Behållar avbildning med `it-IT` språkvarianten. |
| `ja-jp`                     | Behållar avbildning med `ja-JP` språkvarianten. |
| `ko-kr`                     | Behållar avbildning med `ko-KR` språkvarianten. |
| `mr-in`                     | Behållar avbildning med `mr-IN` språkvarianten. |
| `nb-no`                     | Behållar avbildning med `nb-NO` språkvarianten. |
| `nl-nl`                     | Behållar avbildning med `nl-NL` språkvarianten. |
| `pl-pl`                     | Behållar avbildning med `pl-PL` språkvarianten. |
| `pt-br`                     | Behållar avbildning med `pt-BR` språkvarianten. |
| `pt-pt`                     | Behållar avbildning med `pt-PT` språkvarianten. |
| `ru-ru`                     | Behållar avbildning med `ru-RU` språkvarianten. |
| `sv-se`                     | Behållar avbildning med `sv-SE` språkvarianten. |
| `ta-in`                     | Behållar avbildning med `ta-IN` språkvarianten. |
| `te-in`                     | Behållar avbildning med `te-IN` språkvarianten. |
| `th-th`                     | Behållar avbildning med `th-TH` språkvarianten. |
| `tr-tr`                     | Behållar avbildning med `tr-TR` språkvarianten. |
| `zh-cn`                     | Behållar avbildning med `zh-CN` språkvarianten. |
| `zh-hk`                     | Behållar avbildning med `zh-HK` språkvarianten. |
| `zh-tw`                     | Behållar avbildning med `zh-TW` språkvarianten. |

---

## <a name="text-to-speech"></a>Text till tal

Det går att hitta [text till tal-][sp-tts] behållar avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `1.8.0-amd64-<locale-and-voice>` :

**Funktion**
* Fullständigt migrerat till .NET 3,1

| Bildtaggar                                  | Kommentarer                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.8.0-amd64-en-us-ariarus`.  |


| Språk för v-1.8.0                          | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            |
| `ar-sa-naayf`                               | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           |
| `bg-bg-ivan`                                | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            |
| `ca-es-herenarus`                           | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       |
| `cs-cz-jakub`                               | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           |
| `da-dk-hellerus`                            | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        |
| `de-at-michael`                             | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         |
| `de-ch-karsten`                             | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         |
| `de-de-hedda`                               | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-heddarus`                            | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-stefan-apollo`                       | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `el-gr-stefanos`                            | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        |
| `en-au-catherine`                           | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `en-au-hayleyrus`                           | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `en-ca-heatherrus`                          | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      |
| `en-ca-linda`                               | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           |
| `en-gb-george-apollo`                       | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `en-gb-hazelrus`                            | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `en-gb-susan-apollo`                        | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `en-ie-sean`                                | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            |
| `en-in-heera-apollo`                        | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `en-in-priyarus`                            | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `en-in-ravi-apollo`                         | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `en-us-benjaminrus`                         | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `en-us-guy24krus`                           | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `en-us-aria24krus`                          | Behållar avbildning med `en-US` språket och `en-US-Aria24kRUS` rösten.      |
| `en-us-ariarus`                             | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.         |
| `en-us-zirarus`                             | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `es-es-helenarus`                           | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `es-es-laura-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `es-es-pablo-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `es-mx-hildarus`                            | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `es-mx-raul-apollo`                         | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `fi-fi-heidirus`                            | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        |
| `fr-ca-caroline`                            | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `fr-ca-harmonierus`                         | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `fr-ch-guillaume`                           | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       |
| `fr-fr-hortenserus`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `fr-fr-julie-apollo`                        | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `fr-fr-paul-apollo`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `he-il-asaf`                                | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            |
| `hi-in-hemant`                              | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          |
| `hi-in-kalpana-apollo`                      | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  |
| `hi-in-kalpana`                             | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         |
| `hr-hr-matej`                               | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           |
| `hu-hu-szabolcs`                            | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        |
| `id-id-andika`                              | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          |
| `it-it-cosimo-apollo`                       | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `it-it-luciarus`                            | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `ja-jp-ayumi-apollo`                        | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `ja-jp-harukarus`                           | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `ja-jp-ichiro-apollo`                       | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `ko-kr-heamirus`                            | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `ms-my-rizwan`                              | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          |
| `nb-no-huldarus`                            | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        |
| `nl-nl-hannarus`                            | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        |
| `pl-pl-paulinarus`                          | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      |
| `pt-br-daniel-apollo`                       | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `pt-br-heloisarus`                          | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `pt-pt-heliarus`                            | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        |
| `ro-ro-andrei`                              | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          |
| `ru-ru-ekaterinarus`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    |
| `ru-ru-irina-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    |
| `ru-ru-pavel-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    |
| `sk-sk-filip`                               | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           |
| `sl-si-lado`                                | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            |
| `sv-se-hedvigrus`                           | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       |
| `ta-in-valluvar`                            | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        |
| `te-in-chitra`                              | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          |
| `th-th-pattara`                             | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         |
| `tr-tr-sedarus`                             | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         |
| `vi-vn-an`                                  | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              |
| `zh-cn-huihuirus`                           | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `zh-cn-kangkang-apollo`                     | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `zh-cn-yaoyao-apollo`                       | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `zh-hk-danny-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    |
| `zh-hk-tracy-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    |
| `zh-hk-tracyrus`                            | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        |
| `zh-tw-hanhanrus`                           | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       |
| `zh-tw-yating-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   |
| `zh-tw-zhiwei-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `1.7.0-amd64-<locale-and-voice>` :

**Funktion**
* Uppgraderade komponenter till .NET 3,1

| Bildtaggar                                  | Kommentarer                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | första GA-versionen. Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.7.0-amd64-en-us-ariarus`.  |


| Språk för v-1.7.0                          | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            |
| `ar-sa-naayf`                               | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           |
| `bg-bg-ivan`                                | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            |
| `ca-es-herenarus`                           | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       |
| `cs-cz-jakub`                               | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           |
| `da-dk-hellerus`                            | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        |
| `de-at-michael`                             | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         |
| `de-ch-karsten`                             | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         |
| `de-de-hedda`                               | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-heddarus`                            | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-stefan-apollo`                       | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `el-gr-stefanos`                            | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        |
| `en-au-catherine`                           | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `en-au-hayleyrus`                           | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `en-ca-heatherrus`                          | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      |
| `en-ca-linda`                               | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           |
| `en-gb-george-apollo`                       | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `en-gb-hazelrus`                            | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `en-gb-susan-apollo`                        | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `en-ie-sean`                                | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            |
| `en-in-heera-apollo`                        | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `en-in-priyarus`                            | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `en-in-ravi-apollo`                         | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `en-us-benjaminrus`                         | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `en-us-guy24krus`                           | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `en-us-aria24krus`                          | Behållar avbildning med `en-US` språket och `en-US-Aria24kRUS` rösten.      |
| `en-us-ariarus`                             | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.         |
| `en-us-zirarus`                             | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `es-es-helenarus`                           | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `es-es-laura-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `es-es-pablo-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `es-mx-hildarus`                            | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `es-mx-raul-apollo`                         | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `fi-fi-heidirus`                            | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        |
| `fr-ca-caroline`                            | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `fr-ca-harmonierus`                         | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `fr-ch-guillaume`                           | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       |
| `fr-fr-hortenserus`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `fr-fr-julie-apollo`                        | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `fr-fr-paul-apollo`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `he-il-asaf`                                | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            |
| `hi-in-hemant`                              | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          |
| `hi-in-kalpana-apollo`                      | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  |
| `hi-in-kalpana`                             | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         |
| `hr-hr-matej`                               | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           |
| `hu-hu-szabolcs`                            | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        |
| `id-id-andika`                              | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          |
| `it-it-cosimo-apollo`                       | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `it-it-luciarus`                            | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `ja-jp-ayumi-apollo`                        | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `ja-jp-harukarus`                           | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `ja-jp-ichiro-apollo`                       | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `ko-kr-heamirus`                            | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `ms-my-rizwan`                              | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          |
| `nb-no-huldarus`                            | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        |
| `nl-nl-hannarus`                            | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        |
| `pl-pl-paulinarus`                          | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      |
| `pt-br-daniel-apollo`                       | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `pt-br-heloisarus`                          | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `pt-pt-heliarus`                            | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        |
| `ro-ro-andrei`                              | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          |
| `ru-ru-ekaterinarus`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    |
| `ru-ru-irina-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    |
| `ru-ru-pavel-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    |
| `sk-sk-filip`                               | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           |
| `sl-si-lado`                                | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            |
| `sv-se-hedvigrus`                           | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       |
| `ta-in-valluvar`                            | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        |
| `te-in-chitra`                              | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          |
| `th-th-pattara`                             | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         |
| `tr-tr-sedarus`                             | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         |
| `vi-vn-an`                                  | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              |
| `zh-cn-huihuirus`                           | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `zh-cn-kangkang-apollo`                     | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `zh-cn-yaoyao-apollo`                       | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `zh-hk-danny-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    |
| `zh-hk-tracy-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    |
| `zh-hk-tracyrus`                            | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        |
| `zh-tw-hanhanrus`                           | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       |
| `zh-tw-yating-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   |
| `zh-tw-zhiwei-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   |

---

## <a name="neural-text-to-speech"></a>Neurala text till tal

Du hittar [neurala text till tal-][sp-ntts] behållar avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `neural-text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).

| Bildtaggar                                  | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.2.0-amd64-en-us-arianeural-preview`. |


| v-1.2.0 för hands versioner och röster           | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `de-de-katjaneural-preview`                 | Behållar avbildning med `de-DE` språket och `de-DE-KatjaNeural` rösten.     |
| `en-au-natashaneural-preview`               | Behållar avbildning med `en-AU` språket och `en-AU-NatashaNeural` rösten.   |
| `en-ca-claraneural-preview`                 | Behållar avbildning med `en-CA` språket och `en-CA-ClaraNeural` rösten.     |
| `en-gb-libbyneural-preview`                 | Behållar avbildning med `en-GB` språket och `en-GB-LibbyNeural` rösten.     |
| `en-gb-mianeural-preview`                   | Behållar avbildning med `en-GB` språket och `en-GB-MiaNeural` rösten.       |
| `en-us-arianeural-preview`                  | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `en-us-guyneural-preview`                   | Behållar avbildning med `en-US` språket och `en-US-GuyNeural` rösten.       |
| `es-es-elviraneural-preview`                | Behållar avbildning med `es-ES` språket och `es-ES-ElviraNeural` rösten.    |
| `es-mx-dalianeural-preview`                 | Behållar avbildning med `es-MX` språket och `es-MX-DaliaNeural` rösten.     |
| `fr-ca-sylvieneural-preview`                | Behållar avbildning med `fr-CA` språket och `fr-CA-SylvieNeural` rösten.    |
| `fr-fr-deniseneural-preview`                | Behållar avbildning med `fr-FR` språket och `fr-FR-DeniseNeural` rösten.    |
| `it-it-elsaneural-preview`                  | Behållar avbildning med `it-IT` språket och `it-IT-ElsaNeural` rösten.      |
| `ja-jp-nanamineural-preview`                | Behållar avbildning med `ja-JP` språket och `ja-JP-NanamiNeural` rösten.    |
| `ko-kr-sunhineural-preview`                 | Behållar avbildning med `ko-KR` språket och `ko-KR-SunHiNeural` rösten.     |
| `pt-br-franciscaneural-preview`             | Behållar avbildning med `pt-BR` språket och `pt-BR-FranciscaNeural` rösten. |
| `zh-cn-xiaoxiaoneural-preview`              | Behållar avbildning med `zh-CN` språket och `zh-CN-XiaoxiaoNeural` rösten.  |

## <a name="speech-language-detection"></a>Språkidentifiering

Behållar avbildningen av [tal språk identifiering][sp-lid] finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `language-detection` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Bildtaggar                                  | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/textanalytics/` lagrings platsen och får namnet `keyphrase` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)


| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Identifiering av språk i text

[Språkidentifiering][ta-la] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/textanalytics/` lagrings platsen och får namnet `language` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Senaste versionerna](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)


| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Sentimentanalys

[Attitydanalys][ta-se] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/textanalytics/` lagrings platsen och får namnet `sentiment` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Bildtaggar | Kommentarer                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Attitydanalys v3 (engelska)               |
| `3.0-es`   | Attitydanalys v3 (spanska)               |
| `3.0-fr`   | Attitydanalys v3 (franska)                |
| `3.0-it`   | Attitydanalys v3 (italiensk)               |
| `3.0-de`   | Attitydanalys v3 (tyska)                |
| `3.0-zh`   | Attitydanalys v3 (kinesiskt-förenklat)  |
| `3.0-zht`  | Attitydanalys v3 (kinesiska – traditionell) |
| `3.0-ja`   | Attitydanalys v3 (japanska)              |
| `3.0-pt`   | Attitydanalys v3 (portugisiska)            |
| `3.0-nl`   | Attitydanalys v3 (nederländska)                 |
| `2.1`    | Attitydanalys v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
