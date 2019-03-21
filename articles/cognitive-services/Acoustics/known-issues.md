---
title: Kända problem med projekt Akustik plugin-programmet
titlesuffix: Azure Cognitive Services
description: Följande kända problem kan uppstå när du använder förhandsversionen av Designer för projektet Akustik.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 4cbede768409596365bea0cdbbb451cc5195ac4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58136267"
---
# <a name="project-acoustics-known-issues"></a>Projektet Akustik kända problem
Följande kända problem kan uppstå när du använder förhandsversionen av Designer för projektet Akustik.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustiska parametrar försvinner när du byter namn på en scen

Om du byter namn på en scen överföra alla akustiska parametrarna som tillhör den scenen kommer inte automatiskt till nya scenen. De kommer fortfarande finns i gamla tillgångsfilen men. Leta efter den **SceneName_AcousticParameters.asset** filen i den **redigeraren** katalogen bredvid din scenfilen. Byt namn på filen för att återspegla det nya scennamnet.

## <a name="unity-crashes-when-closing-project"></a>Unity kraschar när du stänger projekt

Det finns ett känt fel där Unity att svara när du stänger projektet på de senaste versionerna av Unity (2018.2 +). Detta spåras av [problemet Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problem med att distribuera till Android
Om du vill använda projekt Akustik på Android, ändra build målet till Android. Vissa versioner av Unity har ett fel med att distribuera ljud plugin-program. Kontrollera att du inte använder en version som påverkas av [programfelet](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Det uppstår ett fel som ”kunde inte hitta metadatafilen System.Security.dll'

Se till att körningsversion skript i Player-inställningar har angetts till **.NET 4.x motsvarande**, och starta om Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Jag har autentiseringsproblem med vid anslutning till Azure

Kontrollera du har använt rätt autentiseringsuppgifter för din Azure-konto att ditt konto har stöd för typ av nod som efterfrågas i ändamålet och att systemklockan stämmer.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Avbryter en ändamålet lämnar fliken ändamålet i ”ta bort” tillstånd
Projektet Akustik kommer att rensa upp alla Azure-resurser för ett jobb på genomförts eller uppsägning. Det kan ta upp till 5 minuter.

## <a name="next-steps"></a>Nästa steg
* Prova den [Unity](unity-quickstart.md) eller [Unreal](unreal-quickstart.md) exempelinnehåll

