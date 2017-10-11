---
title: "Logga supportärende för StorSimple 8000-serien | Microsoft Docs"
description: "Lär dig mer om att skapa en supportbegäran och starta en session stöd på din StorSimple-enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cecc2566b432e897b5eff0c12e66598f0518ed80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="contact-microsoft-support-for-your-storsimple"></a>Kontakta Microsofts Support för din StorSimple
Om det uppstår problem med Microsoft Azure StorSimple-lösning kan du skapa en tjänstbegäran för teknisk support. I en online-session med supportpersonalen, kan du också behöva starta en session med stöd på StorSimple-enheten. Den här artikeln vägleder dig genom:

* Så här skapar du en supportbegäran.
* Hur du startar en supportsession i Windows PowerShell-gränssnittet för din StorSimple-enhet.

Granska de [StorSimple 8000-serien stöd SLA och information](https://msdn.microsoft.com/library/mt433077.aspx) innan du skapar en supportbegäran.

## <a name="create-a-support-request"></a>Skapa en supportförfrågan
Utför följande steg för att skapa en supportförfrågan:

#### <a name="to-create-a-support-request"></a>Så här skapar du en supportbegäran
1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/)i det övre högra hörnet, klickar du på namnet på ditt konto och klicka sedan på **kontakta Microsoft Support**.
   
    ![Kontakta MS supporten via ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. Du omdirigeras till den nya Azure-portalen (portal.azure.com). Klicka på den **ny supportbegäran** panelen.
   
    ![Kontakta supporten för MS via nya portalen](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    På höger sida av skärmen, den **ny supportbegäran** visas. 
   
    ![Nytt stöd för begäran i](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. I den **grunderna** dialogrutan Fyll följande:                                
   
   1. Från den **utfärda typ** listrutan, Välj **tekniska**.
   2. Välj en **prenumeration** från den nedrullningsbara listan.
   3. Från den **Service** listrutan, Välj **StorSimple**. 
   4. Välj en **supportavtal** från den nedrullningsbara listan. Du behöver ett betalt supportavtal för att aktivera teknisk Support.
4. Klicka på **Nästa**. Den **problemet** dialogrutan visas.
   
    ![Nytt stöd för begäran i](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. I den **problemet** dialogrutan Fyll följande:
   
   1. Välj en **allvarlighetsgrad** nivån från den nedrullningsbara listan.
   2. Välj en **problemtyp** från den nedrullningsbara listan.
   3. Välj en **kategori** från den nedrullningsbara listan. 
   4. I den **information** rutan, en kort beskrivning av problemet.
   5. I den **tidsram** rutan, ange datum, tid och tidszon som motsvarar den senaste förekomsten av problemet.
   6. Under **filuppladdning**, klicka på mappikonen och bläddra till support-paketet.
   7. Välj den **dela diagnostikinformation** kryssrutan.
6. Klicka på **Nästa**. Den **kontaktinformation** dialogrutan visas.
   
    ![Nytt stöd för begäran i](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. Ange din kontaktinformation och välj en kontaktmetod (telefon eller e-post). 
8. Välj den **spara kontaktändringar för framtida supportförfrågningar** kryssrutan.
9. Klicka på **Skapa**.

När du har skickat en begäran om en supporttekniker kommer att kontakta dig så snart som möjligt för att fortsätta med din begäran.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starta en session med stöd i Windows PowerShell för StorSimple
Om du vill felsöka eventuella problem som kan uppstå med StorSimple-enhet behöver kommunicera med Microsoft Support-teamet. Microsoft Support kan behöva använda en session med stöd för att logga in på din enhet. 

Utför följande steg för att starta en supportsession:

#### <a name="to-start-a-support-session"></a>Starta en supportsession
1. Komma åt enheten direkt via seriekonsolen eller via en telnet-session från en fjärrdator. Om du vill göra detta, följer du anvisningarna i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
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
> 
> 

