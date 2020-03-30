---
title: Generera värmekarta för jordfukt
description: Beskriver hur du skapar Heatmap för jordfukt i Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482574"
---
# <a name="generate-soil-moisture-heatmap"></a>Generera värmekarta för jordfukt

Markfukt är vattnet som hålls i utrymmena mellan jordpartiklar.Soil Moisture Heatmap hjälper dig att förstå fuktdata på alla djup, och med hög upplösning inom dina gårdar. För att generera en korrekt och användbar värmekarta för markfukt krävs en enhetlig användning av sensorer från samma leverantör. Olika leverantörer kommer att ha skillnader i hur markfukt mäts tillsammans med skillnader i kalibrering. Heatmap genereras för ett visst djup med hjälp av de sensorer som används på det djupet.

I den här artikeln beskrivs processen att generera en soil moisture heatmap för din servergrupp med hjälp av Azure FarmBeats Accelerator. I den här artikeln får du lära dig hur du:

- [Skapa gårdar](#create-a-farm)
- [Tilldela sensorer till gårdar](#get-soil-moisture-sensor-data-from-partner)
- [Generera värmekarta för jordfukt](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera följande:  

- En Azure-prenumeration.
- En förekomst av Azure FarmBeats körs.
- Minst tre markfuktsensorer finns tillgängliga för gården.

## <a name="create-a-farm"></a>Skapa en servergrupp

En gård är ett geografiskt område av intresse som du vill skapa en värmekarta för markfukt. Du kan skapa en servergrupp med [api:et för gårdar](https://aka.ms/FarmBeatsDatahubSwagger) eller i [användargränssnittet för FarmsBeats Accelerator](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Distribuera sensorer

Du bör fysiskt distribuera mark fuktsensorer på gården. Du kan köpa mark fuktsensorer från någon av våra godkända partners - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) och [Teralytic](https://teralytic.com/). Du bör samordna med sensorleverantören för att göra den fysiska konfigurationen på din gård.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Få markfuktsensordata från partner

När sensorer börjar strömma, data till partnerdatainstrumentpanelen, aktiverar de data i Azure FarmBeats. Detta kan göras från partnerprogrammet.

Om du till exempel har köpt Davis-sensorer loggar du in på ditt väderlänkkonto och anger de autentiseringsuppgifter som krävs för att aktivera data som direktuppspelar till Azure FarmBeats. För att få de autentiseringsuppgifter som krävs, följ instruktionerna från [Hämta sensordata](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

När du har angett dina autentiseringsuppgifter och valt **Skicka** på partnerprogrammet kan du låta data flöda in i Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Tilldela markens fuktsensorer till gården

När du har länkat ditt sensorkonto till Azure FarmBeats måste du tilldela markfuktsensorerna till den grupp av intresse.

1.  På startsidan väljer du **Gårdar** på menyn, listsidan **Gruppera** visas.
2.  Välj Lägg**till enheter för** **MyFarm** > .
3.  Fönstret **Lägg till enheter** visas. Välj en enhet som är kopplad till marken fuktsensorer för din gård.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Välj **Lägg till enheter**.     

## <a name="generate-soil-moisture-heatmap"></a>Generera värmekarta för jordfukt

Detta steg är att skapa ett jobb eller en långvarig operation som kommer att generera Soil Moisture Heatmap för din gård.

1.  På startsidan går du till **Gårdar** från den vänstra navigeringsmenyn för att visa sidan för grupper.
2.  Välj **MyFarm**.
3.  På sidan **Servergruppsinformation** väljer du **Generera precisionskarta**.
4.  Välj **Jordfuktning**på den nedrullningsbara menyn.
5.  I fönstret **Jordfukt** väljer du **Den här veckan**.
6.  I välj **markfuktsensormått** **Sensor Measure**anger du det mått som du vill använda för kartan.
    För att hitta sensormåttet, i **Sensorer,** välj någon markfuktsensor. Använd värdet **Mätnamn** i **Sensoregenskaper.**

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Välj **Generera kartor**.
    Ett bekräftelsemeddelande med jobbinformation visas. Mer information finns i Jobbstatus i jobb.

    >[!NOTE]
    > Jobbet tar cirka tre till fyra timmar att slutföra.

### <a name="download-the-soil-moisture-heatmap"></a>Ladda ner Soil Moisture Heatmap

Använd följande steg:

1. På sidan **Jobb** kontrollerar du **jobbstatus** för det jobb som du skapade i den senaste proceduren.
2. När jobbstatusen visar **Lyckades**väljer du **Kartor** på menyn.
3. Sök efter kartan när den skapades i formatet <jord-moisture_MyFarm_YYYY-MM-DD>.
4. Välj en karta i kolumnen **Namn,** ett popup-fönster visas med förhandsgranskningen av den valda kartan.
5. Välj **Ladda ned**. Kartan hämtas och lagras i den lokala mappen på datorn.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har genererat en Soil Moisture Heatmap, lära sig att [generera sensorplacering](generate-maps-in-azure-farmbeats.md#sensor-placement-map) och [inta historiska telemetridata](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
