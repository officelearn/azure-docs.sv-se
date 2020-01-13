---
title: Fordons förbruknings modeller för routning | Microsoft Azure Maps
description: I den här artikeln får du lära dig om fordons förbruknings modeller för routning i Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911701"
---
# <a name="consumption-model"></a>Förbrukningsmodell

Online-routning innehåller en uppsättning parametrar för en detaljerad beskrivning av den fordonsbaserade förbruknings modellen.
Beroende på värdet för **fordonets motortyp**, stöds två huvudsakliga förbrukningsmodeller: _förbränning_ och _elektrisk_. Det är fel att ange parametrar som hör till olika modeller i samma begäran.
Förbrukningsmodellen kan inte användas med **färdlägena** _cykel_ och _fotgängare_.

## <a name="parameter-constraints-for-consumption-model"></a>Parameter begränsningar för förbruknings modell

I båda konsumtions modellerna måste du uttryckligen ange vissa parametrar för att även ange andra. Dessa beroenden är:

* Alla parametrar kräver att **constantSpeedConsumption** anges av användaren. Det går inte att ange någon annan förbruknings modell parameter, med undantag för **vehicleWeight**, om **constantSpeedConsumption** inte har angetts.
* **accelerationEfficiency** och **decelerationEfficiency** måste alltid anges som ett par (dvs. båda eller inga).
* Om **accelerationEfficiency** och **decelerationEfficiency** har angetts får produkten av deras värden inte vara större än 1 (för att förhindra beständig rörelse).
* **uphillEfficiency** och **downhillEfficiency** måste alltid anges som ett par (dvs. båda eller inga).
* Om **uphillEfficiency** och **downhillEfficiency** har angetts får produkten av deras värden inte vara större än 1 (för att förhindra beständig rörelse).
* Om \*__effektivitets__ parametrar anges av användaren måste även **vehicleWeight** anges. När **vehicleEngineType** är _förbränning_måste även **fuelEnergyDensityInMJoulesPerLiter** anges.
* **maxChargeInkWh** och **currentChargeInkWh** måste alltid anges som ett par (dvs. båda eller inga).

> [!NOTE]
> Om endast **constantSpeedConsumption** anges används inga andra användnings aspekter som lutning och fordons acceleration i beräkningen av förbruknings beräkningar.

## <a name="combustion-consumption-model"></a>Förbrännings förbruknings modell

Förbrännings- och förbrukningsmodellen används när **fordonets motortyp** är inställd på _förbränning_.
Listan över parametrar som tillhör den här modellen är nedan. Se avsnittet parametrar för detaljerad beskrivning.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektrisk förbruknings modell

Elförbrukningsmodellen används när **fordonets motortyp** är inställd på _elektrisk_.
Listan över parametrar som tillhör den här modellen är nedan. Se avsnittet parametrar för detaljerad beskrivning.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Lämpliga värden för förbruknings parametrar

En viss uppsättning förbruknings parametrar kan avvisas, även om den kan uppfylla alla explicita krav som anges ovan. Det inträffar när värdet för en speciell parameter, eller en kombination av värden för flera parametrar, anses leda till orimliga värden för förbruknings värden. Om detta inträffar indikerar det förmodligen ett indatafel, eftersom det är så viktigt att ta hänsyn till alla lämpliga-värden för förbruknings parametrar. Om en viss uppsättning förbruknings parametrar avvisas, innehåller det medföljande fel meddelandet en text förklaring av anledningen (erna).
De detaljerade beskrivningarna av parametrarna innehåller exempel på lämpliga-värden för båda modellerna.
