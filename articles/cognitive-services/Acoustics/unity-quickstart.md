---
title: Projektet Akustik Snabbstart med Unity
titlesuffix: Azure Cognitive Services
description: Med hjälp av exemplen, experimentera med projekt Akustik design kontroller i Unity och distribuera Windows-skrivbordet.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: b4eedabbc47738eb2f5797ffd67a3e3ebc9529ca
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136229"
---
# <a name="project-acoustics-unity-quickstart"></a>Snabbstart för projektet Akustik Unity
Använd projektet Akustik exempelinnehåll för Unity att experimentera med kontroller som backas upp av simulering design.

Programvarukrav:
* [Unity 2018.2 +](http://unity3d.com) för Windows
* [Projektet Akustik exempel innehållspaket](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

Vad ingår i exemplet paketet?
* Unity-scen med geometri, ljud källor och spelupplevelse kontroller
* Projektet Akustik plugin-programmet 
* Bakade Akustik tillgångar för scenen

## <a name="import-the-sample-package"></a>Importera exempelpaketet
Importera exempelpaketet till ett nytt Unity-projekt. 
* I Unity, går du till **tillgångar > Importera paket > anpassat paket...**

    ![Importera paket](media/import-package.png)  

* Choose **ProjectAcoustics.unitypackage**

Om du importerar paketet till ett befintligt projekt, se [Unity-integrering](unity-integration.md) för ytterligare åtgärder och anteckningar.

## <a name="restart-unity"></a>Starta om Unity
Ändamålet delen av verktyget Akustik kräver .NET 4.x scripting runtime-versionen. Paketimport uppdaterar dina Unity player-inställningar. Starta om Unity för den här inställningen ska börja gälla.

Du kan kontrollera den här inställningen tog effekt genom att öppna den **Player-inställningar**:

![Player-inställningar](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="experiment-with-design-controls"></a>Experimentera med design kontroller
Öppna exemplet scen i den **ProjectAcousticsSample** mappen och klicka på uppspelningsknappen i Unity-redigeraren. Använd W, A, S, D och musen för att flytta. Om du vill jämföra hur scenen låter med och utan akustik trycker du på knappen **R** tills överläggstexten blir röd och spelar upp ”Acoustics: Disabled.” (”Akustik: inaktiverad”.) Om du vill se tangentbordsgenvägar för fler kontroller trycker du på **F1**. Kontroller är också användbar genom att högerklicka för att välja åtgärden som ska utföras, sedan vänsterklicka på för att utföra åtgärden.

Skriptet **AcousticsAdjust** är kopplad till ljud källor i exemplet scenen, vilket gör att parametrarna per källkod design. 

![AcousticsAdjust](media/acoustics-adjust.png)

Följande utforskar några av de effekter som kan produceras med de angivna kontrollerna. Detaljerad information om varje kontroll finns i den [projekt Akustik Unity Design självstudien](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Ändra avståndet-baserade dämpning
Ljudet DSP tillhandahålls av den **projekt Akustik** Unity spatializer plugin-programmet respekterar de per källkod avståndet-baserade dämpning bygger i Unity-redigeraren. Kontroller för avståndet-baserade dämpning finns i den **ljud källa** komponent finns i den **Inspector** med ljud datakällor under **3D-ljudinställningar**:

![Avståndet dämpning](media/distance-attenuation.png)

Projektet Akustik utför beräkning i en ”simulering region” heldag player-platsen. Ljud dämpning bör utformas för att växla till 0 på ungefär 45 m eftersom Akustik tillgångar i exempelpaketet har inbyggd med en storlek för simulering av 45m omgivande spelaren.

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
* Om den **ocklusion** multiplikatorn är större än 1 (standardvärdet är 1), är spärrat vara överdrivna. Ange den mindre än 1 gör ocklusion i kraft mer diskreta.

* Om du vill aktivera via vägg överföring, flytta den **överföring (dB)** skjutreglaget av sin lägsta nivå. 

### <a name="modify-wetness-for-a-source"></a>Ändra wetness för en källa
* Du kan ändra hur snabbt wetness ändras med avståndet med den **Perceptuell avståndet tänja**. **Projektet Akustik** beräkningarna provmaterialet nivåer i hela adressutrymmet från simulering, som kan variera smidigt med avståndet och ger Perceptuell avståndet tips. Öka avståndet otroligt blir detta genom att öka avståndet-relaterade våt nivåer. Kontrollera avståndet-baserade genljudet ändra mer diskreta skev värden är mindre än 1. Detta kan också justeras i mer detaljerad information genom att justera den **Wetness (dB)**.

* Öka tid som decay i hela utrymmet genom att justera den **Decay skala**. Om simulering resultatet för en viss källa-lyssnare plats par är en decay tid på 1,5s, och **Decay skala** anges till 2, decay-tiden som tillämpas på källan är 3s.

## <a name="next-steps"></a>Nästa steg
* Läsa fullständig information på den [Unity-baserade projekt Akustik utforma kontroller](unity-workflow.md)
* Utforska ytterligare koncepten bakom den [utforma processen](design-process.md)
* [Skapa ett Azure-konto](create-azure-account.md) att utforska före ändamålet och skapa processer

