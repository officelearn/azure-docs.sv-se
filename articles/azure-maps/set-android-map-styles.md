---
title: Ange en kart stil med Azure Maps Android SDK | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om Microsoft Azure Maps-relaterade funktioner för Android SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 96564a89a2b64203eef913b0d8300f0dafa332c5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209587"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ange kart stil med Azure Maps Android SDK

Den här artikeln visar två sätt att ange kart stilar med hjälp av Azure Maps Android SDK. Azure Maps har sex olika typer av kartor att välja mellan. Mer information om mappnings format som stöds finns i [mappnings format som stöds i Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Förutsättningar

För att slutföra processen i den här artikeln måste du installera [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) för att läsa in en karta.


## <a name="set-map-style-in-the-layout"></a>Ange kart stil i layouten

Du kan ange en kart stil i layout filen för din aktivitets klass. Redigera **res > layout > activity_main. XML**, så det ser ut ungefär så här:

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

Attributet `mapcontrol_style` ovan anger mappnings formatet till **grayscale_dark**. 

<center>

![stil – grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Ange kart stil i aktivitets klassen

Du kan ställa in kart stil i aktivitets klassen. Kopiera följande kodfragment till metoden **onCreate ()** i `MainActivity.java`-klassen. Den här koden anger kart formatet till **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Style – satellit väg etiketter](./media/set-android-map-styles/satellite-road-labels.png)</center>