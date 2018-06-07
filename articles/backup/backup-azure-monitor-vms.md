---
title: Övervaka aviseringar om säkerhetskopiering för virtuella Azure-datorer
description: Övervaka händelser och aviseringar från virtuell dator i Azure säkerhetskopieringsjobb. Skicka e-post baserat på aviseringar.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 3783014738ec4e8f185531773b1259dc63e7f49f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606315"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Övervaka varningar vid säkerhetskopiering av virtuella Azure-datorer
Aviseringar är svar från tjänsten för att en händelse tröskelvärdet har uppnåtts eller överskridits. Att veta när problem start kan vara viktigt att som företag kostnaderna hålls nere. Aviseringar, vanligtvis sker inte enligt ett schema och därför är det bra att känna så snart som möjligt efter aviseringar. När en säkerhetskopiering eller återställning av jobbet misslyckas, till exempel visas en varning inom fem minuter till felet. I instrumentpanelen för valvet visas panelen Backup aviseringar kritiskt och varningsnivå händelser. Du kan visa alla händelser i inställningarna för säkerhetskopiering aviseringar. Men vad gör du om en varning visas när du arbetar på ett separat problem? Om du inte vet när aviseringen händer, kan det bero på en mindre besvär eller det kan äventyra data. Kontrollera att rätt personer är medvetna om en avisering - när det uppstår genom att konfigurera tjänsten för att skicka aviseringar via e-post. Mer information om hur du konfigurerar e-postaviseringar finns [konfigurera meddelanden](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Hur hittar jag information om aviseringar?
Om du vill visa information om den händelse som utlöste en avisering, måste du öppna avsnittet säkerhetskopiering aviseringar. Det finns två sätt att öppna avsnittet säkerhetskopiering aviseringar: antingen från säkerhetskopia aviseringar panelen i valvet instrumentpanelen eller från avsnittet aviseringar och händelser.

Öppna bladet säkerhetskopiering aviseringar från panelen Backup aviseringar:

* På den **säkerhetskopiering aviseringar** panelen på valvet instrumentpanelen klickar du på **kritisk** eller **varning** operativa händelser för den allvarlighetsgraden.

    ![Aviseringar om säkerhetskopiering panelen](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Öppna bladet säkerhetskopiering aviseringar från avsnittet aviseringar och händelser:

1. Från instrumentpanelen valvet klickar du på **alla inställningar**. ![Alla inställningar för](./media/backup-azure-monitor-vms/all-settings-button.png)
2. På den **inställningar** bladet, klickar du på **aviseringar och händelser**. ![Aviseringar och händelser](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. På den **aviseringar och händelser** bladet, klickar du på **säkerhetskopiering aviseringar**. ![Säkerhetskopiera aviseringar knappen](./media/backup-azure-monitor-vms/backup-alerts.png)

    Den **säkerhetskopiering aviseringar** avsnittet öppnas och visar de filtrerade varningarna.

    ![Aviseringar om säkerhetskopiering panelen](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Om du vill visa detaljerad information om en viss avisering i listan över händelser, klickar du på aviseringen för att öppna dess **information** avsnitt.

    ![Händelseinformation](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Om du vill anpassa de attribut som visas i listan finns [visa ytterligare händelse attribut](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Konfigurera meddelanden
 Du kan konfigurera tjänsten för att skicka e-postmeddelanden för aviseringar som uppstod under den senaste timmen eller när vissa typer av händelser inträffar.

Att ställa in e-postmeddelanden för aviseringar

1. Klicka på menyn säkerhetskopiering aviseringar **konfigurera meddelanden**

    ![Säkerhetskopiera aviseringar-menyn](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Avsnittet Konfigurera meddelanden öppnas.

    ![Konfigurera meddelanden bladet](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Klicka på avsnittet Konfigurera aviseringar för e-postmeddelanden **på**.

    Mottagarna och allvarlighetsgrad dialogrutor har en stjärna bredvid dem, eftersom denna information krävs. Ange minst en e-postadress och Välj minst en allvarlighetsgrad.
3. I den **mottagarna (e-post)** dialogrutan Skriv e-postadresserna för som ta emot meddelanden. Använd formatet: username@domainname.com. Avgränsa flera e-postadresser med semikolon (;).
4. I den **Avisera** området, Välj **Per avisering** att skicka meddelande när den angivna signalen eller **timvis sammanfattad** att skicka en sammanfattning för den senaste timmen.
5. I den **allvarlighetsgrad** dialogrutan Välj en eller flera nivåer som du vill ska utlösa e-postmeddelande.
6. Klicka på **Spara**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Vilka aviseringstyper är tillgängliga för Azure IaaS-VM säkerhetskopiering?
   | Aviseringsnivå | Aviseringar skickas |
   | --- | --- |
   | Kritiska | om säkerhetskopieringen har misslyckats, Återställningsfel |
   | Varning | för säkerhetskopieringsjobb har slutförts med varningar (till exempel: vissa skrivare kunde inte skapa en ögonblicksbild) |
   | Information | finns för närvarande inga informationsaviseringar för Virtuella Azure-säkerhetskopiering |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Finns det situationer där det inte skickas någon e-post, även om konfigurationen anger att avisering ska skickas?
Det finns situationer där en avisering inte skickas, trots att meddelanden har konfigurerats korrekt. I följande situationer e-postmeddelande skickas inte meddelanden att undvika avisering brus:

* Om meddelanden har konfigurerats för varje timme sammanfattad och en avisering aktiveras som lösts inom en timme.
* Jobbet har avbrutits.
* Ett säkerhetskopieringsjobb utlöses och misslyckas och en annan säkerhetskopiering pågår.
* En schemalagd säkerhetskopiering för en Resource Manager-aktiverad virtuell startar, men den virtuella datorn finns inte längre.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Med hjälp av aktivitetsloggar få meddelanden efter säkerhetskopiering

Om du vill meddelas när säkerhetskopieringar har lyckats kan du använda aviseringar som bygger på den [aktivitetsloggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) på valvet.

### <a name="login-into-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure portal och gå vidare till relevanta Azure Recovery Services-valvet och klicka på avsnittet ”aktivitetsloggen” i egenskaperna.

### <a name="identify-appropriate-log"></a>Identifiera relevant logg

Använda filter som visas i följande bild för att kontrollera om du tar emot aktivitetsloggar för lyckade säkerhetskopieringar. Ändras timespan om du vill visa poster.

![Aktivitetsloggar](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Du kan klicka på ”JSON” segment för att få mer information och visa det genom att kopiera klistra in den på en textredigerare. Den ska visa information för valvet och artikeln som utlöste aktivitetsloggen d.v.s. Säkerhetskopiera objekt.

Klicka på ”Lägg till loggen varning” generera varningar för alla loggar.

### <a name="add-activity-log-alert"></a>Lägg till aktivitet loggen varning

Klicka på ”Lägg till aktivitet loggen avisering” visas en skärm som visas nedan

![Aktivitetsloggavisering](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
Prenumeration och resursgrupp används för att lagra aviseringen. Kriterierna som kommer att vara förifyllda. Kontrollera att alla värden som är relevanta för dina behov.

Nivån har markerats som ”information” och Status som ”Succeeded” för lyckade säkerhetskopieringar.

Om du väljer en ”resurs” ovan genereras aviseringen när aktivitetsloggar registreras för resursen eller valvet. Om du vill att regeln ska gälla alla valv lämna ”resurs” för att vara tomt.

### <a name="define-action-on-alert-firing"></a>Definiera åtgärd i aviseringen att

Använd åtgärdsgruppen ”” definiera åtgärden på Generera en avisering. Klicka på ”typ” om du vill veta mer om tillgängliga åtgärder sådana e-post/SMS/integrering med ITSM osv.

![Aktivitetsgrupp log-åtgärd](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


När du klickar på OK kommer att genereras en varning för loggen och efterföljande aktivitetsloggar registreras för lyckade säkerhetskopieringar utlöses åtgärden som definierats i åtgärdsgruppen.

### <a name="limitations-on-alerts"></a>Begränsningar för aviseringar
Händelsebaserat aviseringar regleras följande begränsningar:

1. Aviseringar aktiveras på alla virtuella datorer i Recovery Services-valvet. Du kan anpassa varningen för en delmängd av virtuella datorer i en Recovery Services-valvet.
2. Aviseringar skickas från ”alerts-noreply@mail.windowsazure.com”. För närvarande kan du ändra avsändarens e-post.

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar en virtuell dator från en återställningspunkt igen kolla [återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md).

Om du behöver information om hur du skyddar virtuella datorer, se [förhandstitt: Säkerhetskopiera virtuella datorer till ett Recovery Services-valv](backup-azure-vms-first-look-arm.md). 

Mer information om hanteringsaktiviteter för VM-säkerhetskopieringar i artikeln [hantera Azure-säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md).
