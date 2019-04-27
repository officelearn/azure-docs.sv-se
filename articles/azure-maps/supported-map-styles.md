---
title: Koppla format som stöds i Azure Maps | Microsoft Docs
description: Koppla format som stöds av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 76ab49c7f28260249483bf3bc4387e8cbaca13b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767243"
---
# <a name="azure-maps-supported-map-styles"></a>Koppla format som stöds av Azure Maps
Azure Maps stöder flera olika inbyggda Koppla format som beskrivs nedan.

## <a name="road"></a>Väg
En **väg** kartan är en standard karta som visar vägar, naturliga och konstgjorda funktioner tillsammans med etiketterna för dessa funktioner.

![Väg](./media/supported-map-styles/road.png)

**Tillämpliga API: er:**
* [Kartbilden](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Panelen tjänstkarta](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS kartkontroll
* Android kartkontroll

## <a name="satellite"></a>Satellit 
Den **satellit** style är en kombination av satellit och Flygfoto bilder.

![Satellit](./media/supported-map-styles/satellite.png)

**Tillämpliga API: er:**
* [Satellit-panel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS kartkontroll
* Android kartkontroll

## <a name="satelliteroadlabels"></a>satellite_road_labels
Den här kartan format är en kombination av vägar och etiketter som aktiviteter över satellit och Flygfoto bilder.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Tillämpliga API: er:**
* JS kartkontroll
* Android kartkontroll

## <a name="grayscaledark"></a>grayscale_dark
**Gråskala mörk** är en mörk version av formatmallen översikten.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Tillämpliga API: er:**
* JS kartkontroll 
* Android kartkontroll

## <a name="night"></a>natten
**natten** är en mörk version av formatet vägkarta med färgade vägar och symboler.

![natten](./media/supported-map-styles/night.png)

**Tillämpliga API: er:**
* JS kartkontroll
* Android kartkontroll

## <a name="roadshadedrelief"></a>road_shaded_relief
**väg skuggas befrielse** är en huvudsakliga Azure Maps-stil slutfördes med konturer på Jordens.

![skuggade befrielse](./media/supported-map-styles/shaded-relief.png)

**Tillämpliga API: er:**
* [Panelen tjänstkarta](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS kartkontroll
* Android kartkontroll
