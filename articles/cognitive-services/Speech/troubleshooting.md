---
title: Felsöka Taligenkänning i Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Lösa problem när du använder Taligenkänning i Bing.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1213045867f84efd6d77aebff12b8c8725d82d08
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965570"
---
# <a name="troubleshooting-bing-speech"></a>Felsöka Taligenkänning i Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Fels`HTTP 403 Forbidden`

När du använder API: t för tal igenkänning `HTTP 403 Forbidden` returneras ett fel.

### <a name="cause"></a>Orsak

Det här felet orsakas ofta av autentiseringsproblem. Anslutnings begär Anden utan `Ocp-Apim-Subscription-Key` giltig `Authorization` eller rubrik avvisas av tjänsten med `HTTP 403 Forbidden` ett svar.

Om du använder en prenumerations nyckel för autentisering kan orsaken vara att

- Prenumerations nyckeln saknas eller är ogiltig
- användnings kvoten för prenumerations nyckeln har överskridits
- `Ocp-Apim-Subscription-Key` fältet har inte angetts i begär ande huvudet när REST API anropas

Om du använder autentiseringstoken för autentisering kan följande orsaker orsaka felet.

- `Authorization` rubriken saknas i begäran när rest används
- den autentiseringstoken som anges i Authorization-huvudet är ogiltig
- autentiseringstoken har upphört att gälla. Åtkomsttoken har ett förfallo datum på 10 minuter

Mer information om autentisering finns på sidan [autentisering](How-to/how-to-authentication.md) .

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

#### <a name="verify-that-your-subscription-key-is-valid"></a>Kontrol lera att prenumerations nyckeln är giltig

Du kan köra följande kommando för att verifiera. Observera att ersätta *YOUR_SUBSCRIPTION_KEY* med din egen prenumerations nyckel. Om din prenumerations nyckel är giltig får du svar på den token som en JSON Web Token (JWT). Annars får du ett fel som svar.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumerations nyckel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

Exemplet använder sväng i Linux med bash. Om den inte är tillgänglig på din plattform kan du behöva installera en sväng. Exemplet bör även fungera på Cygwin i Windows, git bash, zsh och andra gränssnitt.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Se till att du använder samma prenumerations nyckel i programmet eller i REST-begäran som används ovan.

#### <a name="verify-the-authorization-token"></a>Verifiera autentiseringstoken

Det här steget behövs bara om du använder autentiseringstoken för autentisering. Kör följande kommando för att kontrol lera att autentiseringstoken fortfarande är giltig. Kommandot gör en POST-begäran till tjänsten och förväntar sig ett svarsmeddelande från tjänsten. Om du fortfarande får ett `403 Forbidden` HTTP-fel kan du kontrol lera att åtkomsttoken är korrekt inställd och inte upphör att gälla.

> [!IMPORTANT]
> Token har ett förfallo datum på 10 minuter.
> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till den förregistrerade ljud filen och `YOUR_ACCESS_TOKEN` med den autentiseringstoken som returnerades i föregående steg.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Fels`HTTP 400 Bad Request`

Detta är vanligt vis att begär ande texten innehåller ogiltiga ljud data. För närvarande stöder vi bara WAV-filer.

## <a name="error-http-408-request-timeout"></a>Fels`HTTP 408 Request Timeout`

Felet beror förmodligen på att inga ljuddata skickas till tjänsten och att tjänsten returnerar felet när tids gränsen har passerats. För REST API ska ljud data placeras i begär ande texten.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` I svaret är`InitialSilenceTimeout`

Ljuddata är vanligt vis orsaken till problemet. Exempel:

- ljudet har en lång tystnads tid i början. Tjänsten stoppar igenkänningen efter ett antal sekunder och returnerar `InitialSilenceTimeout`.
- ljudet använder ett codec-format som inte stöds, vilket gör att ljuddata behandlas som tystnads vis.
