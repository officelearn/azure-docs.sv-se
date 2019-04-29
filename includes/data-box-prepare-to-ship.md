---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a8070d25e2606d8ad72ac231a0a208072c612c5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60463194"
---
Det sista steget är att förbereda enheten för att skickas. I det här steget frånkopplas alla enhetsresurser. Det går inte att komma åt resurserna när du har startat den här processen.

> [!IMPORTANT]
> Förbered för att skicka krävs eftersom det flaggar data som inte följer Azure-namngivningskonventionerna. Hoppar över det här steget kan leda till potentiella data överföra fel som beror på data som inte uppfyller kraven.

1. Gå till **Förbered för att skicka** och klicka på **Starta förberedelser**. Som standard beräknas kontrollsummor infogat under leveransförberedelserna. Beräkningen av kontrollsumma kan ta några timmar till flera dagar beroende på storleken på data. 
   
    ![Förbered för att skicka 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Om du behöver stoppa förberedelsen av enheten klickar du på **Stoppa förberedelse**. Du kan återuppta Förbered för att skicka senare.
        
    ![Förbered för att skicka 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Förbered för att skicka startas och enhetsresurserna kopplas från. Du ser en påminnelse om att ladda ned adressetiketten när enheten är klar.

    ![Påminnelse om att ladda ned adressetikett](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Enhetsstatusen uppdateras till *Klar för leverans* och enheten låses när enhetsförberedelserna är klara.
        
    ![Förbered för att skicka 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Om du vill kopiera mer data till enheten kan du låsa upp enheten, kopiera mer data och köra Förbered för att skicka igen.

    Om det inträffar fel i det här steget behöver du ladda ned felloggen och åtgärda felen. När felen har åtgärdats kör du **Förbered för att skicka**.

4. När Förbered för att skicka är klart (utan fel) laddar du ned listan över filer (som även kallas manifest) som kopierats i den här processen. Du kan senare använda den här listan för att verifiera filerna som har laddats upp till Azure.
        
    ![Förbered för att skicka 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Stäng av enheten. Gå till sidan **Stäng av eller starta om** och klicka på **Stäng av**. När du uppmanas att bekräfta klickar du på **OK** för att fortsätta.

6. Ta bort kablarna. Nästa steg är att skicka enheten till Microsoft.
