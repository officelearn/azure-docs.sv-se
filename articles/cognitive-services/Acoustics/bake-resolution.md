---
title: Projektet Akustik ändamålet upplösning
titlesuffix: Azure Cognitive Services
description: Den här konceptuell översikt beskrivs skillnaden mellan grov och bra lösningar vid gräddning Akustik.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471838"
---
# <a name="project-acoustics-bake-resolution"></a>Projektet Akustik ändamålet upplösning
Den här konceptuell översikt beskrivs skillnaden mellan grov och bra lösningar vid gräddning Akustik. Du väljer den här inställningen under steget avsökningar av bageriprodukter arbetsflödet.

## <a name="Coarse-vs-Fine-Resolution"></a>Grov vs bra lösning

Den enda skillnaden mellan grov och bra lösning inställningar är den frekvens med vilken simuleringen utförs. Fine använder en frekvens som är dubbelt så mycket som grov. Detta har ett antal effekter akustiska simuleringen:

* Våglängden för grov två gånger är så långa så bra och voxels är därför två gånger så stora.
* Simulering tiden är direkt relaterad till den voxel storlek, vilket gör en grov ändamålet cirka 16 gånger snabbare än en bra ändamålet.
* Vara det går inte att simuleras portaler (till exempel dörrar eller windows) och mindre än storleken voxel. Grov inställningen kan göra att vissa av dessa mindre portaler för inte simuleras; de kommer inte därför skickar ljud via vid körning. Du kan se om detta sker genom att visa voxels.
* Lägre simulering frekvensen resulterar i mindre diffraction runt hörn och kanter.
* Ljud källor får inte finnas inuti ”fyllda” voxels (d.v.s. voxels som innehåller geometri). Detta resulterar i utan ljud. Det är svårare att placera ljud källor så att de inte är i större voxels av grov än när du använder inställningen bra.
* Större voxels kommer intrude mer till portaler, enligt nedan. Den första bilden har skapats med hjälp av grov, medan andra är samma nyckeln med hjälp av bra lösning. Som anges med röd markeringar, är det mycket mindre intrång i nyckeln med hjälp av bra inställningen. Den blå linjen är nyckeln som definieras av geometri, medan den röda linjen är effektiva akustiska portalen definieras av voxel storlek. Hur den här intrång spelar i en viss situation beror helt på hur voxels stämmer överens med portalen, vilket avgörs av storlek och platserna för dina objekt i scenen geometri.

![Skärmbild av grov voxels fylla något i Unreal](media/unreal-coarse-bake.png)

![Skärmbild av bra voxels i något i Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Nästa steg

Testa inställningarna för grov och bra matchning själv med hjälp av vår [Unreal](unreal-baking.md) eller [Unity](unity-baking.md) plugin-program.