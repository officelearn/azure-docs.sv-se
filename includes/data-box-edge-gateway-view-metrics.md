---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618558"
---
Du kan också visa statistik för att övervaka prestanda för enheten och i vissa fall för att felsöka problem med enheter.

Vidta följande steg i Azure portal för att skapa ett diagram för vald enhetsmått.

1. För din resurs i Azure-portalen, gå till **övervakning > mått** och välj **Lägg till mått**.

    ![Lägga till mått](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Resursen fylls i automatiskt.  

    ![Aktuell resurs](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Välj resursen om du vill ange en annan resurs. På **väljer du en resurs** bladet väljer du prenumerationen, resursgruppen, resurstyp och den specifika resursen som du vill visa mått och välj **tillämpa**.

    ![Välj en annan resurs](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

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
    |**Edge-beräkning - minnesanvändning**      | Det här måttet är inte gäller för Data Box-Gateway och är därför inte ifyllt.          |
    |**Edge-beräkning – procent CPU**    | Det här måttet är inte gäller för Data Box-Gateway och är därför inte ifyllt.         |

4. När ett enskilt mått har valts i listrutan, kan du också definiera aggregering. Aggregering refererar till det faktiska värdet samman under en angiven tidsperiod. De sammanställda värdena kan vara medelvärdet, minimum eller det största värdet. Välj aggregeringen Avg, Max och Min.

    ![Visa diagram](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Om det mått som du har valt har flera instanser, finns alternativet delande. Välj **gäller dela** och välj sedan värdet som du vill se en analys på detaljnivå.

    ![Tillämpa dela](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Om du vill nu se nedbrytning endast för några instanser, kan du filtrera data. Till exempel, i det här fallet kan om du vill se nätverksdataflöde endast för de två nätverksgränssnitt som är anslutna på enheten kan du filtrera dessa gränssnitt. Välj **Lägg till filter** och ange namnet på nätverksgränssnittet för filtrering.

    ![Lägg till filter](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Du kan också fästa diagrammet på instrumentpanelen för enkel åtkomst.

    ![Fäst vid instrumentpanelen](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Om du vill exportera diagramdata till ett Excel-kalkylblad eller hämta en länk till ett diagram som du kan dela, väljer du alternativet dela i kommandofältet.

    ![Exportera data](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)