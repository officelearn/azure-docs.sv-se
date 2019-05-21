---
title: Självstudie – stegvisa anvisningar för att skapa en ny HoloLens Unity-app med hjälp av Azure Spatial ankare | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en ny HoloLens Unity-app med hjälp av Azure Spatial fästpunkter.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3c125c54c1e0a70cdec19af912b17759d82a9936
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969470"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Självstudier: Stegvisa instruktioner för att skapa en ny HoloLens Unity-app med hjälp av Azure Spatial ankare

Den här självstudien visar hur du skapar en ny HoloLens Unity-app med Azure Spatial fästpunkter.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa den här självstudien måste du ha:

1. En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> installeras med den **Universal Windows Platform-utveckling** arbetsbelastning och **Windows 10 SDK (10.0.17763.0 eller senare)** komponenten och <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
2. [C++/WinRT Visual Studio-tillägget (VSIX)](https://aka.ms/cppwinrt/vsix) för Visual Studio bör installeras från [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. En HoloLens-enhet med [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat. Den här artikeln kräver en HoloLens-enhet med [Windows-uppdateringen från 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (kallas även RS5). Uppdatera till den senaste versionen på HoloLens genom att öppna appen **Inställningar**, gå till **Uppdatering och säkerhet** och välja **Sök efter uppdateringar**.

## <a name="getting-started"></a>Komma igång

Vi först ange våra projekt och Unity scen:
1. Starta Unity.
2. Välj **Ny**.
4. Se till att **3D** har valts.
5. Namnge ditt projekt och ange sparas **plats**.
6. Klicka på **skapa projekt**.
7. Spara scenen tom standard till en ny fil med: **Filen** > **Spara som**.
8. Namnge ny scen **Main** och tryck på den **spara** knappen.

**Konfigurera inställningar för projektet**

Vi kan nu ange vissa inställningar för Unity-projekt som hjälper oss att målet Windows Holographic SDK för utveckling. 

Först, kan du ange kvalitetsinställningar för för vårt program. 
1. Välj **redigera** > **Projektinställningar** > **kvalitet**
2. I kolumnen under den **Windows Store** logotyp, klicka på pilen i det **standard** raden och välj **mycket låg**. Du kan hålla reda på inställningen tillämpas korrekt när rutan i den **Windows Store** kolumn och **mycket låg** rad är grönt.

Vi behöver låta Unity vet att appen som vi försöker exportera ska skapa en integrerande vy i stället för en 2D-vy. Vi skapar du en integrerande sammanfattningsvy genom att aktivera stöd för virtuella verklighet på Unity som riktar in sig på Windows 10 SDK.

1. Gå till **redigera** > **Projektinställningar** > **Player**.
2. I den **panelen Granska** för **Player-inställningar**väljer den **Windows Store** ikon.
3. Expandera den **XR inställningar** grupp.
4. I den **Rendering** avsnittet, kontrollera den **virtuella verklighet stöds** kryssrutan om du vill lägga till en ny **virtuella verklighet SDK** lista.
5. Kontrollera att **Windows Mixed Reality** visas i listan. Om inte, Välj den **+** knappen längst ned i listan och väljer **Windows Mixed Reality**.
 
> [!NOTE]
> Om du inte ser ikonen för Windows Store, dubbelklickar du kontrollera att du har valt Windows Store .NET-skript-serverdelen före installationen. Om inte, du kan behöva installera om Unity med rätt Windows-installationen.

**Verifiera konfigurationen av .NET**
1. Gå till **redigera** > **Projektinställningar** > **Player** (du kan fortfarande ha **Player** öppna från den tidigare steg).
2. I den **panelen Granska** för **Player-inställningar**väljer den **Windows Store** ikon.
3. I den **andra inställningar** Configuration avsnittet, se till att **skript serverdel** är inställd på **.NET**.

**set-funktioner**
1. Gå till **redigera** > **Projektinställningar** > **Player** (du kan fortfarande ha **Player** öppna från den tidigare steg).
2. I den **panelen Granska** för **Player-inställningar**väljer den **Windows Store** ikon.
3. I den **publiceringsinställningarna** konfigurationsavsnittet, kontrollera **InternetClientServer** och **SpatialPerception**.

**Ställ in största virtuella kameran**
1. I den **hierarki panelen**väljer **Main kamera**.
2. I den **Inspector**, ange dess transform-placering **0,0,0**.
3. Hitta den **Rensa flaggor** egenskapen, och ändra listrutan från **Skybox** till **enfärgad**.
4. Klicka på den **bakgrund** fältet för att öppna en färgväljare.
5. Ange **R, G, B och A** till **0**.
6. Välj **Lägg till komponent** och Sök efter **Spatial mappning Collider**.

**Skapa våra skript**
1. I den **projekt** fönstret Skapa en ny mapp **skript**under den **tillgångar** mapp. 
2. Högerklicka på mappen och välj sedan **skapa >**,  **C# skriptet**. Rubriken **AzureSpatialAnchorsScript**. 
3. Gå till **GameObject** -> **skapa tom**. 
4. Markera den och i den **Inspector** Byt namn på den från **GameObject** till **MixedRealityCloud**. Välj **Lägg till komponent** och söka efter och lägga till den **AzureSpatialAnchorsScript**.

## <a name="trying-it-out"></a>Att använda
Skapa din app för att testa att allt fungerar **Unity** och distribuera den från **Visual Studio**. Följ kapitel 6 från den [ **MR grunderna 100: Komma igång med Unity** kursen](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) att göra detta. Du bör se Unity Starta skärmen och sedan rensa skärmen.

## <a name="place-an-object-in-the-real-world"></a>Placera ett objekt i den verkliga världen
Nu ska vi skapa och placera ett objekt som använder din app. Öppna Visual Studio-lösningen som vi skapade när vi [distribuerat vår app](#trying-it-out). 

Lägg först till följande importer i din `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Lägg sedan till följande variabler för medlemmar i din `AzureSpatialAnchorsScript` klass: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Lägg sedan till följande kod till din `Start()` metod. Den här koden kommer att koppla samman `GestureRecognizer`, som identifierar när det finns en luften och anropa `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

Nu har vi lägga till följande `HandleTap()` metod nedan `Update()`. Den gör en ray cast och få en träffar som du vill placera en sfär. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

Vi behöver nu skapa området. Området är inledningsvis vit, men det här värdet kommer att justeras vid ett senare tillfälle. Lägg till följande `CreateAndSaveSphere()` metoden:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Kör en app från **Visual Studio** att verifiera den en gång till. Den här gången tryck på skärmen för att skapa och placera din vit sphere över ytan på ditt val.

## <a name="set-up-the-dispatcher-pattern"></a>Konfigurera dispatcher-mönster

När du arbetar med Unity, måste alla Unity API: er, till exempel API: er som du använder för att göra UI-uppdateringar på huvudtråden. I den kod som vi skriver men få vi återanrop på andra trådar. Vi vill uppdatera Användargränssnittet i dessa återanrop så att vi behöver ett sätt att gå från en separat tråd till huvudtråden. För att köra kod på huvudtråden från en separat tråd, använder vi dispatcher-mönster. 

Vi lägger till en medlemsvariabel, dispatchQueue, vilket är en kö av åtgärder. Vi push-åtgärder till kön, och sedan ta bort från kön och kör åtgärderna på huvudtråden. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Nu ska vi lägga till ett sätt att lägga till en åtgärd i kön. Lägg till `QueueOnUpdate()` direkt efter `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

Nu ska vi nu använda Update() loopen och kontrollera om det finns en åtgärd i kö. I så fall ska vi ta bort från kön åtgärden och kör den.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Hämta Azure Spatial ankare SDK

Vi kommer nu att ladda ned Azure-SDK: N för Spatial fästpunkter. Gå till [Azure Spatial ankare GitHub-sidan med versioner](https://github.com/Azure/azure-spatial-anchors-samples/releases). Under tillgångar, ladda ned den **AzureSpatialAnchors.unitypackage** fil. 

I Unity, går du till **tillgångar**, klickar du på **Importera paket** > **anpassade paket...** . Navigera till paketet och välj **öppna**.

I den nya **importera Unity-paketet** fönster som POP, Välj **ingen** längst ned till vänster. Sedan under **AzureSpatialAnchorsPlugin** > **plugin-program**väljer **vanliga**, **redigeraren**, och  **HoloLens**. Klicka på **Import** i det nedre högra hörnet.

Vi nu måste du återställa Nuget-paket för att hämta Azure SDK för Spatial fästpunkter. Skapa från **Unity** och öppna och skapa den resulterande **Visual Studio** lösningen igen, enligt beskrivningen i [testar den](#trying-it-out). 

I din **Visual Studio** lösning, Lägg till följande import till din `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Lägg sedan till följande Medlemsvariabler i din `AzureSpatialAnchorsScript` klass:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Bifoga en lokal Spatial ankare för Azure till lokala fästpunkt

Vi ställer in Azure Spatial ankare CloudSpatialAnchorSession. Vi börjar genom att lägga till följande `InitializeSession()` metod i din `AzureSpatialAnchorsScript` klass. När anropas säkerställer en Spatial fästpunkter för Azure-session skapas och initierats korrekt vid start av din app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

Vi nu måste du skriva kod för att hantera ombud anrop. Vi ska lägga till fler dem när vi fortsätter.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

Nu kan vi koppla din `initializeSession()` metod till din `Start()` metod.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Slutligen lägger du till följande kod till din `CreateAndSaveSphere()` metod. Det ska kopplas till en lokal Azure Spatial ankare inom det område som vi monterar i verkligheten.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Innan du fortsätter, måste du skapa en Azure-Spatial fästpunkter konto-ID och nyckel, om inte redan har. Se följande avsnitt för att få dem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Ladda upp ankaret lokalt till molnet

När du har din Azure Spatial ankare konto identifieraren och nyckel kan gå och klistra in den `Account Id` till `SpatialAnchorsAccountId` och `Account Key` till `SpatialAnchorsAccountKey`.

Slutligen ska vi anslutas allt tillsammans. I din `SpawnNewAnchoredObject()` metoden Lägg till följande kod. Den anropar den `CreateAnchorAsync()` metoden så snart din sphere har skapats. När metoden returnerar, koden nedan utför en slutlig uppdatering av din sfär, ändra dess färg till blått.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Kör en app från **Visual Studio** en gång till. Flytta runt huvudet och sedan trycka i luften på tryck för att placera din sphere. När vi har tillräckligt med bildrutor området inaktiveras i gult och molnet överföringen startar. När överföringen är klar blir din sphere blå. Du kan också använda utdatafönstret i **Visual Studio** att övervaka din app skickar loggmeddelanden. Du kommer att kunna titta på den rekommenderade för skapa förlopp, samt det ankar-ID som molnet returnerar när överföringen är klar.

> [!NOTE]
> Om du får ”DllNotFoundException: Det gick inte att läsa in DLL 'AzureSpatialAnchors': Det gick inte att hitta den angivna modulen ”., bör du **Rengör** och **skapa** din lösning igen.

## <a name="locate-your-cloud-spatial-anchor"></a>Leta upp ankaret molnet spatial

En ankaret överförs till molnet, vi är redo att försöka hitta det igen. Lägg till följande kod till din `HandleTap()` metod. Den här koden kommer att:

* Anropa `ResetSession()`, vilket stoppar den `CloudSpatialAnchorSession` och ta bort våra befintliga blå sphere från skärmen.
* Initiera `CloudSpatialAnchorSession` igen. Vi gör det så vi vet till ankaret som vi kommer att hitta kommer från molnet i stället för till lokala ankaret som vi skapade.
* Skapa en **Watcher** som söker efter till ankaret som vi laddade upp till Azure Spatial fästpunkter.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Låt oss nu lägga till vår `ResetSession()` och `CleanupObjects()` metoder. Du kan placera dem nedan `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Vi måste nu koppla samman den kod som kommer att anropas när vi frågar för ankaret finns. Inuti `InitializeSession()`, Lägg till följande återanropen:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
Nu ska vi lägga till kod som ska skapa och placera en grön sfär när CloudSpatialAnchor finns. Det gör även att skärmen trycka igen, så att du kan upprepa hela scenariot en gång till: skapa en annan lokal fästpunkt, ladda upp den och leta upp den igen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

Klart! Kör en app från **Visual Studio** en sista gång du prova att använda hela scenariot från slutpunkt till slutpunkt. Flytta din enhet och placera din vit sphere. Sedan att flytta huvudet att samla in miljödata tills området blir gul. Ankaret lokala ska överföras och din sphere inaktiveras blå. Knacka på skärmen en gång till så att ankaret lokala tas bort och sedan ska vi frågar för motparten molnet. Fortsätta flytta enheten förrän ankaret molnet spatial finns. En grön sfär ska visas på rätt plats och du kan spola och upprepa hela scenariot igen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]