---
title: Använda holografisk ommottering och fjärrrendering i Unity
description: Så här kan förhandsversionen av Holographic Remoting användas i kombination med Azure Remote Rendering
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681212"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Använda holografisk ommottering och fjärrrendering i Unity

[Holografisk ommotsättning](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) och Azure Remote Rendering är ömsesidigt uteslutande inom ett program. [Unity play-läget](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) är därför inte heller tillgängligt.

För varje körning av Unity-redigeraren kan endast en av de två användas. Om du vill använda den andra startar du om Unity först.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Använd Unity-spelläge för att förhandsgranska på Hololens 2

 Unity play-läge kan fortfarande användas, till exempel för att testa programmets användargränssnitt. Det är dock viktigt att ARR aldrig initieras. Annars kommer det att krascha.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Använda ett WMR VR-headset för att förhandsgranska på datorn

Om det finns ett Windows Mixed Reality VR-headset kan det användas för att förhandsgranska inuti Unity. I det här fallet är det bra att initiera ARR, men det kommer inte att vara möjligt att ansluta till en session medan WMR-headsetet används.
