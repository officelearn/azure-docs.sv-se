---
title: Felsökning | Microsoft Docs
description: Hur du löser problem när du använder Microsoft tal Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 04f3da19939d523d201d357b2b0293db1508431d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352185"
---
# <a name="troubleshooting"></a>Felsökning

## <a name="error-http-403-forbidden"></a>Fel `HTTP 403 Forbidden`

När du använder taligenkänning API, returneras ett `HTTP 403 Forbidden` fel.

### <a name="cause"></a>Orsak

Det här felet beror ofta på problem med autentisering. Anslutningsbegäranden utan giltig `Ocp-Apim-Subscription-Key` eller `Authorization` huvud avvisas av tjänsten med en `HTTP 403 Forbidden` svar.

Om du använder prenumeration nyckel för autentisering, kan det bero

- nyckeln för prenumerationen är ogiltig eller saknas
- kvoten för nyckeln prenumeration har överskridits
- den `Ocp-Apim-Subscription-Key` fältet har inte angetts i rubriken begäran när REST API anropas

Om du använder auktoriseringstoken för autentisering kan av följande skäl orsaka fel.

- den `Authorization` huvudet saknas i begäran när du använder REST
- den autentiseringstoken som anges i Authorization-huvud är ogiltig
- Autentiseringstoken har upphört att gälla. Åtkomst-token har en utgången av 10 minuter

Mer information om autentisering finns i [autentisering](How-to/how-to-authentication.md) sidan.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

#### <a name="verify-that-your-subscription-key-is-valid"></a>Kontrollera att din prenumeration nyckel är giltig

Du kan köra följande kommando för verifiering. Observera att ersätta *YOUR_SUBSCRIPTION_KEY* med din egen prenumeration nyckel. Om din prenumeration nyckeln är giltig, visas i svaret autentiseringstoken som en JSON-Webbtoken (JWT). Annars visas ett fel i svaret.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumeration nyckel.

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

I exemplet används curl på Linux med bash. Om den inte är tillgänglig på din plattform som du kan behöva installera curl. Exemplet bör också fungera på Cygwin på Windows, Git Bash, zsh och andra.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Kontrollera att du använder samma prenumeration nyckel i ditt program eller i REST-begäran som används som ovan.

#### <a name="verify-the-authorization-token"></a>Kontrollera autentiseringstoken

Det här steget behövs bara om du använder auktoriseringstoken för autentisering. Kör följande kommando för att kontrollera att autentiseringstoken fortfarande är giltigt. Kommandot gör en POST-begäran till tjänsten och förväntar sig ett svarsmeddelande från tjänsten. Om du fortfarande ta emot HTTP `403 Forbidden` fel, kontrollera åtkomst-token är korrekt och inte har gått ut.

> [!IMPORTANT]
> Token har en utgången av 10 minuter.
> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil och `YOUR_ACCESS_TOKEN` med autentiseringstoken returneras i föregående steg.

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Fel `HTTP 400 Bad Request`

Därför är vanligtvis att begärandetexten innehåller ogiltiga data för ljud. Vi stöder för närvarande endast WAV-filen.

## <a name="error-http-408-request-timeout"></a>Fel `HTTP 408 Request Timeout`

Felet beror troligen att inga ljuddata skickas till tjänsten och tjänsten returnerar felet efter tidsgränsen. För REST API placeras ljuddata i begärandetexten.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>Den `RecognitionStatus` i svaret `InitialSilenceTimeout`

Ljuddata är vanligtvis därför orsaken till problemet. Exempel:

- ljuduppspelningen har en lång tystnad tiden i början. Tjänsten stoppas inte igenkänning efter vissa antal sekunder och returnerar `InitialSilenceTimeout`.
- ljuduppspelningen formatet codec som inte stöds, vilket gör ljuddata behandlas som tystnad.
