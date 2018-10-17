---
title: 'Exempel: Lägg till ansikten – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda Ansikts-API för att lägga till ansikten i bilder.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: fb5d03e2cb3c11daf7a94966fda46345ee910ded
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125110"
---
# <a name="example-how-to-add-faces"></a>Exempel: så här lägger du till ansikten

Den här guiden visar den bästa metoden för att lägga till stora antal personer och ansikten i en PersonGroup.
Samma strategi gäller även för FaceList och LargePersonGroup.
Exemplen är skrivna i C#- med Ansikts-API-klientbiblioteket.

## <a name="step-1-initialization"></a>Steg 1: Initiering

Flera variabler deklareras och en hjälpfunktionen implementeras för att schemalägga begäranden.

- `PersonCount` är det totala antalet personer.
- `CallLimitPerSecond` är det maximala antalet anrop per sekund enligt prenumerationsnivån.
- `_timeStampQueue` är en kö för att registrera tidstämplar.
- `await WaitCallLimitPerSecondAsync()` väntar tills det går att skicka nästa begäran.

```CSharp
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

När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för klassen FaceServiceClient. Exempel:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Prenumerationsnyckeln kan hämtas från Marketplace-sidan på Azure-portalen. Se [prenumerationer](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step-3-create-the-persongroup"></a>Steg 3: Skapa PersonGroup

En PersonGroup med namnet ”MyPersonGroup” skapas för att spara personerna.
Tiden för begäran står i kö till `_timeStampQueue` för att säkerställa den övergripande verifieringen.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Steg 4: Skapa personerna i PersonGroup

Personer skapas samtidigt och `await WaitCallLimitPerSecondAsync()` används också för att inte överskrida anropsgränsen.

```CSharp
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

```CSharp
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
