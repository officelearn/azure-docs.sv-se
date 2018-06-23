---
title: Kognitiva Services tal SDK felsökning | Microsoft Docs
description: Felsökning kognitiva Services tal SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 16eaebcf9494ab57521068a9418ccf2ac7f5a8fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354549"
---
# <a name="troubleshooting-speech-services-sdk"></a>Felsökning av tal Services SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder SDK för tal.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fel `WebSocket Upgrade failed with an authentication error (403).`

Du kan ha fel slutpunkten för din region eller tjänst. Kontrollera URI för att kontrollera att den är korrekt. Även finns i nästa avsnitt, eftersom detta kan också vara problem med din prenumeration nyckel eller auktorisering token.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Fel `HTTP 403 Forbidden` eller fel `HTTP 401 Unauthorized`

Det här felet beror ofta på problem med autentisering. Anslutningsbegäranden utan en giltig `Ocp-Apim-Subscription-Key` eller `Authorization` huvud avvisas med status 401 eller 403.

* Om du använder en prenumeration nyckel för autentisering, kan orsaken vara:

    - nyckeln för prenumerationen är ogiltig eller saknas
    - du har överskridit kvot för din prenumeration

* Om du använder en Autentiseringstoken för autentisering, kan orsaken vara:

    - Autentiseringstoken är ogiltig
    - Autentiseringstoken har upphört att gälla

### <a name="validate-your-subscription-key"></a>Verifiera din prenumeration nyckel

Du kan kontrollera om du vill kontrollera att du har en giltig prenumeration nyckel genom att köra något av kommandona nedan.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` och `YOUR_REGION` med din egen prenumeration nyckeln och associerade region respektive.

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

Om du använder en Autentiseringstoken för autentisering, kör du något av följande kommandon för att kontrollera att autentiseringstoken fortfarande är giltigt. Token är giltig i 10 minuter.

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

Det här felet uppstår vanligen när begärandetexten innehåller ogiltiga data för ljud. Endast `WAV` format stöds. Kontrollera också begärandehuvuden att se till att du anger en lämplig `Content-Type` och `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Fel `HTTP 408 Request Timeout`

Felet beror förmodligen inga ljuddata skickas till tjänsten. Det här felet kan också orsakas av nätverksproblem.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>Den `RecognitionStatus` i svaret `InitialSilenceTimeout`

Ljuddata är vanligtvis därför orsaken till problemet. Exempel:

* Det finns en lång stretch tystnad i början av ljud. Tjänsten stoppas inte igenkänning efter några sekunder och returnera `InitialSilenceTimeout`.
* Ljuduppspelningen använder en codec som inte stöds-format, vilket gör att data ska behandlas som tystnad ljud.

## <a name="next-steps"></a>Nästa steg

* [Viktig information](releasenotes.md)

