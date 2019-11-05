---
title: Installera och köra behållare – Translator Text
titleSuffix: Azure Cognitive Services
description: Hur du hämtar, installerar och kör behållare för Translator Text Analytics i den här själv studie kursen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507884"
---
# <a name="install-and-run-translator-text-containers"></a>Installera och kör Translator Text behållare

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Med behållare kan du köra Translator Text-API: er i din egen miljö och är utmärkta för specifika säkerhets-och data styrnings krav.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Translator Text behållare:

|Krävs|Syfte|
|--|--|
|Docker-motor| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). För en introduktion till Docker-och container-grunderna, se [Docker-översikten](https://docs.docker.com/engine/docker-overview/).<br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker`-kommandon.|

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Du måste först fylla i och skicka in [kognitiva Translator text behållar formuläret](https://aka.ms/translatorcontainerform) för att begära åtkomst till behållaren.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>Värddatorn

Värden är en x64-baserad dator med ett Linux-operativsystem som kör Docker-behållaren. Det kan vara en dator på din lokala dator eller en Docker-värd tjänst i Azure, till exempel:

* Azure Kubernetes-tjänsten.
* Azure Container Instances.
* Ett [Kubernetes](https://kubernetes.io/) -kluster distribuerat till Azure Stack.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade processor kärnorna, minst 2,6 gigahertz (GHz) eller snabbare, och minne, i gigabyte (GB), för att allokera för varje Translator Text behållare.

| Container | Minimum | Språk par |
|-----------|---------|---------------|
| Translator för textöversättning | 4 kärnor, 4 GB minne | 4 |

För varje språk par rekommenderar vi att du har 1 GB minne. Som standard har Translator Text container 3 eller 4 språk par, beroende på `<image-tag>` du kör. Se [språk som stöds och översättning](#supported-languages-and-translation) för information. Kärnan och minnet motsvarar `--cpus` och `--memory` inställningar som används som en del av `docker run` kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Behållar avbildningar för Translator Text är tillgängliga i följande behållar lagrings plats. Tabellen mappar också behållar avbildnings taggarna och deras motsvarande språk som stöds.

| Container | Bildtagg | Språk som stöds |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE`och `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`och `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Docker-hämtning för Translator Text container

För att utföra kommandot [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) måste du först ha åtkomst till behållar registret. Från Azure CLI kan du logga in på Azure Container Registry med hjälp av [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) kommandot.

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Det här kommandot autentiserar den aktuella användaren med motsvarande Azure Container Registry. Nu är du kostnads fri att köra kommandot `docker pull`.

> [!NOTE]
> Ange motsvarande `<image-tag>`beroende på vilket språk stöd du behöver.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run). Fler [exempel](translator-text-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens översättnings slut punkt](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra någon av de tre behållarna. [Exempel](translator-text-container-config.md#example-docker-run-commands) på kommandot `docker run` är tillgängliga.

### <a name="run-container-example-of-docker-run-command"></a>Kör container-exempel för kommandot Docker Run

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Det här kommandot:

* Kör en Translator Text behållare från behållar avbildningen
* Allokerar 4 processor kärnor och 4 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Accepterar slut användar avtalet (EULA)
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn

### <a name="how-to-collect-docker-logs"></a>Samla in Docker-loggar

I fel söknings syfte kan det vara användbart att Visa Docker-loggar från behållarens körning. Kör först kommandot [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) med formaterings flaggan för att begränsa den information som visas för alla behållare.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Kör sedan kommandot [Docker-loggar](https://docs.docker.com/engine/reference/commandline/logs/) med `<Container ID>` för motsvarande behållare i fråga för att visa dess loggar.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

Kommandot Docker-loggar ovan samlar in fel loggarna för de senaste fyra timmarna.

## <a name="supported-languages-and-translation"></a>Språk och översättning som stöds

Metoden `POST /translate` stöder följande språk konverteringar, som flyttas från *engelska* till ett mål språk och vice versa. Observera att du kan gå till och från engelska med ett av de språk som anges i listan, men du *kan inte* gå *från ett annat språk till* ett annat språk *än* engelska.

> [!NOTE]
> För optimal kvalitet bör konsumenterna bara skicka en mening per begäran.

| Språk konvertering | Språk ISO-konvertering | Bildtaggar |
|--|--|:--|
| Engelska: left_right_arrow: kinesiska | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Engelska: left_right_arrow: ryska | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Engelska: left_right_arrow: arabiska | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Engelska: left_right_arrow: tyska | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` och `de_en_es_fr_1.0.0` |
| Engelska: left_right_arrow: spanska | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Engelska: left_right_arrow: franska | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> `Eula` måste anges för att köra behållaren. annars startar inte behållaren.

## <a name="query-the-containers-translate-endpoint"></a>Fråga behållarens översättnings slut punkt

Behållaren tillhandahåller ett REST-baserat API för översättnings slut punkt. Det finns flera exempel på användning av slut punkten i följande exempel:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Kör följande spiral kommando från önskad CLI.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Om du försöker använda den här spiral posten innan behållaren är klar, får du svaret "tjänsten är tillfälligt otillgänglig". Vänta bara tills behållaren är klar och försök sedan igen.

Följande utdata kommer att skrivas ut till-konsolen.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Gå till sidan Swagger http://localhost:5000/swagger/index.html

1. Välj **post/translate**
1. Välj **prova**
1. Ange parametern **från** som `en-US`
1. Ange parametern **till** som `de-DE`
1. Ange **API-version-** parametern som `3.0`
1. Under **texter**ersätter du `string` med följande JSON
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Välj **Kör**, de resulterande översättningarna matas ut i **svars texten**. Du bör förvänta dig något liknande följande:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Starta Visual Studio och skapa ett nytt konsol program.
1. Redigera `*.csproj`-filen för att lägga till noden `<LangVersion>7.1</LangVersion>` – detta C# anger 7,1.
1. Lägg till [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet-paketet, version 11.0.2.
1. I `Program.cs`ersätter du all befintlig kod med följande:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Tryck på **F5** för att köra programmet.
1. Följande utdata kommer att skrivas ut till-konsolen.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](translator-text-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Translator Text behållare. Sammanfattningsvis:

* Translator Text innehåller flera Linux-behållare för Docker, som kapslar in olika språk par.
* Behållar avbildningar hämtas från "container Preview"-registret.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Translator Text behållare genom att ange behållarens värd-URI.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](translator-text-container-config.md) för konfigurations inställningar
* Se [vanliga frågor och svar om behållare](../containers/container-faq.md) för att lösa problem som rör funktioner.
