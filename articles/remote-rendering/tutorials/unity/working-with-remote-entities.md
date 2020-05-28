---
title: Arbeta med fjärentiteter i Unity
description: Självstudie som visar hur du arbetar med ARR-entiteter.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021253"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Självstudie: arbeta med fjär entiteter i enhet

[Självstudie: skapa ett Unity-projekt från grunden](project-setup.md) som visade hur man konfigurerar ett nytt Unity-projekt för att arbeta med Azure-fjärrrendering. I den här självstudien har vi en titt på de vanligaste funktionerna som varje ARR-användare behöver.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Välj objekt med hjälp av Ray-sändningar.
> * Åsidosätt objekt tillstånd som färg tons färg, markerings tillstånd och synlighet.
> * Ta bort fjär entiteter.
> * Flytta fjär entiteter runt.
> * Använd klipp ut plan för att titta inuti objekt.

## <a name="prerequisites"></a>Förutsättningar

* Den här själv studie Kursen bygger vidare på [Självstudier: Konfigurera ett Unity-projekt från grunden](project-setup.md).

> [!TIP]
> [Plats för arr-exempel](https://github.com/Azure/azure-remote-rendering) innehåller för beredda Unit-projekt för alla självstudier i mappen *Unity* , som du kan använda som referens.

## <a name="pick-objects"></a>Välj objekt

Vi vill interagera med objekt, så det första vi behöver, är att plocka objekt under mus markören.

Skapa ett [nytt skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) med namnet **RemoteRaycaster** och ersätt hela innehållet med koden nedan:

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

Lägg till den här komponenten i *RemoteRendering* -objektet i din scen.

> [!WARNING]
>
> *RemoteRaycaster* -komponenten kräver att en *ARRServiceUnity* -komponent är kopplad till samma objekt. *ARRServiceUnity* är en hjälp klass för att få åtkomst till vissa arr-funktioner lättare. Det kan dock bara finnas en enda instans av den här komponenten i scenen. Se därför till att lägga till alla komponenter som kräver *ARRServiceUnity* till samma GameObject.
> Om du vill få åtkomst till ARR-funktioner från flera spel objekt, lägger du antingen till *ARRServiceUnity* -komponenten till en av dem och referensen som finns i de andra skripten eller får åtkomst till arr-funktionen direkt.

Tryck på Play, Anslut till en session och Läs in en modell. Peka nu på objekt i scenen och titta på konsolens utdata. Den ska skriva ut objekt namnet för varje del som du hovrar över.

## <a name="highlight-objects"></a>Markera objekt

Som nästa steg vill vi ge visuell feedback, vilka delar av en modell som användaren pekar på. För att uppnå detta kopplar vi en [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) till den entitet som vi har valt. Den här komponenten kan användas för att aktivera eller inaktivera olika funktioner på ett objekt. Här använder vi den för att ange en färg tons färg och aktivera [dispositions åter givning](../../overview/features/outlines.md).

Skapa en annan skript fil med namnet **RemoteModelEntity** och ersätt dess innehåll med följande kod:

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
> Tilldela inte det här skriptet till något Unity Game-objekt eftersom det kommer att tilldelas program mässigt av koden nedan.

Härnäst måste vi utöka vår *RemoteRaycaster* för att lägga till *RemoteModelEntity* -komponenten till objektet som vi nyss har valt.

Lägg till följande kod i **RemoteRaycaster** -implementeringen och ta bort de duplicerade funktionerna:

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

Kör ditt projekt och peka på en modell. du bör se det får en röd nyans och en vit markerings disposition.

## <a name="isolate-the-selected-object"></a>Isolera det valda objektet

En annan användning av [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) är möjligheten att åsidosätta synlighet. På så sätt kan du isolera ett markerat objekt från resten av modellen. Öppna **RemoteModelEntity** -skriptet, Lägg till följande kod och ta bort de duplicerade funktionerna:

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

Den här koden förlitar sig på att en komponent för tillstånds åsidosättning finns i det översta objektet i hierarkin, vilket gör alla objekt osynliga. Sedan åsidosätter den synligheten igen vid det valda objektet, så att ett objekt visas. Därför måste vi skapa en komponent för tillstånds åsidosättning vid rotobjektet.

Öppna **RemoteRendering** -skriptet och infoga koden nedan överst i *LoadModel* -funktionen:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Slutligen behöver vi ett sätt att växla synlighet. Öppna **RemoteRaycaster** -skriptet och Ersätt *Update* -funktionen:

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

Kör koden och högerklicka på en del av modellen. Resten av modellen försvinner och endast den markerade pjäsen förblir synlig.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Ta bort GameObject instanser av fjär entiteter

Du kanske har märkt att koden behåller skapandet av objekt, men rensar aldrig upp dem. Detta är också synligt i objektets hierarki panel. När du expanderar Fjärrobjektets hierarki under simuleringen kan du se fler och fler objekt som visas varje gång du hovrar över en ny del av modellen.

Om många objekt i en scen påverkar prestanda negativt. Du bör alltid Rensa objekt som inte behövs längre.

Infoga koden nedan i **RemoteRaycaster** -skriptet och ta bort de duplicerade funktionerna:

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

Som nästa steg vill vi flytta ett markerat objekt runt. I **RemoteRaycaster** -skriptet infogar du den här koden och tar bort funktionen duplicera:

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
> Om du kör den här koden ser du att ingenting händer. Det beror på att om du ändrar ett objekts transformering synkroniserar inte tillstånds ändringen automatiskt på servern av prestanda skäl. I stället måste du antingen skicka den här tillstånds ändringen till servern manuellt, eller så aktiverar du **SyncEveryFrame** på *RemoteEntitySyncObject* -komponenten.

Öppna **RemoteModelEntity** -skriptet och Lägg till den här raden:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Om du kör koden igen bör du kunna vänsterklicka på ett objekt och dra det runt.

## <a name="add-a-cut-plane"></a>Lägg till ett klipp plan

Den sista funktionen vi vill testa i den här självstudien använder [klipp plan](../../overview/features/cut-planes.md). Ett klipp plan klipper bort delar av återgivna objekt, som du kan titta på i dem.

Skapa en ny GameObject i scenen **CutPlane**. Skapa ett nytt skript och anropa det **RemoteCutPlane**. Lägg till komponenten i den nya GameObject.

Öppna skript filen och ersätt innehållet med följande kod:

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

När du kör din kod nu bör du se hur modellen klipps ut öppen av planet. Du kan välja *CutPlane* -objektet och flytta och rotera det i *scen* fönstret. Du kan växla mellan avskurna planet genom att inaktivera objektet klipp ut plan.

## <a name="next-steps"></a>Nästa steg

Nu vet du de viktigaste funktionerna för att interagera med fjärrobjekt. I nästa självstudie får vi en titt på hur du anpassar en scens utseende.

> [!div class="nextstepaction"]
> [Självstudie: ändra miljö och material](changing-environment-and-materials.md)
