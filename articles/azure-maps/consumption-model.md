---
title: Fordonsförbrukningsmodeller för routning | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om fordonsförbrukningsmodeller för routning i Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190254"
---
# <a name="consumption-model"></a>Förbrukningsmodell

Routningstjänsten innehåller en uppsättning parametrar för en detaljerad beskrivning av den fordonsspecifika förbrukningsmodellen.
Beroende på värdet av **vehicleEngineType**stöds två huvudsakliga förbrukningsmodeller: _Förbränning_ och _Elektrisk_. Det är felaktigt att ange parametrar som tillhör olika modeller i samma begäran. Dessutom kan parametrar för förbrukningsmodell inte användas med följande **travelMode-värden:** _cykel_ och _fotgängare_.

## <a name="parameter-constraints-for-consumption-model"></a>Parameterbegränsningar för förbrukningsmodell

I båda förbrukningsmodellerna finns det vissa beroenden när du anger parametrar. Vilket innebär att uttryckligen ange vissa parametrar kan kräva att ange några andra parametrar. Här är dessa beroenden att vara medveten om:

* Alla parametrar kräver **konstantHastighetKonsumtion** som ska anges av användaren. Det är ett fel att ange någon annan förbrukningsmodellparameter, om **konstantSpeedConsumption** inte anges. Parametern **vehicleWeight** är ett undantag för detta krav.
* **accelerationEffektivitet** och **retardationEffektivitet** måste alltid anges som ett par (det vill säga både eller ingen).
* Om **accelerationEffektivitet** och **retardationEffektivitet** anges får produkten av deras värden inte vara större än 1 (för att förhindra evig rörelse).
* **uppförsbackeEffektivitet** och **nedförsbackeEffektivitet** måste alltid anges som ett par (det vill säga både eller ingen).
* Om **uppförsbacke Och** **nedförsbackeEffektivitet** anges får produkten av deras värden inte vara större än 1 (för att förhindra evig rörelse).
* Om \* __effektivitetsparametrarna__ anges av användaren måste **även vehicleWeight** anges. När **vehicleEngineType** är _förbränning_måste **även bränsleEnergiDensityInMJoulesPerLiter** specificeras.
* **maxChargeInkWh** och **currentChargeInkWh** måste alltid anges som ett par (det vill säga både eller ingen).

> [!NOTE]
> Om endast **constantSpeedKonsumtion specificeras,** ingen annan förbrukningsaspekteråtygn, sluttar, och medelaccelerationer tas in i konto för förbrukningberäkningar.

## <a name="combustion-consumption-model"></a>Förbränningsförbrukningsmodell

Förbränningsförbrukningsmodellen används när **vehicleEngineType** är inställt på _förbränning_.
Listan över parametrar som tillhör den här modellen finns nedan. Se avsnittet Parametrar för detaljerad beskrivning.

* constantSpeedConsumptionInLitersPerHundredkm
* fordonetVikt
* nuvarandeFuelInLiters
* hjälpPowerInLitersPerHour
* bränsleEnergiDensityInMJoulesPerLiter
* accelerationEffektivitet
* inbromsningEffektivitet
* uppförsbacke
* nedförsbackeEffektivitet

## <a name="electric-consumption-model"></a>Modell för elektrisk förbrukning

Den elektriska förbrukningsmodellen används när **vehicleEngineType** är inställt på _elektrisk_.
Listan över parametrar som tillhör den här modellen finns nedan. Se avsnittet Parametrar för detaljerad beskrivning.

* constantSpeedConsumptionInkWhPerHundredkm
* fordonetVikt
* nuvarandeChargeInkWh
* maxChargeInkWh
* extraPowerInkW
* accelerationEffektivitet
* inbromsningEffektivitet
* uppförsbacke
* nedförsbackeEffektivitet

## <a name="sensible-values-of-consumption-parameters"></a>Förnuftiga värden för förbrukningsparametrar

En viss uppsättning förbrukningsparametrar kan avvisas, även om uppsättningen kan uppfylla alla uttryckliga krav. Det händer när värdet av en viss parameter, eller en kombination av värden för flera parametrar, anses leda till orimliga storlekar av förbrukningsvärden. Om det händer indikerar det troligen ett indatafel, eftersom vederbörlig omsorg är noga med att tillgodose alla vettiga värden för förbrukningsparametrar. Om en viss uppsättning förbrukningsparametrar avvisas innehåller det medföljande felmeddelandet en textförklaring av orsaken/behoven.
De detaljerade beskrivningarna av parametrarna har exempel på förnuftiga värden för båda modellerna.
