---
title: Kartformat som stöds | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om olika kartåtergivningsformat som stöds av Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334028"
---
# <a name="azure-maps-supported-map-styles"></a>Kartastilar för Azure Maps-stöds
Azure Maps stöder flera olika inbyggda kartformat enligt beskrivningen nedan.

## <a name="road"></a>Road
En **färdplan** är en standardkarta som visar vägar, naturliga och konstgjorda funktioner tillsammans med etiketterna för dessa funktioner.

![vägkarta stil](./media/supported-map-styles/road.png)

**Tillämpliga API:er:**
* [Karta bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Karta kakel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kartkontroll för Web SDK
* Kartkontroll för Android

## <a name="blank-and-blank_accessible"></a>tomma och blank_accessible

De **tomma** och **blank_accessible** kartformaten innehåller en tom arbetsyta som du kan visualisera data på. Den **blank_accessible** stilen fortsätter att ge skärmläsare uppdateringar med kartans platsinformation, även om baskartan inte visas.

> [!Note]
> I webb-SDK kan du ändra bakgrundsfärgen på `background-color` kartan genom att ange CSS-stilen för kart-DIV-element.

**Tillämpliga API:er:**
* Kartkontroll för Web SDK

## <a name="satellite"></a>satellite 
**Satellitstilen** är en kombination av satellit- och flygbilder.

![satellit kakel karta stil](./media/supported-map-styles/satellite.png)

**Tillämpliga API:er:**
* [Satellitpanel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kartkontroll för Web SDK
* Kartkontroll för Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Denna karta stil är en hybrid av vägar och etiketter överlagrad ovanpå satellit och flygbilder.

![satellite_road_labels kartstil](./media/supported-map-styles/satellite-road-labels.png)

**Tillämpliga API:er:**
* Kartkontroll för Web SDK
* Kartkontroll för Android

## <a name="grayscale_dark"></a>grayscale_dark
**gråskala mörk** är en mörk version av färdplanen stil.

![gray_scale kartstil](./media/supported-map-styles/grayscale-dark.png)

**Tillämpliga API:er:**
* [Karta bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Karta kakel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kartkontroll för Web SDK 
* Kartkontroll för Android


## <a name="grayscale_light"></a>grayscale_light
**gråskaleljus** är en ljusversion av vägkartans stil.

![kartaformat för gråskaleljus](./media/supported-map-styles/grayscale-light.png)

**Tillämpliga API:er:**
* Kartkontroll för Web SDK
* Kartkontroll för Android


## <a name="night"></a>natt
**natten** är en mörk version av färdplanen stil med färgade vägar och symboler.

![natt karta stil](./media/supported-map-styles/night.png)

**Tillämpliga API:er:**
* Kartkontroll för Web SDK
* Kartkontroll för Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**väg skuggad lättnad** är en Azure Maps huvudstil som kompletteras med konturer av jorden.

![skuggad reliefkarta stil](./media/supported-map-styles/shaded-relief.png)

**Tillämpliga API:er:**
* [Karta kakel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kartkontroll för Web SDK
* Kartkontroll för Android

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** är en mörk kartstil med en högre kontrast än de andra formaten.

![hög kontrast mörk karta stil](./media/supported-map-styles/high-contrast-dark.png)

**Tillämpliga API:er:**
* Kartkontroll för Web SDK

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du anger en kartstil i Azure Maps:

> [!div class="nextstepaction"]
> [Välj ett kartformat](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
