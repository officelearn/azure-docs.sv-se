---
title: Arbeta med fjärentiteter i Unity
description: Självstudiekurs som visar hur du arbetar med ARR-entiteter.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310201"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Självstudiekurs: Arbeta med fjärrenheter i Unity

[Självstudiekurs: Ställa in ett Unity-projekt från grunden](project-setup.md) visade hur du konfigurerar ett nytt Unity-projekt för att arbeta med Azure Remote Rendering. I den här guiden har vi en titt på de vanligaste funktionerna som varje ARR-användare behöver.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Plocka objekt med hjälp av stråle avgjutningar.
> * Åsidosätta objektlägen som färgtonsfärg, markeringsläge och synlighet.
> * Ta bort fjärrentiteter.
> * Flytta fjärrentiteter runt.
> * Använd klippta plan för att titta inuti objekt.

## <a name="prerequisites"></a>Krav

* Den här självstudien bygger på [handledning: Ställa in ett Unity-projekt från grunden](project-setup.md).

> [!TIP]
> [ARR-exempeldatabasen](https://github.com/Azure/azure-remote-rendering) innehåller förberedda Unity-projekt för *Unity* alla självstudier i unity-mappen, som du kan använda som referens.

## <a name="pick-objects"></a>Plocka objekt

Vi vill interagera med objekt, så det första vi behöver, är att plocka objekt under muspekaren.

Skapa ett [nytt skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) som heter **RemoteRaycaster** och ersätt hela innehållet med koden nedan:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Lägg till den här komponenten i *RemoteRendering-objektet* i scenen.

> [!WARNING]
>
> *RemoteRaycaster-komponenten* kräver att en *ARRServiceUnity-komponent* ska kopplas till samma objekt. *ARRServiceUnity* är en hjälpare klass för att komma åt vissa ARR-funktioner lättare. Det kan dock bara finnas en enda instans av den här komponenten i scenen. Se därför till att lägga till alla komponenter som kräver *ARRServiceUnity* till samma GameObject.
> Om du vill komma åt ARR-funktioner från flera spelobjekt lägger du antingen till *ARRServiceUnity-komponenten* bara i ett av dem och refererar till den i de andra skripten eller kommer åt ARR-funktionen direkt.

Tryck på play, anslut till en session och ladda en modell. Peka nu på objekt i scenen och titta på konsolen utgång. Objektnamnet för varje del som du hovrar över ska skrivas ut.

## <a name="highlight-objects"></a>Markera objekt

Som ett nästa steg vill vi ge visuell feedback, vilka delar av en modell som användaren pekar på. För att uppnå detta bifogar vi en [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) till den entitet som vi valde. Den här komponenten kan användas för att aktivera eller inaktivera olika funktioner på ett objekt. Här använder vi den för att ställa in en nyans färg och aktivera [kontur rendering](../../overview/features/outlines.md).

Skapa en annan skriptfil som heter **RemoteModelEntity** och ersätt innehållet med följande kod:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Tilldela inte skriptet till något spelobjekt eftersom det kommer att tilldelas programmässigt av koden nedan.

Nästa upp, vi måste utöka vår *RemoteRaycaster* att lägga till *RemoteModelEntity* komponenten till objektet som vi just plockade.

Lägg till följande kod i **RemoteRaycaster-implementeringen** och ta bort dubblettfunktionerna:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Kör ditt projekt och peka på en modell, bör du se det få en röd nyans och en vit markeringskontur.

## <a name="isolate-the-selected-object"></a>Isolera det markerade objektet

En annan användning av [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) är möjligheten att åsidosätta synligheten. På så sätt kan du isolera ett markerat objekt från resten av modellen. Öppna **remotemodelEntity-skriptet,** lägg till följande kod och ta bort dubblettfunktionerna:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Den här koden är beroende av att ha en tillståndssidosättningskomponent högst upp i hierarkin, vilket gör alla objekt osynliga. Sedan åsidosätter den synligheten igen vid det markerade objektet, så att det blir synligt för ett objekt. Därför måste vi skapa en tillståndssidosättningskomponent vid rotobjektet.

Öppna **Fjärrrenderingsskriptet** och infoga koden nedan högst upp i *funktionen LoadModel:*

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Slutligen behöver vi ett sätt att göra synligheten synlig. Öppna **RemoteRaycaster-skriptet** och ersätt *uppdateringsfunktionen:*

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Kör koden och högerklicka på en del av modellen. Resten av modellen kommer att försvinna och endast den markerade pjäsen kommer att förbli synlig.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Ta bort GameObject-instanser av fjärrenheter

Du kanske har märkt att koden fortsätter att skapa objekt, men aldrig rensar upp dem. Detta visas också på objekthierarkipanelen. När du expanderar fjärrobjekthierarkin under simuleringen kan du se fler och fler objekt som visas varje gång du hovrar över en ny del av modellen.

Att ha många objekt i en scen påverkar prestanda negativt. Du bör alltid rensa upp objekt som inte behövs längre.

Sätt in koden nedan i **RemoteRaycaster-skriptet** och ta bort dubblettfunktionerna:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Flytta objekt

Som ett nästa steg vill vi flytta runt ett markerat objekt. I **RemoteRaycaster-skriptet infogar** du den här koden och tar bort dubblettfunktionen:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Om du kör den här koden kommer du att märka att ingenting händer. Det beror på att om du ändrar ett objekts transformering automatiskt synkroniseras tillståndsändringen till servern, av prestandaskäl. I stället måste du antingen skicka den här tillståndsändringen till servern manuellt eller aktivera **SyncEveryFrame** på *RemoteEntitySyncObject-komponenten.*

Öppna **remotemodelEntity-skriptet** och lägg till den här raden:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Köra koden igen, bör du kunna vänsterklicka på ett objekt och dra runt.

## <a name="add-a-cut-plane"></a>Lägg till ett snittplan

Den sista funktionen vi vill prova i den här guiden, använder [klippa plan](../../overview/features/cut-planes.md). Ett snittplan skär bort delar av renderade objekt, så att du kan titta inuti dem.

Skapa en ny GameObject i scenen **CutPlane**. Skapa ett nytt skript och kalla det **RemoteCutPlane**. Lägg till komponenten i det nya GameObject.

Öppna skriptfilen och ersätt innehållet med följande kod:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

När du kör koden nu bör du se hur modellen skärs upp av planet. Du kan markera *CutPlane-objektet* och flytta och rotera det i *scenfönstret.* Du kan växla det skurna planet på och av genom att inaktivera det skurna planet objektet.

## <a name="next-steps"></a>Nästa steg

Du vet nu den viktigaste funktionen för att interagera med fjärrobjekt. I nästa handledning kommer vi att ta en titt på att anpassa en scen utseende.

> [!div class="nextstepaction"]
> [Handledning: Ändra miljö och material](changing-environment-and-materials.md)
