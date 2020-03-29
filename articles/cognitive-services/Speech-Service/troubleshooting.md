---
title: Felsöka tal-SDK - Taltjänsten
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder Tal-SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815407"
---
# <a name="troubleshoot-the-speech-sdk"></a>Felsöka Speech SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder Tal-SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fel: WebSocket-uppgraderingen misslyckades med ett autentiseringsfel (403)

Du kan ha fel slutpunkt för din region eller tjänst. Kontrollera uri:n för att kontrollera att den är korrekt.

Det kan också vara problem med din prenumerationsnyckel eller auktoriseringstoken. Mer information finns i nästa avsnitt.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Fel: HTTP 403 Förbjuden eller HTTP 401 Obehörig

Det här felet orsakas ofta av autentiseringsproblem. Anslutningsbegäranden `Ocp-Apim-Subscription-Key` utan `Authorization` giltigt eller huvud avvisas med statusen 403 eller 401.

* Om du använder en prenumerationsnyckel för autentisering kan felet visas eftersom:

    - Prenumerationsnyckeln saknas eller är ogiltig
    - Du har överskridit din prenumerations användningskvot

* Om du använder en auktoriseringstoken för autentisering kan felet visas eftersom:

    - Auktoriseringstoken är ogiltig
    - Auktoriseringstoken har upphört att gälla

### <a name="validate-your-subscription-key"></a>Validera din prenumerationsnyckel

Du kan kontrollera att du har en giltig prenumerationsnyckel genom att köra något av följande kommandon.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` `YOUR_REGION` och med din egen prenumerationsnyckel och associerade region.

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

Om du har angett en giltig prenumerationsnyckel returnerar kommandot en auktoriseringstoken, annars returneras ett fel.

### <a name="validate-an-authorization-token"></a>Verifiera en auktoriseringstoken

Om du använder en auktoriseringstoken för autentisering kör du något av följande kommandon för att kontrollera att auktoriseringstoken fortfarande är giltig. Tokens är giltiga i 10 minuter.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till den förinspelade ljudfilen. Ersätt `YOUR_ACCESS_TOKEN` med auktoriseringstoken som returnerades i föregående steg. Ersätt `YOUR_REGION` med rätt region.

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

Om du har angett en giltig auktoriseringstoken returneras transkriptionen för ljudfilen, annars returneras ett fel.

---

## <a name="error-http-400-bad-request"></a>Fel: HTTP 400 Felaktig begäran

Det här felet uppstår vanligtvis när begäranden innehåller ogiltiga ljuddata. Endast WAV-format stöds. Kontrollera också begärans rubriker för att se till `Content-Type` att `Content-Length`du anger lämpliga värden för och .

## <a name="error-http-408-request-timeout"></a>Fel: TIMEOUT FÖR BEGÄRAN OM HTTP 408

Felet uppstår troligen eftersom inga ljuddata skickas till tjänsten. Det här felet kan också orsakas av nätverksproblem.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" i svaret är "InitialSilenceTimeout"

Det här problemet orsakas vanligtvis av ljuddata. Det här felet kan visas eftersom:

* Det är en lång sträcka av tystnad i början av ljudet. I så fall stoppar tjänsten igenkänningen `InitialSilenceTimeout`efter några sekunder och returnerar .

* Ljudet använder ett codec-format som inte stöds, vilket gör att ljuddata behandlas som tystnad.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](releasenotes.md)
