---
title: Kända problem med plugin-programmet för Project-akustiskt
titlesuffix: Azure Cognitive Services
description: Du kan stöta på följande kända problem när du använder för hands versionen av designer för projekt akustiskt.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a70d9ea1090f1c518f804dd28f3461918af965cd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854289"
---
# <a name="project-acoustics-known-issues"></a>Kända problem med Project-Akustiskhet
Du kan stöta på följande kända problem när du använder för hands versionen av designer för projekt akustiskt.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustiska parametrar försvinner när du byter namn på en scen

Om du byter namn på en scen överförs inte alla akustiska parametrar som tillhör den scenen automatiskt till den nya scenen. De finns dock kvar i den gamla till gångs filen. Leta efter filen **SceneName_AcousticParameters. Asse** i **redigerings** katalogen bredvid din scen fil. Byt namn på filen så att den återspeglar det nya scen namnet.

## <a name="unity-crashes-when-closing-project"></a>Unit kraschar vid stängning av projekt

I de senaste versionerna av Uniting (2018.2 +) finns ett känt fel där Unity kraschar när du stänger projektet. Detta spåras av [detta Unity-problem](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Distribuera till Android från vissa Unit-versioner

Vissa versioner av Unit har ett fel med att distribuera ljud-plugin-program till Android. Kontrol lera att du inte använder en version som påverkas av [denna bugg](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Jag får ett fel meddelande om att det inte gick att hitta metadata file system. Security. dll

Se till att skript körnings versionen i Player-inställningarna är inställd på **.NET 4. x-motsvarighet**och starta om Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Jag har problem med autentiseringen vid anslutning till Azure

Kontrol lera att du har använt rätt autentiseringsuppgifter för ditt Azure-konto, att ditt konto stöder den typ av nod som begärs i bagerien och att system klockan är korrekt.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Om du avbryter en bageri lämnar du fliken bak i läget "ta bort"
Med projektets akustiska raderas alla Azure-resurser för ett jobb vid lyckad slut för ande eller annullering. Detta kan ta upp till 5 minuter.

## <a name="next-steps"></a>Nästa steg
* Prova innehållet [](unity-quickstart.md) i Unity-eller [Unreal](unreal-quickstart.md) -exempel

