---
title: Unity spel objekt och komponenter
description: Beskriver Unity-specifika metoder för att arbeta med fjärrrenderingsentiteter och komponenter.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409857"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagera med spelobjekt och komponenter i Unity

Azure Remote Rendering (ARR) är optimerad för ett stort antal objekt (se [Begränsningar](../../reference/limits.md)). Även om det är möjligt att hantera stora och komplexa hierarkier på värden, är det omöjligt att replikera dem alla i Unity på lågdrivna enheter.

När en modell läses in på värden speglar Azure Remote Rendering därför informationen om modellstrukturen på klientenheten (som kommer att ådra sig nätverkstrafik), men replikerar inte objekt och komponenter i Unity. Istället förväntar den sig att du begär de nödvändiga Unity-spelobjekten och komponenterna manuellt, så att du kan begränsa omkostnaderna till vad som faktiskt behövs. På så sätt har du mer kontroll över klientsidans prestanda.

Därför levereras Unity-integreringen av Azure Remote Rendering med ytterligare funktioner för att replikera fjärråtergivningsstrukturen på begäran.

## <a name="load-a-model-in-unity"></a>Ladda en modell i Unity

När du läser in en modell får du en referens till rotobjektet för den inlästa modellen. Den här referensen är inte ett Unity-spelobjekt, men `Entity.GetOrCreateGameObject()`du kan förvandla det till ett med hjälp av tilläggsmetoden . Den funktionen förväntar sig `UnityCreationMode`ett argument av typen . Om du `CreateUnityComponents`klarar dig fylls det nyligen skapade Unity-spelobjektet dessutom med proxykomponenter för alla fjärrrenderingskomponenter som finns på värden. Det rekommenderas dock att `DoNotCreateUnityComponents`föredra , att hålla overhead minimal.

### <a name="load-model-with-task"></a>Läs in modell med uppgift

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Ladda modell med Unity coroutines

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Ladda modell med inväntningsmönster

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Kodexemplen ovan använde modellen lastning sökvägen via SAS eftersom den inbyggda modellen är inläst. Att adressera modellen via blob-behållare (med och `LoadModelAsync` `LoadModelParams`) fungerar helt analogt.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Om du skapar ett Unity-spelobjekt läggs en `RemoteEntitySyncObject` komponent implicit till i spelobjektet. Den här komponenten används för att synkronisera entitetens transformering till servern. Som `RemoteEntitySyncObject` standard kräver användaren `SyncToRemote()` att uttryckligen ringa för att synkronisera den lokala Unity tillstånd till servern. Om `SyncEveryFrame` du aktiverar objektet synkroniseras objektet automatiskt.

Objekt med `RemoteEntitySyncObject` en kan få sina avlägsna barn instansierade och visas i Unity-redigeraren via knappen **Visa barn.**

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Omslagskomponenter

[Komponenter](../../concepts/components.md) som är kopplade till fjärrrenderingsentiteter exponeras för Unity via proxy. `MonoBehavior` Dessa proxyservrar representerar fjärrkomponenten i Unity och vidarebefordrar alla ändringar till värden.

Om du vill skapa proxykomponenter `GetOrCreateArrComponent`för fjärråtergivning använder du tilläggsmetoden:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Kopplade livstider

Livslängden för en [fjärrenhet](../../concepts/entities.md) och ett Unity-spelobjekt kopplas ihop medan de är bundna genom en `RemoteEntitySyncObject`. Om du `UnityEngine.Object.Destroy(...)` ringer med ett sådant spelobjekt tas även fjärrentiteten bort.

För att förstöra Unity-spelobjektet, utan att påverka `Unbind()` fjärrentiteten, måste du först anropa `RemoteEntitySyncObject`.

Detsamma gäller för alla proxykomponenter. Om du bara vill förstöra representationen `Unbind()` på klientsidan måste du först anropa proxykomponenten:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Remote Rendering för Unity](unity-setup.md)
* [Självstudiekurs: Arbeta med fjärrenheter i Unity](../../tutorials/unity/working-with-remote-entities.md)
