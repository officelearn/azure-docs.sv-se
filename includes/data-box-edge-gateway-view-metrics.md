---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187794"
---
Du kan också visa mått för att övervaka enhetens prestanda och i vissa fall för felsökning av enhetsproblem.

Gör följande i Azure-portalen för att skapa ett diagram för valda enhetsmått.

1. För din resurs i Azure-portalen går du till **Övervakning > mått** och väljer Lägg till **mått**.

    ![Lägga till mått](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Resursen fylls i automatiskt.  

    ![Aktuell resurs](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Om du vill ange en annan resurs markerar du resursen. På **Välj ett resursblad** väljer du prenumeration, resursgrupp, resurstyp och den specifika resurs som du vill visa måtten för och väljer **Använd**.

    ![Välj en annan resurs](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Välj ett mått som övervakar enheten i listrutan. Måtten kan vara **kapacitetsmått** eller **transaktionsmått**. Kapacitetsmåtten är relaterade till enhetens kapacitet. Transaktionsmåtten är relaterade till läs- och skrivåtgärderna till Azure Storage.

    |Kapacitetsmått                     |Beskrivning  |
    |-------------------------------------|-------------|
    |**Tillgänglig kapacitet**               | Refererar till storleken på de data som kan skrivas till enheten. Med andra ord är detta den kapacitet som kan göras tillgänglig på enheten. <br></br>Du kan frigöra enhetens kapacitet genom att ta bort den lokala kopian av filer som har en kopia på både enheten och molnet.        |
    |**Total kapacitet**                   | Refererar till den totala byte på enheten som ska skrivas data till. Detta kallas också den totala storleken på den lokala cachen. <br></br> Du kan nu öka kapaciteten för en befintlig virtuell enhet genom att lägga till en datadisk. Lägg till en datadisk via hypervisorhanteringen för den virtuella datorn och starta sedan om den virtuella datorn. Den lokala lagringspoolen på gateway-enheten expanderas så att den nya datadisken får plats. <br></br>Mer information finns i [Lägga till en hårddisk för virtuell hyper-v-dator](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Transaktionsmått              | Beskrivning         |
    |-------------------------------------|---------|
    |**Molnbyte laddas upp (enhet)**    | Summan av alla byte som laddats upp för alla resurser på din enhet        |
    |**Molnbyte laddas upp (dela)**     | Byte som laddats upp per aktie. Detta kan vara: <br></br> Genomsnittlig, vilket är (Summan av alla byte uppladdade per aktie / Antal aktier),  <br></br>Max, vilket är det maximala antalet byte som laddats upp från en resurs <br></br>Min, vilket är det minsta antalet byte som laddats upp från en resurs      |
    |**Moln nedladdningsdataflöde (dela)**| Byte hämtas per aktie. Detta kan vara: <br></br> Genomsnittlig, vilket är (Summan av alla byte läsa eller laddas ner till en aktie / Antal aktier) <br></br> Max, vilket är det maximala antalet byte som hämtats från en resurs<br></br> och Min, vilket är det minsta antalet byte som hämtats från en aktie  |
    |**Molnavläs dataflöde**            | Summan av alla byte som lästs från molnet över alla resurser på din enhet     |
    |**Molnuppladdningsdataflöde**          | Summan av alla byte som skrivits till molnet för alla resurser på enheten     |
    |**Molnuppladdningsdataflöde (dela)**  | Summan av alla byte som skrivs till molnet från en aktie / # av aktier är genomsnitt, max och min per aktie      |
    |**Läs dataflöde (nätverk)**           | Inkluderar dataflödet för systemnätverket för alla byte som läs avst läs från molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Om du delar upp trafiken över alla nätverkskort på enheten visas trafiken. Detta inkluderar kort som inte är anslutna eller aktiverade.      |
    |**Skriv dataflöde (nätverk)**       | Inkluderar dataflödet för systemnätverk för alla byte som skrivs till molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Om du delar upp trafiken över alla nätverkskort på enheten visas trafiken. Detta inkluderar kort som inte är anslutna eller aktiverade.          |
    |**Edge compute - minnesanvändning**      | Det här måttet är inte tillämpligt för Data Box Gateway och därför inte ifylld.          |
    |**Edge beräkning - procent CPU**    | Det här måttet är inte tillämpligt för Data Box Gateway och därför inte ifylld.         |

4. När ett mått väljs från listrutan kan aggregering också definieras. Aggregering refererar till det faktiska värdet som aggregerats över en angiven tidsperiod. De aggregerade värdena kan vara medelvärde, minimum eller det maximala värdet. Välj aggregering från Medel, Max eller Min.

    ![Visa diagram](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Om det valda måttet har flera instanser är delningsalternativet tillgängligt. Välj **Använd delning** och välj sedan det värde som du vill visa uppdelningen med.

    ![Använda delning](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Om du nu bara vill se uppdelningen för ett fåtal instanser kan du filtrera data. Om du till exempel bara vill se nätverksdataflödet för de två anslutna nätverksgränssnitten på enheten kan du filtrera dessa gränssnitt. Välj **Lägg till filter** och ange nätverksgränssnittets namn för filtrering.

    ![Lägg till filter](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Du kan också fästa diagrammet på instrumentpanelen för enkel åtkomst.

    ![Fäst vid instrumentpanelen](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Om du vill exportera diagramdata till ett Excel-kalkylblad eller få en länk till diagrammet som du kan dela väljer du delningsalternativet i kommandofältet.

    ![Exportera data](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)