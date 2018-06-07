---
title: Förbrukning modellen i Azure Maps | Microsoft Docs
description: Lär dig mer om förbrukningen modellen i Azure Maps
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: cf20c7dbfbf7cd3f09579b03b835148c1c295137
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600637"
---
# <a name="consumption-model"></a>Förbrukningsmodell

Online Routning tillhandahåller en uppsättning parametrar för en detaljerad beskrivning av vehicle-specifika förbrukning av modellen.
Beroende på värdet för **vehicleEngineType**, två huvudsakliga förbrukning modeller stöds: _förbränning_ och _elektriska_. Ange parametrar som hör till olika modeller i samma begäran uppstår ett fel.
Förbrukning modellen kan inte användas med **travelMode** värden _cykel_ och _fotgängare_.

## <a name="parameter-constraints-for-consumption-model"></a>Begränsningarna för typparametrar för förbrukning modellen

I båda förbrukning modeller måste explicit ange vissa parametrar vissa andra program. Dessa beroenden är:

* Alla parametrar kräver **constantSpeedConsumption** anges av användaren. Det går inte att ange andra förbrukning modellen parametrar, med undantag av **vehicleWeight**om **constantSpeedConsumption*** har inte angetts.
* **accelerationEfficiency** och **decelerationEfficiency** måste alltid anges som ett par (d.v.s. båda eller inga).
* Om **accelerationEfficiency** och **decelerationEfficiency** anges, produkten av värdena inte får vara större än 1 (för att förhindra beständig rörelse).
* **uphillEfficiency** och **downhillEfficiency** måste alltid anges som ett par (d.v.s. båda eller inga).
* Om **uphillEfficiency** och **downhillEfficiency** anges, produkten av värdena inte får vara större än 1 (för att förhindra beständig rörelse).
* Om \* **effektivitet** parametrar har angetts av användaren, sedan **vehicleWeight** måste också anges. När **vehicleEngineType** är _förbränning_, **fuelEnergyDensityInMJoulesPerLiter** måste anges.
* **maxChargeInkWh** och **currentChargeInkWh** måste alltid anges som ett par (d.v.s. båda eller inga).

> [!NOTE]
> Om endast **constantSpeedConsumption** har angetts, ingen andra förbrukning aspekter som lutning och vehicle acceleration beaktas för förbrukning beräkningar.

## <a name="combustion-consumption-model"></a>Förbränning förbrukning modellen

Förbränning förbrukning modellen används när **vehicleEngineType** är inställd på _förbränning_.
Listan över parametrar som hör till den här modellen är lägre än. Se avsnittet Parametrar detaljerad beskrivning.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektriska förbrukning modellen

Elektriska förbrukning modellen används när **vehicleEngineType** är inställd på _elektriska_.
Listan över parametrar som hör till den här modellen är lägre än. Se avsnittet Parametrar detaljerad beskrivning.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Sensible värdena för förbrukning parametrar

En viss uppsättning förbrukning parametrar kan avvisas trots att den kan utföra alla explicit krav som anges ovan. Det händer när värdet för en viss parameter eller en kombination av värden för flera parametrar, anses leda till orimligt omfattningen av värden för användning. Om detta händer innehåller troligen ett inkommande fel som rätt noggrant för alla sensible värdena för förbrukning av parametrar. Om en viss uppsättning förbrukning parametrar avvisas innehåller medföljande felmeddelandet en textrepresentation förklaring av av orsaker.
Detaljerade beskrivningar av parametrarna har sensible exempelvärden för båda modellerna.
