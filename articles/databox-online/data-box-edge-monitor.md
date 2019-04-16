---
title: Övervaka din Azure Data Box Edge-enhet | Microsoft Docs
description: Beskriver hur du använder Azure-portalen och lokala webbgränssnittet för att övervaka dina Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 4d4cc8a3eaf2bf806d5b265be226482b23804f82
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580401"
---
# <a name="monitor-your-azure-data-box-edge"></a>Övervaka dina Azure Data Box-Edge

Den här artikeln beskriver hur du övervakar dina Azure Data Box Edge. Du kan använda Azure portal eller det lokala webbgränssnittet för att övervaka din enhet. Använda Azure-portalen för att visa enhetshändelser, konfigurera och hantera aviseringar och visa mått. Använd det lokala webbgränssnittet på den fysiska enheten om du vill visa maskinvarustatus för de olika enhetskomponenterna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Visa enhetshändelser och motsvarande aviseringar
> * Visa maskinvarustatus av komponenter
> * Visa kapacitet och transaktionen mått för din enhet
> * Konfigurera och hantera aviseringar

## <a name="view-device-events"></a>Visa händelser för enhet

Vidta följande steg i Azure portal för att visa en enhetshändelse.

1. I Azure-portalen går du till din Data Box-Edge / Data Box Gateway-resursen och gå sedan till **övervakning > enhetshändelser**.
2. Välj en händelse och visa mer information. Vidta lämpliga åtgärder att lösa aviseringstillståndet.

    ![Välj händelse och visa information](media/data-box-edge-monitor/view-device-events.png)

## <a name="view-hardware-status"></a>Visa maskinvarustatus

Vidta följande steg i det lokala webbgränssnittet visa maskinvarustatus av din enhetskomponenter. Den här informationen är endast tillgänglig för en Data Box Edge-enhet.

1. Ansluta till det lokala webbgränssnittet på din enhet.
2. Gå till **Underhåll > maskinvarustatus**. Du kan visa hälsotillståndet för de olika enhetskomponenterna.

    ![Visa maskinvarustatus](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visa mått

Du kan också visa statistik för att övervaka prestanda för enheten och i vissa fall för att felsöka problem med enheter.

Vidta följande steg i Azure portal för att skapa ett diagram för vald enhetsmått.

1. För din resurs i Azure-portalen, gå till **övervakning > mått** och välj **Lägg till mått**.

    ![Lägga till mått](media/data-box-edge-monitor/view-metrics-1.png)

2. Resursen fylls i automatiskt.  

    ![Aktuell resurs](media/data-box-edge-monitor/view-metrics-2.png)

    Välj resursen om du vill ange en annan resurs. På **väljer du en resurs** bladet väljer du prenumerationen, resursgruppen, resurstyp och den specifika resursen som du vill visa mått och välj **tillämpa**.

    ![Välj en annan resurs](media/data-box-edge-monitor/view-metrics-3.png)

3. Välj ett mått för att övervaka din enhet i listrutan. Mått kan vara **kapacitetsmåtten** eller **transaktionsmått**. Kapacitetsmåtten är relaterade till kapaciteten för enheten. Transaktionsmått är relaterade till Läs- och skrivåtgärder till Azure Storage.

    |Kapacitet mått                     |Beskrivning  |
    |-------------------------------------|-------------|
    |**Tillgänglig kapacitet**               | Refererar till storleken på de data som kan skrivas till enheten. Det här är alltså den kapacitet som kan göras tillgängliga på enheten. <br></br>Du kan frigöra kapacitet för enheten genom att ta bort den lokala kopian av filer som har en kopia på både enheten och molnet.        |
    |**Total kapacitet**                   | Refererar till det totala antal byte på enheten för att skriva data till. Detta kallas även för den totala storleken på den lokala cachen. <br></br> Du kan nu öka kapaciteten för en befintlig virtuell enhet genom att lägga till en datadisk. Lägg till en datadisk genom hypervisor-hantering för den virtuella datorn och starta sedan om den virtuella datorn. Lokal lagringspoolen med Gateway-enheten ska expanderas för att hantera den nyligen tillagda datadisken. <br></br>Mer information går du till [lägga till en hårddisk för Hyper-V-dator](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Transaktionsmått              | Beskrivning         |
    |-------------------------------------|---------|
    |**Molnet byte överförda (enhet)**    | Summan av alla byte som överförs i alla resurser på din enhet        |
    |**Byte som överförts (resurs) i molnet**     | Byte har överförts per resurs. Detta kan vara: <br></br> Genomsnittlig, vilket är den (summan av alla byte som överförs per resurs / antal resurser),  <br></br>Max, vilket är det maximala antalet byte som överförs från en resurs <br></br>Min, vilket är det minsta antalet byte som överförs från en resurs      |
    |**Molnet download dataflöde (resurs)**| Byte ned per resurs. Detta kan vara: <br></br> Genomsnittlig, vilket är den (summan av alla byte läsa eller laddas ned till en resurs / antal resurser) <br></br> Max, vilket är det maximala antalet byte som hämtas från en resurs<br></br> och Min, vilket är det minsta antalet byte som hämtas från en resurs  |
    |**Molnet läsningsdataflöde**            | Summan av alla byte läser från molnet över alla resurser på din enhet     |
    |**Molnet uppladdningsdataflödet**          | Summan av alla byte som skrivs till molnet över alla resurser på din enhet     |
    |**Molnet uppladdningsdataflödet (resurs)**  | Summan av alla byte som skrivs till molnet från en resurs / antal filresurser är average, max och min per resurs      |
    |**Läs dataflöde (nätverk)**           | Innehåller system-nätverksgenomflöde för alla byte som läses från molnet. Den här vyn kan innehålla data som inte är begränsad till resurser. <br></br>Dela upp visar trafiken över alla nätverkskort på enheten. Detta inkluderar nätverkskort som inte är ansluten eller aktiverat.      |
    |**Skriva dataflöde (nätverk)**       | Innehåller system-nätverksgenomflöde för alla byte som skrivs till molnet. Den här vyn kan innehålla data som inte är begränsad till resurser. <br></br>Dela upp visar trafiken över alla nätverkskort på enheten. Detta inkluderar nätverkskort som inte är ansluten eller aktiverat.          |
    |**Edge-beräkning - minnesanvändning**      | Minnesanvändning för IoT Edge-enhet för din Data Box-Edge. Kontakta Microsoft Support för att avgöra nästa steg om du ser en hög användning och om enhetens prestanda påverkas av de aktuella arbetsbelastningar som du har distribuerat. <br></br>Det här måttet fylls inte i Data Box-Gateway.          |
    |**Edge-beräkning – procent CPU**    | CPU-användning för IoT Edge-enhet för din Data Box-Edge. Kontakta Microsoft Support för att avgöra nästa steg om du ser en hög användning och om enhetens prestanda påverkas av de aktuella arbetsbelastningar som du har distribuerat. <br></br>Det här måttet fylls inte i Data Box-Gateway.        |
1. När ett enskilt mått har valts i listrutan, kan du också definiera aggregering. Aggregering refererar till det faktiska värdet samman under en angiven tidsperiod. De sammanställda värdena kan vara medelvärdet, minimum eller det största värdet. Välj aggregeringen Avg, Max och Min.

    ![Visa diagram](media/data-box-edge-monitor/view-metrics-4.png)

5. Om det mått som du har valt har flera instanser, finns alternativet delande. Välj **gäller dela** och välj sedan värdet som du vill se en analys på detaljnivå.

    ![Tillämpa dela](media/data-box-edge-monitor/view-metrics-5.png)

6. Om du vill nu se nedbrytning endast för några instanser, kan du filtrera data. Till exempel, i det här fallet kan om du vill se nätverksdataflöde endast för de två nätverksgränssnitt som är anslutna på enheten kan du filtrera dessa gränssnitt. Välj **Lägg till filter** och ange namnet på nätverksgränssnittet för filtrering.

    ![Lägg till filter](media/data-box-edge-monitor/view-metrics-6.png)

7. Du kan också fästa diagrammet på instrumentpanelen för enkel åtkomst.

    ![Fäst vid instrumentpanelen](media/data-box-edge-monitor/view-metrics-7.png)

8. Om du vill exportera diagramdata till ett Excel-kalkylblad eller hämta en länk till ett diagram som du kan dela, väljer du alternativet dela i kommandofältet.

    ![Exportera data](media/data-box-edge-monitor/view-metrics-8.png)

## <a name="manage-alerts"></a>Hantera aviseringar

Konfigurera Varningsregler för att informera dig om aviseringsvillkoren som rör förbrukningen av resurser på din enhet. Du kan konfigurera Varningsregler för att övervaka din enhet för aviseringsvillkor. Mer detaljerad information om aviseringar, gå till [skapa, visa och hantera aviseringar för mått i Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg 

Lär dig hur du [hanterar bandbredd](data-box-edge-manage-bandwidth-schedules.md).