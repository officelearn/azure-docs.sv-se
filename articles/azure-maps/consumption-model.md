---
title: Användning-modell i Azure Maps | Microsoft Docs
description: Lär dig mer om användning-modell i Azure Maps
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60797912"
---
# <a name="consumption-model"></a>Förbrukningsmodell

Online Routning tillhandahåller en uppsättning parametrar för en detaljerad beskrivning av vehicle-specifika Förbrukningsmodell.
Beroende på värdet för **vehicleEngineType**, två huvudsakliga Förbrukningsmodellerna stöds: _Förbränning_ och _Electric_. Det är fel att ange parametrar som hör till olika modeller i samma begäran.
Förbrukningsmodellen kan inte användas med **färdlägena** _cykel_ och _fotgängare_.

## <a name="parameter-constraints-for-consumption-model"></a>Parametern-begränsningar för förbrukningsmodell

I båda Förbrukningsmodellerna måste uttryckligen vissa parametrar du specificera vissa andra också. Dessa beroenden är:

* Alla parametrar kräver **constantSpeedConsumption** anges av användaren. Det är inte tillåtet att ange andra förbrukning modellen parametrar, med undantag för **vehicleWeight**om **constantSpeedConsumption** har inte angetts.
* **accelerationEfficiency** och **decelerationEfficiency** måste alltid anges som par (båda eller ingen).
* Om **accelerationEfficiency** och **decelerationEfficiency** anges, produkten av deras värden inte får vara större än 1 (för att förhindra beständig rörelse).
* **uphillEfficiency** och **downhillEfficiency** måste alltid anges som par (båda eller ingen).
* Om **uphillEfficiency** och **downhillEfficiency** anges, produkten av deras värden inte får vara större än 1 (för att förhindra beständig rörelse).
* Om den \* __effektivitet__ parametrar har angetts av användaren, sedan **vehicleWeight** måste också anges. När **vehicleEngineType** är _förbränning_, **fuelEnergyDensityInMJoulesPerLiter** måste anges också.
* **maxChargeInkWh** och **currentChargeInkWh** måste alltid anges som par (båda eller ingen).

> [!NOTE]
> Om det bara **constantSpeedConsumption** anges, inga andra förbrukning aspekter som lutning och vehicle acceleration beaktas för förbrukning beräkningar.

## <a name="combustion-consumption-model"></a>Förbränning förbrukningsmodell

Förbrännings- och förbrukningsmodellen används när **fordonets motortyp** är inställd på _förbränning_.
Listan över parametrar som hör till den här modellen finns nedan. Se avsnittet Parametrar detaljerad beskrivning.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Electric förbrukningsmodell

Elförbrukningsmodellen används när **fordonets motortyp** är inställd på _elektrisk_.
Listan över parametrar som hör till den här modellen finns nedan. Se avsnittet Parametrar detaljerad beskrivning.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Användningstyperna värden i parametrar för förbrukning

En viss uppsättning förbrukning parametrar kan avvisas trots att den kan uppfylla explicit kraven som anges ovan. Det händer när värdet för en specifik parameter eller en kombination av värden för flera parametrar, anses leda till orimligt omfattningen av förbrukning värden. Om detta händer, indikerar troligen ett inkommande fel som rätt noggrant för alla känsliga värden i parametrar för förbrukning. Om en viss uppsättning parametrar för förbrukning avvisas innehåller det felmeddelande som visas en textbaserade förklaring av en eller flera orsaker.
Detaljerade beskrivningar av parametrar har användningstyperna exempelvärden för båda modellerna.
