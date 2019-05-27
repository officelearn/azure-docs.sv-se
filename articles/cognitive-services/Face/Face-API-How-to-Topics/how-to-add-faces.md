---
title: 'Exempel: Lägg till ansikten i en PersonGroup - Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda Ansikts-API för att lägga till ansikten i bilder.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 83aef90702e4a4cc4fd9bdfda486841f9b2a63a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124505"
---
# <a name="add-faces-to-a-persongroup"></a>Lägg till ansikten i en PersonGroup

Den här guiden visar hur du lägger till ett stort antal personer och ansikten i ett PersonGroup-objekt. Samma strategin gäller även för LargePersonGroup och FaceList LargeFaceList objekt. Det här exemplet är skriven i C# med hjälp av Azure Cognitive Services Ansikts-API för .NET-klientbiblioteket.

## <a name="step-1-initialization"></a>Steg 1: Initiering

Följande kod deklarerar flera variabler och implementerar en hjälpfunktionen att schemalägga ansiktet Lägg till begäranden:

- `PersonCount` är det totala antalet personer.
- `CallLimitPerSecond` är det maximala antalet anrop per sekund enligt prenumerationsnivån.
- `_timeStampQueue` är en kö för att registrera tidstämplar.
- `await WaitCallLimitPerSecondAsync()` väntar tills det går att skicka nästa begäran.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Steg 2: Auktorisera API-anropet

När du använder ett klientbibliotek, måste du ange prenumerationsnyckeln till konstruktorn i klassen FaceServiceClient. Exempel:

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Prenumerationsnyckeln, gå till Azure Marketplace från Azure-portalen. Mer information finns i [prenumerationer](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Steg 3: Skapa PersonGroup

En PersonGroup med namnet ”MyPersonGroup” skapas för att spara personerna.
Tiden för begäran står i kö till `_timeStampQueue` för att säkerställa den övergripande verifieringen.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Steg 4: Skapa personer för PersonGroup

Personer skapas samtidigt, och `await WaitCallLimitPerSecondAsync()` används också för att inte överskridas för anropet.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Steg 5: Lägg till ansikten till personerna

Ansikten som lagts till olika personer bearbetas samtidigt. Ansikten har lagts till för en viss person utförs i ordningsföljd.
Igen, `await WaitCallLimitPerSecondAsync()` anropas för att se till att frekvensen för begäran är inom omfånget för begränsningen.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Sammanfattning

I den här guiden beskrivs processen för att skapa en PersonGroup med ett stort antal personer och ansikten. Flera påminnelser:

- Den här strategin gäller även för FaceLists och LargePersonGroups.
- Att lägga till eller ta bort ansikten till olika FaceLists eller personer i LargePersonGroups bearbetas samtidigt.
- Att lägga till eller ta bort ansikten till en specifik FaceList person eller i en LargePersonGroup utförs sekventiellt.
- För enkelhetens skull utelämnas hur du hanterar en potentiell undantag i den här guiden. Om du vill förbättra mer stabilitet, tillämpa principen för nya försök.

Följande funktioner har beskrivs och visas:

- Skapa PersonGroups med hjälp av den [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Skapa personer med hjälp av den [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Lägg till ansikten för personer med hjälp av den [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API.

## <a name="related-topics"></a>Relaterade ämnen

- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Använda funktionen för storskaliga](how-to-use-large-scale.md)
