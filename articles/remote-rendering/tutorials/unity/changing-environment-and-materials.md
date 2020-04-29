---
title: Ändra miljö och material
description: Självstudie som visar hur du ändrar den himmelsblå kartan och objekt materialet i en Unity-scen.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679620"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Självstudie: ändra miljö och material

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Ändra miljö kartan för en scen.
> * Ändra material parametrar.
> * Läs in anpassade texturer.

## <a name="prerequisites"></a>Krav

I den här självstudien förutsätter vi att du är bekant med [Självstudier: arbeta med fjär entiteter i](working-with-remote-entities.md)enhet. Men du behöver bara ett beställnings projekt som du kan använda för att ansluta till sessioner och läsa in en modell som visas i [Självstudier: Konfigurera ett Unity-projekt från grunden](project-setup.md).

> [!TIP]
> [Plats för arr-exempel](https://github.com/Azure/azure-remote-rendering) innehåller för beredda Unit-projekt för alla självstudier i mappen *Unity* , som du kan använda som referens.

## <a name="change-the-environment-map"></a>Ändra miljö kartan

Azure Remote rendering stöder användning av [himmelsblå rutor](../../overview/features/sky.md) (kallas ibland "miljö kartor") för att simulera omgivande belysning. Detta är särskilt användbart när dina objekt använder *[fysiskt baserad åter givning](../../overview/features/pbr-materials.md)*, eftersom våra exempel modeller gör det. ARR levereras också med en mängd inbyggda himmelsblå texturer som vi kommer att använda i den här självstudien.

Skapa ett nytt skript med namnet **RemoteSky** och Lägg till det i en ny GameObject. Öppna skript filen och ersätt den med följande kod:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Observera att `LoadTextureFromSASAsync` varianten används ovan eftersom inbyggda texturer har lästs in. Om du läser in från [länkade BLOB-lagringar](../../how-tos/create-an-account.md#link-storage-accounts)använder du `LoadTextureAsync` varianten. Ett exempel på hur detta fungerar för modeller finns i avsnittet om [modell inläsning](../../concepts/models.md#loading-models).

När du kör koden och växlar genom rummets kartor, kommer du att märka en drastiskt annorlunda belysning i modellen. Bakgrunden kommer dock att vara svart och du kan inte se den faktiska luft rummets struktur. Detta är avsiktligt, eftersom åter givning av en bakgrund skulle vara störande med en förhöjd verklighet. I ett korrekt program bör du använda himmelsblå texturer som liknar din riktiga världs miljö, eftersom det gör att objekt visas mer i verkligheten.

## <a name="modify-materials"></a>Ändra material

I den föregående själv studie kursen använde vi [tillstånd att åsidosätta komponenter](../../overview/features/override-hierarchical-state.md) för att ändra färg tonen för valda objekt. Nu vill vi uppnå en liknande påverkan, men gör detta genom att ändra [material](../../concepts/materials.md) för ett objekt.

Först behöver vi ett skript för att plocka objekt, precis som vi gjorde i [den andra själv studie kursen](working-with-remote-entities.md). Om du inte har ett **RemoteRaycaster** -skript ännu skapar du det nu. Ersätt dess innehåll med följande kod:

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
}
```

Lägg till komponenten i *RemoteRendering* Game-objektet. Den ansvarar för att plocka objekt under musen och lägga till *RemoteModelEntity* -komponenter i de plockade objekten. Den komponent klassen implementerar vi de faktiska material ändrings funktionerna.

Om du inte har ett **RemoteModelEntity** -skript ännu skapar du det och ersätter dess innehåll med den här koden:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

När du kör den här koden är de objekt som du hovrar med musen markerade. Resultatet liknar vad vi gjorde i själv studie kursen 2, men det sätt som den uppnår är annorlunda. Här hämtar vi listan över material för det plockade objektet och ändrar sedan det första för att ha en annan albedo-färg.

> [!IMPORTANT]
> Tänk på att om den här metoden markerar rätt delar av en modell beror på hur en modell har skapats. Den fungerar perfekt, om varje objekt använder exakt ett material. Men om modellen inte har en 1:1-relation mellan delar och material, kommer naïve-koden ovan inte att göra rätt sak.

## <a name="use-a-different-texture"></a>Använd en annan struktur

[Texturer](../../concepts/textures.md) ingår vanligt vis i en käll modell. Under [modell konverteringen](../../quickstarts/convert-model.md)konverteras alla texturer till det nödvändiga körnings formatet och paketeras i den slutliga modell filen. Om du vill ersätta en struktur vid körning måste du spara den i [DDS-filformat](https://en.wikipedia.org/wiki/DirectDraw_Surface) och ladda upp den till Azure Blob Storage. Läs [den här snabb starts guiden](../../quickstarts/convert-model.md) för hur du skapar en Azure Blob-behållare. När du har en BLOB-behållare kan du öppna den i Azure Storage Explorer och ladda upp filen via dra och släpp.

På körnings sidan kan du adressera en struktur till gång i Blob Storage på två olika sätt:

* Adress struktur efter SAS-URI. Högerklicka sedan på den överförda filen och välj "**Hämta signatur för delad åtkomst...**" från snabb menyn. Använd den här SAS-URI `LoadTextureFromSASAsync` : n med Function-varianten (se exempel koden nedan).
* Adressera strukturen efter Blob Storage-parametrar direkt, om [Blob Storage är länkat till kontot](../../how-tos/create-an-account.md#link-storage-accounts). Relevant inläsnings funktion i det `LoadTextureAsync`här fallet är.

Öppna nu **RemoteModelEntity** -skriptet, Lägg till följande kod och ta bort dubbletter:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Kör den här koden och hovra över din modell. Om din modell har riktiga UV-koordinater bör du se att texturen visas. Annars kanske du bara märker en färg ändring.

## <a name="next-steps"></a>Nästa steg

Detta avslutar introduktions serien om hur du använder Azure Remote rendering med Unity. I nästa steg ska du bekanta dig med några grundläggande koncept för ARR, till exempel [sessioner](../../concepts/sessions.md), [entiteter](../../concepts/entities.md)och [modeller](../../concepts/models.md) , för att bygga en djupare förståelse. Det finns också olika funktioner som [lampor](../../overview/features/lights.md), [dispositions åter givning](../../overview/features/outlines.md), [hierarkiska tillstånds åsidosättningar](../../overview/features/override-hierarchical-state.md)och [material](../../concepts/materials.md) som du bör utforska i detalj.

> [!div class="nextstepaction"]
> [Unity Game-objekt och-komponenter](../../how-tos/unity/objects-components.md)
