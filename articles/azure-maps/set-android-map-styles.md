---
title: Ange en kartstil med Azure Maps Android SDK| Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om formatrelaterade funktioner i Microsoft Azure Maps för Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334358"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ange kartstil med Azure Maps Android SDK

I den här artikeln visas två sätt att ange kartformat med Hjälp av Azure Maps Android SDK. Azure Maps har sex olika kartstilar att välja mellan. Mer information om kartformat som stöds finns [i kartformat som stöds i Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Krav

För att slutföra processen i den här artikeln måste du installera [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) för att läsa in en karta.


## <a name="set-map-style-in-the-layout"></a>Ange kartformat i layouten

Du kan ange ett kartformat i layoutfilen för din aktivitetsklass. Redigera **res > layout > activity_main.xml**, så det ser ut som en nedan:

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

Attributet `mapcontrol_style` ovan anger att kartformatet **ska grayscale_dark**. 

<center>

![stil-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Ange kartformat i aktivitetsklassen

Kartformatet kan ställas in i aktivitetsklassen. Kopiera följande kodavsnitt till metoden **onCreate()** `MainActivity.java` för klassen. Den här koden ställer in kartformatet så att **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![stil-satellit-väg-etiketter](./media/set-android-map-styles/satellite-road-labels.png)</center>