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
ms.openlocfilehash: 04fe9251ba124ed5d218daf915339c7f84efdeb6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704192"
---
# <a name="how-to-add-faces-to-a-persongroup"></a>Lägga till ansikten i en PersonGroup

Den här guiden visar de rekommenderade metoderna för att lägga till ett stort antal personer och ansikten i ett PersonGroup-objekt. Samma strategin gäller även för LargePersonGroup och FaceList LargeFaceList. Det här exemplet är skriven i C# med Ansikts-API .NET-klientbiblioteket.

## <a name="step-1-initialization"></a>Steg 1: Initiering

Följande kod deklarerar flera variabler och implementerar en hjälpfunktionen att schemalägga ansiktet Lägg till begäranden.

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

Prenumerationsnyckeln kan hämtas från Marketplace-sidan på Azure-portalen. Se [Prenumerationer](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Steg 3: Skapa PersonGroup

En PersonGroup med namnet ”MyPersonGroup” skapas för att spara personerna.
Tiden för begäran står i kö till `_timeStampQueue` för att säkerställa den övergripande verifieringen.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Steg 4: Skapa personerna i PersonGroup

Personer skapas samtidigt och `await WaitCallLimitPerSecondAsync()` används också för att inte överskrida anropsgränsen.

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

Ansikten som läggs till olika personer bearbetas samtidigt medan ansikten för en specifik person bearbetas sekventiellt.
Igen, `await WaitCallLimitPerSecondAsync()` anropas för att säkerställa att frekvensen för begäran är inom omfånget för begränsningen.

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

I den här guiden har du lärt dig hur du skapar en PersonGroup med ett stort antal personer och ansikten. Flera påminnelser:

- Samma strategi gäller även för FaceList och LargePersonGroup.
- Du kan lägga till/ta bort ansikten till olika FaceLists eller Persons i LargePersonGroup samtidigt.
- Samma åtgärder i en specifik FaceList eller Person i en LargePersonGroup bör göras sekventiellt.
- För att hålla guiden enkel utelämnas hantering av eventuella undantag. Om du vill förbättra stabiliteten ska återförsöksprincipen tillämpas.

Det följande är en snabb påminnelse om de viktiga aspekter som förklarats och beskrivits tidigare:

- Skapa PersonGroups med hjälp av API:et [PersonGroup – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Skapa personer med hjälp av API:et [PersonGroup Person – Skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Lägga till ansikten till personer med hjälp av API:et [PersonGroup Person – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Relaterade ämnen

- [Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
- [Känna igen ansikten i en bild](HowtoDetectFacesinImage.md)
- [Känna igen storskaliga funktioner](how-to-use-large-scale.md)
