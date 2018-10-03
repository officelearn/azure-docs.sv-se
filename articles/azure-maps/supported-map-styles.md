---
title: Koppla format som stöds i Azure Maps | Microsoft Docs
description: Koppla format som stöds av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238828"
---
# <a name="azure-maps-supported-map-styles"></a>Koppla format som stöds av Azure Maps
Azure maps stöd för fyra olika inbyggda Koppla format. Format med deras beskrivningar finns nedan.

## <a name="road"></a>Väg
En **väg** kartan är en standard karta som visar vägar, naturliga och konstgjorda funktioner tillsammans med etiketterna för dessa funktioner.

![Väg](./media/supported-map-styles/road.png)

**Tillämpliga API: er:**
* [Kartbilden](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Panelen tjänstkarta](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS kartkontroll

## <a name="satellite"></a>Satellit 
Den **satellit** style är en kombination av satellit och Flygfoto bilder.

![Satellit](./media/supported-map-styles/satellite.png)

**Tillämpliga API: er:**
* [Satellit-panel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS kartkontroll

## <a name="satelliteroadlabels"></a>satellite_road_labels
Den här kartan format är en kombination av vägar och etiketter som aktiviteter över satellit och Flygfoto bilder.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Tillämpliga API: er:**
* JS kartkontroll

## <a name="grayscaledark"></a>grayscale_dark
**Gråskala mörk** är en mörk version av formatmallen översikten.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Tillämpliga API: er:**
* JS kartkontroll 