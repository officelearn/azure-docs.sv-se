---
title: Kart format som stöds i Azure Maps | Microsoft Docs
description: Kart format som stöds av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1aad2284c0f64c92efaefe3f9145d95c4aabec67
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839443"
---
# <a name="azure-maps-supported-map-styles"></a>Mappnings format som stöds Azure Maps
Azure Maps stöder flera olika inbyggda kart format enligt beskrivningen nedan.

## <a name="road"></a>utanför
En **väg** karta är en standard karta som visar vägar, naturliga och artificiella funktioner tillsammans med etiketter för dessa funktioner.

![utanför](./media/supported-map-styles/road.png)

**Tillämpliga API: er:**
* [Kart bild](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kart panel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning

## <a name="blank-and-blank_accessible"></a>Tom och blank_accessible

Kart formaten **blank** och **blank_accessible** är en tom arbets yta där du kan visualisera data på. **Blank_accessible** -formatet kommer fortsätta att tillhandahålla skärm läsar uppdateringar med plats information om var kartan finns, även om bas kartan inte visas.

> [!Note]
> I webb-SDK kan du ändra bakgrunds färgen för kartan genom att ange CSS `background-color` -formatet för Map DIV-elementet.

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK

## <a name="satellite"></a>satellit 
**Satellit** stilen är en kombination av satellit-och antenn bilder.

![satellit](./media/supported-map-styles/satellite.png)

**Tillämpliga API: er:**
* [Satellit panel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning

## <a name="satellite_road_labels"></a>satellite_road_labels
Den här kart stilen är en hybrid av vägar och etiketter som finns ovanpå satellit-och antenn bilder.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning

## <a name="grayscale_dark"></a>grayscale_dark
**gråskala** är en mörk version av vägens kart stil.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK 
* Kontroll av Android-kartning


## <a name="grayscale_light"></a>grayscale_light
**grå Skale lampor** är en låg version av vägens kart stil.

![grå ljus](./media/supported-map-styles/grayscale_light.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning


## <a name="night"></a>natten
**natt** är en mörk version av kart formatet för vägar med färgade vägar och symboler.

![natten](./media/supported-map-styles/night.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning

## <a name="road_shaded_relief"></a>road_shaded_relief
en **nedtonad** avlastning är ett Azure Maps huvud format som har slutförts med hjälp av jordens profiler.

![skuggad reducering](./media/supported-map-styles/shaded-relief.png)

**Tillämpliga API: er:**
* [Kart panel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
