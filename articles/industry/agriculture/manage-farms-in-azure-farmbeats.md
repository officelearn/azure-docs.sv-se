---
title: Hantera gårdar
description: Beskriver hur du hanterar gårdar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271725"
---
# <a name="manage-farms"></a>Hantera lantbruk

Du kan hantera dina gårdar i Azure FarmBeats. Den här artikeln innehåller information om hur du skapar gårdar, installerar enheter, sensorer och drönare som hjälper dig att hantera dina gårdar.

## <a name="create-farms"></a>Skapa gårdar

Använd följande steg:

1. Logga in på Farm Accelerator, visar **farms** sidan.
    På sidan **Gårdar** visas listan över grupper om de redan har skapats i prenumerationen.

    Här är exempelbilden:

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Välj **Skapa servergrupp** och ange **namn,** **grödor** och **adress**.
3. Välj **markera på karta** eller Klistra in **GeoJSON-kod**i fältet **Definiera servergruppsgräns**.

Här är de två sätten att definiera en gårdsgräns:

1. **Markera på karta:** Använd kartkontrollverktyget för att rita och markera gränsen för servergruppen. Om du vill ![markera gränserna](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) markerar Project Farm Beats och markerar de exakta gränserna.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Klistra in GeoJson-kod:** GeoJSON är ett format för kodning av geografiska datastrukturer med JavaScript Object Notation (JSON). Det här alternativet visar en textruta där en GeoJSON-sträng kan anges för att markera servergruppsgränserna. Du kan också skapa GeoJSON-kod från GeoJSON.io.
Använd verktygstipsen för att fylla i informationen.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Välj **Skicka** om du vill skapa en servergrupp. En ny servergrupp skapas och visas på sidan **Gårdar.**

## <a name="view-farm"></a>Visa servergrupp

På listsidan i servergruppen visas en lista över skapade grupper. Välj en grupp om du vill visa listan över:

 - **Enhetsantal** – visar antalet och statusen för enheter som distribueras i servergruppen.
 - **Karta** – karta över servergruppen med de enheter som distribueras i servergruppen.
 - **Telemetri** – visar telemetrin från sensorerna som distribueras i servergruppen.
 - **Senaste Precision Maps** - visar den senaste Satellit Index karta (EVI, NDWI), Soil Moisture Heatmap och Sensor Placement karta.

## <a name="edit-farm"></a>Redigera servergrupp

På sidan **Gårdar** visas en lista över skapade grupper.

1.  Välj en servergrupp om du vill visa och redigera servergruppen.
2.  Välj **Redigera servergrupp** om du vill redigera servergruppsinformationen. I fönstret **Serverningsinformation** kan du redigera **fälten Namn,** **Grödor**, **Adress**och definiera **fälten Servergruppsgräns.**

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Välj **Skicka** om du vill spara informationen som redigerats.

## <a name="delete-farm"></a>Ta bort servergrupp

På sidan **Gårdar** visas en lista över skapade grupper. Så här tar du bort en servergrupp:

1.  Välj en servergrupp i listan om du vill ta bort servergruppsinformation.
2.  Välj **Ta bort servergrupp** om du vill ta bort servergruppen.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > När du tar bort en servergrupp tas inte de enheter och kartor som är associerade med servergruppen bort. All servergruppsinformation som är kopplad till enheten och kartor är inte relevant. Du kan fortsätta att visa enheter, telemetri och kartor från FarmBeats-tjänsten.


## <a name="next-steps"></a>Nästa steg

Nu när du har skapat din gård kan du lära dig hur [du får sensordata](get-sensor-data-from-sensor-partner.md) att flöda in i din servergrupp.
