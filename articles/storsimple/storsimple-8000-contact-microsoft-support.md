---
title: Skapa supportärende eller fallet för StorSimple 8000-serien | Microsoft Docs
description: Lär dig mer om att logga supportbegäran och starta en supportsession på din enhet i StorSimple 8000-serien.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: fb8cfd4767f6bb9afe1b5731d3b4db1c68a73056
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362631"
---
# <a name="contact-microsoft-support"></a>Kontakta Microsofts support

StorSimple Device Manager ger möjlighet att **logga en ny supportbegäran** inom sammanfattningsbladet för tjänsten. Om du får problem med din StorSimple-lösning kan du skapa en tjänstbegäran för teknisk support. I en online-session med supportpersonalen, kan du också behöva starta en supportsession på StorSimple-enheten. Den här artikeln beskriver hur du:

* Så här skapar du en supportförfrågan.
* Så här hanterar du en supportlängd för begäran från i portalen.
* Så här att starta en supportsession i Windows PowerShell-gränssnittet för StorSimple-enheten.

Granska den [StorSimple 8000-serien Support serviceavtal och information](https://msdn.microsoft.com/library/mt433077.aspx) innan du skapar en supportbegäran.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Beroende på din [supportavtal](https://azure.microsoft.com/support/plans/), kan du skapa supportbegäranden för ett problem på din StorSimple-enhet direkt från sammanfattningsbladet för StorSimple Device Manager-tjänsten. Utför följande steg för att skapa en supportbegäran om:

#### <a name="to-create-a-support-request"></a>Skapa en supportbegäran

1. Gå till StorSimple Device Manager-tjänsten. I bladet sammanfattning tjänstinställningar går du till **stöd + felsökning** avsnittet och klicka sedan på **ny supportbegäran**.
     
    ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. I den **ny supportbegäran** bladet väljer **grunderna**. I den **grunderna** bladet gör följande:
   1. Från den **typ av problem** listrutan, väljer **teknisk**.
   2. Aktuellt **prenumeration**, **Service** typ, och **Resource** (StorSimple Device Manager-tjänsten) väljs automatiskt. 
   3. Välj en **supportavtal** från den nedrullningsbara listan om du har flera prenumerationer som är associerade med prenumerationen. Du behöver en plan för betald support för att teknisk Support.
   4. Klicka på **Nästa**.

       ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. I den **ny supportbegäran** bladet väljer **steg 2 problemet**. I den **problemet** bladet gör följande:
    
    1. Välj den **allvarlighetsgrad**.
    2. Ange om problemet är relaterat till installationen eller StorSimple Device Manager-tjänsten.
    3. Välj en **kategori** för den här utfärda och ger mer **information** om problemet.
    4. Ange startdatum och starttid för problemet.
    5. I den **filuppladdning**, klicka på mappikonen och bläddra till din supportpaket.
    6. Kontrollera **dela diagnostikinformation**.
    7. Klicka på **Nästa**.

       ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. I den **ny supportbegäran** bladet klickar du på **steg 3 kontaktinformation**. I den **kontaktinformation** bladet gör följande:

   1. I den **Kontaktalternativ**, ange önskad kontaktmetod (telefon eller e-post) och språk. Svarstiden väljs automatiskt baserat på ditt prenumerationsavtal.
   2. Ange ditt namn, e-post, valfritt kontakt, land i kontaktinformationen. Välj den **spara kontaktändringar för framtida supportförfrågningar** markerar du kryssrutan.
   3. Klicka på **Skapa**.
   
       ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft Support använder den här informationen för att kontakta dig för ytterligare information, diagnos och lösning.
      När du har skickat din begäran om kontaktar en supporttekniker dig så snart som möjligt för att fortsätta med din begäran.

## <a name="manage-a-support-request"></a>Hantera supportärenden

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Hantera dina supportärenden

1. Gå till sidan Hjälp och support genom att gå till **Bläddra > hjälp + support**.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. En listan med alla supportärenden visas i den **hjälp + support** bladet.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Välj och klicka på en supportförfrågan. Du kan visa status och information för den här begäran. Klicka på **+ nytt meddelande** om du vill följa upp den här begäran.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starta en supportsession i Windows PowerShell för StorSimple

För att felsöka eventuella problem som kan uppstå med StorSimple-enheten behöver kommunicera med Microsoft Support-teamet. Microsoft Support kan behöva använda en supportsession för att logga in på din enhet.

Utför följande steg för att starta en supportsession:

#### <a name="to-start-a-support-session"></a>Starta en supportsession

1. Få åtkomst till den direkt via seriekonsolen eller via en telnet-session från en fjärrdator. Om du vill göra detta, följer du stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I sessionen som öppnas, trycker du på den **RETUR** för att få fram en kommandotolk.
3. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
4. I Kommandotolken skriver du följande lösenord:
   
    `Password1`
5. Skriv följande kommando i prompten:
   
    `Enable-HcsSupportAccess`
6. En krypterad sträng visas för dig. Kopiera följande sträng i en textredigerare, till exempel Anteckningar.
7. Spara följande sträng och skicka den i ett e-postmeddelande till Microsoft Support.

> [!IMPORTANT]
> Du kan inaktivera tillgången till support genom att köra `Disable-HcsSupportAccess`. StorSimple-enheten försöker också inaktivera tillgången till support 8 timmar efter att sessionen initierades. Det är en bra idé att ändra din StorSimple-enhetens autentiseringsuppgifter när du initierar en supportsession.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [diagnosticera och lösa problem som rör din enhet i StorSimple 8000-serien](storsimple-8000-troubleshoot-deployment.md)
