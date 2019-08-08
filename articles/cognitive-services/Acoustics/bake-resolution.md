---
title: Problemlösning för projekt akustiskt
titlesuffix: Azure Cognitive Services
description: Den här konceptuella översikten beskriver skillnaden mellan grova och fina lösningar när du bakakustiskt ljud.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854355"
---
# <a name="project-acoustics-bake-resolution"></a>Problemlösning för projekt akustiskt
Den här konceptuella översikten beskriver skillnaden mellan grova och fina lösningar när du bakakustiskt ljud. Du väljer den här inställningen under avsöknings steget i Baknings flödet.

## <a name="Coarse-vs-Fine-Resolution"></a>Grov vs-lösning

Den enda skillnaden mellan de grova och hårda upplösnings inställningarna är den frekvens som simuleringen utförs vid. Fin frekvensen använder en frekvens som är dubbelt så hög som grov. Detta har ett antal konsekvenser för akustisk simulering:

* Våg längden för fin är dubbelt så lång som fint, och därför är voxels två gånger stora.
* Simulerings tiden är direkt relaterad till Voxel storlek, vilket gör en fin genom gång av 16 gånger snabbare än en fin bageri.
* Portaler (till exempel dörrar eller fönster) som är mindre än Voxel-storleken kan inte simuleras. En grov inställning kan orsaka att vissa av dessa mindre portaler inte simuleras. Därför kommer de inte att skicka något ljud genom vid körning. Du kan se om det händer genom att Visa voxels.
* Den lägre simulerings frekvensen ger mindre Diffraction runt hörn och kanter.
* Det går inte att hitta ljud källor inuti "fyllda" voxels (t. ex. voxels som innehåller geometri). Detta resulterar i inget ljud. Det är svårare att placera ljud källor så att de inte är i större voxels än när de använder sig av fin inställningen.
* Den större voxels kommer att inkräktara mer i portaler, som du ser nedan. Den första bilden skapades med grovhet, medan den andra är samma nyckeln med en fin upplösning. Som anges av de röda markeringarna finns det mycket mindre intrång i nyckeln med fin inställningen. Den blå linjen är den nyckeln som definieras av geometrin, medan den röda linjen är den effektiva akustiska portalen som definieras av Voxel-storleken. Hur den här intrången spelas upp i en specifik situation beror helt på hur voxels-raden är i förhållande till portalens geometri, som bestäms av objektens storlek och platser i scenen.

![Skärm bild av grov voxels som fyller en nyckeln i Unreal](media/unreal-coarse-bake.png)

![Skärm bild av fin voxels i en nyckeln i Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Nästa steg

Testa inställningarna för grov upplösning själv med våra [Unreal](unreal-baking.md) -eller [Unit](unity-baking.md) -plugin-program.
