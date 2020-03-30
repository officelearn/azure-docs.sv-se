---
title: Loggsupportbiljett för Azure Data Box Edge, Azure Data Box Gateway | Microsoft-dokument
description: Lär dig hur du loggar supportbegäran för problem relaterade till dina databoxkants- eller databoxgatewayorder.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: c93a2e2f3162891e72cf6d57f3df6c3ceaf0c7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064979"
---
# <a name="open-a-support-ticket-for-azure-data-box-edge-and-azure-data-box-gateway"></a>Öppna en supportbiljett för Azure Data Box Edge och Azure Data Box Gateway

Den här artikeln gäller Azure Data Box Edge och Azure Data Box Gateway som båda hanteras av Azure Data Box Edge/Azure Data Box Gateway-tjänsten. Om du stöter på problem med din tjänst kan du skapa en servicebegäran om teknisk support. I den här artikeln går du igenom:

* Så här skapar du en supportbegäran.
* Så här hanterar du en livscykel för supportbegäran inifrån portalen.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Gör följande för att skapa en supportbegäran:

1. Gå till din databoxkant eller databoxgatewaybeställning. Navigera till **avsnittet Support + felsökning** och välj sedan Ny **supportbegäran**.
   
2. I **Ny supportbegäran**gör du följande på fliken **Grunderna:**
    
    1. Välj **Issue type** **Teknisk**.
    2. Välj din **prenumeration**.
    3. Kontrollera Mina **tjänster under** **Service**. Välj **Data Box Edge och Data Box Gateway**i listrutan .
    4. Välj **din resurs**. Detta motsvarar namnet på din beställning.
    5. Ge en kort **sammanfattning** av problemet du upplever. 
    6. Välj **din problemtyp**.
    7. Baserat på den problemtyp du valde väljer du en motsvarande **problemundertyp**.
    8. Välj **Nästa: Lösningar >>**.

        ![Grundläggande inställningar](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Gör så här på fliken **Information:**
    
    1. Ange startdatum och starttid för problemet.
    2. Ange en **beskrivning** för ditt problem.
    3. I **filöverföringen**väljer du mappikonen för att bläddra bland andra filer som du vill ladda upp.
    4. Kontrollera **Dela diagnostikinformation**.
    5. Baserat på din prenumeration fylls en **supportplan** i automatiskt.
    6. Välj **allvarlighetsgrad**i listrutan .
    7. Ange en **önskad kontaktmetod**.
    8. **Svarstiderna** väljs automatiskt baserat på din prenumerationsplan.
    9. Ange det språk du föredrar för support.
    10. Ange namn, e-post, telefon, valfri kontakt, land/region i **kontaktinformationen.** Microsoft Support använder den här informationen för att nå ut till dig för ytterligare information, diagnos och lösning. 
    11. Välj **Nästa: Granska + Skapa >>**.

        ![Problem](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. På fliken **Granska + Skapa** läser du informationen om supportbiljetten. Välj **Skapa**. 

    ![Problem](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    När du har skapat supportbiljetten kontaktar en supporttekniker dig så snart som möjligt för att gå vidare med din förfrågan.

## <a name="get-hardware-support"></a>Få maskinvarusupport

Den här informationen gäller endast Data Box Edge-enheten. Processen för att rapportera maskinvaruproblem är följande:

1. Öppna en supportbiljett från Azure-portalen för ett maskinvaruproblem. Under **Problemtyp**väljer du **Data Box Edge Hardware**. Välj **undertypen Problem** som **maskinvarufel**. 

    ![Maskinvaruproblem](./media/data-box-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    När du har skapat supportbiljetten kontaktar en supporttekniker dig så snart som möjligt för att gå vidare med din förfrågan. 

2. Om Microsoft Support fastställer att detta är ett maskinvaruproblem inträffar någon av följande åtgärder: 

    - En ersättningsenhet för fältersättning (FRU) för den misslyckade maskinvarudelen skickas. För närvarande är strömförsörjningsenheten den enda SOM stöds FRU. 
    - För andra delfel gör Microsoft en fullständig systemersättning (FSR) eller en enhetsväxling.

3. Om en supportbiljett höjs före 16:30 lokal tid (måndag till fredag) skickas en tekniker på plats nästa arbetsdag till din plats för att utföra en FRU eller en fullständig enhetsersättning.

## <a name="manage-a-support-request"></a>Hantera en supportbegäran

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Så här hanterar du dina supportförfrågningar

1. Om du vill komma till hjälp- och supportsidan navigerar du till **Bläddra > hjälp + support**.

    ![Hantera supportförfrågningar](./media/data-box-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)   

2. En tabelllista över **Senaste supportbegäranden** visas i **hjälp + support**.

    <!--[Manage support requests](./media/data-box-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Markera och klicka på en supportbegäran. Du kan visa status och information för den här begäran. Klicka på **+ Nytt meddelande** om du vill följa upp den här begäran.

   
## <a name="next-steps"></a>Nästa steg

Läs om hur du [felsöker problem relaterade till Data Box Edge](data-box-edge-troubleshoot.md).
Läs om hur du [felsöker problem relaterade till Data Box Gateway](data-box-gateway-troubleshoot.md).
