---
title: Visa och hantera Microsoft Azure StorSimple Virtual Array-aviseringar | Microsoft Docs
description: Beskriver StorSimple Virtual Array aviseringsvillkor och allvarlighetsgrad och hur du använder StorSimple Manager-tjänsten för att hantera aviseringar.
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
ms.openlocfilehash: bb6ef5a87c5610d90188471db961ef20dfb18835
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302813"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Använd StorSimple Device Manager för att hantera aviseringar för StorSimple Virtual Array

## <a name="overview"></a>Översikt

Funktionen för säkerhetsvarningar i StorSimple Device Manager-tjänsten gör det möjligt för dig att granska och ta bort aviseringar som rör StorSimple Virtual array på basis av i realtid. Du kan använda aviseringar på den **sammanfattning av tjänst** bladet för att centralt övervaka problem med hälsotillstånd för din StorSimple Virtual Array och den övergripande Microsoft Azure StorSimple-lösningen.

Den här självstudien beskrivs hur du konfigurerar aviseringar, vanliga aviseringsvillkor, nivåer för aviseringarnas allvarlighetsgrad och hur du visar och spåra aviseringar. Den innehåller dessutom avisering Snabbreferens tabeller, som gör det möjligt att snabbt hitta en specifik avisering och svara på rätt sätt.

![Sidan varningar](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

Du kan välja om du vill bli meddelad via e-post om aviseringsvillkoren för var och en av din StorSimple Virtual Array. Du kan dessutom identifiera andra aviseringsmottagare genom att ange deras e-postadresser i den **ytterligare e-postmottagare** box, avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per virtuell matris.

När du aktiverar e-postmeddelande för en virtuell matris kan får medlemmar i meddelandelistan över ett e-postmeddelande varje gång en kritisk varning inträffar. Meddelanden skickas från *storsimple-aviseringar-noreply\@mail.windowsazure.com* och beskriver aviseringstillståndet. Mottagare kan klicka på **Unsubscribe** att ta bort själva från listan över e-post-meddelande.

#### <a name="to-enable-email-notification-for-alerts"></a>Aktivera e-postmeddelande för aviseringar

1. Gå till din StorSimple Device Manager-tjänsten och i den **Management** markerar och klicka på **enheter**. Välj i listan med enheter som visas, och klicka på din enhet.
   
    ![aviseringsinställningar](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Gör det öppnas den **inställningar** bladet. I den **Enhetsinställningar** väljer **Allmänt**. Gör det öppnas den **allmänna inställningar** bladet.
   
    ![meddelandekonfigurationen för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. I den **allmänna inställningar** gå till bladet **varningsinställningar** avsnittet och ange följande:
   
   1. I den **Aktivera e-postmeddelanden** väljer **Ja**.
   2. I den **e-tjänstadministratörer** väljer **Ja** om du inte vill att tjänstadministratören och alla medadministratörer ska få aviseringar skickas.
   3. I den **ytterligare e-postmottagare** fältet, anger du e-postadresserna för alla andra mottagare som ska ta emot aviseringar skickas. Ange namn i formatet *någon\@somewhere.com*. Avgränsa e-postadresser med semikolon. Du kan konfigurera högst 20 e-postadresser per virtuell enhet.
      
       ![meddelandekonfigurationen för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Om du vill skicka ett testmeddelande för e-post, klickar du på **skicka e-posttestmeddelande**. StorSimple Device Manager-tjänsten visar statusmeddelanden som vidarebefordras av test-meddelande.
      
       ![Testa e-postmeddelande skickas för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Om inte går att skicka testmeddelandet, visas ett meddelande visas med StorSimple Device Manager-tjänsten. Klicka på **OK**, Vänta några minuter och försök sedan att skicka din testnotismeddelande igen.
      >
      >
   5. Längst ned på sidan klickar du på **spara** att spara din konfiguration. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.
      
      ![Testa e-postmeddelande skickas för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Vanliga aviseringsvillkor

StorSimple Virtual Array genererar varningar som svar på en mängd olika villkor. Följande är de vanligaste typerna av aviseringsvillkor:

* **Problem med nätverksanslutningen** – dessa aviseringar inträffar när det är svårt att överföra data. Kommunikationsproblem kan uppstå under överföring av data till och från Azure storage-kontot eller på grund av avsaknad av anslutning mellan virtuella enheter och StorSimple Device Manager-tjänsten. Kommunikationsproblem är några av de svåraste åtgärda eftersom det finns så många felpunkter. Du bör alltid först kontrollera att nätverksanslutningen och tillgång till Internet är tillgängliga innan du fortsätter till mer avancerad felsökning. Information om portar och brandväggsinställningar, går du till [systemkrav för StorSimple Virtual Array](storsimple-ova-system-requirements.md). För hjälp med felsökning kan du gå till [felsöka med cmdleten Test-Connection](storsimple-troubleshoot-deployment.md).
* **Prestandaproblem** – de här aviseringarna orsakas när datorn inte är presterar optimalt, till exempel när den är hårt belastat.

Du kan dessutom se aviseringar som rör säkerhet, uppdateringar eller misslyckade jobb.

## <a name="alert-severity-levels"></a>Nivåer för aviseringarnas allvarlighetsgrad

Aviseringar har olika allvarlighetsgrader, beroende på effekten som aviseringen situationen har och behovet av att ett svar på aviseringen. Allvarlighetsgraderna finns:

* **Kritiska** – den här aviseringen är reaktion på ett villkor som påverkar systemets lyckad prestanda. Åtgärd krävs för att se till att StorSimple tjänsten inte avbryts.
* **Varning** – det här tillståndet kan bli kritiska om inte matcha. Du bör undersöka situationen och vidta några åtgärder som krävs för att rensa problemet.
* **Information** – den här aviseringen innehåller information som kan vara användbara i spåra och hantera dina system.

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Sammanfattningsbladet för StorSimple Device Manager-tjänsten ger dig en snabb titt på hur många aviseringar på dina virtuella enheter, ordnade efter allvarlighetsgrad.

![Instrumentpanel för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klicka på vilken allvarlighetsgrad för att öppna den **aviseringar** bladet. Resultatet innehåller bara de aviseringar som matchar den allvarlighetsgraden.

![Aviseringsrapport för typ av avisering](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klicka på en avisering i listan för att få ytterligare information om aviseringen, inklusive den senaste gången aviseringen rapporterades antalet förekomster av avisering på enheten och den rekommenderade åtgärden för att lösa aviseringen.

![Aviseringslistan och information](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Du kan kopiera Aviseringsinformationen till en textfil, om du vill skicka information till Microsoft Support. När du har följt rekommendationerna och löst varningsvillkor lokala platser, bör du radera aviseringen från listan. Markera aviseringen i listan och klicka sedan på **Rensa**. Om du vill ta bort flera aviseringar, väljer varje avisering, klickar du på alla kolumner utom den **avisering** kolumnen och klicka sedan på **Rensa** när du har valt alla aviseringar rensas.

När du klickar på **Rensa**, du har möjlighet att ange kommentarer om aviseringen och de steg som du har gjort för att lösa problemet.

![aviseringen kommentarer](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Vissa händelser kommer att tas bort av systemet om en annan händelsen utlöses med den nya informationen.

## <a name="sort-and-review-alerts"></a>Sortera aktiviteter och aviseringar

Den **aviseringar** bladet kan visa upp till 250 aviseringar. Om du har överskridit det antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält om du vill anpassa vilka aviseringar visas:

* **Status för** – du kan visa antingen **Active** eller **avmarkerad** aviseringar. Aktiva aviseringar aktiveras fortfarande i systemet, medan avmarkerad aviseringar antingen har rensats manuellt av en administratör eller programmatiskt eftersom systemet uppdateras aviseringstillståndet med den nya informationen.
* **Allvarlighetsgrad** – du kan visa aviseringar för alla allvarlighetsgrader (kritisk, varning, information) eller bara en viss allvarlighetsgrad, till exempel endast kritiska aviseringar.
* **Källan** – du kan visa aviseringar från alla källor eller begränsa aviseringar till dem som kommer från tjänsten eller virtuella enheter.
* **Tidsintervall** – genom att ange den **från** och **till** datum och tidsstämplar, kan du titta på aviseringar under den tidsperiod som du är intresserad av.

## <a name="alerts-quick-reference"></a>Snabbreferens för aviseringar

I tabellerna nedan listas några av de StorSimple-aviseringar som kan uppstå, samt ytterligare information och rekommendationer där det är tillgängligt. StorSimple Virtual Array aviseringar indelas i följande kategorier:

* [Anslutningsvarningar i molnet](#cloud-connectivity-alerts)
* [Konfigurationsaviseringar](#configuration-alerts)
* [Varningar om jobbfel](#job-failure-alerts)
* [Aviseringar om programprestanda](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Anslutningsvarningar i molnet

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enhet <*enhetsnamn*> inte är ansluten till molnet. |Namngiven enhet kan inte ansluta till molnet. |Det gick inte att ansluta till molnet. Detta kan bero på något av följande:<ul><li>Det kan finnas ett problem med nätverksinställningarna på din enhet.</li><li>Det kan finnas ett problem med autentiseringsuppgifterna för lagringskontot.</li></ul>Mer information om hur du felsöker problem med nätverksanslutningen går du till den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för enheten. |

### <a name="configuration-alerts"></a>Konfigurationsaviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lokala virtuella enhetskonfigurationen stöds inte. |Långsamma prestanda. |Den aktuella konfigurationen kan resultera i försämrade prestanda. Kontrollera att din server uppfyller minimikraven för konfiguration. Mer information går du till [krav för StorSimple Virtual Array](storsimple-ova-system-requirements.md). |
| Du kör utanför det allokerade diskutrymmet på <*enhetsnamn*\>. |Disk varning. |Du slut på det allokerade diskutrymmet. Överväg att flytta arbetsbelastningar till en annan volym eller resurs eller ta bort data för att frigöra utrymme. |

### <a name="job-failure-alerts"></a>Varningar om jobbfel

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Säkerhetskopiering av <*enhetsnamn* \> gick inte att slutföra. |Säkerhetskopieringen misslyckas. |Det gick inte att skapa en säkerhetskopia. Överväg att något av följande:<ul><li>Problem med nätverksanslutningen kan göra att säkerhetskopieringen inte kan slutföras. Se till att det inte finns några anslutningsproblem. Mer information om hur du felsöker problem med nätverksanslutningen går du till den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för din virtuella enhet.</li><li>Du har nått gränsen för tillgänglig lagring. Ta bort eventuella säkerhetskopior som inte längre behövs för att frigöra utrymme.</li></ul> Lös problemen, radera aviseringen och försök igen. |
| Klon av <*enhetsnamn* \> gick inte att slutföra. |Klona jobbfel. |Det gick inte att skapa en klon. Överväg att något av följande:<ul><li>Säkerhetskopiera listan kanske inte är giltigt. Uppdatera listan för att kontrollera att den fortfarande är giltig.</li><li>Problem med nätverksanslutningen kan göra att klonåtgärden inte kan slutföras. Se till att det inte finns några anslutningsproblem.</li><li>Du har nått gränsen för tillgänglig lagring. Ta bort eventuella säkerhetskopior som inte längre behövs för att frigöra utrymme.</li></ul>Lös problemen, radera aviseringen och försök igen. |

### <a name="networking-alerts"></a>Aviseringar för nätverk

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att ansluta till Autentiseringstjänsten. |DataPath fel |Den URL som används för att autentisera kan inte nås. Se till att brandväggsreglerna omfattar URL-mönster som angetts för StorSimple-enheten. Mer information om URL-mönster i Azure-portalen går du till [nätverkskraven för StorSimple Virtual Array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Aviseringar om programprestanda

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Du upplever oväntade fördröjningar i dataöverföringen. |Långsam dataöverföring. |Begränsningsfel inträffa när du överskrider det för skalbarhetsmål för en lagringstjänst. Storage-tjänsten gör detta för att säkerställa att ingen enskild kund eller klient kan använda tjänsten på bekostnad av andra. Mer information om hur du felsöker ditt Azure storage-konto går du till [övervaka, diagnostisera och Felsök Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Du har snart slut på lokalt reserverat utrymme ledigt diskutrymme på <*enhetsnamn*\>. |Långsamma svarstider. |10% av den totala etablerade storleken för <*enhetsnamn* \> är reserverad på lokalt enheten och du har nu ont om reserverat minne. Arbetsbelastningen för <*enhetsnamn* \> genererar en högre andel omsättning eller du kanske har nyligen migrerat en stor mängd data. Detta kan resultera i nedsatt prestanda. Använd någon av följande åtgärder för att lösa problemet:<ul><li>Öka bandbredden molnet till den här enheten.</li><li>Minska eller flytta arbetsbelastningar till en annan volym eller resurs.</li></ul> |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lösenordet för <*enhetsnamn* \> går ut om <*nummer* \> dagar. |Varning om lösenord. |Lösenordet går ut om <*nummer* \> dagar. Överväg att ändra ditt lösenord. Mer information går du till [ändra administratörslösenordet för enheten StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om StorSimple Virtual Array](storsimple-ova-overview.md).
