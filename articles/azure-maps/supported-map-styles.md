---
title: Koppla format som stöds i Azure Maps | Microsoft Docs
description: Koppla format som stöds av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 33b0f5df57623f0b4433a4a09c7cd15688783485
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191163"
---
# <a name="azure-maps-supported-map-styles"></a>Koppla format som stöds av Azure Maps
Azure maps stöd för fyra olika inbyggda Koppla format. Format tillsammans med deras beskrivningar finns nedan.

## <a name="road"></a>Väg
En **väg** kartan är en standard karta som visar vägar, naturliga och konstgjorda funktioner tillsammans med etiketterna för dessa funktioner.

![väg](./media/supported-map-styles/road.png)

**Tillämpliga API: er:**
* [Kartbilden](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Panelen tjänstkarta](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS kartkontroll

## <a name="satellite"></a>Satellit 
Den **satellit** style är en kombination av satellit och Flygfoto bilder.

![satellit](./media/supported-map-styles/satellite.png)

**Tillämpliga API: er:**
* [Satellit-panel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS kartkontroll

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
Den här kartan format är en kombination av vägar och etiketter som aktiviteter över satellit och Flygfoto bilder.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Tillämpliga API: er:**
* JS kartkontroll

## <a name="grayscaledark"></a>Grayscale_Dark
**Gråskala mörk** är en mörk version av formatmallen översikten.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Tillämpliga API: er:**
* JS kartkontroll