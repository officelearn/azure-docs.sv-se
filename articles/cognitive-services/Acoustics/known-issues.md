---
title: Kända problem med Akustik plugin-program – Cognitive Services
description: Följande kända problem kan uppstå när du använder förhandsversionen av Designer för projektet Akustik.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: e42a2f854d5d4fa72e17a0b75ffef55069ccb626
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267877"
---
# <a name="known-issues"></a>Kända problem
Följande kända problem kan uppstå när du använder förhandsversionen av Designer för projektet Akustik.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustiska parametrar försvinner när du byter namn på en scen

Om du byter namn på en scen överförs inte de akustiska parametrar som tillhör den scenen automatiskt till ny scen. De finns kvar i gamla tillgångsfilen men. Leta efter den **SceneName_AcousticParameters.asset** filen i den **redigeraren** katalogen bredvid din scenfilen. Byt namn på filen för att återspegla det nya scennamnet.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Runtime voxels är en annan storlek än scen förhandsversion voxels

Om du gör en **Calculate** på den **avsökningar** fliken och visa voxels, och gör sedan en ändamålet och visa voxels vid körning för samma scenen, voxels har samma storlek. Voxels visas före ändamålet är voxels som används i simuleringen. Voxels visas vid körning används för interpolering mellan avsökningen punkter. Detta kan orsaka en inkonsekvens där portaler visas öppen vid körning som inte är faktiskt öppen.

## <a name="unity-crashes-when-closing-project"></a>Unity kraschar när du stänger projekt

Det finns ett känt fel där Unity att svara när du stänger projektet på de senaste versionerna av Unity (2018.2 +). Detta spåras av [problemet Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problem med att distribuera till Android
Om du vill använda projekt Akustik på Android, ändra build målet till Android. Vissa versioner av Unity har ett fel med att distribuera ljud plugin-program – Kontrollera att du inte använder en version som påverkas av [programfelet](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Det uppstår ett fel som ”kunde inte hitta metadatafilen System.Security.dll'

Se till att körningsversion skript i Player-inställningar har angetts till **.NET 4.x motsvarande**, och starta om Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Jag har autentiseringsproblem med vid anslutning till Azure

Kontrollera du har använt rätt autentiseringsuppgifter för din Azure-konto att ditt konto har stöd för typ av nod som efterfrågas i ändamålet och att systemklockan stämmer.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Avbryter en ändamålet lämnar fliken ändamålet i ”ta bort” tillstånd
Projektet Akustik rensas alla Azure-resurser för ett jobb på genomförts eller uppsägning vilket kan ta upp till 5 minuter.

## <a name="next-steps"></a>Nästa steg
* Kom igång med att [integrera akustik i ditt Unity-projekt](getting-started.md)

