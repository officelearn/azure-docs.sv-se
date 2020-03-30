---
title: Visa och hantera aviseringar för StorSimple Virtual Array
description: Beskriver lagringsvillkor för virtuell array och allvarlighetsgrad och hur du använder StorSimple Manager-tjänsten för att hantera aviseringar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7c469be4a1d8aba23857b1ba52ee829c126a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267435"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Använd StorSimple Enhetshanteraren för att hantera aviseringar för StorSimple Virtual Array

## <a name="overview"></a>Översikt

Varningsfunktionen i StorSimple Device Manager-tjänsten är ett sätt för dig att granska och rensa aviseringar relaterade till StorSimples virtuella matriser i realtid. Du kan använda aviseringarna på **sammanfattningsbladet För tjänsten** för att centralt övervaka hälsoproblemen för dina StorSimple-virtuella matriser och den övergripande Microsoft Azure StorSimple-lösningen.

I den här självstudien beskrivs hur du konfigurerar aviseringar, vanliga varningsvillkor, allvarlighetsgrad för aviseringar och hur du visar och spårar aviseringar. Dessutom innehåller den snabbreferenstabeller för aviseringar, vilket gör att du snabbt kan hitta en viss avisering och svara på rätt sätt.

![Sidan Aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurera varningsinställningar

Du kan välja om du vill meddelas via e-post om varningsvillkoren för var och en av dina StorSimple-virtuella matriser. Dessutom kan du identifiera andra mottagare av varningsmeddelanden genom att ange deras e-postadresser i rutan **Ytterligare e-postmottagare,** avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per virtuell matris.

När du har aktiverat e-postavisering för en virtuell matris får medlemmar i meddelandelistan ett e-postmeddelande varje gång en kritisk avisering inträffar. Meddelandena skickas från *storsimple-alerts-noreply\@mail.windowsazure.com* och kommer att beskriva varningsvillkoret. Mottagarna kan klicka på **Avsluta prenumeration** om du vill ta bort sig själva från e-postmeddelandet.

#### <a name="to-enable-email-notification-for-alerts"></a>Så här aktiverar du e-postmeddelanden för aviseringar

1. Gå till tjänsten StorSimple Device Manager och välj och klicka på **Enheter**i avsnittet **Hantering** . Välj och klicka på enheten i listan över enheter som visas.
   
    ![varningsinställningar](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Då öppnas bladet **Inställningar.** Välj **Allmänt**i avsnittet **Enhetsinställningar** . Då öppnas bladet **Allmänna inställningar.**
   
    ![meddelandekonfiguration för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. I bladet **Allmänna inställningar** går du till avsnittet **Varningsinställningar** och ställer in följande:
   
   1. Välj **JA**i fältet **Aktivera e-postmeddelande.**
   2. I fältet **E-posttjänstadministratörer** väljer du **JA** om du vill att tjänstadministratören och alla medadministratörer ska få aviseringarna.
   3. I fältet **Ytterligare e-postmottagare** anger du e-postadresserna till alla andra mottagare som ska ta emot varningsmeddelandena. Ange namn i det format *som någon\@somewhere.com*. Använd semikolon för att separera e-postadresserna. Du kan konfigurera högst 20 e-postadresser per virtuell enhet.
      
       ![meddelandekonfiguration för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Om du vill skicka ett testmeddelande via e-post klickar du på **Skicka testmeddelande**. Tjänsten StorSimple Device Manager visar statusmeddelanden när testmeddelandet vidarebefordras.
      
       ![E-postmeddelande med e-postmeddelande för aviseringar som skickats](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Om testmeddelandet inte kan skickas visas ett lämpligt meddelande i Tjänsten StorSimple Device Manager. Klicka på **OK,** vänta några minuter och försök sedan skicka testmeddelandet igen.
      >
      >
   5. Längst ned på sidan klickar du på **Spara** för att spara konfigurationen. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.
      
      ![E-postmeddelande med e-postmeddelande för aviseringar som skickats](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Vanliga varningsvillkor

Din StorSimple Virtual Array genererar aviseringar som svar på en mängd olika villkor. Följande är de vanligaste typerna av varningsvillkor:

* **Anslutningsproblem** – Dessa aviseringar inträffar när det är svårt att överföra data. Kommunikationsproblem kan uppstå under överföring av data till och från Azure-lagringskontot eller på grund av bristande anslutning mellan de virtuella enheterna och Tjänsten StorSimple Device Manager. Kommunikationsproblem är några av de svåraste att fixa eftersom det finns så många felpunkter. Du bör alltid först kontrollera att nätverksanslutning och Internet-åtkomst är tillgängliga innan du fortsätter till mer avancerad felsökning. Information om portar och brandväggsinställningar finns i [Systemkraven för StorSimple Virtual Array](storsimple-ova-system-requirements.md). Om du vill ha hjälp med felsökning går du till [Felsök med cmdleten Test-Connection](storsimple-troubleshoot-deployment.md).
* **Prestandaproblem** – Dessa aviseringar orsakas när systemet inte fungerar optimalt, till exempel när det är under en tung belastning.

Dessutom kan du se aviseringar relaterade till säkerhet, uppdateringar eller jobbfel.

## <a name="alert-severity-levels"></a>Allvarlighetsgrad för aviseringar

Aviseringar har olika allvarlighetsgrad, beroende på vilken inverkan aviseringssituationen kommer att ha och behovet av ett svar på aviseringen. Allvarlighetsgraderna är:

* **Kritisk** – Den här aviseringen är ett svar på ett tillstånd som påverkar systemets prestanda. Åtgärder krävs för att säkerställa att StorSimple-tjänsten inte avbryts.
* **Varning** – Detta tillstånd kan bli kritiskt om det inte löses. Du bör undersöka situationen och vidta alla åtgärder som krävs för att rensa problemet.
* **Information** – Den här aviseringen innehåller information som kan vara användbar för att spåra och hantera ditt system.

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Sammanfattningsbladet för Tjänsten StorSimple Device Manager ger dig en snabb överblick över antalet aviseringar på dina virtuella enheter, ordnade efter allvarlighetsgrad.

![Instrumentpanelen Aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klicka på allvarlighetsgraden för att öppna bladet **Aviseringar.** Resultaten innehåller endast aviseringar som matchar allvarlighetsgraden.

![Aviseringar rapport begränsad till aviseringstyp](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klicka på en avisering i listan för att få ytterligare information om aviseringen, inklusive den senaste gången aviseringen rapporterades, antalet förekomster av aviseringen på enheten och den rekommenderade åtgärden för att lösa aviseringen.

![Lista och information om varningar](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Du kan kopiera aviseringsinformationen till en textfil om du behöver skicka informationen till Microsoft Support. När du har följt rekommendationen och löst aviseringsvillkoret lokalt bör du rensa aviseringen från listan. Markera aviseringen i listan och klicka sedan på **Rensa**. Om du vill ta bort flera aviseringar markerar du varje avisering, klickar på en kolumn utom kolumnen **Avisering** och klickar sedan på **Rensa** när du har markerat alla aviseringar som ska rensas.

När du klickar på **Rensa**får du möjlighet att ge kommentarer om aviseringen och de åtgärder som du tog för att lösa problemet.

![varna kommentarer](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Vissa händelser rensas av systemet om en annan händelse utlöses med ny information.

## <a name="sort-and-review-alerts"></a>Sortera och granska aviseringar

**Bladet Varningar** kan visa upp till 250 varningar. Om du har överskridit antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält för att anpassa vilka aviseringar som visas:

* **Status** – Du kan visa antingen **aktiva** eller **rensade** aviseringar. Aktiva aviseringar utlöses fortfarande på datorn, medan rensade aviseringar har antingen rensats manuellt av en administratör eller programmässigt rensats eftersom systemet uppdaterade varningsvillkoret med ny information.
* **Allvarlighetsgrad** – Du kan visa aviseringar om alla allvarlighetsgrader (kritisk, varning, information) eller bara en viss allvarlighetsgrad, till exempel endast kritiska aviseringar.
* **Källa** – Du kan visa aviseringar från alla källor eller begränsa aviseringarna till de som kommer från antingen tjänsten eller en eller alla virtuella enheter.
* **Tidsintervall** – Genom att ange datum och tidsstämplar **från** **och till** kan du titta på aviseringar under den tidsperiod som du är intresserad av.

## <a name="alerts-quick-reference"></a>Snabbreferens för varningar

I följande tabeller visas några av de StorSimple-aviseringar som du kan stöta på, samt ytterligare information och rekommendationer där sådana finns tillgängliga. StorSimple Virtual Array-aviseringar kan delas in i någon av följande kategorier:

* [Aviseringar för molnanslutning](#cloud-connectivity-alerts)
* [Konfigurationsaviseringar](#configuration-alerts)
* [Aviseringar om jobbfel](#job-failure-alerts)
* [Prestandavarningar](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Aviseringar för molnanslutning

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enhetsnamnet <*enhetsnamn*> inte är anslutet till molnet. |Den namngivna enheten kan inte ansluta till molnet. |Det gick inte att ansluta till molnet. Detta kan bero på något av följande:<ul><li>Det kan vara problem med nätverksinställningarna på enheten.</li><li>Det kan vara problem med autentiseringsuppgifterna för lagringskontot.</li></ul>Mer information om felsökning av anslutningsproblem finns i [enhetens lokala webbgränssnitt.](storsimple-ova-web-ui-admin.md) |

### <a name="configuration-alerts"></a>Konfigurationsaviseringar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lokal konfiguration av virtuella enheter stöds inte. |Långsam prestanda. |Den aktuella konfigurationen kan resultera i prestandaförsämring. Se till att servern uppfyller minimikraven för konfiguration. Mer information finns i [StorSimples krav för virtuell matris](storsimple-ova-system-requirements.md). |
| Du börjar få på etablerat diskutrymme på <*enhetsnamn*\>. |Varning för diskutrymme. |Du börjar få på etablerat diskutrymme. Om du vill frigöra utrymme bör du överväga att flytta arbetsbelastningar till en annan volym eller dela eller ta bort data. |

### <a name="job-failure-alerts"></a>Aviseringar om jobbfel

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att slutföra säkerhetskopieringen av <*enhetsnamnet.* \> |Fel på säkerhetskopieringsjobbet. |Det gick inte att skapa en säkerhetskopia. Tänk på något av följande:<ul><li>Anslutningsproblem kan hindra säkerhetskopieringen från att slutföras. Kontrollera att det inte finns några anslutningsproblem. Mer information om felsökning av anslutningsproblem finns i det [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för den virtuella enheten.</li><li>Du har nått den tillgängliga lagringsgränsen. Om du vill frigöra utrymme bör du överväga att ta bort eventuella säkerhetskopior som inte längre behövs.</li></ul> Lös problemen, avmarkera aviseringen och försök igen. |
| Klon av <*enhetsnamnet* \> kunde inte slutföras. |Klona jobbfel. |Det gick inte att skapa en klon. Tänk på något av följande:<ul><li>Din säkerhetskopia kanske inte är giltig. Uppdatera listan för att kontrollera att den fortfarande är giltig.</li><li>Anslutningsproblem kan hindra klonåtgärden från att slutföras. Kontrollera att det inte finns några anslutningsproblem.</li><li>Du har nått den tillgängliga lagringsgränsen. Om du vill frigöra utrymme bör du överväga att ta bort eventuella säkerhetskopior som inte längre behövs.</li></ul>Lös problemen, avmarkera aviseringen och försök igen. |

### <a name="networking-alerts"></a>Aviseringar om nätverk

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att ansluta till autentiseringstjänsten. |Datapath-fel |Url:en som används för att autentisera kan inte nås. Kontrollera att brandväggsreglerna innehåller de URL-mönster som angetts för StorSimple-enheten. Mer information om URL-mönster i Azure-portalen finns i [StorSimples nätverkskrav för virtual array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Prestandavarningar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Du upplever oväntade fördröjningar i dataöverföringen. |Långsam dataöverföring. |Begränsningsfel uppstår när du överskrider skalbarhetsmålen för en lagringstjänst. Lagringstjänsten gör detta för att säkerställa att ingen enskild klient eller klient kan använda tjänsten på bekostnad av andra. Mer information om felsökning av ditt Azure-lagringskonto finns i [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Det tar ont om lokalt reservationsdiskutrymme på <*enhetsnamn*\>. |Långsam svarstid. |10 % av den totala etableringsstorleken för <*enhetsnamn* \> är reserverad på den lokala enheten och du börjar nu få ont om det reserverade utrymmet. Arbetsbelastningen på <*enhetsnamn* \> genererar en högre omsättningshastighet eller så har du nyligen migrerat en stor mängd data. Detta kan resultera i försämrad prestanda. Tänk dig en av följande åtgärder för att lösa detta:<ul><li>Öka molnbandbredden till den här enheten.</li><li>Minska eller flytta arbetsbelastningar till en annan volym eller resurs.</li></ul> |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lösenordet för <*enhetsnamn* \> upphör att gälla <*antal* \> dagar. |Varning för lösenord. |Ditt lösenord upphör att gälla <*antal* \> dagar. Överväg att ändra ditt lösenord. Mer information finns i Ändra lösenordet för [StorSimple Virtual Array-enhetens administratör](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om StorSimple Virtual Array](storsimple-ova-overview.md).
