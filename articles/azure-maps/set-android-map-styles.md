---
title: Ange en kart stil med Azure Maps Android SDK
description: Lär dig två sätt att ställa in formatet på en karta. Se hur du använder Microsoft Azure mappar Android SDK i antingen layoutvyn eller i klassen aktivitet för att justera formatet.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532491"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ange kart stil med Azure Maps Android SDK

Den här artikeln visar hur du ställer in kart stilar med Azure Maps Android SDK. Azure Maps har sex olika typer av kartor att välja mellan. Mer information om mappnings format som stöds finns i [mappnings format som stöds i Azure Maps](./supported-map-styles.md).

## <a name="prerequisites"></a>Krav

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. Hämta och installera [Azure Maps Android SDK](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Ange kart stil i layouten

Du kan ange en kart stil i layout filen för din aktivitets klass. Redigera `res > layout > activity_main.xml` , så att det ser ut som på bilden nedan:

```XML
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

`mapcontrol_style`Attributet ovan anger kart formatet till **grayscale_dark**.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, kart bild som visar format som grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Ange kart stil i MainActivity-klassen

Map-formatet kan också anges i klassen MainActivity. Öppna `java > com.example.myapplication > MainActivity.java` filen och kopiera följande kodfragment till metoden **onCreate ()** . Den här koden anger kart formatet till **satellite_road_labels**.

>[!WARNING]
>Android Studio kanske inte har importerat de obligatoriska klasserna.  Därför har koden vissa referenser som inte kan matchas. Om du vill importera de obligatoriska klasserna hovrar du bara över varje olöst referens och trycker på `Alt + Enter` (alternativ + retur på en Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, kart bild som visar format som satellite_road_labels":::