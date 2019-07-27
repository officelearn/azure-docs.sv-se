---
title: Webhooks – tal tjänst
titleSuffix: Azure Cognitive Services
description: Webhooks är HTTP-anrop som är idealiska för att optimera lösningen vid hantering av långvariga processer som import, anpassning, precisions test eller avskrifter av långa filer.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558808"
---
# <a name="webhooks-for-speech-services"></a>Webhooks för tal tjänster

Webhooks liknar HTTP-återanrop som gör det möjligt för ditt program att ta emot data från tal tjänsterna när de blir tillgängliga. Med Webhooks kan du optimera din användning av våra REST-API: er genom att eliminera behovet av att söka efter ett svar kontinuerligt. I kommande avsnitt får du lära dig hur du använder Webhooks med Speech Services.

## <a name="supported-operations"></a>Åtgärder som stöds

Tal tjänsterna stöder webhookar för alla tids krävande åtgärder. Varje åtgärd som anges nedan kan utlösa en HTTP-motringning när den har slutförts.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Nu ska vi skapa en webhook.

## <a name="create-a-webhook"></a>Skapa en webhook

Nu ska vi skapa en webhook för en offline-avskrift. Scenariot: en användare har en lång ljud fil som de vill skriva över asynkront med API: et för batch-avskriftering.

Webhooks kan skapas genom att göra en post-begäran till\<https://\>region. Cris.AI/API/speechtotext/v2.1/Transcriptions/hooks.

Konfigurations parametrar för begäran anges som JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Alla POST-förfrågningar till API: et för batch- `name`avskrift kräver en. Parametrarna `description` och`properties` är valfria.

`Active` Egenskapen används för att växla tillbaka till och från din URL utan att behöva ta bort och återskapa webhook-registreringen. Om du bara behöver logga tillbaka en gång efter att processen har slutförts tar du bort webhooken och växlar `Active` egenskapen till falsk.

Händelse typen `TranscriptionCompletion` anges i arrayen events. Den kommer att gå tillbaka till din slut punkt när en avskrift får ett Terminal-`Succeeded` tillstånd `Failed`(eller). Vid anrop till den registrerade URL: en, kommer begäran att innehålla `X-MicrosoftSpeechServices-Event` ett huvud som innehåller en av de registrerade händelse typerna. Det finns en begäran per registrerad händelse typ.

Det finns en händelse typ som du inte kan prenumerera på. Det är `Ping` händelse typen. En begäran med den här typen skickas till URL: en när du har skapat en webhook när du använder ping-URL: en (se nedan).  

`url` Egenskapen krävs i konfigurationen. POST-förfrågningar skickas till denna URL. `secret` Används för att skapa en SHA256-hash av nytto lasten, med hemligheten som en HMAC-nyckel. Hashen anges som `X-MicrosoftSpeechServices-Signature` rubrik vid anrop tillbaka till den registrerade URL: en. Den här rubriken är Base64-kodad.

Det här exemplet illustrerar hur du verifierar en C#nytto last med:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
I det här kodfragmentet `secret` avkodas och verifieras. Du ser också att händelse typen för webhooken har växlats. Det finns för närvarande en händelse per slutförd avskrift. Kod försöken fem gånger för varje händelse (med en fördröjning på en sekund) innan den ger upp.

### <a name="other-webhook-operations"></a>Andra webhook-åtgärder

För att hämta alla registrerade webhookar: TA https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Så här hämtar du en enskild webhook: TA https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Så här tar du bort en enskild webhook: TA BORT https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> I exemplet ovan är regionen "väst". Detta bör ersättas av den region där du har skapat din röst tjänst resurs i Azure Portal.

Inläggs https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping text: Tom

Skickar en POST-begäran till den registrerade URL: en. Begäran innehåller ett `X-MicrosoftSpeechServices-Event` huvud med ett värde-ping. Om webhooken har registrerats med en hemlighet, kommer den att `X-MicrosoftSpeechServices-Signature` innehålla ett huvud med en SHA256-hash av nytto lasten med hemligheten som HMAC-nyckel. Hash-värdet är Base64-kodat.

Inläggs https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test text: Tom

Skickar en POST-begäran till den registrerade URL: en om en entitet för den prenumerations händelse typ (avskrift) finns i systemet och är i rätt tillstånd. Nytto lasten kommer att genereras från den sista entiteten som skulle ha anropat webb-hooken. Om det inte finns någon entitet kommer inlägget att svara med 204. Om en testbegäran kan göras svarar den med 200. Begär ande texten är av samma form som i GET-begäran för en viss entitet som webbhooken prenumererar på (för instans avskrift). Begäran kommer att ha `X-MicrosoftSpeechServices-Event` -och `X-MicrosoftSpeechServices-Signature` -rubrikerna enligt beskrivningen ovan.

### <a name="run-a-test"></a>Kör ett test

Ett snabb test kan göras med hjälp av webbplatsen https://bin.webhookrelay.com. Därifrån kan du hämta URL: er för uppringning för att skicka som parameter till HTTP-posten för att skapa en webhook som beskrivs tidigare i dokumentet.

Klicka på "skapa Bucket" och följ anvisningarna på skärmen för att hämta en Hook. Använd sedan informationen på den här sidan för att registrera hooken med röst tjänsten. Nytto lasten för ett relä meddelande – som svar på slutförandet av en avskrift – ser ut så här:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Meddelandet innehåller inspelnings-URL och modeller som används för att skriva av den inspelningen.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
