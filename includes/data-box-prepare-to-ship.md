---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: a78bce9f6e714aa5564791f6bb63996554beabf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375925"
---
Det sista steget är att förbereda enheten för att skickas. I det här steget frånkopplas alla enhetsresurser. Det går inte att komma åt resurserna när du har startat den här processen.

> [!IMPORTANT]
> Förbered för att skicka krävs eftersom det flaggar data som inte följer Azure-namngivningskonventionerna. Det här steget förhindrar potentiella datauppladdningsfel på grund av data som inte uppfyller kraven.

1. Gå till **Förbered för att skicka** och klicka på **Starta förberedelser**. Som standard beräknas kontrollsummor medan data kopieras. ”Förbered för att skicka” slutför beräkningen av kontrollsumman och skapar listan med filer ( *BOM-filer* ). Beräkningen av kontrollsumma kan ta några timmar till flera dagar beroende på storleken på data. 
   
    ![Förbered för att skicka 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Om du behöver stoppa förberedelsen av enheten klickar du på **Stoppa förberedelse**. Du kan återuppta Förbered för att skicka senare.
        
    ![Förbered för att skicka 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Förbered för att skicka startas och enhetsresurserna kopplas från. <!--You see a reminder to download the shipping label once the device is ready.--> Enhetsstatusen uppdateras till *Klar för leverans* och enheten låses när enhetsförberedelserna är klara.
        
    ![Förbered för att skicka 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Om du vill kopiera mer data till enheten kan du låsa upp enheten, kopiera mer data och köra Förbered för att skicka igen.

    Om det uppstår fel i det här steget uppdateras statusen till *Skanningen slutfördes med fel*. Lås upp enheten och gå till sidan **Anslut och kopiera** , ladda ned listan med problem och åtgärda felen.

    ![Förbered för att skicka 4](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    När felen har åtgärdats kör du **Förbered för att skicka**.

4. När Förbered för att skicka har utförts (utan fel) följer du dessa steg:

    1. Skriv ned referensnumret som du får när åtgärden utförts. Beroende på vilket land du befinner dig i kan det här numret krävas för olika åtgärder.
    2. Ladda ned listan med filer (kallas även manifest) som har kopierats i den här processen. Du kan senare använda den här listan för att verifiera filerna som har laddats upp till Azure. Mer information finns i [Inspektera BOM-filer under Förbered för att skicka](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![Förbered för att skicka 5](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Välj och ladda ned leveransanvisningarna för enheten. Leveransanvisningarna skiljer sig åt beroende på vilket land du befinner dig i.
    4. Om fraktsedeln inte visas på e-bläckskärmen kan du ladda ned returfraktsedeln här. 

5. Stäng av enheten. Gå till sidan **Stäng av eller starta om** och klicka på **Stäng av**. När du uppmanas att bekräfta klickar du på **OK** för att fortsätta.

6. Ta bort kablarna. Nästa steg är att skicka enheten till Microsoft.
