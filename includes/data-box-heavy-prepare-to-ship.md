---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419575"
---
Det sista steget är att förbereda enheten för att skickas. I det här steget frånkopplas alla enhetsresurser. Det går inte att komma åt resurserna när du har startat den här processen.

> [!IMPORTANT]
> Förbered för att skicka krävs eftersom det flaggar data som inte följer Azure-namngivningskonventionerna. Hoppar över det här steget kan leda till potentiella data överföra fel som beror på data som inte uppfyller kraven.

1. Gå till **Förbered för att skicka** och klicka på **Starta förberedelser**. Som standard beräknas kontrollsummor när data kopieras. Förbered för att skicka har slutförts beräkning av kontrollsumma och skapar listan med filer (även kallat *BOM filer* eller manifestet). Beräkningen av kontrollsumma kan ta några timmar till flera dagar beroende på storleken på data.
   
    ![Förbered för att skicka 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Om du behöver stoppa förberedelsen av enheten klickar du på **Stoppa förberedelse**. Du kan återuppta Förbered för att skicka senare.
        
    ![Förbered för att skicka 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. Förbered för att skicka startas och enhetsresurserna kopplas från. Du ser en påminnelse om att ladda ned adressetiketten när enheten är klar.

    ![Påminnelse om att ladda ned adressetikett](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Enhetsstatusen uppdateras till *Klar för leverans* och enheten låses när enhetsförberedelserna är klara.
        
    ![Förbered för att skicka 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Om du vill kopiera mer data till enheten kan du låsa upp enheten, kopiera mer data och köra Förbered för att skicka igen.

    Om det uppstår fel i det här steget kan du hämta felloggen och åtgärda felen. När felen har åtgärdats kör du **Förbered för att skicka**.

4. Efter Förbered för att skicka är slutförd (med några fel), ladda ned listan med filer (även kallat *BOM filer* eller manifestet) kopieras i den här processen. 

    ![Hämta lista över filer eller BOM filer](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Du kan senare använda den här listan för att verifiera filerna som har laddats upp till Azure. Mer information finns i [inspektera BOM filer under Förbered för att skicka](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![BOM exempelfil](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Stäng av enheten. Gå till sidan **Stäng av eller starta om** och klicka på **Stäng av**. När du uppmanas att bekräfta klickar du på **OK** för att fortsätta.

    ![Stäng av den första enhetsnoden](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Upprepa ovanstående steg för den andra enhetsnoden.
7. När enheten har helt stänga av skulle alla led: ar på baksidan av enheten ha inaktiverat. Nästa steg är att ta bort alla kablar och skicka till Microsoft.
