---
title: Cognitive Services tal SDK felsökning
description: Felsökning Cognitive Services tal SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284130"
---
# <a name="troubleshooting-speech-services-sdk"></a>Felsöka Speech Services SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när med hjälp av tal-SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fel `WebSocket Upgrade failed with an authentication error (403).`

Du kan ha fel slutpunkten för din region eller tjänst. Kontrollera URI för att kontrollera att det stämmer. Även finns i nästa avsnitt, eftersom detta kan också vara ett problem med din prenumerationsnyckel eller auktorisering token.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Fel `HTTP 403 Forbidden` eller fel `HTTP 401 Unauthorized`

Det här felet beror ofta på problem med autentisering. Anslutningsförfrågningar utan ett giltigt `Ocp-Apim-Subscription-Key` eller `Authorization` rubrik avvisas med status 401 eller 403.

* Om du använder en prenumerationsnyckel för autentisering, kan orsaken vara:

    - prenumerationsnyckeln är ogiltig eller saknas
    - du har överskridit kvoten för användning av din prenumeration

* Om du använder en Autentiseringstoken för autentisering, kan orsaken vara:

    - auktoriseringstoken är ogiltig
    - Autentiseringstoken har upphört att gälla

### <a name="validate-your-subscription-key"></a>Verifiera din prenumerationsnyckel

Du kan kontrollera om du vill kontrollera att du har en giltig prenumerationsnyckel genom att köra något av kommandona nedan.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` och `YOUR_REGION` med dina egna prenumerationsnyckel och associerade region respektive.

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

### <a name="validate-an-authorization-token"></a>Validera en autentiseringstoken

Om du använder en Autentiseringstoken för autentisering, kör du något av följande kommandon för att verifiera att autentiseringstoken är fortfarande giltig. Token är giltig i 10 minuter.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil `YOUR_ACCESS_TOKEN` med den autentiseringstoken som returneras i föregående steg, och `YOUR_REGION` med rätt region.

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
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Fel `HTTP 400 Bad Request`

Det här felet uppstår vanligen när begärandetexten innehåller ogiltigt ljuddata. Endast `WAV` formatet stöds. Också kontrollera begärandehuvuden att kontrollera att du anger en lämplig `Content-Type` och `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Fel `HTTP 408 Request Timeout`

Felet beror förmodligen inga ljuddata som skickas till tjänsten. Det här felet kan också orsakas av nätverksproblem.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>Den `RecognitionStatus` i svaret är `InitialSilenceTimeout`

Ljuddata är vanligtvis orsaken orsaken till problemet. Exempel:

* Det finns en lång stretch tystnad i början av ljudet. Tjänsten stoppar erkännande efter några sekunder och returnera `InitialSilenceTimeout`.
* Ljudet använder en stöds inte codec-format, vilket orsakar ljuddata behandlas som tystnad.

## <a name="next-steps"></a>Nästa steg

* [Viktig information](releasenotes.md)

