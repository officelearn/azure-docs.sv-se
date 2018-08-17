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
ms.openlocfilehash: b0dd5e0c365c65fc7b29752f7885acb71bdb3df8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181801"
---
# <a name="known-issues"></a>Kända problem
Följande kända problem kan uppstå när du använder förhandsversionen av Designer för projektet Akustik.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustiska parametrar försvinner när du byter namn på en scen

Om du byter namn på en scen överförs inte de akustiska parametrar som tillhör den scenen automatiskt till ny scen. De finns kvar i gamla tillgångsfilen men. Leta efter den **SceneName_AcousticParameters.asset** filen i den **redigeraren** katalogen bredvid din scenfilen. Byt namn på filen för att återspegla det nya scennamnet.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>Standardsökvägen för mappen AcousticsData i avsökningar flik är en absolut sökväg

Detta bör en relativ sökväg för att göra det enklare att dela projekt mellan medarbetare som standard. Som en lösning kan du ändra sökvägen för att vara i förhållande till projektkatalogen.

## <a name="runtime-voxels-are-a-different-size-than-design-time-voxels"></a>Runtime voxels är en annan storlek än designfasen voxels

Om du gör en **Calculate** på den **avsökningar** fliken och visa voxels, och gör sedan en ändamålet och visa voxels vid körning för samma scenen, voxels har samma storlek. Voxels visas före ändamålet är voxels som används i simuleringen. Voxels visas vid körning används för interpolering mellan avsökningen punkter. Detta kan orsaka en inkonsekvens där portaler visas öppen vid körning som inte är faktiskt öppen.

## <a name="uwp-builds-not-working"></a>UWP bygger fungerar inte

UWP-versioner inte lyckas på de senaste versionerna av Unity (2018.2 +). Körningsfasen för versionen kommer stopp och du får ”Unity-tillägg har inte initierats än” fel. Detta spåras av [problemet Unity](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d).

## <a name="unity-crashes-when-closing-project"></a>Unity kraschar när du stänger projekt

Det finns ett känt fel där Unity att svara när du stänger projektet på de senaste versionerna av Unity (2018.2 +). Detta spåras av [problemet Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problem med att distribuera till Android
Om du vill använda projekt Akustik på Android, ändra build målet till Android. Vissa versioner av Unity har ett fel med att distribuera ljud plugin-program – Kontrollera att du inte använder en version som påverkas av [programfelet](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Det uppstår ett fel som ”kunde inte hitta metadatafilen System.Security.dll'

Se till att körningsversion skript i Player-inställningar har angetts till **.NET 4.x motsvarande**, och starta om Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Jag har autentiseringsproblem med vid anslutning till Azure

Kontrollera du har använt rätt autentiseringsuppgifter för din Azure-konto att ditt konto har stöd för typ av nod som efterfrågas i ändamålet och att systemklockan stämmer.

## <a name="next-steps"></a>Nästa steg
* Kom igång [integrera Akustik i dina Unity-projekt](getting-started.md)

