---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187794"
---
Du kan också visa måtten för att övervaka enhetens prestanda och i vissa fall vid fel sökning av enhets problem.

Utför följande steg i Azure Portal för att skapa ett diagram för valda enhets mått.

1. För resursen i Azure Portal går du till **övervakning > mått** och väljer **Lägg till mått**.

    ![Lägga till mått](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Resursen fylls i automatiskt.  

    ![Aktuell resurs](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Om du vill ange en annan resurs väljer du resursen. På bladet **Välj en resurs** väljer du den prenumeration, resurs grupp, resurs typ och den resurs som du vill visa måtten för och väljer **tillämpa**.

    ![Välj en annan resurs](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. I list rutan väljer du ett mått för att övervaka enheten. Måtten kan vara **kapacitets mått** eller **transaktions mått**. Kapacitets måtten är relaterade till enhetens kapacitet. Transaktions måtten är relaterade till Läs-och skriv åtgärder för att Azure Storage.

    |Kapacitetsmått                     |Beskrivning  |
    |-------------------------------------|-------------|
    |**Tillgänglig kapacitet**               | Avser storleken på de data som kan skrivas till enheten. Med andra ord är detta den kapacitet som kan göras tillgänglig på enheten. <br></br>Du kan frigöra enhets kapaciteten genom att ta bort den lokala kopian av filer som har en kopia på både enheten och molnet.        |
    |**Total kapacitet**                   | Avser det totala antalet byte på enheten som data ska skrivas till. Detta kallas även för den totala storleken på det lokala cacheminnet. <br></br> Du kan nu öka kapaciteten för en befintlig virtuell enhet genom att lägga till en datadisk. Lägg till en datadisk via hypervisor-hantering för den virtuella datorn och starta sedan om den virtuella datorn. Den lokala lagringspoolen för gateway-enheten kommer att utökas för att hantera den nyligen tillagda data disken. <br></br>Mer information finns i lägga till [en hård disk för virtuell Hyper-V-dator](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Transaktionsmått              | Beskrivning         |
    |-------------------------------------|---------|
    |**Överförda moln byte (enhet)**    | Summan av alla byte som laddats upp över alla resurser på enheten        |
    |**Överförda moln byte (resurs)**     | Överförda byte per resurs. Detta kan vara: <br></br> AVG, som är (summan av alla byte som överförs per resurs/antal resurser),  <br></br>Max, vilket är det maximala antalet byte som överförs från en resurs <br></br>Min, som är det minsta antalet byte som överförs från en resurs      |
    |**Data flöde för moln hämtning (resurs)**| Hämtade byte per resurs. Detta kan vara: <br></br> AVG, som är (summan av alla byte som lästs eller laddats ned till resurs/antal resurser) <br></br> Max, vilket är det maximala antalet byte som hämtas från en resurs<br></br> och min, som är det minsta antalet byte som hämtas från en resurs  |
    |**Moln läsnings data flöde**            | Summan av alla byte som lästs från molnet över alla resurser på enheten     |
    |**Moln överförings data flöde**          | Summan av alla byte som skrivits till molnet över alla resurser på enheten     |
    |**Moln överförings data flöde (resurs)**  | Summan av alla byte som skrivs till molnet från resurs/antal resurser är genomsnitt, max och min per resurs      |
    |**Läs data flöde (nätverk)**           | Innehåller systemets nätverks data flöde för alla byte som läses från molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Vid delning visas trafiken över alla nätverkskort på enheten. Detta inkluderar kort som inte är anslutna eller aktiverade.      |
    |**Skriv data flöde (nätverk)**       | Innehåller systemets nätverks data flöde för alla byte som skrivs till molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Vid delning visas trafiken över alla nätverkskort på enheten. Detta inkluderar kort som inte är anslutna eller aktiverade.          |
    |**Edge Compute-minnes användning**      | Måttet gäller inte för Data Box Gateway och fylls därför inte i.          |
    |**Edge Compute-procent CPU**    | Måttet gäller inte för Data Box Gateway och fylls därför inte i.         |

4. När ett mått väljs i list rutan kan agg regering också definieras. Aggregator refererar till det faktiska värdet som sammanställs under en angiven tidsrymd. De sammanställda värdena kan vara genomsnitt, lägsta eller högsta värde. Välj agg regeringen från AVG, Max eller min.

    ![Visa diagram](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Om det mått du har valt har flera instanser är delnings alternativet tillgängligt. Välj **Använd delning** och välj sedan det värde som du vill visa uppdelningen för.

    ![Använd delning](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Om du nu bara vill se detalj nivån för några få instanser kan du filtrera data. I det här fallet kan du till exempel filtrera dessa gränssnitt om du bara vill se nätverks data flödet för de två anslutna nätverks gränssnitten på enheten. Välj **Lägg till filter** och ange nätverks gränssnitts namnet för filtrering.

    ![Lägg till filter](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Du kan också fästa diagrammet på instrument panelen för enkel åtkomst.

    ![Fäst vid instrumentpanelen](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Om du vill exportera diagram data till ett Excel-kalkylblad eller hämta en länk till diagrammet som du kan dela, väljer du alternativet dela i kommando fältet.

    ![Exportera data](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)