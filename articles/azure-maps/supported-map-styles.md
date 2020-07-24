---
title: Kart format som stöds | Microsoft Azure Maps
description: I den här artikeln får du lära dig om olika kart åter givnings format som stöds av Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c22363b3b1e0b8283de572ff578e6d78f0627486
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128071"
---
# <a name="azure-maps-supported-map-styles"></a>Mappnings format som stöds Azure Maps
Azure Maps stöder flera olika inbyggda kart format enligt beskrivningen nedan.

## <a name="road"></a>utanför
En **väg** karta är en standard karta som visar vägar, naturliga och artificiella funktioner tillsammans med etiketter för dessa funktioner.

![kart stil för vägar](./media/supported-map-styles/road.png)

**Tillämpliga API: er:**
* [Kart bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kart panel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="blank-and-blank_accessible"></a>Tom och blank_accessible

De **tomma** och **blank_accessible** kart formaten innehåller en tom arbets yta där du kan visualisera data på. **Blank_accessible** formatet fortsätter att tillhandahålla skärm läsar uppdateringar med kartans plats information, även om bas kartan inte visas.

> [!Note]
> I webb-SDK kan du ändra bakgrunds färgen för kartan genom att ange CSS- `background-color` formatet för Map DIV-elementet.

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK

## <a name="satellite"></a>satellite 
**Satellit** stilen är en kombination av satellit-och antenn bilder.

![kart stil för satellit panel](./media/supported-map-styles/satellite.png)

**Tillämpliga API: er:**
* [Satellit panel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="satellite_road_labels"></a>satellite_road_labels
Den här kart stilen är en hybrid av vägar och etiketter som finns ovanpå satellit-och antenn bilder.

![satellite_road_labels kart stil](./media/supported-map-styles/satellite-road-labels.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="grayscale_dark"></a>grayscale_dark
**gråskala** är en mörk version av vägens kart stil.

![gray_scale kart stil](./media/supported-map-styles/grayscale-dark.png)

**Tillämpliga API: er:**
* [Kart bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kart panel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK 
* Kontroll av Android-kartning
* Visuellt Power BI-objekt


## <a name="grayscale_light"></a>grayscale_light
**grå Skale lampor** är en låg version av vägens kart stil.

![format för grå tons indikator](./media/supported-map-styles/grayscale-light.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt


## <a name="night"></a>natt
**natt** är en mörk version av kart formatet för vägar med färgade vägar och symboler.

![stil för natt karta](./media/supported-map-styles/night.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="road_shaded_relief"></a>road_shaded_relief
en **nedtonad** avlastning är ett Azure Maps huvud format som har slutförts med hjälp av jordens profiler.

![skuggad avbilds stil](./media/supported-map-styles/shaded-relief.png)

**Tillämpliga API: er:**
* [Kart panel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** är ett mörkt kart format med en högre kontrast än andra format.

![mörkt kart format med hög kontrast](./media/supported-map-styles/high-contrast-dark.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Visuellt Power BI-objekt

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du anger ett kart format i Azure Maps:

> [!div class="nextstepaction"]
> [Välj ett kartformat](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
