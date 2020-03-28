---
title: 'Exempel: Lägga till ansikten i en persongrupp - ansikte'
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du lägger till ett stort antal personer och ansikten i ett PersonGroup-objekt med Azure Cognitive Services Face-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169836"
---
# <a name="add-faces-to-a-persongroup"></a>Lägga till ansikten i en persongrupp

Den här guiden visar hur du lägger till ett stort antal personer och ansikten i ett PersonGroup-objekt. Samma strategi gäller även för LargePersonGroup-, FaceList- och LargeFaceList-objekt. Det här exemplet skrivs i C# med hjälp av Azure Cognitive Services Face .NET-klientbiblioteket.

## <a name="step-1-initialization"></a>Steg 1: Initiering

Följande kod deklarerar flera variabler och implementerar en hjälpfunktion för att schemalägga ansiktstilläggsbegäranden:

- `PersonCount` är det totala antalet personer.
- `CallLimitPerSecond` är det maximala antalet anrop per sekund enligt prenumerationsnivån.
- `_timeStampQueue` är en kö för att registrera tidstämplar.
- `await WaitCallLimitPerSecondAsync()`väntar tills det är giltigt att skicka nästa begäran.

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

## <a name="step-2-authorize-the-api-call"></a>Steg 2: Auktorisera API-anrop

När du använder ett klientbibliotek måste du skicka prenumerationsnyckeln till konstruktorn för klassen **FaceClient.** Ett exempel:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Om du vill hämta prenumerationsnyckeln går du till Azure Marketplace från Azure-portalen. Mer information finns i [Prenumerationer](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Steg 3: Skapa PersonGroup

En PersonGroup med namnet ”MyPersonGroup” skapas för att spara personerna.
Tiden för begäran står i kö till `_timeStampQueue` för att säkerställa den övergripande verifieringen.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Steg 4: Skapa personer för PersonGroup

Personer skapas samtidigt och `await WaitCallLimitPerSecondAsync()` tillämpas också för att undvika att överskrida samtalsgränsen.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Steg 5: Lägg till ansikten till personerna

Ansikten som läggs till olika personer bearbetas samtidigt. Ansikten som läggs till för en viss person bearbetas sekventiellt.
Återigen `await WaitCallLimitPerSecondAsync()` åberopas för att säkerställa att begäran frekvensen är inom ramen för begränsning.

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
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Sammanfattning

I den här guiden lärde du dig processen att skapa en PersonGroup med ett stort antal personer och ansikten. Flera påminnelser:

- Den här strategin gäller även för FaceLists och LargePersonGroups.
- Att lägga till eller ta bort ansikten till olika facelists eller personer i LargePersonGroups bearbetas samtidigt.
- Att lägga till eller ta bort ansikten till en viss FaceList eller person i en LargePersonGroup görs sekventiellt.
- För enkelhetens skull utelämnas hur du hanterar ett potentiellt undantag i den här guiden. Om du vill öka mer robusthet, tillämpa rätt återförsök politik.

Följande funktioner förklarades och demonstrerades:

- Skapa persongrupper med hjälp av [PersonGroup - Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Skapa personer med hjälp av [PersonGroup Person - Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Lägg till ansikten för personer med hjälp av [PersonGroup Person - Lägg till](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ansikts-API.

## <a name="related-topics"></a>Relaterade ämnen

- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Använda den storskaliga funktionen](how-to-use-large-scale.md)
