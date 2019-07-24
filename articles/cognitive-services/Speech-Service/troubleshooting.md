---
title: Felsöka tal SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal-SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 99cb23afcdb40f74485a7dcec34435a46d0e7476
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405903"
---
# <a name="troubleshoot-the-speech-sdk"></a>Felsöka Speech SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal-SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fel: WebSocket-uppgraderingen misslyckades med ett autentiseringsfel (403)

Du kanske fel slutpunkten för din region eller tjänst. Kontrollera URI för att kontrollera att det stämmer.

Dessutom det kan finnas ett problem med din prenumerationsnyckel eller auktorisering token. Mer information finns i nästa avsnitt.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Fel: HTTP 403 förbud eller HTTP 401 obehörig

Det här felet orsakas ofta av problem med autentisering. Anslutningsförfrågningar utan ett giltigt `Ocp-Apim-Subscription-Key` eller `Authorization` rubrik avvisas med status 403 eller 401.

* Om du använder en prenumerationsnyckel för autentisering, kanske du ser felet eftersom:

    - prenumerationsnyckeln är ogiltig eller saknas
    - du har överskridit kvoten för användning av din prenumeration

* Om du använder en Autentiseringstoken för autentisering, kanske du ser felet eftersom:

    - auktoriseringstoken är ogiltig
    - Autentiseringstoken har upphört att gälla

### <a name="validate-your-subscription-key"></a>Verifiera din prenumerationsnyckel

Du kan kontrollera att du har en giltig prenumerationsnyckel genom att köra ett av följande kommandon.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` och `YOUR_REGION` med dina egna prenumerationsnyckel och associerade region.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Om du har angett en giltig prenumerations nyckel returnerar kommandot en autentiseringstoken, annars returneras ett fel.

### <a name="validate-an-authorization-token"></a>Validera en autentiseringstoken

Om du använder en Autentiseringstoken för autentisering, kör du något av följande kommandon för att verifiera att autentiseringstoken är fortfarande giltig. Token är giltig i 10 minuter.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil. Ersätt `YOUR_ACCESS_TOKEN` med autentiseringstoken som returnerades i föregående steg. Ersätt `YOUR_REGION` med rätt region.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Om du har angett en giltig autentiseringstoken returnerar kommandot avskriften för ljud filen, annars returneras ett fel.

---

## <a name="error-http-400-bad-request"></a>Fel: HTTP 400 Felaktig begäran

Det här felet uppstår vanligen när begärandetexten innehåller ogiltigt ljuddata. Endast WAV formatet stöds. Kontrollera också begärandehuvuden för att se till att du kan ange lämpliga värden för `Content-Type` och `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Fel: Timeout för HTTP 408-begäran

Felet sannolikt beror på att inga ljuddata som skickas till tjänsten. Det här felet kan också orsakas av nätverksproblem.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>”RecognitionStatus” i svaret är ”InitialSilenceTimeout”

Det här problemet orsakas vanligtvis av ljuddata. Du kanske ser det här felet eftersom:

* Det finns en lång stretch tystnad i början av ljudet. I så fall kan tjänsten stoppas erkännande efter några sekunder och returnerar `InitialSilenceTimeout`.

* Ljudet använder en stöds inte codec-format, vilket orsakar ljuddata behandlas som tystnad.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](releasenotes.md)
