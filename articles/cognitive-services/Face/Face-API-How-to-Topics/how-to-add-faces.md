---
title: Lägg till personerna bakom Ansikts-API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Använda Ansikts-API i kognitiva Services för att lägga till ytor i bilder.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351573"
---
# <a name="how-to-add-faces"></a>Hur du lägger till ytor

Den här guiden visar det bästa sättet att lägga till massiv antal personer och ytor i en PersonGroup.
Samma strategin gäller även för FaceList och LargePersonGroup.
Exemplen är skrivna i C# med hjälp av klientbiblioteket Ansikts-API.

## <a name="step1"></a> Steg 1: initiering

Flera variabler deklareras och en hjälpfunktion implementeras om du vill schemalägga begäranden.

- `PersonCount` är det totala antalet personer.
- `CallLimitPerSecond` är de maximala per sekund enligt prenumeration nivån.
- `_timeStampQueue` är en kö för att registrera begäran tidsstämplar.
- `await WaitCallLimitPerSecondAsync()` ska vänta tills det är tillåtet att skicka nästa begäran.

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

## <a name="step2"></a> Steg 2: Ge API-anrop

När du använder ett klientbibliotek skickas nyckeln prenumeration via konstruktorn för klassen FaceServiceClient. Exempel:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Nyckeln för prenumerationen kan hämtas från Marketplace-sidan i din Azure-portalen. Se [prenumerationer](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a> Steg 3: Skapa PersonGroup

En PersonGroup med namnet ”MyPersonGroup” skapas för att spara personerna.
Tid för begäran är i kö till `_timeStampQueue` att garantera övergripande verifieringen.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> Steg 4: Skapa personerna som PersonGroup

Personer skapas samtidigt och `await WaitCallLimitPerSecondAsync()` används också för att undvika anropet överskrids.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> Steg 5: Lägg till ytor personer

Lägga till ytor till olika personer bearbetas samtidigt, medan för en specifik person är sekventiella.
Igen, `await WaitCallLimitPerSecondAsync()` anropas för att kontrollera begäran frekvensen är inom omfånget för begränsningen.

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

## <a name="summary"></a> Sammanfattning

I den här guiden har du lärt dig att skapa en PersonGroup med massiv antal personer och ytor. Flera påminnelser:

- Den här strategin gäller även för FaceList och LargePersonGroup.
- Lägga till/ta bort ytor till olika FaceLists eller personer i LargePersonGroup kan bearbetas samtidigt.
- Samma åtgärder för att en specifik FaceList Person eller i LargePersonGroup bör göras sekventiellt.
- Om du vill behålla enkelheten utelämnas hanteringen av eventuella undantag i den här guiden. Om du vill förbättra mer stabilitet ska rätt i principen tillämpas.

Följande är en snabb påminnelse funktioner beskrivs tidigare och visas:

- Skapa PersonGroups med hjälp av den [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API
- Skapa personer som använder den [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API
- Lägger till ytor till personer som använder den [PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API

## <a name="related"></a> Närliggande information
- [Så här identifierar du står i avbildningen](HowtoIdentifyFacesinImage.md)
- [Hur du identifierar står i avbildningen](HowtoDetectFacesinImage.md)
- [Hur du använder funktionen storskaliga](how-to-use-large-scale.md)
