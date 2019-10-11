---
title: Kända problem med Project akustiska plugin-program
titlesuffix: Azure Cognitive Services
description: Följande kända problem kan uppstå i Project-Akustisker.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243032"
---
# <a name="project-acoustics-known-issues"></a>Kända problem med Project-Akustiskhet
Den här artikeln beskriver problem som kan uppstå när du använder Project-Akustisker.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustiska parametrar försvinner när du byter namn på en scen

Om du byter namn på en scen överförs inte alla akustiska parametrar som tillhör den scenen automatiskt till den nya scenen. Men de finns fortfarande i den gamla till gångs filen. Leta efter filen *[SceneName] _AcousticParameters. Asset* i *redigerings* katalogen bredvid din scen fil. Byt namn på filen så att den återspeglar det nya scen namnet.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Distribuera till Android-bugg från vissa Unit-versioner

Vissa versioner av Unit har ett [fel](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) i hur de distribuerar ljud-plugin-program till Android. Kontrol lera att du inte använder en version som påverkas av denna bugg.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Fel meddelandet "Det gick inte att hitta metadata file system. Security. dll"

Kontrol lera att **skript körnings versionen** i **Player** -inställningarna är *.NET 4. x-motsvarighet*och starta om Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Autentiseringsfel vid anslutning till Azure

Kontrol lera att:
- Du har använt rätt autentiseringsuppgifter för ditt Azure-konto.
- Ditt konto stöder den typ av nod som du har begärt i bagerien.
- System klockan är korrekt inställd.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Fliken bageri visar fortfarande "ta bort" när du har avbrutit
Med projekt akustiskt rensas alla Azure-resurser för ett jobb efter lyckad slut för ande eller uppsägning. Den här processen kan ta upp till 5 minuter.

## <a name="next-steps"></a>Nästa steg
* Prova innehållet i [Unity](unity-quickstart.md) -eller [Unreal](unreal-quickstart.md) -exemplet.
