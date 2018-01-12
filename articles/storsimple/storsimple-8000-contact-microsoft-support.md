---
title: "Skapa supportärende eller fallet för StorSimple 8000-serien | Microsoft Docs"
description: "Lär dig hur du loggar begäran och starta en session med stöd på enheten StorSimple 8000-serien."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: e0df86e40d0dc1092ad7ff04f01bbc3e5e3d1c4e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="contact-microsoft-support"></a>Kontakta Microsofts support

Enhetshanteraren StorSimple ger möjlighet att **logga en ny supportförfrågan** inom tjänsten sammanfattning bladet. Om det uppstår problem med din StorSimple-lösning kan du skapa en tjänstbegäran för teknisk support. I en online-session med supportpersonalen, kan du också behöva starta en session med stöd på StorSimple-enheten. Den här artikeln vägleder dig genom:

* Så här skapar du en supportbegäran.
* Hur du hanterar en begäran om livscykeln för support från i portalen.
* Hur du startar en supportsession i Windows PowerShell-gränssnittet för din StorSimple-enhet.

Granska de [StorSimple 8000-serien stöd SLA och information](https://msdn.microsoft.com/library/mt433077.aspx) innan du skapar en supportbegäran.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Beroende på din [supportavtal](https://azure.microsoft.com/support/plans/), du kan skapa supportärenden på ett problem på din StorSimple-enhet direkt från bladet StorSimple Enhetshanteraren service sammanfattning. Utför följande steg för att skapa en supportförfrågan:

#### <a name="to-create-a-support-request"></a>Så här skapar du en supportbegäran

1. Gå till StorSimple Device Manager-tjänsten. Inställningar för tjänsten sammanfattning bladet, gå till **stöd + felsökning** avsnittet och klicka sedan på **ny supportbegäran**.
     
    ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. I den **ny supportbegäran** bladet väljer **grunderna**. I den **grunderna** bladet gör du följande:
   1. Från den **utfärda typ** listrutan, Välj **tekniska**.
   2. Aktuellt **prenumeration**, **Service** typ, och **resurs** (StorSimple Device Manager service) väljs automatiskt. 
   3. Välj en **supportavtal** från den nedrullningsbara listan om du har flera planer som är associerad med din prenumeration. Du behöver ett betalt supportavtal för att aktivera teknisk Support.
   4. Klicka på **Nästa**.

       ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. I den **ny supportbegäran** bladet väljer **steg 2 problemet**. I den **problemet** bladet gör du följande:
    
    1. Välj den **allvarlighetsgrad**.
    2. Ange om problemet beror på enheten eller StorSimple enheten Manager-tjänsten.
    3. Välj en **kategori** för den här utfärda och ge fler **information** om problemet.
    4. Ange startdatum och tidpunkt för problemet.
    5. I den **filuppladdning**, klicka på mappikonen och bläddra till support-paketet.
    6. Kontrollera **dela diagnostikinformation**.
    7. Klicka på **Nästa**.

       ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. I den **ny supportbegäran** bladet, klickar du på **steg 3 kontaktinformation**. I den **kontaktinformation** bladet gör du följande:

    1. I den **Kontaktalternativ**, ange önskad kontaktmetod (telefon eller e-post) och språk. Svarstiden väljs automatiskt baserat på din prenumeration planen.
    2. Kontaktinformation, anger du ditt namn, e-post, valfritt kontakt, land. Välj den **spara kontaktändringar för framtida supportförfrågningar** kryssrutan.
    3. Klicka på **Skapa**.
   
        ![Kontakta supporten för MS via nya portalen](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Microsoft Support använder den här informationen för att nå ut till dig för ytterligare information, diagnostik och upplösning.
När du har skickat en begäran om en supporttekniker kommer att kontakta dig så snart som möjligt för att fortsätta med din begäran.

## <a name="manage-a-support-request"></a>Hantera en supportbegäran

När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen.

#### <a name="to-manage-your-support-requests"></a>Att hantera dina supportärenden

1. Gå till sidan Hjälp och support, gå till **Bläddra > hjälp + support**.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. En tabell lista över alla supportärenden visas i den **hjälp + support** bladet.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Välj och klicka på en supportbegäran. Du kan visa status och detaljer för denna begäran. Klicka på **+ nytt meddelande** om du vill följa upp denna begäran.

    ![Hantera supportförfrågningar](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starta en session med stöd i Windows PowerShell för StorSimple

Om du vill felsöka eventuella problem som kan uppstå med StorSimple-enhet behöver kommunicera med Microsoft Support-teamet. Microsoft Support kan behöva använda en session med stöd för att logga in på din enhet.

Utför följande steg för att starta en supportsession:

#### <a name="to-start-a-support-session"></a>Starta en supportsession

1. Komma åt enheten direkt via seriekonsolen eller via en telnet-session från en fjärrdator. Om du vill göra detta, följer du anvisningarna i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I sessionen som öppnas, trycker du på den **RETUR** för att få fram en kommandotolk.
3. Välj alternativ 1, i menyn för seriekonsolen **logga in med fullständig åtkomst**.
4. I Kommandotolken skriver du följande lösenord:
   
    `Password1`
5. Skriv följande kommando i Kommandotolken:
   
    `Enable-HcsSupportAccess`
6. En krypterad sträng visas för dig. Kopiera den här strängen i en textredigerare, till exempel Anteckningar.
7. Spara den här strängen och skicka den i ett e-postmeddelande till Microsoft Support.

> [!IMPORTANT]
> Du kan inaktivera stöd åtkomst genom att köra `Disable-HcsSupportAccess`. StorSimple-enheten försöker inaktivera stöd för åtkomst till 8 timmar när sessionen har startats. Det är en bra idé att ändra autentiseringsuppgifterna StorSimple-enhet när du initierar en supportsession.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [diagnostisera och lösa problem som rör enheten StorSimple 8000-serien](storsimple-8000-troubleshoot-deployment.md)
