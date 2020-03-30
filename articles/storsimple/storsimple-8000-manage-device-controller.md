---
title: Hantera enhetsstyrenheter i StorSimple 8000-serien | Microsoft-dokument
description: Läs om hur du stoppar, startar om, stänger av eller återställer StorSimple-enhetsstyrenheterna.
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
ms.openlocfilehash: ce49dcaa06288ba9e7a4d232338c727064d59685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267773"
---
# <a name="manage-your-storsimple-device-controllers"></a>Hantera dina StorSimple-enhetsstyrenheter

## <a name="overview"></a>Översikt

I den här självstudien beskrivs de olika åtgärder som kan utföras på dina StorSimple-enhetsstyrenheter. Styrenheterna i StorSimple-enheten är redundanta (peer)-styrenheter i en aktiv-passiv konfiguration. Vid en viss tidpunkt är endast en styrenhet aktiv och bearbetar alla disk- och nätverksåtgärder. Den andra styrenheten är i ett passivt läge. Om den aktiva styrenheten misslyckas aktiveras den passiva styrenheten automatiskt.

Den här självstudien innehåller steg-för-steg-instruktioner för att hantera enhetskontrollanterna med hjälp av:

* **Controllers-blad** för enheten i StorSimple Device Manager-tjänsten.
* Windows PowerShell för StorSimple.

Vi rekommenderar att du hanterar enhetskontrollanterna via StorSimple Device Manager-tjänsten. Om en åtgärd bara kan utföras med hjälp av Windows PowerShell för StorSimple, noterar självstudien den.

Efter att ha läst den här guiden kommer du att kunna:

* Starta om eller stänga av en StorSimple-enhetsstyrenhet
* Stänga av en StorSimple-enhet
* Återställa StorSimple-enheten till fabriksinställningar

## <a name="restart-or-shut-down-a-single-controller"></a>Starta om eller stänga av en enda styrenhet
En omstart eller avstängning av styrenheten krävs inte som en del av normal systemdrift. Avstängningsåtgärder för en enda enhetsstyrenhet är vanliga endast i fall där en misslyckad maskinvarukomponent kräver ersättning. En omstart av styrenheten kan också krävas i en situation där prestanda påverkas av överdriven minnesanvändning eller en felaktig styrenhet. Du kan också behöva starta om en styrenhet efter en lyckad handkontroll ersättning, om du vill aktivera och testa den utbytta styrenheten.

Omstart av en enhet stör inte anslutna initierare, förutsatt att den passiva styrenheten är tillgänglig. Om en passiv styrenhet inte är tillgänglig eller är inaktiverad kan omstart av den aktiva styrenheten leda till avbrott i tjänsten samt driftstopp.

> [!IMPORTANT]
> * **En löpkontrollenhet bör aldrig tas bort fysiskt eftersom detta skulle leda till förlust av redundans och en ökad risk för driftstopp.**
> * Följande procedur gäller endast för Den fysiska storsimpleenheten. Information om hur du startar, stoppar och startar om StorSimple Cloud Appliance finns i [Arbeta med molninstallationen](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Du kan starta om eller stänga av en enhetsstyrenhet via Azure-portalen för StorSimple Device Manager-tjänsten eller Windows PowerShell för StorSimple.

Om du vill hantera enhetsstyrenheterna från Azure-portalen utför du följande steg.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Så här startar du om eller stänger av en styrenhet i Azure Portal
1. Gå till **Enheter**i Tjänsten StorSimple Device Manager . Välj din enhet i listan över enheter. 

    ![Välj en enhet](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Gå till **Inställningar > Controllers**.
   
    ![Kontrollera att StorSimple-enhetsstyrenheter är felfria](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Kontrollera att statusen för båda handkontrollerna på enheten är **felfri**i **handkontrollens** blad . Välj en styrenhet, högerklicka och välj sedan **Starta om** eller **Stäng av**.

    ![Välj starta om eller stänga av StorSimple-enhetsstyrenheter](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Ett jobb skapas för att starta om eller stänga av styrenheten och du presenteras med eventuella varningar. Om du vill övervaka omstarten eller avstängningen går du till **Service > Aktivitetsloggar** och filtrerar sedan efter parametrar som är specifika för din tjänst. Om en handkontroll stängdes av måste du trycka på strömbrytaren för att slå på handkontrollen för att slå på den.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Så här startar du om eller stänger av en styrenhet i Windows PowerShell för StorSimple
Utför följande steg för att stänga av eller starta om en enda handkontroll på StorSimple-enheten från Windows PowerShell för StorSimple.

1. Öppna enheten via seriekonsolen eller en telnet-session från en fjärrdator. Om du vill ansluta till Controller 0 eller Controller 1 följer du stegen i [Använd PuTTY för att ansluta till enhetens seriella konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**.
3. I bannermeddelandet antecknar du den styrenhet som du är ansluten till (Controller 0 eller Controller 1) och om den är den aktiva eller den passiva (standby)-styrenheten.
   
   * Om du vill stänga av en enda styrenhet skriver du:
     
       `Stop-HcsController`
     
       Detta stänger av den handkontroll som du är ansluten till. Om du stoppar den aktiva handkontrollen växlar enheten över till den passiva handkontrollen.

   * Om du vill starta om en styrenhet skriver du:
     
       `Restart-HcsController`
     
       Detta startar om den styrenhet som du har anslutning till. Om du startar om den aktiva styrenheten växlar den över till den passiva styrenheten före omstarten.

## <a name="shut-down-a-storsimple-device"></a>Stänga av en StorSimple-enhet

I det här avsnittet beskrivs hur du stänger av en löp- eller en misslyckad StorSimple-enhet från en fjärrdator. En enhet stängs av när båda enhetskontrollanterna har stängts av. En enhetsavstängning görs när enheten flyttas fysiskt eller tas ur drift.

> [!IMPORTANT]
> Kontrollera enhetens hälsa innan du stänger av enheten. Navigera till enheten och klicka sedan på **Inställningar > maskinvaruhälsa**. Kontrollera att LED-statusen för alla komponenter är grön i **hälsobladet Status och maskinvara.** Endast en felfri enhet har en grön status. Om enheten stängs av för att ersätta en komponent som inte fungerar visas en misslyckad (röd) eller försämrad (gul) status för respektive komponent(er).


#### <a name="to-shut-down-a-storsimple-device"></a>Så här stänger du av en StorSimple-enhet

1. Använd [omstarten eller stäng av en styrenhetsprocedur](#restart-or-shut-down-a-single-controller) för att identifiera och stänga av den passiva styrenheten på enheten. Du kan utföra den här åtgärden i Azure-portalen eller i Windows PowerShell för StorSimple.
2. Upprepa ovanstående steg för att stänga av den aktiva styrenheten.
3. Du måste nu titta på enhetens bakre plan. När de två styrenheterna har stängts av helt ska statuslysdioderna på båda styrenheterna blinka rött. Om du behöver stänga av enheten helt just nu, vänd strömbryn på både ström- och kylmoduler (PCM) till off-läge. Detta bör stänga av enheten.

## <a name="reset-the-device-to-factory-default-settings"></a>Återställa enheten till fabriksinställningarna

> [!IMPORTANT]
> Om du behöver återställa enheten till fabriksinställningarna kontaktar du Microsoft Support. Proceduren som beskrivs nedan bör endast användas tillsammans med Microsoft Support.

Den här proceduren beskriver hur du återställer din Microsoft Azure StorSimple-enhet till fabriksinställningar med Windows PowerShell för StorSimple.
Om du återställer en enhet tas alla data och inställningar bort från hela klustret som standard.

Gör följande för att återställa microsoft Azure StorSimple-enheten till fabriksinställningarna:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Så här återställer du enheten till standardinställningarna i Windows PowerShell för StorSimple
1. Öppna enheten via seriekonsolen. Kontrollera banderollmeddelandet för att se till att du är ansluten till den **aktiva** handkontrollen.
2. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**.
3. Skriv följande kommando i prompten för att återställa hela klustret och ta bort alla inställningar för data, metadata och styrenhet:
   
    `Reset-HcsFactoryDefault`
   
    Om du i stället vill återställa en enda styrenhet använder du `-scope` cmdleten [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) med parametern.)
   
    Systemet startar om flera gånger. Du får ett meddelande när återställningen har slutförts. Beroende på systemmodellen kan det ta 45-60 minuter för en 8100-enhet och 60-90 minuter för en 8600 för att avsluta denna process.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Frågor och svar om hur du hanterar enhetsstyrenheter
I det här avsnittet har vi sammanfattat några av de vanliga frågorna om hantering av StorSimple-enhetsstyrenheter.

**F.** Vad händer om båda handkontrollerna på min enhet är felfria och påslagna och jag startar om eller stänger av den aktiva handkontrollen?

**A.** Om båda handkontrollerna på enheten är felfria och aktiverade uppmanas du att bekräfta det. Du kan välja att:

* **Starta om den aktiva styrenheten** – Du får ett meddelande om att omstarten av en aktiv styrenhet gjorde att enheten växlade över till den passiva styrenheten. Handkontrollen startar om.
* **Stäng av en aktiv styrenhet** – Du får ett meddelande om att om du stänger av en aktiv styrenhet blir det driftstopp. Du måste också trycka på strömbrytaren på enheten för att slå på handkontrollen.

**F.** Vad händer om den passiva handkontrollen på enheten inte är tillgänglig eller avstängd och jag startar om eller stänger av den aktiva handkontrollen?

**A.** Om den passiva handkontrollen på enheten inte är tillgänglig eller avstängd och du väljer att:

* **Starta om den aktiva styrenheten** – Du får ett meddelande om att en fortsatt åtgärd kommer att resultera i ett tillfälligt avbrott i tjänsten och du uppmanas att bekräfta.
* **Stäng av en aktiv styrenhet** – Du får ett meddelande om att om du fortsätter med åtgärden blir det driftstopp. Du måste också trycka på strömbrytaren på en eller båda handkontrollerna för att slå på enheten. Du uppmanas att bekräfta.

**F.** När går det inte att starta om eller stänga av handkontrollen?

**A.** Det kan misslyckas om du startar om eller stänger av en styrenhet om:

* En enhetsuppdatering pågår.
* En omstart av styrenheten pågår redan.
* En avstängning av styrenheten pågår redan.

**F.** Hur kan du ta reda på om en styrenhet startades om eller stängdes av?

**A.** Du kan kontrollera handkontrollens status på Controller-bladet. Styrenhetens status anger om en styrenhet håller på att starta om eller stängas av. Dessutom innehåller **bladet Aviseringar** en informationsavisering om styrenheten startas om eller stängs av. Styrenhetens omstarts- och avstängningsåtgärder registreras också i aktivitetsloggarna. Mer information om aktivitetsloggar finns [i Visa aktivitetsloggarna](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**F.** Finns det någon inverkan på I/O till följd av redundans för kontrollanten?

**A.** TCP-anslutningarna mellan initierare och aktiv styrenhet återställs som ett resultat av rederingskontroller, men återställs igen när den passiva styrenheten tar sig an drift. Det kan finnas en tillfällig (mindre än 30 sekunder) paus i I/O-aktivitet mellan initierare och enheten under den här åtgärden.

**F.** Hur återgår jag till min controller till service efter att den har stängts av och tagits bort?

**A.** Om du vill återställa en styrenhet till service måste du sätta in den i chassit enligt beskrivningen i [Ersätt en styrenhetsmodul på StorSimple-enheten](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Nästa steg
* Om du stöter på problem med storsimple-enhetsstyrenheten som du inte kan lösa med hjälp av procedurerna i den här självstudien [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Om du vill veta mer om hur du använder Tjänsten StorSimple Device Manager går du till [Använd Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

