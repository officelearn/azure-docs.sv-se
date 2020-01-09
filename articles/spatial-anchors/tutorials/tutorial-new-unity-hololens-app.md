---
title: 'Självstudie: skapa en ny HoloLens Unit-app'
description: I den här självstudien får du lära dig hur du skapar en ny HoloLens Unity-app med hjälp av Azure spatiala ankare.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457719"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Självstudie: steg-för-steg-anvisningar för att skapa en ny HoloLens Unity-app med hjälp av Azure spatiala ankare

I den här självstudien visas hur du skapar en ny HoloLens Unity-app med Azure spatiala ankare.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha:

1. En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> installerad med arbets belastningen **universell Windows-plattform utveckling** och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)** och <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a>.
2. [C++/WinRT Visual Studio-tillägget (VSIX)](https://aka.ms/cppwinrt/vsix) för Visual Studio bör installeras från [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. En HoloLens-enhet med [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) aktiverat. Den här artikeln kräver en HoloLens-enhet med [Windows-uppdateringen från 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (kallas även RS5). Uppdatera till den senaste versionen på HoloLens genom att öppna appen **Inställningar**, gå till **Uppdatering och säkerhet** och välja **Sök efter uppdateringar**.

## <a name="getting-started"></a>Komma igång

Vi konfigurerar först vår projekt-och Unity-scen:
1. Start Unit.
2. Välj **Ny**.
4. Se till att **3D** är markerat.
5. Namnge projektet och ange en lagrings **plats**.
6. Klicka på **skapa projekt**.
7. Spara den tomma standard scenen till en ny fil med hjälp av: **fil** > **Spara som**.
8. Namnge den nya scenens **huvud** namn och tryck på knappen **Spara** .

**Konfigurera projekt inställningarna**

Vi kommer nu att ange vissa inställningar för enhets projekt som hjälper oss att rikta Windows Holographic SDK för utveckling.

Först kan du ange kvalitets inställningar för programmet.
1. Välj **redigera** > **projekt inställningar** > **kvalitet**
2. I kolumnen under **Windows Store** -logotypen klickar du på pilen på **standard** raden och väljer **mycket låg**. Du vet att inställningen tillämpas korrekt när rutan i **Windows Store** -kolumnen och en **mycket låg** rad är grön.

Vi måste meddela att appen vi försöker exportera ska kunna skapa en fördjupad vy i stället för en 2D-vy. Vi skapar en fördjupad vy genom att aktivera stöd för Virtual Reality på Unity riktade mot Windows 10 SDK.

1. Gå till **redigera** > **projekt inställningar** > **Player**.
2. I **panelen kontrollant** för **Player-inställningar**väljer du **Windows Store** -ikonen.
3. Expandera **inställnings gruppen XR** .
4. I avsnittet **rendering** markerar du kryss rutan **Virtual verklighet support** för att lägga till en ny **Virtual verklighet SDK** -lista.
5. Kontrol lera att **Windows Mixed Reality** visas i listan. Om inte väljer du knappen **+** längst ned i listan och väljer **Windows Mixed Reality**.

> [!NOTE]
> Om du inte ser Windows Store-ikonen, kontrol lera att du har valt Windows Store .NET-skript Server del före installationen. Om inte, kan du behöva installera om Unity med rätt Windows-installation.

**Verifiera konfiguration av skript Server del**
1. Gå till **redigera** > **projekt inställningar** > **Player** (du kanske fortfarande har en **spelare** öppen från föregående steg).
2. I **panelen kontrollant** för **Player-inställningar**väljer du **Windows Store** -ikonen.
3. I avsnittet **andra** konfigurations inställningar kontrollerar du att **skript Server delen** är inställd på **IL2CPP**.

**Ange funktioner**
1. Gå till **redigera** > **projekt inställningar** > **Player** (du kanske fortfarande har en **spelare** öppen från föregående steg).
2. I **panelen kontrollant** för **Player-inställningar**väljer du **Windows Store** -ikonen.
3. I avsnittet konfiguration av **publicerings inställningar** kontrollerar du **InternetClientServer** och **SpatialPerception**.

**Konfigurera den virtuella huvud kameran**
1. I **panelen hierarki**väljer du **huvud kamera**.
2. I **kontrollen**anger du dess omvandlings position till **0, 0, 0**.
3. Hitta egenskapen **Clear Flags** och ändra List rutan från **Skybox** till **heldragen färg**.
4. Klicka på **bakgrunds** fältet för att öppna en färg väljare.
5. Ange **R, G, B och A** till **0**.
6. Välj **Lägg till komponent** och Sök efter och Lägg till en **kollision för spatial mappning**.

**Skapa vårt skript**
1. I fönstret **projekt** skapar du en ny mapp, **skript**, under mappen **till gångar** .
2. Högerklicka på mappen och välj **Skapa >** ,  **C# skript**. Rubriken IT- **AzureSpatialAnchorsScript**.
3. Gå till **GameObject** -> **Skapa tom**.
4. Markera den och i **kontrollanten** byter du namn på den från **GameObject** till **MixedRealityCloud**. Välj **Lägg till komponent** och Sök efter och Lägg till **AzureSpatialAnchorsScript**.

**Skapa sfär-Prefab**
1. Gå till **GameObject** -> **3d-objektet** -> **Sphere**.
2. I **kontrollen**anger du dess skala till **0,25, 0,25, 0,25**.
3. Hitta **klot** -objektet i **hierarkifönstret** . Klicka på den och dra den till mappen **till gångar** i fönstret **projekt** .
4. Högerklicka och **ta bort** den ursprungliga klotet som du skapade i **hierarkifönstret** .

Nu bör du ha en sfär-Prefab i ditt **projekt** fönster.

## <a name="trying-it-out"></a>Testa
Testa att allt fungerar genom att bygga din **app i enhet** och distribuera den från **Visual Studio**. Följ kapitel 6 från [ **Mr-grunderna 100: kom igång med en unions** kurs](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) för att göra det. Du bör se sidan Unity start och sedan en tydlig visning.

## <a name="place-an-object-in-the-real-world"></a>Placera ett objekt i den verkliga världen
Nu ska vi skapa & Placera ett objekt med din app. Öppna Visual Studio-lösningen som vi skapade när vi [distribuerade vår app](#trying-it-out).

Lägg först till följande importer i `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Lägg sedan till följande medlemsvariabler-variabler i `AzureSpatialAnchorsScript`-klassen:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Innan vi fortsätter måste vi ställa in sfär-Prefab som vi skapade i vår spherePrefab-medlems variabel. Gå tillbaka till **Unity**.
1. I **Unity**väljer du **MixedRealityCloud** -objektet i fönstret **hierarki** .
2. Klicka på **sfär** -Prefab som du sparade i fönstret **projekt** . Dra **klotet** som du klickade på i **sfär Prefab** -området under **Azure spatial ankare skript (skript)** i fönstret **kontrollant** .

Du bör nu ha **sfär** uppsättningen som Prefab i skriptet. Bygg från **Unity** och öppna sedan den resulterande **Visual Studio** -lösningen igen, precis som när du precis gjorde [det](#trying-it-out).

I **Visual Studio**öppnar du `AzureSpatialAnchorsScript.cs` igen. Lägg till följande kod i `Start()`-metoden. Den här koden kopplar samman `GestureRecognizer`, vilket kommer att identifiera när det finns ett luft tryck och anropa `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Vi måste nu lägga till följande `HandleTap()` metod nedan `Update()`. Den kommer att göra en Ray-Cast och få en träff punkt där du kan placera en sfär.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Vi måste nu skapa sfären. Klotet blir inlednings vis vitt, men det här värdet kommer att justeras senare. Lägg till följande `CreateAndSaveSphere()` metod:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Kör appen från **Visual Studio** för att verifiera den en gång till. Nu trycker du på skärmen för att skapa & Placera din vita sfär över valfri yta.

## <a name="set-up-the-dispatcher-pattern"></a>Konfigurera dispatcher-mönstret

När du arbetar med Unity, alla unions-API: er som du använder för att göra GRÄNSSNITTs uppdateringar, måste ske i huvud tråden. I koden kommer vi att skriva återanrop på andra trådar. Vi vill uppdatera användar gränssnittet i dessa återanrop, så vi behöver ett sätt att gå från en tråds tråd till huvud tråden. Om du vill köra kod i huvud tråden från en sido tråd använder vi dispatcher-mönstret.

Nu ska vi lägga till en medlems variabel, dispatchQueue, som är en kö med åtgärder. Vi kommer att skicka åtgärder till kön och sedan ta bort kön och köra åtgärderna i huvud tråden.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Nu ska vi lägga till ett sätt att lägga till en åtgärd i kön. Lägg till `QueueOnUpdate()` direkt efter `Update()`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Nu ska vi använda loopen Update () för att kontrol lera om det finns en åtgärd i kö. I så fall, kommer vi att köa åtgärden och köra den.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Hämta SDK för Azure spatiala ankare

## <a name="via-unity-packagetabunitypackage"></a>[Via Unity-paket](#tab/UnityPackage)

Vi kommer nu att ladda ned Azures spatiala ankare SDK. Gå till [sidan med GitHub-versioner för Azure spatial ankare](https://github.com/Azure/azure-spatial-anchors-samples/releases). Under till gångar laddar du ned **AzureSpatialAnchors. unitypackage**. I Unity går du till **till gångar**, klickar på **Importera paket** > **anpassat paket.** .. Navigera till paketet och välj **Öppna**.

I fönstret nytt **import Uniting-paket** som öppnas, avmarkerar du **plugin** -program och klickar sedan på **Importera** i det nedre högra hörnet.

Vi behöver nu återställa NuGet-paket för att få Azures spatialdata SDK. Bygg från **Unity** och öppna och bygg sedan den resulterande **Visual Studio** -lösningen igen, så som beskrivs i [testa](#trying-it-out).

## <a name="via-nugetforunitytabnugetforunity"></a>[Via NuGetForUnity](#tab/NuGetForUnity)

Först måste vi installera NuGetForUnity. Gå till [sidan med NuGetForUnity GitHub-versioner](https://github.com/GlitchEnzo/NuGetForUnity/releases). Under till gångar laddar du ned det senaste **NuGetForUnity. unitypackage**. I Unity går du till **till gångar**, klickar på **Importera paket** > **anpassat paket.** .. Navigera till paketet och välj **Öppna**. Unity kommer nu att installera NugetForUnity. Om du inte ser en ny **NuGet** -listruta i enhet kan du behöva Högerklicka under **projekt** > **till gångar**. Välj sedan **Importera alla**.

När du har installerat NuGetForUnity väljer du **NuGet** > **Hantera NuGet-paket**. Sök sedan efter Microsoft. Azure. SpatialAnchors. Unition och välj **Installera**.

Vi behöver nu bygga för att få fram de faktiska Azure-Ankarea ankare som SDK, som det NuGet-paket som vi precis hämtade bara innehåller hjälp skript. Bygg från **Unity** och öppna och bygg sedan den resulterande **Visual Studio** -lösningen igen, så som beskrivs i [testa](#trying-it-out).

---

I din **Visual Studio** -lösning lägger du till följande import i `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Lägg sedan till följande medlemsvariabler i `AzureSpatialAnchorsScript`-klassen:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Koppla en lokal Azure-spatial fäst punkt till den lokala fäst punkten

Vi konfigurerar Azure spatial ankare CloudSpatialAnchorSession. Vi börjar med att lägga till följande `InitializeSession()`-metod i `AzureSpatialAnchorsScript`-klassen. När den har anropats ser den till att en session med Azure-ankare skapas och initieras korrekt under starten av din app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Vi måste nu skriva kod för att hantera ombuds anrop. Vi lägger till fler i dem när vi fortsätter.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Nu ska vi koppla din `initializeSession()` metod till din `Start()` metod.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Slutligen lägger du till följande kod i `CreateAndSaveSphere()`-metoden. Den ansluter en lokal Azure-spatial fäst punkt till den sfär som vi placerar i den verkliga världen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Innan du fortsätter måste du skapa en konto identifierare och nyckel för Azure spatial ankare, om du inte redan har dem. Använd följande avsnitt för att hämta dem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Ladda upp ditt lokala ankare i molnet

När du har en konto identifierare och nyckel för ditt Azure spatiala ankare går du till och klistrar in `Account Id` i `SpatialAnchorsAccountId` och `Account Key` i `SpatialAnchorsAccountKey`.

Slutligen är det dags att koppla ihop allt. Lägg till följande kod i din `SpawnNewAnchoredObject()` metod. Den anropar `CreateAnchorAsync()`-metoden så snart din sfär skapas. När metoden returnerar utför koden nedan en slutgiltig uppdatering av din sfär och ändrar färgen till blå.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Kör appen från **Visual Studio** en gång till. Flytta runt ditt huvud och tryck sedan för att placera din sfär. När vi har tillräckligt många ramar förvandlas klotet till gult och moln överföringen startar. När uppladdningen är klar blir klotet blå. Alternativt kan du också använda fönstret utdata i **Visual Studio** för att övervaka de logg meddelanden som appen skickar. Du kommer att kunna se vad som rekommenderas för att skapa förlopp, samt ankar-ID: t som molnet returnerar när överföringen är klar.

> [!NOTE]
> Om du får "DllNotFoundException: det går inte att läsa in DLL-filen AzureSpatialAnchors: det gick inte att hitta den angivna modulen", bör du **rengöra** och **bygga** lösningen igen.

## <a name="locate-your-cloud-spatial-anchor"></a>Hitta molnets avstånds ankare

En fäst punkt överförs till molnet, vi är redo att försöka hitta den igen. Nu ska vi lägga till följande kod i `HandleTap()`-metoden. Den här koden kommer att:

* Anropa `ResetSession()`som kommer att stoppa `CloudSpatialAnchorSession` och ta bort vår befintliga blå sfär från skärmen.
* Initiera `CloudSpatialAnchorSession` igen. Vi gör detta för att vi ska kunna hitta ankaret som vi ska hitta från molnet istället för att vara det lokala ankare som vi har skapat.
* Skapa en **övervakare** som söker efter den fäst som vi har laddat upp till Azure spatiala ankare.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Nu ska vi lägga till våra `ResetSession()` och `CleanupObjects()` metoder. Du kan sortera dem nedan `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Vi måste nu koppla upp koden som ska anropas när ankaret som vi frågar efter finns. I `InitializeSession()`lägger du till följande återanrop:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Nu kan du lägga till kod som skapar & att placera en grön sfär när CloudSpatialAnchor är placerad. Det aktiverar också skärm tryckning igen, så du kan upprepa hela scenariot en gång till: skapa ett annat lokalt ankare, ladda upp det och hitta det igen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Klart! Kör din app från **Visual Studio** en sista gången för att testa hela scenariot från början till slut. Flytta runt enheten och placera din vita sfär. Fortsätt sedan att flytta ditt huvud till att avbilda miljö data tills klotet blir gult. Ditt lokala ankare laddas upp och sfären blir blå. Till sist trycker du på skärmen en gång till, så att ditt lokala ankare tas bort och vi kommer att fråga efter dess moln motsvarighet. Fortsätt att flytta enheten tills molnets spatiala ankare är belägen. En grön sfär bör visas på rätt plats och du kan skölja & upprepa hela scenariot igen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]