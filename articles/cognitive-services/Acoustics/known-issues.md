---
title: Kända problem med project acoustics plug-in
titlesuffix: Azure Cognitive Services
description: Du kan uppleva följande kända problem i Project Acoustics.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243032"
---
# <a name="project-acoustics-known-issues"></a>Kända problem med projektakustik
I den här artikeln beskrivs problem som kan uppstå när du använder Projektakustik.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustiska parametrar går förlorade när du byter namn på en scen

Om du byter namn på en scen överförs inte alla akustiska parametrar som tillhör den scenen automatiskt till den nya scenen. Men de finns fortfarande i den gamla tillgångsfilen. Leta efter *[SceneName]_AcousticParameters.asset-filen* i *redigeringskatalogen* bredvid scenfilen. Byt namn på filen så att den återspeglar det nya scennamnet.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Distribuera-till-Android-bugg från vissa Unity-versioner

Vissa versioner av Unity har en [bugg](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) i hur de distribuerar ljud plug-ins till Android. Kontrollera att du inte använder en version som påverkas av det här felet.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Felet "Det gick inte att hitta metadatafilen System.Security.dll"

Kontrollera att **skriptkörningsversionen** i **inställningarna för Player** är *.NET 4.x Equivalent*och starta om Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Autentiseringsproblem vid anslutning till Azure

Kontrollera att:
- Du använde rätt autentiseringsuppgifter för ditt Azure-konto.
- Ditt konto stöder den typ av nod som du begärde i baken.
- Systemklockan är korrekt inställd.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Fliken Baka visar fortfarande "ta bort" när du har avbrutit
Project Acoustics rensar alla Azure-resurser för ett jobb efter att slutföras eller avbrytas. Denna process kan ta upp till 5 minuter.

## <a name="next-steps"></a>Nästa steg
* Prova exempelinnehållet [Unity](unity-quickstart.md) eller [Unreal.](unreal-quickstart.md)
