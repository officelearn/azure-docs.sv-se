---
title: Om
description: Introduktion till Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: a06c63152cb56be6d94cccc472d2e1d65651d6ce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679956"
---
# <a name="about-azure-remote-rendering"></a>Om Azure Remote Rendering

> [!IMPORTANT]
> **Azure Remote Rendering** är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Azure Remote Rendering* (ARR) är en tjänst som gör att du kan återge högkvalitativt, interaktivt 3D-innehåll i molnet och strömma det i realtid till enheter, till exempel HoloLens 2.

![Exempelmodell](../media/arr-engine.png)

Ej uppbundna enheter har begränsad beräkningskraft för rendering av komplexa modeller. För många program skulle det vara oacceptabelt, dock att minska den visuella trohet på något sätt.

*Fjärr rendering* löser det här problemet genom att flytta renderingsarbetsbelastningen till avancerade GPU:er i molnet. En molnbaserad grafikmotor återger bilden, kodar den som en videoström och strömmar den till målenheten.

## <a name="hybrid-rendering"></a>Hybridåtergivning

I de flesta program är det inte tillräckligt att bara återge en komplex modell. Du behöver också anpassat användargränssnitt för att kunna tillhandahålla funktioner för användaren. Azure Remote Rendering tvingar dig inte att använda ett dedikerat gränssnittsramverk, utan stöder *hybridrendering*. Det innebär att du kan återge element på enheten med den metod du föredrar, till exempel [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

I slutet av en ram kombinerar Azure Remote Rendering sedan automatiskt ditt lokalt renderade innehåll med fjärravbildningen. Det är även kunna göra det med korrekt ocklusion.

## <a name="multi-gpu-rendering"></a>Multi-GPU-rendering

Vissa modeller är för komplexa för att rendera med interaktiva bildhastigheter, även för en avancerad GPU. Särskilt i industriell visualisering är detta ett vanligt problem. För att ytterligare begränsa gränserna kan Azure Remote Rendering distribuera arbetsbelastningen till flera GPU:er. Resultaten slås samman till en enda bild, vilket gör processen helt transparent för användaren.

## <a name="high-level-architecture"></a>Övergripande arkitektur

Det här diagrammet illustrerar fjärråtergivningsarkitekturen:

![Arkitektur](./media/arr-high-level-architecture.png)

En fullständig cykel för bildgenerering innebär följande steg:

1. Klientsidan: Raminställning
    1. Din kod: Användarindata bearbetas, scendiagrammet uppdateras
    1. ARR-kod: Scendiagramuppdateringar och förväntad huvudpose skickas till servern
1. Serversidan: Fjärrrendering
    1. Renderingsmotor distribuerar rendering över tillgängliga GPU:er
    1. Utdata från flera GPU:er komponeras till en bild
    1. Bilden kodas som videoström, skickas tillbaka till klienten
1. Klientsidan: Slutförande
    1. Din kod: Lokalt innehåll (användargränssnitt, markörer, ...) återges
    1. ARR-kod: På "närvarande" slås lokalt renderat innehåll samman automatiskt med videoström

Nätverksfördröjning är det största problemet. Den vända tiden mellan att skicka en begäran och ta emot resultatet är vanligtvis för lång för interaktiva bildhastigheter. Därför kan mer än en ram vara i flykt när som helst.

## <a name="next-steps"></a>Nästa steg

* [Systemkrav](system-requirements.md)
* [Snabbstart: Återge en modell med Unity](../quickstarts/render-model.md)
