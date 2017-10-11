---
title: Hantera StorSimple-styrenheter | Microsoft Docs
description: "Lär dig mer om att stoppa, starta om, stänga av eller återställa din StorSimple-styrenheter."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4ee989d0-956f-4c14-951e-fd4e490ea09d
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli
ms.openlocfilehash: 67dbb0c4066002256efbab6061157c641527e441
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>Hantera din StorSimple-styrenheter
## <a name="overview"></a>Översikt
Den här självstudiekursen beskriver de olika åtgärder som kan utföras på din StorSimple-styrenheter. Domänkontrollanter i din StorSimple-enhet är redundant (peer) domänkontrollanter i ett aktivt-passivt konfiguration. Endast en domänkontrollant är aktiv vid en given tidpunkt och bearbetar alla disk- och åtgärder. Den andra styrenheten är i passivt läge. Om den aktiva styrenheten misslyckas aktiveras passiva controller automatiskt.

Vägledningen innehåller stegvisa instruktioner för att hantera enheten domänkontrollanter med hjälp av den:

* **Domänkontrollanter** avsnitt i den **Underhåll** sidan i StorSimple Manager-tjänsten
* Windows PowerShell för StorSimple.

Vi rekommenderar att du hanterar styrenheter via StorSimple Manager-tjänsten. Om en åtgärd kan endast utföras med hjälp av Windows PowerShell för StorSimple, gör en del av kursen.

När du har läst den här självstudiekursen kommer du att kunna:

* Starta om eller stänga av en domänkontrollant för StorSimple-enhet
* Stänga av en StorSimple-enhet
* Återställa din StorSimple-enhet till fabriksinställningarna

## <a name="restart-or-shut-down-a-single-controller"></a>Starta om eller stänga av en enda domänkontrollant
En domänkontrollant omstart eller avstängning krävs inte som en del av normal drift. Avstängning åtgärder för en enskild enhet är vanliga endast i fall där en misslyckad enhet maskinvarukomponent behöver bytas ut. En omstart av domänkontrollanten kan också krävas i en situation där prestanda påverkas av omfattande minnesanvändning eller en felaktig styrenhet. Du kan också måste starta om en domänkontrollant efter en lyckad controller ersättning, om du vill aktivera och testa den ersatta styrenheten.

Starta om en enhet är inte störande för anslutna initierare, förutsatt att den passiva domänkontrollanten är tillgänglig. Om en passiv domänkontrollant inte är tillgänglig eller aktiverade av och sedan startar om den aktiva styrenheten orsaka avbrott i tjänsten och driftstopp.

> [!IMPORTANT]
> * **En domänkontrollant som körs ska aldrig tas fysiskt bort eftersom det skulle resultera i förlust av redundans och en ökad risk för driftstopp.**
> * Följande procedur gäller enbart för den fysiska StorSimple-enheten. Information om hur du starta, stoppa och starta om den virtuella enheten finns [arbeta med den virtuella enheten](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).
> 
> 

Du kan starta om eller stänga av en enskild enhet domänkontrollant med hjälp av den klassiska Azure-portalen på StorSimple Manager-tjänsten eller Windows PowerShell för StorSimple

Utför följande steg för att hantera din styrenheter från den klassiska Azure-portalen.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Starta om eller stänga av en domänkontrollant i den klassiska portalen
1. Gå till **enheter > Underhåll**.
2. Gå till **maskinvarustatus** och kontrollera att status för båda domänkontrollanterna på din enhet är **felfri**.
   
    ![Kontrollera StorSimple-styrenheter är felfria](./media/storsimple-manage-device-controller/IC766017.png)
3. Längst ned i den **Underhåll** klickar du på **hantera domänkontrollanter**.
   
    ![Hantera StorSimple-styrenheter](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > Om du inte ser **hantera domänkontrollanter**, måste du installera uppdateringar. Mer information finns i [uppdatera din StorSimple-enhet](storsimple-update-device.md).
   > 
   > 
4. I den **ändra inställningarna för domänkontrollanter** dialogrutan Gör följande:
   
   1. Från den **Select Controller** listrutan väljer du den domänkontrollant som du vill hantera. Alternativen är styrenhet 0 och 1. Dessa domänkontrollanter identifieras också aktiva eller passiva.
      
      > [!NOTE]
      > En domänkontrollant inte kan hanteras om det är inte tillgänglig eller aktiverade inaktiverat och visas inte i den nedrullningsbara listan.
      > 
      > 
   2. Från den **Välj åtgärd** listrutan Välj **omstart controller** eller **stänga av domänkontrollant**.
      
       ![Starta om passiva domänkontrollanten för StorSimple-enhet](./media/storsimple-manage-device-controller/IC766020.png)
   3. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-device-controller/IC740895.png).

Det här startar du om eller stänga av registeransvarige. Tabellen nedan sammanfattar information om vad som händer beroende på de val du har gjort i den **ändra inställningarna för domänkontrollanter** dialogrutan.  

| Val av # | Om du vill... | Detta sker. |
| --- | --- | --- |
| 1. |Starta om den passiva domänkontrollanten. |Ett jobb skapas för att starta om domänkontrollanten och du meddelas när jobbet har skapats. Detta initierar controller omstarten. Du kan övervaka omstarten genom att öppna **Service > instrumentpanelen > Visa åtgärdsloggar** och sedan filtrering av parametrar som är specifika för din tjänst. |
| 2. |Starta om den aktiva styrenheten. |Du ser följande varning: ”Om du startar om den aktiva styrenheten enheten växlar över till den passiva styrenheten. Vill du fortsätta ”? </br>Om du väljer att fortsätta med åtgärden nästa steg är identiska med de som används för att starta om den passiva domänkontrollanten (se markeringen 1). |
| 3. |Stäng av den passiva styrenheten. |Följande meddelande visas: ”när har avslutats, du behöver du trycker på strömknappen på styrenheten den aktiveras. Är du säker på att du vill stänga av den här domänkontrollanten ”? </br>Om du väljer att fortsätta med åtgärden nästa steg är identiska med de som används för att starta om den passiva domänkontrollanten (se markeringen 1). |
| 4. |Stäng av den aktiva styrenheten. |Följande meddelande visas: ”när har avslutats, du behöver du trycker på strömknappen på styrenheten den aktiveras. Är du säker på att du vill stänga av den här domänkontrollanten ”? </br>Om du väljer att fortsätta med åtgärden nästa steg är identiska med de som används för att starta om den passiva domänkontrollanten (se markeringen 1). |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Starta om eller stänga av en domänkontrollant i Windows PowerShell för StorSimple
Utför följande steg om du vill stänga av eller starta om en enda domänkontrollanten på StorSimple-enheten från den klassiska Azure-portalen.

1. Komma åt enheten via seriekonsolen eller en telnet-session från en fjärrdator. Ansluta till styrenhet 0 eller 1 för domänkontrollant genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
3. I Banderollmeddelandet, notera den domänkontrollant som du är ansluten till (styrenhet 0 eller 1-styrenhet) och om det är aktivt eller passiva (standby)-styrenheten.
   
   * Om du vill stänga av en enda domänkontrollant i Kommandotolken skriver du:
     
       `Stop-HcsController`
     
       Detta stängs den domänkontrollant som du är ansluten till. Om du avbryter den aktiva styrenheten, sedan växlar den över till den passiva styrenheten innan den stängs av.
   * Om du vill starta om en domänkontrollant i Kommandotolken skriver du:
     
       `Restart-HcsController`
     
       Den domänkontrollant som du är ansluten till kommer att startas om. Om du startar om den aktiva styrenheten, växlar den över till den passiva styrenheten före omstart.

## <a name="shut-down-a-storsimple-device"></a>Stänga av en StorSimple-enhet
Det här avsnittet beskrivs hur du stänga av en löpande eller en misslyckad StorSimple-enhet från en fjärrdator. En enhet är inaktiverat när du stänger av både styrenheter. En enhet avstängning görs när enheten fysiskt flyttas eller tas ur funktion.

> [!IMPORTANT]
> Kontrollera hälsotillståndet för enhetskomponenter innan du stänga av enheten. Gå till **enheter > Underhåll > maskinvarustatus** och kontrollera att Indikator status för alla komponenter är grön. Endast en felfri enhet har en grön status. Om enheten stängs ned till ersätter en felaktig komponent, visas en misslyckad (röd) eller en försämrad (gul) status för respektive komponenterna.
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>Stänga en StorSimple-enhet
1. Använd den [starta om eller stänga av en domänkontrollant](#restart-or-shut-down-a-single-controller) proceduren för att identifiera och stänga av den passiva styrenheten på enheten. Du kan utföra den här åtgärden i den klassiska Azure-portalen eller i Windows PowerShell för StorSimple.
2. Upprepa ovanstående steg för att stänga av den aktiva styrenheten.
3. Nu behöver du titta på det bakre planet för enheten. När två domänkontrollanter är helt avstängd, bör statusen indikatorer på båda domänkontrollanterna blinkande red. Om du behöver stänga av enheten helt just nu Vänd strömbrytare på både ström och kylning moduler (PCMs) till OFF-läge. Detta bör stänga av enheten.

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Återställa enheten till fabriksinställningarna
> [!IMPORTANT]
> Kontakta Microsoft Support om du behöver återställa enheten till fabriksinställningarna. Proceduren som beskrivs nedan bör endast användas tillsammans med Microsoft-supporten.
> 
> 

Den här proceduren beskriver hur du återställer din Microsoft Azure StorSimple-enhet till fabriksinställningarna använder Windows PowerShell för StorSimple.
När du återställer en enhet tar bort alla data och inställningar från hela klustret som standard.

Utför följande steg för att återställa din Microsoft Azure StorSimple-enhet till fabriksinställningarna:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Återställa enheten till standardinställningarna i Windows PowerShell för StorSimple
1. Komma åt enheten via dess seriekonsol. Kontrollera Banderollmeddelandet så att du är ansluten till den aktiva styrenheten.
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
3. I Kommandotolken skriver du följande kommando för att återställa hela klustret, ta bort alla data och metadata för domänkontrollanten inställningar:
   
    `Reset-HcsFactoryDefault`
   
    Om du vill återställa i stället en enda domänkontrollant måste du använda den [Återställ HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) med den `-scope` parameter.)
   
    Systemet startas om flera gånger. Du meddelas när återställningen har slutförts. Beroende på systemmodell, kan det ta 45 – 60 minuter för 8100-enhet och 60-90 minuter för en 8600 att slutföra den här processen.
   
   > [!TIP]
   > * Om du använder Update 1.2 eller tidigare använder den `–SkipFirmwareVersionCheck` parametern för att hoppa över versionskontroll firmware (annars ser du ett matchningsfel för inbyggd programvara: fabriksåterställning kan inte fortsätta på grund av ett matchningsfel för versionerna av inbyggd. ).
   > * Fabriken Återställ proceduren misslyckas för StorSimple-enheter som kör uppdatering 1 eller 1.1 i Government portal och har utfört en lyckad enkel eller dubbel domänkontrollant ersättning (med ersättning domänkontrollanter som levererades med programvara före uppdatering 1). Detta händer när de fabriksåterställa avbildningen verifieras om finns en SHA1-fil på en domänkontrollant som inte finns för före uppdateringen 1 programvara. Om du ser fabriksåterställa fel, kontakta Microsoft Support för att hjälpa dig med nästa steg. Det här problemet visas inte med ersättning domänkontrollanter som har levererats från fabriken med uppdatering 1 eller senare programvara.
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>Frågor och svar om hur du hanterar styrenheter
I det här avsnittet har vi sammanfattas några vanliga frågor om hantering StorSimple-styrenheter.

**F.** Vad händer om båda domänkontrollanterna på enheten är felfri och aktiverade på och jag startar om eller stänga av den aktiva styrenheten?

**S.** Om båda domänkontrollanterna på din enhet är felfri och aktiverade på, du uppmanas att bekräfta. Du kan välja att:

* **Starta om den aktiva styrenheten** – du uppmanas att starta om en domänkontrollant för en aktiv gör att enheten att växla över till den passiva styrenheten. Domänkontrollanten startar om.
* **Stänga av en domänkontrollant för en aktiv** – du meddelas att stänga av en domänkontrollant för active leder till avbrott. Du måste också trycker på strömknappen på enheten för att aktivera på domänkontrollanten.

**F.** Vad händer om den passiva styrenheten på enheten är inte tillgänglig eller aktiverade inaktiverat och jag startar om eller stänga av den aktiva styrenheten?

**S.** Om den passiva styrenheten på din enhet är tillgänglig eller så är aktiverade inaktiverat och du väljer att:

* **Starta om den aktiva styrenheten** – du meddelas att fortsätta den här åtgärden resulterar i ett tillfälligt avbrott i tjänsten och du uppmanas att bekräfta.
* **Stänga av en domänkontrollant för en aktiv** – du meddelas att fortsätta den här åtgärden resulterar i avbrottstid och att du behöver du trycker på strömknappen på en eller båda-domänkontrollanter för att aktivera enheten. Du uppmanas att bekräfta.

**F.** När har controller omstart eller avstängning kan inte gå vidare?

**S.** Starta om eller stänga av en domänkontrollant kan misslyckas om:

* En enhet uppdatering pågår.
* En omstart av domänkontrollanten pågår redan.
* En domänkontrollant avstängning pågår redan.

**F.** Hur kan du ta reda på om en domänkontrollant har startats om eller stänga av?

**S.** Du kan kontrollera status för domänkontrollanten på sidan underhåll. Status för domänkontrollanten indikerar om en domänkontrollant har startats om eller stänga av. Sidan aviseringar innehåller dessutom en informationsavisering om styrenheten har startats om eller stänga av. Domänkontrollanten startar om och stänga operations registreras också i loggarna för åtgärden. Mer information om åtgärdsloggar går du till [visa åtgärdsloggar](storsimple-service-dashboard.md#view-the-operations-logs).

**F.** Finns det någon inverkan på I/o på grund av domänkontrollant växling vid fel?

**S.** TCP-anslutningar mellan initierare och aktiva styrenheten kommer att återställas till följd av domänkontrollant växling vid fel, men återupprättas när den passiva styrenheten förutsätter åtgärden. Det kan finnas en tillfällig (mindre än 30 sekunder)-paus i i/o-aktivitet mellan initierare och enheten under den här åtgärden.

**F.** Hur återställer jag min controller till tjänsten när den stängs av och tas bort

**S.** Om du vill återställa en domänkontrollant till tjänsten måste du infoga den i chassit enligt beskrivningen i [ersätta en domänkontrollant modul på din StorSimple-enhet](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Nästa steg
* Om det uppstår problem med din StorSimple-styrenheter som du inte kan lösa med hjälp av anvisningarna som beskrivs i den här självstudiekursen [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).
* Mer information om hur du använder StorSimple Manager-tjänsten går du till [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

