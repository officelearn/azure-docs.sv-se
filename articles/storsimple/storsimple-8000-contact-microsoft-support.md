---
title: Skapa supportbiljett eller fodral för StorSimple 8000-serien
description: Läs om hur du loggar supportbegäran och startar en supportsession på din StorSimple 8000-serieenhet.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254994"
---
# <a name="contact-microsoft-support"></a>Kontakta Microsoft Support

StorSimple Device Manager ger möjlighet att **logga en ny supportbegäran** i servicesammanfattningsbladet. Om du stöter på problem med din StorSimple-lösning kan du skapa en servicebegäran om teknisk support. I en onlinesession med supportteknikern kan du också behöva starta en supportsession på Din StorSimple-enhet. I den här artikeln går du igenom:

* Så här skapar du en supportbegäran.
* Så här hanterar du en livscykel för supportbegäran inifrån portalen.
* Så här startar du en supportsession i Windows PowerShell-gränssnittet på Din StorSimple-enhet.

Granska [SLA-stöd- och information om StorSimple 8000-serien innan](https://msdn.microsoft.com/library/mt433077.aspx) du skapar en supportbegäran.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Beroende på [supportplanen](https://azure.microsoft.com/support/plans/)kan du skapa supportärenden för ett problem på Din StorSimple-enhet direkt från sammanfattningsbladet för StorSimple-tjänstens enhetshanteraren. Gör följande för att skapa en supportbegäran:

#### <a name="to-create-a-support-request"></a>Så här skapar du en supportbegäran

1. Gå till StorSimple Device Manager-tjänsten. Gå till avsnittet **SUPPORT + FELSÖKNING** i servicesammanfattningsbladsinställningarna och klicka sedan på Ny **supportbegäran**.
     
    ![Kontakta MS Support via ny portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. I bladet **Ny supportbegäran** väljer du **Grunderna**. Gör följande i bladet **Grunderna:**
   1. Välj **Teknisk**i listrutan **Ärendetyp** .
   2. Den aktuella **prenumerations-,** **tjänsttypen** och tjänsten **Resurs** (StorSimple Enhetshanteraren) väljs automatiskt. 
   3. Välj ett **supportabonnemang** i listrutan om du har flera abonnemang kopplade till din prenumeration. Du behöver en betald supportplan för att aktivera teknisk support.
   4. Klicka på **Nästa**.

       ![Kontakta MS Support via ny portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. I bladet **Ny supportbegäran** väljer du **Steg 2-problem**. Gör följande i bladet **Problem:**
    
    1. Välj **allvarlighetsgrad**.
    2. Ange om problemet är relaterat till enheten eller Tjänsten StorSimple Device Manager.
    3. Välj en **kategori** för det här problemet och ge mer **information** om problemet.
    4. Ange startdatum och starttid för problemet.
    5. I **filöverföringen**klickar du på mappikonen för att bläddra till supportpaketet.
    6. Kontrollera **Dela diagnostikinformation**.
    7. Klicka på **Nästa**.

       ![Kontakta MS Support via ny portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Klicka på **Steg 3 Kontaktinformation**i bladet **Ny supportbegäran** . Gör följande i bladet **Kontaktinformation:**

   1. I **kontaktalternativen**anger du önskad kontaktmetod (telefon eller e-post) och språket. Svarstiden väljs automatiskt baserat på din prenumerationsplan.
   2. Ange namn, e-post, valfri kontakt, land/region i kontaktinformationen. Markera kryssrutan **Spara kontaktändringar för framtida supportbegäranden.**
   3. Klicka på **Skapa**.
   
       ![Kontakta MS Support via ny portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft Support använder den här informationen för att kontakta dig för ytterligare information, diagnos och lösning.
      När du har skickat in din begäran kontaktar en supporttekniker dig så snart som möjligt för att gå vidare med din begäran.

## <a name="manage-a-support-request"></a>Hantera en supportbegäran

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Så här hanterar du dina supportförfrågningar

1. Om du vill komma till hjälp- och supportsidan navigerar du till **Bläddra > hjälp + support**.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. En tabelllista över Alla supportbegäranden visas i **supportbladet Hjälp +.**

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Markera och klicka på en supportbegäran. Du kan visa status och information för den här begäran. Klicka på **+ Nytt meddelande** om du vill följa upp den här begäran.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starta en supportsession i Windows PowerShell för StorSimple

Om du vill felsöka eventuella problem med StorSimple-enheten måste du samarbeta med Microsoft Support-teamet. Microsoft Support kan behöva använda en supportsession för att logga in på enheten.

Gör följande för att starta en supportsession:

#### <a name="to-start-a-support-session"></a>Så här startar du en supportsession

1. Öppna enheten direkt med hjälp av seriekonsolen eller via en telnet-session från en fjärrdator. Det gör du genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I sessionen som öppnas trycker du på **Retur** för att få en kommandotolk.
3. I menyn för seriell konsol väljer du alternativ 1, **Logga in med full åtkomst**.
4. Skriv följande lösenord vid prompten:
   
    `Password1`
5. Skriv följande kommando i prompten:
   
    `Enable-HcsSupportAccess`
6. En krypterad sträng kommer att presenteras för dig. Kopiera strängen till en textredigerare, till exempel Anteckningar.
7. Spara strängen och skicka den i ett e-postmeddelande till Microsoft Support.

> [!IMPORTANT]
> Du kan inaktivera supportåtkomst genom att köra `Disable-HcsSupportAccess`. StorSimple-enheten försöker också inaktivera supportåtkomst 8 timmar efter att sessionen initierades. Det är en bra idé att ändra dina StorSimple-enhetsautentiseringsuppgifter när du har initierat en supportsession.


## <a name="next-steps"></a>Nästa steg

Läs om hur du [diagnostiserar och löser problem relaterade till din StorSimple 8000-serieenhet](storsimple-8000-troubleshoot-deployment.md)
