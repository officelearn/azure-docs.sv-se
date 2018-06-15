---
title: Skapa supportärende eller fallet för Azure Import/Export jobb | Microsoft Docs
description: Lär dig logga supportförfrågan för problem relaterade till Import/Export-jobbet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2018
ms.author: alkohli;
ms.openlocfilehash: b82adae5854aa836701e299a0aabd2b61e73ca99
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30324112"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>Öppna ett supportärende för en Import/Export-jobb

Du kan skapa en tjänstbegäran för teknisk support om det uppstår problem med Import/Export-tjänsten. Den här artikeln vägleder dig genom:

* Så här skapar du en supportbegäran.
* Hur du hanterar en begäran om livscykeln för support från i portalen.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Utför följande steg för att skapa en supportförfrågan:

1. Gå till import-/ exportjobb. Gå till **stöd + felsökning** avsnittet och klicka sedan på **ny supportbegäran**.
     
    ![Grundläggande inställningar](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. I **ny supportbegäran**väljer **grunderna**. I **grunderna**, gör du följande:
    
    1. Från den **utfärda typ** listrutan, Välj **tekniska**.
    2. Välj din **prenumeration**.
    3. Under **Service**, kontrollera **My Services**. I listrutan kan du välja något av alternativen - **konto lagringshantering**, **Blob**, eller **filen**. 
        - Om du väljer **konto lagringshantering**väljer **resurs**, och **supportavtal**.
            ![Välj kontot lagringshantering](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - Om du väljer **Blob**väljer **resurs**, **behållarnamn** (valfritt) och **supportavtal**.
            ![Välj Blob](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - Om du väljer **filen**väljer **resurs**, **filresursnamn** (valfritt) och **supportavtal** ![Välj fil](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. Klicka på **Nästa**.

3. I **ny supportbegäran**väljer **steg 2 problemet**. I **problemet**, gör du följande:
    
    1. Välj den **allvarlighetsgrad** som **C - Minimal påverkan**. Stöd för uppdatera den vid behov.
    2. Välj den **problemtyp** som **datamigrering**.
    3. Välj den **kategori** som **importera - exportera**.
    4. Ange en **rubrik** för problemet med mera **information**.
    5. Ange startdatum och tidpunkt för problemet.
    6. I den **filuppladdning**, klicka på mappikonen för att bläddra bland andra filer som du vill överföra.
    7. Kontrollera **dela diagnostikinformation**.
    8. Klicka på **Nästa**.

       ![Problem](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. I **ny supportbegäran**, klickar du på **steg 3 kontaktinformation**. I **kontaktinformation**, gör du följande:

    1. I den **Kontaktalternativ**, ange önskad kontaktmetod (telefon eller e-post) och språk. Svarstiden väljs automatiskt baserat på din prenumeration planen.
    2. Kontaktinformation, anger du ditt namn, e-post, valfritt kontakt, land. Välj den **spara kontaktändringar för framtida supportförfrågningar** kryssrutan.
    3. Klicka på **Skapa**.
   
        ![Kontaktuppgifter](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

    Microsoft Support använder den här informationen för att nå ut till dig för ytterligare information, diagnostik och upplösning.
När du har skickat en begäran om en supporttekniker kommer att kontakta dig så snart som möjligt för att fortsätta med din begäran.

## <a name="manage-a-support-request"></a>Hantera en supportbegäran

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Att hantera dina supportärenden

1. Gå till sidan Hjälp och support, gå till **Bläddra > hjälp + support**.

    ![Hantera supportförfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. En tabell över **senaste stöder begäranden** visas i **hjälp + support**.

    ![Hantera supportförfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. Välj och klicka på en supportbegäran. Du kan visa status och detaljer för denna begäran. Klicka på **+ nytt meddelande** om du vill följa upp denna begäran.

    ![Hantera supportförfrågningar](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [felsöka problem med Import/Export service](storage-import-export-tool-troubleshooting-v1.md).
