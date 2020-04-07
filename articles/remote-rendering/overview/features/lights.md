---
title: Cykellyktor
description: Beskrivning och egenskaper för ljuskälla
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680952"
---
# <a name="lights"></a>Cykellyktor

Som standard lyser fjärrrenderade objekt med ett [himmelsljus](sky.md). För de flesta program är detta redan tillräckligt, men du kan lägga till ytterligare ljuskällor till scenen.

> [!IMPORTANT]
> Endast [PBR-material](pbr-materials.md) påverkas av ljuskällor. [Färgmaterial](color-materials.md) visas alltid helt ljusa.

> [!NOTE]
> Castskuggor stöds för närvarande inte. Azure Remote Rendering är optimerad för att återge enorma mängder geometri, med hjälp av flera GPU:er om det behövs. Traditionella metoder för skugggjutning fungerar inte bra i sådana scenarier.

## <a name="common-light-component-properties"></a>Vanliga ljuskomponentegenskaper

Alla ljustyper härstammar från `LightComponent` den abstrakta basklassen och delar dessa egenskaper:

* **Färg:** Färgen på ljuset i [Gamma utrymme](https://en.wikipedia.org/wiki/SRGB). Alpha ignoreras.

* **Intensitet:** Ljusets ljusstyrka. För punkt- och punktljus definierar intensiteten också hur långt ljuset lyser.

## <a name="point-light"></a>Punkt ljus

I Azure Remote `PointLightComponent` Rendering kan inte bara avge ljus från en enda punkt, men också från en liten sfär eller ett litet rör, för att simulera mjukare ljuskällor.

### <a name="pointlightcomponent-properties"></a>Egenskaper för PointLightComponent

* **Radie:** Standardradien är noll, i vilket fall ljuset fungerar som en punktlampa. Om radien är större än noll fungerar den som sfärisk ljuskälla, vilket ändrar utseendet på speglade högdagrar.

* **Längd:** Om `Length` båda `Radius` och är icke-noll, fungerar ljuset som ett rör ljus. Detta kan användas för att simulera neonrör.

* **DämpningAvstjäl:** Om den lämnas till (0,0) beror dämpningen av ljuset bara på dess `Intensity`. Du kan dock ange anpassade min/max-avstånd över vilka ljusets intensitet skalas linjärt ner till 0. Den här funktionen kan användas för att framtvinga ett mindre inflytande för ett visst ljus.

* **ProjectedCubemap:** Om den är inställd på en giltig [kubkarta](../../concepts/textures.md)projiceras texturen på ljusets omgivande geometri. Kubkartans färg moduleras med ljusets färg.

## <a name="spot-light"></a>Spot ljus

Den `SpotLightComponent` liknar `PointLightComponent` men ljuset är begränsad till formen på en kon. Konens orientering definieras av *ägarentitetens negativa z-axel*.

### <a name="spotlightcomponent-properties"></a>Egenskaper för SpotLightComponent

* **Radie:** Samma som `PointLightComponent`för .

* **SpotAngleDeg:** Detta intervall definierar konens inre och yttre vinkel, mätt i grad. Allt inom den inre vinkeln belyses med full ljusstyrka. En falloff appliceras mot den yttre vinkeln som genererar en penumbra-liknande effekt.

* **FalloffExponent:** Definierar hur kraftigt falloff övergångar mellan den inre och den yttre konvinkel. Ett högre värde resulterar i en skarpare övergång. Standardvärdet på 1,0 resulterar i en linjär övergång.

* **DämpningAvstjäl:** Samma som `PointLightComponent`för .

* **Projected2dTexture:** Om den är inställd på en giltig [2D-textur](../../concepts/textures.md)projiceras bilden på den geometri som ljuset lyser med. Texturens färg moduleras med ljusets färg.

## <a name="directional-light"></a>Riktat ljus

Simulerar `DirectionalLightComponent` en ljuskälla som är oändligt långt borta. Ljuset lyser i riktning mot den *negativa z-axeln av ägarenheten*. Entitetens position ignoreras.

Det finns inga ytterligare egenskaper.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Ljuskällor har en betydande inverkan på renderingsprestanda. Använd dem noggrant och endast om det krävs av programmet. Alla statiska globala ljusförhållanden, inklusive en statisk riktningskomponent, kan uppnås med en [anpassad himmelsstruktur](sky.md), utan extra återgivningskostnader.

## <a name="next-steps"></a>Nästa steg

* [Material](../../concepts/materials.md)
* [Himlen](sky.md)
