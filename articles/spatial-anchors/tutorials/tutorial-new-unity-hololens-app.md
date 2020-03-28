---
title: 'Självstudiekurs: Skapa en ny HoloLens Unity-app'
description: I den här självstudien får du lära dig hur du skapar en ny HoloLens Unity-app med Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75457719"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Självstudiekurs: Steg-för-steg-instruktioner för att skapa en ny HoloLens Unity-app med Azure Spatial Anchors

Den här självstudien visar hur du skapar en ny HoloLens Unity-app med Azure Spatial Anchors.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha:

1. En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> installerad med **arbetsbelastningen för universell utveckling av Windows-plattformen** och **Windows 10 SDK-komponenten (10.0.18362.0 eller nyare)** och <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
2. [C++/WinRT Visual Studio-tillägget (VSIX)](https://aka.ms/cppwinrt/vsix) för Visual Studio bör installeras från [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. En HoloLens-enhet med [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat. Den här artikeln kräver en HoloLens-enhet med [Windows-uppdateringen från 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (kallas även RS5). Uppdatera till den senaste versionen på HoloLens genom att öppna appen **Inställningar**, gå till **Uppdatering och säkerhet** och välja **Sök efter uppdateringar**.

## <a name="getting-started"></a>Komma igång

Vi sätter först upp vårt projekt och Unity scen:
1. Starta Unity.
2. Välj **Ny**.
4. Se till att **3D** är valt.
5. Namnge projektet och ange en spara **plats**.
6. Klicka på **Skapa projekt**.
7. Spara den tomma standardscenen i en ny fil med: **Spara** > **fil som**.
8. Namnge den nya scenen **Main** och tryck på **knappen Spara.**

**Ställ in projektinställningarna**

Vi kommer nu att ställa in några Unity-projektinställningar som hjälper oss att rikta in oss på Windows Holographic SDK för utveckling.

Låt först ställa in kvalitetsinställningar för vårt program.
1. Välj **Redigera** > **projektinställningarskvalitet** **Project Settings** > 
2. I kolumnen under **Windows Store-logotypen** klickar du på pilen på **standardraden** och väljer **Mycket låg**. Du vet att inställningen tillämpas korrekt när rutan i **kolumnen Windows Store** och raden Mycket **låg** är grön.

Vi måste låta Unity veta att appen vi försöker exportera bör skapa en uppslukande vy i stället för en 2D-vy. Vi skapar en uppslukande vy genom att aktivera virtual reality-stöd för Unity som riktar sig till Windows 10 SDK.

1. Gå till **Redigera** > **Project Settings** > **Player**.
2. Välj **Windows Store-ikonen** på **inspektörspanelen** för **inställningar för player.**
3. Expandera gruppen **XR-inställningar.**
4. I avsnittet **Rendering** kontrollerar du kryssrutan **Virtual Reality Som stöds** för att lägga till en ny lista över Virtual Reality **SDK.**
5. Kontrollera att **Windows Mixed Reality** visas i listan. Om inte, **+** välj knappen längst ned i listan och välj **Windows Mixed Reality**.

> [!NOTE]
> Om windows store-ikonen inte visas dubbelkollas du för att kontrollera att du har valt Windows Store .NET Scripting Backend före installationen. Om inte, kan du behöva installera om Unity med rätt Windows-installation.

**Verifiera serverningskonfiguration för skript**
1. Gå till **Redigera** > **Project Settings** > **Player** (du kan fortfarande ha **Player** öppet från föregående steg).
2. Välj **Windows Store-ikonen** på **inspektörspanelen** för **inställningar för player.**
3. Kontrollera att **skriptdelens serverdel** är inställd på **IL2CPP**i avsnittet **Andra inställningar.**

**Ange funktioner**
1. Gå till **Redigera** > **Project Settings** > **Player** (du kan fortfarande ha **Player** öppet från föregående steg).
2. Välj **Windows Store-ikonen** på **inspektörspanelen** för **inställningar för player.**
3. I avsnittet Konfiguration **av publiceringsinställningar** kontrollerar du **InternetClientServer** och **SpatialPerception**.

**Konfigurera huvudkameran för virtuell kamera**
1. Välj **Huvudkamera**på **hierarkipanelen**.
2. I **inspektören**anger du dess transformeringsposition till **0,0,0**.
3. Leta reda på egenskapen **Rensa flaggor** och ändra listrutan från **Skybox** till **Enfärgad**.
4. Klicka på fältet **Bakgrund** för att öppna en färgväljare.
5. Ställ in **R, G, B och A** till **0**.
6. Välj **Lägg till komponent** och sök efter och lägg till spatial mapping **collider**.

**Skapa vårt skript**
1. Skapa en ny mapp, **Skript**under mappen **Resurser** i **projektfönstret.**
2. Högerklicka på mappen och välj sedan **Skapa >**, **C# Script**. Titel det **AzureSpatialAnchorsScript**.
3. Gå till **GameObject** -> **Skapa tom**.
4. Markera den och **byt** namn på den från **GameObject** till **MixedRealityCloud**. Välj **Lägg till komponent** och sök efter och lägg till **AzureSpatialAnchorsScript**.

**Skapa sfären prefab**
1. Gå till **GameObject** -> **3D-objekt** -> **sfär**.
2. I **inspektören**anger du dess skala till **0,25, 0,25, 0,25**.
3. Leta reda på **sphere-objektet** i **hierarkifönstret.** Klicka på den och dra den till mappen **Tillgångar** i **projektfönstret.**
4. Högerklicka och **ta bort** den ursprungliga sfären som du skapade i fönstret **Hierarki.**

Du bör nu ha en sfärprefab i **projektfönstret.**

## <a name="trying-it-out"></a>Prova det
Om du vill testa att allt fungerar skapar du appen i **Unity** och distribuerar den från **Visual Studio**. Följ kapitel 6 från [ **MR Basics 100: Komma igång med Unity** kurs](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) för att göra det. Du bör se Unity startskärmen och sedan en tydlig skärm.

## <a name="place-an-object-in-the-real-world"></a>Placera ett objekt i den verkliga världen
Nu ska vi skapa & placera ett objekt med hjälp av appen. Öppna Visual Studio-lösningen som vi skapade när vi [distribuerade vår app.](#trying-it-out)

Lägg först till följande `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`import i din:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Lägg sedan till följande medlemsvariabler i klassen: `AzureSpatialAnchorsScript`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Innan vi fortsätter måste vi ställa in den sfär prefab vi skapade på vår spherePrefab medlem variabel. Gå tillbaka till **Unity**.
1. Markera **Kommandot MixedRealityCloud** i **fönstret Hierarki** i **Unity.**
2. Klicka på **Sphere** sphereprefab som du sparade i **projektfönstret.** Dra den **sfär som** du klickade på till **sphere prefab-området** under **Azure Spatial Anchors Script (Script)** i **fönstret Inspektör.**

Du bör nu ha **spheren** inställd som prefab på ditt manus. Bygg från **Unity** och öppna sedan den resulterande **Visual Studio-lösningen** igen, som du just gjorde i [Prova det](#trying-it-out).

Öppna igen `AzureSpatialAnchorsScript.cs` i **Visual Studio.** Lägg till följande `Start()` kod i din metod. Denna kod kommer `GestureRecognizer`att ansluta , som kommer att `HandleTap`upptäcka när det finns en luftkran och samtal .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Vi måste nu lägga `HandleTap()` till `Update()`följande metod nedan . Det kommer att göra en stråle rösterna och få en träffpunkt där att placera en sfär.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Vi måste nu skapa sfären. Sfären kommer inledningsvis att vara vit, men detta värde kommer att justeras senare. Lägg till `CreateAndSaveSphere()` följande metod:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Kör appen från **Visual Studio** för att verifiera den en gång till. Den här gången trycker du på skärmen för att skapa & placera din vita sfär över valfri yta.

## <a name="set-up-the-dispatcher-pattern"></a>Ställ in avsändarens mönster

När du arbetar med Unity måste alla Unity-API:er, till exempel API:er som du använder för att göra gränssnittsuppdateringar, ske i huvudtråden. I koden vi skriver dock får vi motringning på andra trådar. Vi vill uppdatera användargränssnittet i dessa motringningar, så vi behöver ett sätt att gå från en sidotråd till huvudtråden. För att köra kod på huvudtråden från en sidotråd använder vi avsändaremönstret.

Nu ska vi lägga till en medlemsvariabel, dispatchQueue, som är en kö av åtgärder. Vi kommer att driva åtgärder i kön och sedan avmarkera och köra åtgärder på huvudtråden.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Nu ska vi lägga till ett sätt att lägga till en åtgärd i kön. Lägg `QueueOnUpdate()` till `Update()` direkt efter:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Nu ska vi använda loopen Update() för att kontrollera om det finns en åtgärd i kö. Om så är fallet kommer vi att dequeue åtgärden och köra den.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Skaffa Azure Spatial Anchors SDK

## <a name="via-unity-package"></a>[Via Unity-paketet](#tab/UnityPackage)

Vi hämtar nu Azure Spatial Anchors SDK. Gå till [sidan Azure Spatial Anchors GitHub-versioner](https://github.com/Azure/azure-spatial-anchors-samples/releases). Ladda ned **AzureSpatialAnchors.unitypackage**under Tillgångar . I Unity går du till **Tillgångar,** klickar på **Anpassat paket för importpaket...** > **Custom Package...**. Navigera till paketet och välj **Öppna**.

I det nya fönstret **Importera enhetspaket** som visas avmarkerar du **plugin-program** och klickar sedan på **Importera** i det nedre högra hörnet.

Vi måste nu återställa Nuget-paket för att få Azure Spatial Anchors SDK. Bygg från **Unity** och öppna och bygg sedan den resulterande **Visual Studio-lösningen** igen, som beskrivs i [Prova det](#trying-it-out).

## <a name="via-nugetforunity"></a>[Via NuGetForUnity](#tab/NuGetForUnity)

Först måste vi installera NuGetForUnity. Gå till [sidan NuGetForUnity GitHub-versioner](https://github.com/GlitchEnzo/NuGetForUnity/releases). Under Tillgångar, ladda ner den senaste **NuGetForUnity.unitypackage**. I Unity går du till **Tillgångar,** klickar på **Anpassat paket för importpaket...** > **Custom Package...**. Navigera till paketet och välj **Öppna**. Unity kommer nu att installera NugetForUnity. Om du inte ser en ny **NuGet-listruta** i Unity kan du behöva högerklicka under > **Projekttillgångar**. **Projects** Välj sedan **Importera alla igen**.

När du har installerat NuGetForUnity väljer du **NuGet** > **Hantera NuGet-paket**. Sök sedan efter Microsoft.Azure.SpatialAnchors.Unity och välj **Installera**.

Vi måste nu bygga för att få den faktiska Azure Spatial Anchors SDK, eftersom NuGet paketet vi just hämtat bara innehåller hjälpskript. Bygg från **Unity** och öppna och bygg sedan den resulterande **Visual Studio-lösningen** igen, som beskrivs i [Prova det](#trying-it-out).

---

Lägg till följande import i din `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` **Visual Studio-lösning** i din:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Lägg sedan till följande medlemsvariabler i klassen: `AzureSpatialAnchorsScript`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Koppla ett lokalt Azure Spatial Anchor till det lokala ankaret

Nu konfigurerar vi Azure Spatial Anchors CloudSpatialAnchorSession. Vi börjar med att `InitializeSession()` lägga till `AzureSpatialAnchorsScript` följande metod i din klass. När den har anropats säkerställer den att en Azure Spatial Anchors-session skapas och initieras korrekt under starten av din app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Vi måste nu skriva kod för att hantera delegatsamtal. Vi kommer att lägga till mer till dem när vi fortsätter.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Nu, låt oss `initializeSession()` koppla in `Start()` din metod i din metod.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Lägg slutligen till följande `CreateAndSaveSphere()` kod i din metod. Det kommer att bifoga ett lokalt Azure Spatial Anchor till den sfär som vi placerar i den verkliga världen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Innan du fortsätter vidare måste du skapa en Azure Spatial Anchors-kontoidentifierare och nyckel, om du inte redan har dem. Följ följande avsnitt för att hämta dem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Ladda upp ditt lokala ankare i molnet

När du har din Azure Spatial Anchors-kontoidentifierare `SpatialAnchorsAccountId` och `Account Key` `SpatialAnchorsAccountKey`nyckel går du och klistrar `Account Id` in i och in i .

Slutligen, låt oss koppla ihop allt. Lägg `SpawnNewAnchoredObject()` till följande kod i metoden. Metoden anropas `CreateAnchorAsync()` så snart din sfär har skapats. När metoden återvänder, koden nedan kommer att utföra en slutlig uppdatering till din sfär, ändra dess färg till blå.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Kör appen från **Visual Studio** en gång till. Flytta runt huvudet och sedan luftkran för att placera din sfär. När vi har tillräckligt med ramar, kommer sfären förvandlas till gult, och molnet uppladdningen kommer att starta. När uppladdningen är klar blir din sfär blå. Du kan också använda utdatafönstret i **Visual Studio** för att övervaka de loggmeddelanden som appen skickar. Du kan titta på den rekommenderade för att skapa förlopp, samt ankaridentifieraren som molnet returnerar när överföringen är klar.

> [!NOTE]
> Om du får "DllNotFoundException: Unable to load DLL 'AzureSpatialAnchors': Den angivna modulen kunde inte hittas.", bör du **rensa** och **skapa** din lösning igen.

## <a name="locate-your-cloud-spatial-anchor"></a>Hitta ditt rumsliga ankare i molnet

Ett ankare överförs till molnet, vi är redo att försöka hitta det igen. Låt oss lägga till följande `HandleTap()` kod i din metod. Denna kod kommer att:

* Ring `ResetSession()`, som `CloudSpatialAnchorSession` kommer att stoppa och ta bort vår befintliga blå sfär från skärmen.
* Initiera `CloudSpatialAnchorSession` igen. Vi gör detta så vi är säkra på att ankaret vi kommer att hitta kommer från molnet istället för att vara det lokala ankaret vi skapade.
* Skapa en **watcher** som letar efter ankaret vi laddade upp till Azure Spatial Anchors.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Låt oss nu `ResetSession()` lägga `CleanupObjects()` till våra och metoder. Du kan lägga dem under`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Vi måste nu koppla in koden som kommer att anropas när ankaret vi frågar efter finns. Inuti `InitializeSession()`lägger du till följande motringningar:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Nu kan lägga till kod som kommer att skapa & placera en grön sfär när CloudSpatialAnchor finns. Det kommer också att aktivera skärmavtryck igen, så att du kan upprepa hela scenariot en gång till: skapa ett annat lokalt ankare, ladda upp det och hitta det igen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Klart! Kör appen från **Visual Studio** en sista gång för att testa hela scenariot från början till. Flytta runt enheten och placera din vita sfär. Fortsätt sedan att flytta huvudet för att fånga miljödata tills sfären blir gul. Ditt lokala ankare laddas upp och din sfär blir blå. Tryck slutligen på skärmen en gång till, så att ditt lokala ankare tas bort och sedan frågar vi efter dess molnmotsvarighet. Fortsätt att flytta runt enheten tills molnets rumsliga ankare finns. En grön sfär ska visas på rätt plats och du kan skölja & upprepa hela scenariot igen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]