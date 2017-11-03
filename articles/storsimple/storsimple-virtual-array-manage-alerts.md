---
title: Visa och hantera Microsoft Azure StorSimple virtuell matris aviseringar | Microsoft Docs
description: "Beskriver virtuella StorSimple-matris avisering villkor och allvarlighetsgrad och hur du använder StorSimple Manager-tjänsten för att hantera aviseringar."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3062c7c060c3730c5edef3ada16f3a5077cf5558
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Använd StorSimple enheten Manager för att hantera aviseringar för den virtuella StorSimple-matrisen

## <a name="overview"></a>Översikt

Funktionen aviseringar i StorSimple enheten Manager-tjänsten kan du granska och ta bort aviseringar som rör virtuella StorSimple-matriser på ett i realtid. Du kan använda aviseringar på den **sammanfattning av tjänst** bladet centralt övervaka hälsotillstånd problem för din virtuella StorSimple-matriser och den övergripande Microsoft Azure StorSimple-lösningen.

Den här självstudiekursen beskriver hur du konfigurerar aviseringar, vanliga avisering villkor, allvarlighetsgrad nivåer och hur du visar och spåra aviseringar. Dessutom innehåller aviseringen Snabbreferens tabeller, vilket gör att du snabbt hitta en specifik avisering och svara på lämpligt sätt.

![Sidan varningar](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

Du kan välja om du vill meddelas via e-post av aviseringen villkoren för var och en av din virtuella StorSimple-matriser. Dessutom kan du identifiera andra mottagare varningsmeddelanden genom att ange sina e-postadresser i den **ytterligare e-postmottagare** rutan, avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per virtuell matris.


När du aktiverar e-postmeddelanden för en virtuell matris får medlemmar i meddelandelistan över ett e-postmeddelande varje gång en kritisk varning inträffar. Meddelanden skickas från  *storsimple-alerts-noreply@mail.windowsazure.com*  och beskriver villkoret för aviseringen. Mottagarna kan klicka på **Unsubscribe** att ta bort själva från listan över e-postavisering.

#### <a name="to-enable-email-notification-for-alerts"></a>Så här aktiverar du e-postmeddelanden för aviseringar

1. Gå till Enhetshanteraren för StorSimple-tjänsten och i den **Management** markerar och klicka på **enheter**. Välj i listan över enheter som visas och klicka på enheten.
   
    ![aviseringsinställningar](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Detta öppnar den **inställningar** bladet. I den **Enhetsinställningar** väljer **allmänna**. Detta öppnar den **allmänna inställningar** bladet.
   
    ![Aviseringskonfiguration för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. I den **allmänna inställningar** gå till bladet **varningsinställningar** och ange följande:
   
   1. I den **Aktivera e-postmeddelanden** väljer **Ja**.
   2. I den **e-tjänstadministratörer** väljer **Ja** om du vill ha tjänstadministratören och medadministratörer för alla får aviseringar skickas.
   3. I den **ytterligare e-postmottagare** anger du de e-postadresserna för alla andra mottagare som får aviseringar skickas. Skriv namnen i formatet  *someone@somewhere.com* . Avgränsa e-postadresser med semikolon. Du kan konfigurera högst 20 e-postadresser per virtuell enhet.
      
       ![Aviseringskonfiguration för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Om du vill skicka ett testmeddelande för e-post, klickar du på **skicka testmeddelandet**. Tjänsten StorSimple Enhetshanteraren visar statusmeddelanden som vidarebefordrar testmeddelande.
      
       ![Aviseringar testa e-postmeddelande skickas](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Om testmeddelandet inte kan skickas visas StorSimple enheten Manager-tjänsten ett meddelande. Klicka på **OK**, Vänta några minuter och försök sedan att skicka din anmälan testmeddelande igen.
      > 
      > 
   5. Längst ned på sidan klickar du på **spara** att spara konfigurationen. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.
      
      ![Aviseringar testa e-postmeddelande skickas](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Vanliga avisering villkor

Din virtuella StorSimple-matris genererar aviseringar som svar på en mängd olika villkor. Följande är de vanligaste typerna av aviseringen villkor:

* **Problem med nätverksanslutningen** – dessa aviseringar inträffar när det är svårt att överföra data. Kommunikationsproblem kan uppstå under överföring av data till och från Azure storage-konto eller på grund av bristande anslutningen mellan virtuella enheter och StorSimple enheten Manager-tjänsten. Kommunikationsproblem är några av de svåraste åtgärda eftersom det finns så många felpunkter. Du bör alltid först kontrollera att nätverksanslutningen och Internet-åtkomst är tillgängliga innan du fortsätter in mer avancerad felsökning. För information om portar och brandväggsinställningar, gå till [virtuella StorSimple-matris systemkrav](storsimple-ova-system-requirements.md). För hjälp med felsökning kan du gå till [felsökning med cmdleten Test-Connection](storsimple-troubleshoot-deployment.md).
* **Prestandaproblem** – aviseringarna orsakas när systemet inte är presterar optimalt, till exempel när det är hårt belastat.

Dessutom kan du se aviseringar relaterade till säkerhet, uppdateringar eller jobbfel.

## <a name="alert-severity-levels"></a>Nivåer för aviseringarnas allvarlighetsgrad

Aviseringar har olika allvarlighetsgrader, beroende på inverkan som aviseringen situationen ska ha och behovet av att ett svar till aviseringen. Allvarlighetsgrader finns:

* **Kritiska** – den här aviseringen är som svar på ett villkor som påverkar systemets lyckade prestanda. Åtgärd krävs för att säkerställa att StorSimple tjänsten inte avbryts.
* **Varning** – det här tillståndet kan bli kritiska om inte matcha. Du bör undersöka situationen och vidta någon åtgärd krävs för att rensa problemet.
* **Information** – den här aviseringen innehåller information som kan vara användbar vid spåra och hantera datorn.

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Bladet StorSimple Enhetshanteraren service sammanfattning ger dig en snabb glimt antalet aviseringar på dina virtuella enheter, ordnade efter allvarlighetsgrad.

![Instrumentpanel för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klicka på allvarlighetsgrad för att öppna den **aviseringar** bladet. Resultatet innehåller bara de aviseringar som matchar den allvarlighetsgraden.

![Aviseringsrapporten omfattar aviseringstyp](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klicka på en avisering i listan för att få ytterligare information för aviseringen, inklusive den senaste gången aviseringen rapporterades antalet förekomster av avisering på enheten och den rekommenderade åtgärden för att lösa aviseringen.

![Aviseringslistan och information](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Du kan kopiera Aviseringsinformationen till en textfil, om du vill skicka information till Microsoft Support. När du har följt rekommendationen varningsvillkor lokal du bör ta bort aviseringen från listan. Markera aviseringen i listan och klicka sedan på **Rensa**. Om du vill rensa flera aviseringar, väljer du varje avisering klickar du på alla kolumner utom den **avisering** kolumnen och klicka sedan på **Rensa** när du har valt alla aviseringar som ska tömmas.

När du klickar på **Rensa**, du har möjlighet att ange kommentarer om aviseringen och de steg som du gjorde för att lösa problemet. 

![aviseringen kommentarer](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Kommer att tas bort vissa händelser i systemet om en annan händelse utlöses med ny information. 

## <a name="sort-and-review-alerts"></a>Sortera och granska aviseringar

Den **aviseringar** bladet kan visa upp till 250 aviseringar. Om du har överskridit det antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält om du vill anpassa vilka aviseringar visas:

* **Status för** – du kan visa antingen **Active** eller **avmarkerad** aviseringar. Aktiva aviseringar aktiveras fortfarande i systemet, medan avmarkerad aviseringar antingen har rensats manuellt av en administratör eller programmatiskt eftersom systemet uppdateras aviseringstillståndet med ny information.
* **Allvarlighetsgrad** – du kan visa aviseringar för alla allvarlighetsgrader (kritisk, varning, information) eller bara en viss allvarlighetsgrad, till exempel endast kritiska aviseringar.
* **Källan** – du kan visa aviseringar från alla källor eller begränsa aviseringar till dem som kommer från tjänsten eller virtuella enheter.
* **Tidsintervallet** – genom att ange den **från** och **till** datum och tidsstämplar du tittar på aviseringar under den tidsperiod som du är intresserad av.

## <a name="alerts-quick-reference"></a>Snabbreferens för aviseringar

I tabellerna nedan listas vissa av StorSimple-aviseringar som kan uppstå, samt ytterligare information och rekommendationer i förekommande fall. StorSimple virtuell matris aviseringar indelas i följande kategorier:

* [Molnet anslutningsvarningar](#cloud-connectivity-alerts)
* [Konfigurationsaviseringar](#configuration-alerts)
* [Jobbet aviseringar](#job-failure-alerts)
* [Prestandavarningar](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)
* [Uppdatera aviseringar](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Molnet anslutningsvarningar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enheten  *<device name>*  inte är anslutna till molnet. |Namngivna enheten kan inte ansluta till molnet. |Det gick inte att ansluta till molnet. Detta kan bero på något av följande:<ul><li>Det kan finnas problem med nätverksinställningarna på enheten.</li><li>Det kan finnas problem med autentiseringsuppgifterna för lagringskontot.</li></ul>Mer information om hur du felsöker problem med nätverksanslutningen går du till den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för enheten. |

### <a name="configuration-alerts"></a>Konfigurationsaviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lokala virtuella enhetskonfigurationen stöds inte. |Långsam prestanda. |Den aktuella konfigurationen kan resultera i försämrade prestanda. Kontrollera att servern uppfyller minimikraven för konfiguration. Mer information finns på [krav för virtuell StorSimple-matris](storsimple-ova-system-requirements.md). |
| Du kör allokerade diskutrymmet är slut på <*enhetsnamn*>. |Disk varning. |Du saknar etablerade diskutrymme. Överväg att flytta arbetsbelastningar till en annan volym eller resurs eller ta bort data för att frigöra utrymme. |

### <a name="job-failure-alerts"></a>Jobbet aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Säkerhetskopiering av <*enhetsnamn*> Det gick inte att slutföra. |Säkerhetskopieringsjobbet misslyckades. |Det gick inte att skapa en säkerhetskopia. Överväg att något av följande:<ul><li>Problem med nätverksanslutningen kan hindra att säkerhetskopieringen från en lyckad. Se till att det inte finns några anslutningsproblem. Mer information om hur du felsöker problem med nätverksanslutningen går du till den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för din virtuella enhet.</li><li>Tillgängligt lagringsutrymme gränsen har uppnåtts. Om du vill frigöra utrymme, Överväg att ta bort alla säkerhetskopieringar som inte längre behövs.</li></ul> Lös problemen, rensa aviseringen och försök igen. |
| Klona av <*enhetsnamn*> Det gick inte att slutföra. |Klona jobbet misslyckades. |Det gick inte att skapa en klon. Överväg att något av följande:<ul><li>Säkerhetskopiera listan kanske inte giltig. Uppdatera listan för att kontrollera att den fortfarande är giltig.</li><li>Problem med nätverksanslutningen kan hindra att kopieringen från en lyckad. Se till att det inte finns några anslutningsproblem.</li><li>Tillgängligt lagringsutrymme gränsen har uppnåtts. Om du vill frigöra utrymme, Överväg att ta bort alla säkerhetskopieringar som inte längre behövs.</li></ul>Lös problemen, rensa aviseringen och försök igen. |

### <a name="performance-alerts"></a>Prestandavarningar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Du fördröjs oväntat dataöverföring. |Långsam dataöverföring. |Bandbreddsbegränsning fel uppstår när du överskrider skalbarhetsmål för storage-tjänst. Lagringstjänsten gör detta för att säkerställa att ingen enskild klient eller innehavare kan använda tjänsten på bekostnad av andra. Mer information om hur du felsöker ditt Azure storage-konto går du till [övervaka, diagnostisera och felsöka Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Du har ont om lokala reservation ledigt diskutrymme på <*enhetsnamn*>. |Långsam svarstid. |10% av den totala allokerade storleken för <*enhetsnamn*> är reserverad på lokalt enheten och du har nu ont om reserverat minne. Arbetsbelastningen för <*enhetsnamn*> genererar ett större antal omsättning eller du kanske har nyligen har migreras stora mängder data. Detta kan medföra nedsatt prestanda. Använd någon av följande åtgärder för att lösa problemet:<ul><li>Öka molnet bandbredden på den här enheten.</li><li>Minska eller flytta arbetsbelastningar till en annan volym eller resurs.</li></ul> |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lösenordet för <*enhetsnamn*> upphör att gälla om <*nummer*> dagar. |Varning för lösenord. |Ditt lösenord upphör att gälla om < nummer < dagar. Överväg att ändra ditt lösenord. Mer information finns på [ändra StorSimple virtuell matris enhetens administratörslösenord](storsimple-virtual-array-change-device-admin-password.md). |

### <a name="update-alerts"></a>Uppdatera aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det finns nya uppdateringar för din enhet. |Uppdateringar för den virtuella StorSimple-matrisen är tillgängliga. |Du kan installera nya uppdateringar från den **Underhåll** sidan. |
| Kunde inte söka efter nya uppdateringar på <*enhetsnamn*>. |Uppdatera fel. |Ett fel uppstod när du installerar nya uppdateringar. Du kan installera uppdateringarna manuellt. Om problemet kvarstår kontaktar du [Microsoft-supporten](storsimple-contact-microsoft-support.md). |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om den virtuella StorSimple-matrisen](storsimple-ova-overview.md).

