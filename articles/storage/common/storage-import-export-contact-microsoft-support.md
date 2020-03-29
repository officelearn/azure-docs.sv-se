---
title: Skapa supportbiljett eller ärende för Azure Import/Export-jobb | Microsoft-dokument
description: Läs om hur du loggar supportbegäran för problem som är relaterade till ditt import-/exportjobb.
services: storsimple
author: alkohli
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 8361c32860d48b78c778ea5106ba04715fcc5f3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67621043"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>Öppna en supportbiljett för ett import-/exportjobb

Om du stöter på problem med din import-/exporttjänst kan du skapa en servicebegäran om teknisk support. I den här artikeln går du igenom:

* Så här skapar du en supportbegäran.
* Så här hanterar du en livscykel för supportbegäran inifrån portalen.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Gör följande för att skapa en supportbegäran:

1. Gå till ditt import-/exportjobb. Navigera till **avsnittet SUPPORT + FELSÖKNING** och klicka sedan på Ny **supportbegäran**.
     
    ![Grundläggande inställningar](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. I **Ny supportbegäran**väljer du **Grunderna**. Gör följande i **Basics:**
    
    1. Välj **Issue type** **Teknisk**.
    2. Välj din **prenumeration**.
    3. Kontrollera Mina **tjänster under** **Service**. I listrutan kan du välja ett av alternativen - **Storage Account Management**, **Blob**eller **Arkiv**. 
        - Om du väljer **Hantering av lagringskonto**väljer du **Resurs**och **Supportplan**.
            ![Välj Hantering av lagringskonto](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - Om du väljer **Blob**väljer du **Resurs,** **Behållarnamn** (valfritt) och **Supportplan**.
            ![Välj Blob](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - Om du väljer **Arkiv**väljer du **Resurs,** **Filresursnamn** (valfritt) och **Supportplan** ![Välj fil](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. Klicka på **Nästa**.

3. I **Ny supportbegäran**väljer du **Steg 2-problem**. Gör följande i **Problem:**
    
    1. Välj **allvarlighetsgrad** som **C - Minimal påverkan**. Supporten uppdateras om det behövs.
    2. Välj **problemtyp** som **datamigrering**.
    3. Välj **kategorin** som **import - exportera**.
    4. Ange en **rubrik** för problemet och mer **information**.
    5. Ange startdatum och starttid för problemet.
    6. Klicka på mappikonen i **filöverföringen**för att bläddra bland andra filer som du vill ladda upp.
    7. Kontrollera **Dela diagnostikinformation**.
    8. Klicka på **Nästa**.

       ![Problem](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. Klicka på Steg **3 Kontaktinformation** **i Ny supportbegäran**. Gör följande i **Kontaktinformation:**

   1. I **kontaktalternativen**anger du önskad kontaktmetod (telefon eller e-post) och språket. Svarstiden väljs automatiskt baserat på din prenumerationsplan.
   2. Ange namn, e-post, valfri kontakt, land/region i kontaktinformationen. Markera kryssrutan **Spara kontaktändringar för framtida supportbegäranden.**
   3. Klicka på **Skapa**.
   
       ![Kontaktinformation](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

      Microsoft Support använder den här informationen för att kontakta dig för ytterligare information, diagnos och lösning.
      När du har skickat in din begäran kontaktar en supporttekniker dig så snart som möjligt för att gå vidare med din begäran.

## <a name="manage-a-support-request"></a>Hantera en supportbegäran

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Så här hanterar du dina supportförfrågningar

1. Om du vill komma till hjälp- och supportsidan navigerar du till **Bläddra > hjälp + support**.

    ![Hantera supportförfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. En tabelllista över **Senaste supportbegäranden** visas i **hjälp + support**.

    ![Hantera supportförfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. Markera och klicka på en supportbegäran. Du kan visa status och information för den här begäran. Klicka på **+ Nytt meddelande** om du vill följa upp den här begäran.

    ![Hantera supportförfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>Nästa steg

Läs om hur du [felsöker problem relaterade till tjänsten Import/Export](storage-import-export-tool-troubleshooting-v1.md).
