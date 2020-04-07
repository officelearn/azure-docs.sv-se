---
title: Ändra miljö och material
description: Självstudiekurs som visar hur du ändrar himmelskartan och objektmaterial i en Unity-scen.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679620"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Handledning: Ändra miljö och material

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Ändra miljökartan för en scen.
> * Ändra materialparametrar.
> * Läs in anpassade texturer.

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du är bekant med [självstudiekurs: Arbeta med fjärrentiteter i Unity](working-with-remote-entities.md). Du behöver dock bara ett Unity-projekt som du kan ansluta till sessioner med och läsa in en modell, som visas i [Självstudiekurs: Ställa in ett Unity-projekt från grunden](project-setup.md).

> [!TIP]
> [ARR-exempeldatabasen](https://github.com/Azure/azure-remote-rendering) innehåller förberedda Unity-projekt för *Unity* alla självstudier i unity-mappen, som du kan använda som referens.

## <a name="change-the-environment-map"></a>Ändra miljökartan

Azure Remote Rendering stöder användningen av [himmelslådor](../../overview/features/sky.md) (ibland även kallade "miljökartor") för att simulera omgivande belysning. Detta är särskilt användbart när dina objekt använder *[fysiskt baserad rendering](../../overview/features/pbr-materials.md)*, som våra exempelmodeller gör. ARR levereras också med en mängd inbyggda himmel texturer, som vi kommer att använda i den här guiden.

Skapa ett nytt skript som heter **RemoteSky** och lägg till det i ett nytt GameObject. Öppna skriptfilen och ersätt den med följande kod:

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

Observera att `LoadTextureFromSASAsync` varianten används ovan eftersom inbyggda texturer läses in. Vid inläsning från [länkade blob-lagringar](../../how-tos/create-an-account.md#link-storage-accounts)använder du varianten. `LoadTextureAsync` Ett exempel på hur detta fungerar för modeller finns i [avsnittet modellinläsning](../../concepts/models.md#loading-models).

När du kör koden och växla genom himlen kartor, kommer du att märka drastiskt olika belysning på din modell. Bakgrunden förblir dock svart och du kan inte se själva himmelsstrukturen. Detta är avsiktligt, som gör en bakgrund skulle vara störande med en Augmented Reality-enhet. I ett korrekt program bör du använda himmel texturer som liknar din verkliga världen omgivning, eftersom detta kommer att bidra till att göra objekt verkar mer verklig.

## <a name="modify-materials"></a>Ändra material

I föregående självstudie använde vi [tillståndssidosättningskomponenter](../../overview/features/override-hierarchical-state.md) för att ändra nyansfärgen för markerade objekt. Nu vill vi uppnå en liknande effekt, men gör det genom att ändra [materialet](../../concepts/materials.md) i ett objekt.

Först behöver vi ett skript för att plocka objekt, som vi gjorde i [den andra handledningen](working-with-remote-entities.md). Om du inte har ett **RemoteRaycaster-skript** ännu skapar du det nu. Ersätt innehållet med följande kod:

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

Lägg till komponenten i *fjärrrenderingsspelobjektet.* Den ansvarar för att plocka objekt under musen och lägga till *RemoteModelEntity-komponenter* i de plockade objekten. Den komponentklassen är där vi implementerar den faktiska materialförändringsfunktionen.

Om du inte har ett **RemoteModelEntity-skript** ännu skapar du det och ersätter innehållet med den här koden:

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

När du kör den här koden markeras objekt som du hovrar med musen. Effekten liknar vad vi gjorde i handledning 2, men hur det uppnås är annorlunda. Här får vi listan över material på det plockade objektet och sedan ändra den första att ha en annan albedo färg.

> [!IMPORTANT]
> Tänk på att om den här metoden belyser rätt delar av en modell beror på hur en modell skapas. Det kommer att fungera perfekt, om varje objekt använder exakt ett material. Men om modellen inte har en 1:1 relation mellan delar och material, kommer den naiva koden ovan inte göra det rätta.

## <a name="use-a-different-texture"></a>Använda en annan textur

[Texturer](../../concepts/textures.md) är vanligtvis en del av en källmodell. Under [modellkonvertering](../../quickstarts/convert-model.md)konverteras alla texturer till det nödvändiga körningsformatet och paketeras till den slutliga modellfilen. Om du vill ersätta en textur vid körning måste du spara den i [DDS-filformat](https://en.wikipedia.org/wiki/DirectDraw_Surface) och överföra den till Azure blob storage. I [den här snabbstartsguiden finns i den här snabbstartsguiden](../../quickstarts/convert-model.md) för hur du skapar en Azure-blob-behållare. När du har en blob-behållare kan du öppna den i Azure Storage Explorer och ladda upp filen via dra och släpp.

På körningssidan kan du adressera en texturtillgång i bloblagring på två olika sätt:

* Adressstruktur av dess SAS URI. För det högerklickar du på den uppladdade filen och väljer "**Få delad åtkomstsignatur...**" från snabbmenyn. Använd denna SAS URI med funktionsvarianten `LoadTextureFromSASAsync` (se exempelkod nedan).
* Åtgärda texturen med blob storage-parametrar direkt, om [blob-lagringen är länkad till kontot](../../how-tos/create-an-account.md#link-storage-accounts). Relevant lastfunktion i `LoadTextureAsync`detta fall är .

Öppna nu **RemoteModelEntity-skriptet,** lägg till följande kod och ta bort dubblettfunktioner:

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

Kör den här koden och hovra över din modell. Om din modell har rätt UV-koordinater, bör du se din textur visas. Annars kan du bara märka en färgförändring.

## <a name="next-steps"></a>Nästa steg

Därmed avslutas vår introduktionsserie om hur du använder Azure Remote Rendering med Unity. Som ett nästa steg bör du bekanta dig med några grundläggande begrepp i ARR, till exempel [sessioner,](../../concepts/sessions.md) [entiteter](../../concepts/entities.md)och [modeller](../../concepts/models.md) för att skapa en djupare förståelse. Det finns också olika funktioner som [lampor,](../../overview/features/lights.md) [dispositionsrendering,](../../overview/features/outlines.md) [hierarkiska tillståndsöversidoringar](../../overview/features/override-hierarchical-state.md)och [material](../../concepts/materials.md) som du bör utforska mer i detalj.

> [!div class="nextstepaction"]
> [Unity spel objekt och komponenter](../../how-tos/unity/objects-components.md)
