---
title: 'Exempel: Lägg till ansikten i en PersonGroup'
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 3643fad1c9e821a78df6d78edeede2341ec79ea8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303769"
---
# <a name="add-faces-to-a-persongroup"></a>Lägga till ansikten i en PersonGroup

Den här guiden visar hur du lägger till ett stort antal personer och ansikten i ett PersonGroup-objekt. Samma strategi gäller även för LargePersonGroup-, FaceList-och LargeFaceList-objekt. Det här exemplet är skrivet i C# med hjälp av klient biblioteket för Azure Cognitive Services Face .NET.

## <a name="step-1-initialization"></a>Steg 1: Initiering

Följande kod deklarerar flera variabler och implementerar en hjälp funktion för att schemalägga ansikts Lägg till förfrågningar:

- `PersonCount` är det totala antalet personer.
- `CallLimitPerSecond` är det maximala antalet anrop per sekund enligt prenumerationsnivån.
- `_timeStampQueue` är en kö för att registrera tidstämplar.
- `await WaitCallLimitPerSecondAsync()` väntar tills den är giltig för att skicka nästa begäran.

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

När du använder ett klient bibliotek måste du skicka prenumerations nyckeln till konstruktorn för **FaceClient** -klassen. Exempel:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Hämta prenumerations nyckeln genom att gå till Azure Marketplace från Azure Portal. Mer information finns i [prenumerationer](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Steg 3: Skapa PersonGroup

En PersonGroup med namnet ”MyPersonGroup” skapas för att spara personerna.
Tiden för begäran står i kö till `_timeStampQueue` för att säkerställa den övergripande verifieringen.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Steg 4: skapa personerna för PersonGroup

Personer skapas samtidigt och används `await WaitCallLimitPerSecondAsync()` också för att undvika att anrops gränsen överskrids.

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

Ansikten som läggs till i olika personer bearbetas samtidigt. Ansikten som lagts till för en viss person bearbetas sekventiellt.
Återigen `await WaitCallLimitPerSecondAsync()` anropas för att säkerställa att begär ande frekvensen ligger inom begränsningens omfattning.

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

I den här guiden har du lärt dig hur du skapar en PersonGroup med ett stort antal personer och ansikten. Flera påminnelser:

- Den här strategin gäller även för FaceLists och LargePersonGroups.
- Att lägga till eller ta bort ansikten i olika FaceLists eller personer i LargePersonGroups bearbetas samtidigt.
- Att lägga till eller ta bort ansikten i en viss FaceList eller person i en LargePersonGroup görs i tur och ordning.
- För enkelhetens skull utelämnas hanteringen av ett potentiellt undantag i den här hand boken. Om du vill förbättra stabilare använder du rätt princip för återförsök.

Följande funktioner förklaras och visas:

- Skapa PersonGroups med hjälp av [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Skapa personer med hjälp av [PersonGroup-personen – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Lägg till ansikten till personer med hjälp av [PersonGroup-personen – Lägg till ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -API.

## <a name="related-topics"></a>Relaterade ämnen

- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
- [Använda den storskaliga funktionen](how-to-use-large-scale.md)
