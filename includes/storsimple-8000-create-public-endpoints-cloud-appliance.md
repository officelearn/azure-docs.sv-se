---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1cf5bbdad555c50c418851904f36a578522843b2
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166358"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Så här skapar du offentliga slutpunkter för molninstallationen

1. Logga in på Azure Portal.
2. Gå till **Virtual Machines** och markera och klicka på den virtuella dator som används som molninstallation.
    
3. Du måste skapa en NSG-regel (nätverkssäkerhetsgrupp) för att styra flödet av trafik in till och ut från den virtuella datorn. Skapa en NSG-regel genom att utföra stegen nedan.
    1. Välj **Nätverkssäkerhetsgrupp**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Klicka på standardnätverkssäkerhetsgruppen.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Välj **Inkommande säkerhetsregel**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Skapa en säkerhetsregel för inkommande trafik genom att klicka på **+ Lägg till**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        På bladet Lägg till inkommande säkerhetsregel:

        1. I fältet **Namn** anger du följande namn för slutpunkten: WinRMHttps.
        
        2. För **Prioritet** väljer du ett tal som är mindre än 1 000 (som är prioriteten för standardregeln). Desto högre värde, ju lägre prioritet.

        3. Ange **Källa** till **Alla**.

        4. För **Tjänst** väljer du **WinRM**. **Protokoll** ställs automatiskt in till **TCP**, och **Portintervall** till **5986**.

        5. Skapa regeln genom att klicka på **OK**.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Det sista steget är att associera nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. Du associerar nätverkssäkerhetsgruppen med ett undernät genom att utföra stegen nedan.
    1. Gå till **Undernät**.
    2. Klicka på **+ Associera**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Välj ditt virtuella nätverk och välj sedan lämpligt undernät.
    4. Skapa regeln genom att klicka på **OK**.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

När regeln har skapats kan du visa information om regeln för att ta reda på den offentliga virtuella IP-adressen (VIP). Anteckna den adressen.


