---
title: Projektet Akustik Baka Upplösning
titlesuffix: Azure Cognitive Services
description: Denna konceptuella översikt beskriver skillnaden mellan grova och fina upplösningar medan du bakar akustik.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854355"
---
# <a name="project-acoustics-bake-resolution"></a>Projektet Akustik Baka Upplösning
Denna konceptuella översikt beskriver skillnaden mellan grova och fina upplösningar medan du bakar akustik. Du väljer den här inställningen under steget Avsökningar i bakningsarbetsflödet.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Grov vs fin upplösning

Den enda skillnaden mellan de grova och fina upplösningsinställningarna är den frekvens med vilken simuleringen utförs. Fine använder en frekvens dubbelt så hög som grov. Detta har ett antal konsekvenser för den akustiska simuleringen:

* Våglängden för grov är dubbelt så lång som fin, och därför voxels är dubbelt så stora.
* Simuleringstiden är direkt relaterad till voxel storlek, vilket gör en grov baka ca 16 gånger snabbare än en fin baka.
* Portaler (till exempel dörrar eller fönster) som är mindre än voxelstorleken kan inte simuleras. Den grova inställningen kan orsaka att vissa av dessa mindre portaler inte simuleras. Därför kommer de inte att passera ljud genom vid körning. Du kan se om detta händer genom att visa voxels.
* Den lägre simuleringsfrekvensen resulterar i mindre diffraktion runt hörn och kanter.
* Ljudkällor kan inte placeras inuti "fyllda" voxels (dvs. voxels som innehåller geometri). Detta resulterar i inget ljud. Det är svårare att placera ljudkällor så att de inte är inne i de större voxels av grova än det är när du använder den fina inställningen.
* De större voxels kommer att tränga in mer i portaler, som visas nedan. Den första bilden skapades med grov, medan den andra är samma dörröppning med fin upplösning. Som framgår av de röda markeringarna, det är mycket mindre intrång i dörröppningen med hjälp av fininställningen. Den blå linjen är dörröppningen som definieras av geometrin, medan den röda linjen är den effektiva akustiska portalen som definieras av voxelstorleken. Hur detta intrång utspelar sig i en viss situation beror helt på hur voxels linje upp med geometrin i portalen, som bestäms av storleken och platserna för dina objekt i scenen.

![Skärmdump av grova voxels fylla en dörröppning i Unreal](media/unreal-coarse-bake.png)

![Skärmdump av fina voxels i en dörröppning i Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Nästa steg

Prova grova och fina upplösningsinställningar själv med hjälp av våra [Unreal](unreal-baking.md) eller [Unity](unity-baking.md) plugins.
