---
title: Hantera styrenheter för StorSimple 8000-serien | Microsoft Docs
description: Lär dig mer om att stoppa, starta om, stänga av eller återställa din StorSimple-styrenheter.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875118"
---
# <a name="manage-your-storsimple-device-controllers"></a>Hantera din StorSimple-styrenheter

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver de olika åtgärder som kan utföras på din StorSimple-styrenheter. Domänkontrollanter i din StorSimple-enhet är redundant (peer) domänkontrollanter i ett aktivt-passivt konfiguration. Endast en domänkontrollant är aktiv vid en given tidpunkt och bearbetar alla disk- och åtgärder. Den andra styrenheten är i passivt läge. Om den aktiva styrenheten misslyckas aktiveras den passiva styrenheten automatiskt.

Vägledningen innehåller stegvisa instruktioner för att hantera enheten domänkontrollanter med hjälp av den:

* **Domänkontrollanter** bladet för din enhet i Enhetshanteraren för StorSimple-tjänsten.
* Windows PowerShell för StorSimple.

Vi rekommenderar att du hanterar styrenheter via tjänsten StorSimple Enhetshanteraren. Om en åtgärd kan endast utföras med hjälp av Windows PowerShell för StorSimple, gör en del av kursen.

När du har läst den här självstudiekursen kommer du att kunna:

* Starta om eller stänga av en domänkontrollant för StorSimple-enhet
* Stänga av en StorSimple-enhet
* Återställa din StorSimple-enhet till fabriksinställningarna

## <a name="restart-or-shut-down-a-single-controller"></a>Starta om eller stänga av en enda domänkontrollant
En domänkontrollant omstart eller avstängning krävs inte som en del av normal drift. Avstängning åtgärder för en enskild enhet är vanliga endast i fall där en misslyckad enhet maskinvarukomponent behöver bytas ut. En omstart av domänkontrollanten kan också krävas i en situation där prestanda påverkas av omfattande minnesanvändning eller en felaktig styrenhet. Du kan också måste starta om en domänkontrollant efter en lyckad controller ersättning, om du vill aktivera och testa den ersatta styrenheten.

Starta om en enhet är inte störande för anslutna initierare, förutsatt att den passiva domänkontrollanten är tillgänglig. Om en passiv domänkontrollant inte är tillgänglig eller aktiverade av och sedan startar om den aktiva styrenheten orsaka avbrott i tjänsten och driftstopp.

> [!IMPORTANT]
> * **En domänkontrollant som körs ska aldrig tas fysiskt bort eftersom det skulle resultera i förlust av redundans och en ökad risk för driftstopp.**
> * Följande procedur gäller enbart för den fysiska StorSimple-enheten. Information om hur du starta, stoppa och starta om StorSimple moln installation finns [arbeta med cloud anordningen](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Du kan starta om eller stänga av en enskild enhet domänkontrollant via Azure portal på StorSimple Enhetshanteraren tjänsten eller Windows PowerShell för StorSimple.

Utför följande steg för att hantera din styrenheter från Azure-portalen.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Starta om eller stänga av en domänkontrollant i Azure-portalen
1. I Enhetshanteraren för StorSimple-tjänsten går du till **enheter**. Välj enheten från listan över enheter. 

    ![Välj en enhet](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Gå till **Inställningar > styrenheter**.
   
    ![Kontrollera StorSimple-styrenheter är felfria](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. I den **domänkontrollanter** bladet, kontrollera att statusen för båda domänkontrollanterna på din enhet är **felfri**. Markera en domänkontrollant, högerklicka och välj sedan **starta om** eller **Stäng**.

    ![Välj Starta om eller stänga av StorSimple-styrenheter](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Ett jobb skapas för att starta om eller stänga av domänkontrollanten och visas med tillämpliga varningar, om sådana finns. Om du vill övervaka omstart eller avstängning, gå till **Service > aktivitetsloggar** och filtrera sedan händelseloggen efter parametrar som är specifika för din tjänst. Om en domänkontrollant stängdes måste trycka på strömknappen för att aktivera styrenheten för att aktivera den.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Starta om eller stänga av en domänkontrollant i Windows PowerShell för StorSimple
Utför följande steg om du vill stänga av eller starta om en enda domänkontrollanten på StorSimple-enheten från Windows PowerShell för StorSimple.

1. Komma åt enheten via seriekonsolen eller en telnet-session från en fjärrdator. Om du vill ansluta till styrenhet 0 eller 1-styrenhet, följer du stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
3. I Banderollmeddelandet, notera den domänkontrollant som du är ansluten till (styrenhet 0 eller 1-styrenhet) och om det är aktivt eller passiva (standby)-styrenheten.
   
   * Om du vill stänga av en enda domänkontrollant i Kommandotolken skriver du:
     
       `Stop-HcsController`
     
       Detta avslutar den domänkontrollant som du är ansluten till. Om du stoppar den aktiva styrenheten flyttas enheten över till den passiva styrenheten.

   * Om du vill starta om en domänkontrollant i Kommandotolken skriver du:
     
       `Restart-HcsController`
     
       Detta startar om den domänkontrollant som du är ansluten till. Om du startar om den aktiva styrenheten växlar den till den passiva styrenheten före omstart.

## <a name="shut-down-a-storsimple-device"></a>Stänga av en StorSimple-enhet

Det här avsnittet beskrivs hur du stänga av en löpande eller en misslyckad StorSimple-enhet från en fjärrdator. En enhet är avstängd när båda styrenheter är stängt. En enhet avstängning görs när enheten är fysiskt flyttas eller tas ur funktion.

> [!IMPORTANT]
> Kontrollera hälsotillståndet för enhetskomponenter innan du stänga av enheten. Navigera till din enhet och klicka sedan på **Inställningar > maskinvara hälsa**. I den **Status och maskinvara hälsa** bladet kontrollerar du att Indikator status för alla komponenter är grön. Endast en felfri enhet har en grön status. Om enheten stängs ned till ersätter en felaktig komponent, visas en misslyckad (röd) eller en försämrad (gul) status för respektive komponenterna.


#### <a name="to-shut-down-a-storsimple-device"></a>Stänga en StorSimple-enhet

1. Använd den [starta om eller stänga av en domänkontrollant](#restart-or-shut-down-a-single-controller) proceduren för att identifiera och stänga av den passiva styrenheten på enheten. Du kan utföra den här åtgärden i Azure-portalen eller i Windows PowerShell för StorSimple.
2. Upprepa ovanstående steg för att stänga av den aktiva styrenheten.
3. Du måste nu titta på det bakre planet för enheten. När två domänkontrollanter är helt avstängd, bör statusen indikatorer på båda domänkontrollanterna blinkande red. Om du behöver stänga av enheten helt just nu Vänd strömbrytare på både ström och kylning moduler (PCMs) till OFF-läge. Detta bör stänga av enheten.

## <a name="reset-the-device-to-factory-default-settings"></a>Återställa enheten till fabriksinställningarna

> [!IMPORTANT]
> Kontakta Microsoft Support om du behöver återställa enheten till fabriksinställningarna. Proceduren som beskrivs nedan bör endast användas tillsammans med Microsoft-supporten.

Den här proceduren beskriver hur du återställer din Microsoft Azure StorSimple-enhet till fabriksinställningarna använder Windows PowerShell för StorSimple.
När du återställer en enhet tar bort alla data och inställningar från hela klustret som standard.

Utför följande steg för att återställa din Microsoft Azure StorSimple-enhet till fabriksinställningarna:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Återställa enheten till standardinställningarna i Windows PowerShell för StorSimple
1. Komma åt enheten via dess seriekonsol. Kontrollera Banderollmeddelandet så att du är ansluten till den **Active** domänkontrollant.
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
3. I Kommandotolken skriver du följande kommando för att återställa hela klustret, ta bort alla data och metadata för domänkontrollanten inställningar:
   
    `Reset-HcsFactoryDefault`
   
    Om du vill återställa i stället en enda domänkontrollant måste du använda den [Återställ HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) med den `-scope` parameter.)
   
    Systemet startas om flera gånger. Du meddelas när återställningen har slutförts. Beroende på systemmodell, kan det ta 45 – 60 minuter för 8100-enhet och 60-90 minuter för en 8600 att slutföra den här processen.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Frågor och svar om hur du hanterar styrenheter
I det här avsnittet har vi sammanfattas några vanliga frågor om hantering StorSimple-styrenheter.

**F.** Vad händer om båda domänkontrollanterna på enheten är felfri och aktiverade på och jag startar om eller stänga av den aktiva styrenheten?

**S.** Om båda domänkontrollanterna på din enhet är felfri och aktiverade på, du uppmanas att bekräfta. Du kan välja att:

* **Starta om den aktiva styrenheten** – du meddelas att starta om en domänkontrollant för active orsakade enheten att växla över till den passiva styrenheten. Domänkontrollanten har startats om.
* **Stänga av en domänkontrollant för en aktiv** – du meddelas att stänga av en domänkontrollant för en aktiv resulterar i driftstopp. Du måste också trycker på strömknappen på enheten för att aktivera på domänkontrollanten.

**F.** Vad händer om den passiva styrenheten på enheten är inte tillgänglig eller aktiverade inaktiverat och jag startar om eller stänga av den aktiva styrenheten?

**S.** Om den passiva styrenheten på din enhet är tillgänglig eller så är aktiverade inaktiverat och du väljer att:

* **Starta om den aktiva styrenheten** – du meddelas att fortsätta den här åtgärden resulterar i ett tillfälligt avbrott i tjänsten och du uppmanas att bekräfta.
* **Stänga av en domänkontrollant för en aktiv** – du får ett meddelande som fortsätter åtgärden resulterar i driftstopp. Du måste också trycker på strömknappen på en eller båda-domänkontrollanter för att aktivera enheten. Du uppmanas att bekräfta.

**F.** När har controller omstart eller avstängning kan inte gå vidare?

**S.** Starta om eller stänga av en domänkontrollant kan misslyckas om:

* En enhet uppdatering pågår.
* En omstart av domänkontrollanten pågår redan.
* En domänkontrollant avstängning pågår redan.

**F.** Hur kan du ta reda på om en domänkontrollant har startats om eller stänga av?

**S.** Du kan kontrollera status för domänkontrollanten på bladet för domänkontrollanten. Status för domänkontrollanten indikerar om en domänkontrollant håller på att starta om eller stänga av. Dessutom kan den **aviseringar** bladet innehålla en informationsavisering om styrenheten startas om eller stänga av. Domänkontrollanten startar om och stänga operations registreras också i acitivity loggarna. Mer information om acitivity loggar går du till [visa aktivitetsloggarna](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**F.** Finns det någon inverkan på i/o på grund av domänkontrollant växling vid fel?

**S.** TCP-anslutningar mellan initierare och aktiva styrenheten kommer att återställas till följd av domänkontrollant växling vid fel, men återupprättas när den passiva styrenheten förutsätter åtgärden. Det kan finnas en tillfällig (mindre än 30 sekunder)-paus i i/o-aktivitet mellan initierare och enheten under den här åtgärden.

**F.** Hur återställer jag min controller till tjänsten när den stängs av och tas bort

**S.** Om du vill återställa en domänkontrollant till tjänsten måste du infoga den i chassit enligt beskrivningen i [ersätta en domänkontrollant modul på din StorSimple-enhet](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Nästa steg
* Om det uppstår problem med din StorSimple-styrenheter som du inte kan lösa med hjälp av anvisningarna som beskrivs i den här självstudiekursen [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Mer information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

