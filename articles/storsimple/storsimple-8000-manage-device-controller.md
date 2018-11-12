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
ms.openlocfilehash: 5e461f340e1c58f64c6d645a1e47cfd811bc4de5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261714"
---
# <a name="manage-your-storsimple-device-controllers"></a>Hantera din StorSimple-styrenheter

## <a name="overview"></a>Översikt

Den här självstudien beskrivs de olika åtgärder som kan utföras på din StorSimple-styrenheter. Domänkontrollanter i din StorSimple-enhet är redundant (peer) domänkontrollanter i en aktiv-passiv konfiguration. Vid en given tidpunkt, endast en domänkontrollant är aktiv och bearbetar alla disk- och åtgärder. Den andra styrenheten är i passivt läge. Om den aktiva kontrollenheten misslyckas, blir automatiskt den passiva styrenheten aktivt.

Den här självstudien innehåller stegvisa instruktioner för att hantera styrenheterna genom att använda den:

* **Domänkontrollanter** bladet för din enhet i StorSimple Device Manager-tjänsten.
* Windows PowerShell för StorSimple.

Vi rekommenderar att du hanterar styrenheterna via StorSimple Device Manager-tjänsten. Om en åtgärd kan endast utföras med hjälp av Windows PowerShell för StorSimple, gör en del av självstudien.

När du har läst den här självstudiekursen kommer du att kunna:

* Starta om eller stänga av en StorSimple-enhetens styrenhet
* Stänga av en StorSimple-enhet
* Återställa din StorSimple-enhet till fabriksinställningarna

## <a name="restart-or-shut-down-a-single-controller"></a>Starta om eller stänga av en enskild domänkontrollant
En domänkontrollant omstart eller avstängning krävs inte som en del av normal drift. Stäng av åtgärder för en enskild enhet kontrollant är vanliga endast i fall där en maskinvarukomponent misslyckade enheten behöver bytas ut. En kontrollantomstart kan också krävas i en situation där prestanda påverkas av omfattande minnesanvändning eller en felaktig styrenhet. Du kan också behöva starta om en domänkontrollant efter en lyckad controller ersättning om du vill aktivera och testa den ersatta styrenheten.

Starta om en enhet är inte söndrande för anslutna initierare, förutsatt att den passiva kontrollenheten är tillgängliga. Om en passiva kontrollenheten inte är tillgängligt eller stängs av och sedan starta om den aktiva kontrollenheten kan leda till avbrott i tjänsten och driftstopp.

> [!IMPORTANT]
> * **En aktiv styrenhet bör aldrig vara fysiskt bort eftersom detta skulle resultera i redundansförlust och en ökad risk för driftstopp.**
> * Följande procedur gäller bara för den fysiska StorSimple-enheten. Information om hur du starta, stoppa och starta om StorSimple-Molninstallationen finns i [arbetar med molninstallationen](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Du kan starta om eller stänga av en enskild enhet kontrollenheten via Azure-portalen för StorSimple Device Manager-tjänsten eller Windows PowerShell för StorSimple.

Utför följande steg för att hantera dina styrenheter från Azure-portalen.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Att starta om eller stänga av en domänkontrollant i Azure-portalen
1. I din StorSimple Device Manager-tjänst går du till **enheter**. Välj enheten i listan över enheter. 

    ![Välj en enhet](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Gå till **Inställningar > styrenheter**.
   
    ![Kontrollera StorSimple styrenheterna är felfria](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. I den **domänkontrollanter** bladet, kontrollera att statusen för båda styrenheterna på din enhet är **felfri**. Välj en domänkontrollant, högerklicka och välj sedan **starta om** eller **stänga**.

    ![Välj Starta om eller stänga av StorSimple-styrenheter](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Ett jobb skapas för att starta om eller stänga av kontrollanten och visas med tillämpliga varningar om några. För att övervaka omstart eller avstängning, gå till **Service > aktivitetsloggar** och sedan filtrera efter parametrar som är specifika för din tjänst. Om en kontrollant stängdes, måste du trycker på strömknappen för att aktivera på styrenheten för att aktivera den.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Att starta om eller stänga av en domänkontrollant i Windows PowerShell för StorSimple
Utför följande steg för att stänga av eller starta om en enskild domänkontrollant på StorSimple-enheten från Windows PowerShell för StorSimple.

1. Komma åt enheten via seriekonsolen eller en telnet-session från en fjärrdator. Om du vill ansluta till kontrollenhet 0 eller kontrollenhet 1, följer du stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
3. I Banderollmeddelandet, notera den domänkontrollant som du är ansluten till (kontrollenhet 0 eller kontrollenhet 1) och om det är aktivt eller passiva (standby) styrenheten.
   
   * Om du vill stänga av en enskild domänkontrollant i Kommandotolken skriver du:
     
       `Stop-HcsController`
     
       Detta stänger av den styrenhet som du är ansluten till. Om du stoppar den aktiva kontrollenheten flyttas enheten över till den passiva styrenheten.

   * Om du vill starta om en domänkontrollant i Kommandotolken skriver du:
     
       `Restart-HcsController`
     
       Detta startar om den styrenhet som du är ansluten till. Om du startar om den aktiva styrenheten, växlar den över till den passiva styrenheten före omstart.

## <a name="shut-down-a-storsimple-device"></a>Stänga av en StorSimple-enhet

Det här avsnittet beskrivs hur du stänger en löpande eller en misslyckad StorSimple-enhet från en fjärrdator. En enhet är avstängd när båda styrenheterna är stängt. En enhet avstängning görs när enheten är fysiskt flyttas eller tas bort från tjänsten.

> [!IMPORTANT]
> Kontrollera hälsotillståndet för enhetskomponenterna innan du stänger av enheten. Gå till din enhet och klicka sedan på **Inställningar > hälsotillstånd för maskinvara**. I den **Status och hälsotillstånd för maskinvara** bladet, kontrollera att LED status för alla komponenter är grönt. Endast en felfri enhet har en grön status. Om enheten har stängts ned till ersätter en felaktig komponent, visas en misslyckad (röd) eller ett degraderat (gul) status för respektive komponenterna.


#### <a name="to-shut-down-a-storsimple-device"></a>Att stänga av en StorSimple-enhet

1. Använd den [starta om eller stänga av en domänkontrollant](#restart-or-shut-down-a-single-controller) procedur för att identifiera och stänga av den passiva styrenheten på din enhet. Du kan utföra den här åtgärden i Azure portal eller i Windows PowerShell för StorSimple.
2. Upprepa ovanstående steg för att stänga av den aktiva kontrollenheten.
3. Du måste nu titta på enheten tillbaka plan. När de två styrenheterna är helt stänga av, bör statusen led: ar på båda styrenheterna blinkande red. Om du vill stänga av enheten helt just nu ska vi gå strömbrytare på både ström och kylning moduler (PCMs) till OFF-läge. Detta bör inaktivera enheten.

## <a name="reset-the-device-to-factory-default-settings"></a>Återställa enheten till fabriksinställningarna

> [!IMPORTANT]
> Kontakta Microsoft Support om du behöver återställa enheten till fabriksinställningarna. Proceduren som beskrivs nedan bör endast användas tillsammans med Microsoft Support.

Den här proceduren beskriver hur du återställer en Microsoft Azure StorSimple-enhet till fabriksinställningarna med hjälp av Windows PowerShell för StorSimple.
När du återställer en enhet tar bort alla data och inställningar från hela klustret som standard.

Utför följande steg om du vill återställa Microsoft Azure StorSimple-enheten till fabriksinställningarna:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Återställa enheten till standardinställningarna i Windows PowerShell för StorSimple
1. Komma åt enheten via dess Seriell konsol. Kontrollera Banderollmeddelandet så att du är ansluten till den **Active** controller.
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
3. I Kommandotolken skriver du följande kommando för att återställa hela klustret, ta bort alla data, metadata och controller inställningar:
   
    `Reset-HcsFactoryDefault`
   
    Om du vill återställa en enskild domänkontrollant i stället, använda den [återställning HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) cmdlet med den `-scope` parameter.)
   
    Systemet startas om flera gånger. Du meddelas när återställningen är klar. Beroende på systemmodell, kan det ta 45 – 60 minuter under en 8100-enhet och 60 – 90 minuter för en 8600 att slutföra den här processen.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Frågor och svar om hur du hanterar styrenheter
I det här avsnittet har vi sammanfattas några av de vanligaste frågorna om Hantera StorSimple-styrenheter.

**F.** Vad händer om båda styrenheterna på enheten är felfria och aktiverade på och jag startar om eller stänger den aktiva kontrollenheten?

**S.** Om båda styrenheterna på enheten är felfria och aktiverade kan du uppmanas att bekräfta. Du kan välja att:

* **Starta om den aktiva kontrollenheten** – du får ett meddelande om att starta om en aktiv kontrollenhet orsakas enheten att växla över till den passiva styrenheten. Domänkontrollanten startas om.
* **Stänga av en aktiv kontrollenhet** – du får ett meddelande om att stänga av en aktiv kontrollenhet resulterar i driftstopp. Du måste också på strömknappen på enheten för att aktivera kontrollanten.

**F.** Vad händer om den passiva styrenheten på enheten är inte tillgänglig eller stängs av och jag startar om eller stänger den aktiva kontrollenheten?

**S.** Om den passiva styrenheten på din enhet är inte tillgänglig eller stängs av och du väljer att:

* **Starta om den aktiva kontrollenheten** – du får ett meddelande om att fortsätta den här åtgärden leder till ett tillfälligt avbrott i tjänsten och du uppmanas att bekräfta.
* **Stänga av en aktiv kontrollenhet** – du får ett meddelande som du kan fortsätta åtgärden resulterar i driftstopp. Du måste också på strömknappen på ena eller båda styrenheterna för att kunna aktivera enheten. Du uppmanas att bekräfta.

**F.** När har controller omstart eller avstängning misslyckas hängt?

**S.** Starta om eller stänga av en domänkontrollant kan misslyckas om:

* En enhetsuppdatering pågår.
* En kontrollantomstart pågår redan.
* En kontrollantavstängning pågår redan.

**F.** Hur kan du ta reda på om en domänkontrollant har startats om eller stänga av?

**S.** Du kan kontrollera status för domänkontrollanten på Controller-bladet. Status för domänkontrollanten anger om en kontrollant håller på att starta om eller stänga av. Dessutom kan den **aviseringar** bladet innehåller en informationsavisering om kontrollanten startats om eller stänga av. Domänkontrollanten startas om och stänga av åtgärderna registreras också i aktivitetsloggarna. Mer information om aktivitetsloggar går du till [visa aktivitetsloggar](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**F.** Finns det någon inverkan på i/o på grund av kontrollenhetsredundans?

**S.** TCP-anslutningar mellan initierare och aktiva kontrollenheten kommer att återställas på grund av kontrollenhetsredundans, men återupprättas när den passiva styrenheten förutsätter igen. Det kan finnas en tillfällig (mindre än 30 sekunder) paus i i/o-aktivitet mellan initierare och enheten under den här åtgärden.

**F.** Hur återställer jag min kontrollant till tjänsten när den har stänga av och tas bort

**S.** Om du vill returnera en domänkontrollant till tjänsten, du måste infoga dem i chassit enligt beskrivningen i [ersätta en controller-modulen på StorSimple-enheten](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Nästa steg
* Om du får problem med din StorSimple-styrenheter som du inte kan lösa med hjälp av anvisningarna som beskrivs i den här självstudien [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Mer information om hur du använder StorSimple Device Manager-tjänsten går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

