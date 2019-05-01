---
title: Mappa style funktioner i Azure Maps | Microsoft Docs
description: Lär dig mer om Azure Maps style relaterade funktioner för Android SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870979"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ställ in kartan format med hjälp av Android SDK för Azure Maps

Den här artikeln beskrivs två sätt att koppla format med hjälp av Azure Maps Android SDK. Azure Maps har sex olika kartor format att välja bland. Mer information om stöds Koppla format finns i [Koppla format som stöds i Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra processen i den här artikeln, måste du installera [Android SDK för Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) att läsa in en karta.


## <a name="set-map-style-in-the-layout"></a>Ange kartan stil i layouten

Du kan ange en formatmall för kartan i layoutfilen för din aktivitetsklassen. Redigera **res > layout > activity_main.xml**, så att det ser ut som i exemplet nedan:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

Den `mapcontrol_style` attribut ovan anger formatmallen karta till **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Ange kartan stil i aktivitetsklassen

Kartan style kan ställas in i aktivitetsklassen. Kopiera följande kodavsnitt i den **onCreate()** -metoden för din `MainActivity.java` klass. Detta anger formatmallen karta till **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>