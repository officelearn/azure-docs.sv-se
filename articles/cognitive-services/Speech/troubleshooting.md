---
title: Felsökning av Bing-taligenkänning | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Så här att lösa problem när du använder Bing-taligenkänning.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 532916106f62e0236b8dd53cf7988a648355aef4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991869"
---
# <a name="troubleshooting-bing-speech"></a>Felsökning för Bing-tal

## <a name="error-http-403-forbidden"></a>Fel `HTTP 403 Forbidden`

När du använder API för taligenkänning, returneras ett `HTTP 403 Forbidden` fel.

### <a name="cause"></a>Orsak

Det här felet beror ofta på problem med autentisering. Anslutningsbegäranden utan giltig `Ocp-Apim-Subscription-Key` eller `Authorization` rubrik avvisas av tjänsten med ett `HTTP 403 Forbidden` svar.

Om du använder prenumerationsnyckel för autentisering, kan orsaken vara

- prenumerationsnyckeln är ogiltig eller saknas
- kvot för användning av prenumerationsnyckeln har överskridits
- den `Ocp-Apim-Subscription-Key` fältet har inte angetts i rubriken, när REST API anropas

Om du använder Autentiseringstoken för autentisering, orsaka följande felet.

- den `Authorization` huvud saknas i begäran när du använder REST
- den autentiseringstoken som anges i auktoriseringsrubriken är ogiltig
- Autentiseringstoken har upphört att gälla. Åtkomsttoken innehåller ett förfallodatum på 10 minuter

Mer information om autentisering finns i den [autentisering](How-to/how-to-authentication.md) sidan.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

#### <a name="verify-that-your-subscription-key-is-valid"></a>Kontrollera att din prenumerationsnyckel är giltig

Du kan köra följande kommando för att bekräfta. Observera att ersätta *YOUR_SUBSCRIPTION_KEY* med din egen prenumerationsnyckel. Om din prenumerationsnyckel är giltig, får du i svaret autentiseringstoken som en JSON Web Token (JWT). Annars kan du få ett felmeddelande som svar.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumerationsnyckel.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[CURL](#tab/curl)

Exemplet använder curl på Linux med bash. Om det inte är tillgänglig på din plattform, kan du behöva installera curl. Exemplet bör också fungera på Cygwin på Windows, Git Bash, zsh och andra gränssnitt.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Kontrollera att du använder samma prenumerationsnyckeln i ditt program eller i REST-begäran som används ovan.

#### <a name="verify-the-authorization-token"></a>Kontrollera autentiseringstoken

Det här steget behövs bara om du använder Autentiseringstoken för autentisering. Kör följande kommando för att verifiera att autentiseringstoken är fortfarande giltig. Kommandot gör en POST-begäran till tjänsten och förväntar sig ett svarsmeddelande från tjänsten. Om du fortfarande ta emot HTTP `403 Forbidden` fel, kontrollera åtkomst-token är korrekt och inte har gått ut.

> [!IMPORTANT]
> Token har en giltighetstid på 10 minuter.
> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil och `YOUR_ACCESS_TOKEN` med autentiseringstoken som returnerades i föregående steg.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[CURL](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Fel `HTTP 400 Bad Request`

Därför är vanligtvis att begärandetexten innehåller ogiltigt ljuddata. För närvarande stöder vi bara WAV-fil.

## <a name="error-http-408-request-timeout"></a>Fel `HTTP 408 Request Timeout`

Felet beror troligen att inga ljud data skickas till tjänsten och tjänsten returnerar det här felet efter tidsgränsen. För REST-API bör ljuddata placeras i begärandetexten.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>Den `RecognitionStatus` i svaret är `InitialSilenceTimeout`

Ljuddata är vanligtvis orsaken orsaken till problemet. Exempel:

- ljudet har en lång tystnad tid i början. Tjänsten stoppas erkännande efter vissa antal sekunder och returnerar `InitialSilenceTimeout`.
- ljudet använder stöds inte codec-format, vilket gör ljuddata behandlas som åsidosatt inaktivitet.
