---
title: Övervaka aviseringar om säkerhetskopiering för Azure-datorer
description: Övervaka händelser och varningar från Azure-dator säkerhetskopieringsjobb. Skicka e-post baserat på varningar.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: c5c3f498f62d6399534333f8166a514a10f044c5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310176"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Övervaka varningar vid säkerhetskopiering av virtuella Azure-datorer

Aviseringar är svar från tjänsten att en händelse tröskelvärde har uppnåtts eller omvandlingsjobbet. Att veta när problem med start kan vara viktiga för att hålla kostnaderna. Aviseringar vanligtvis sker inte enligt ett schema och så är det bra att veta så snart som möjligt när genereras aviseringar. När ett jobb för säkerhetskopieringen eller återställningen misslyckas, till exempel visas en varning inom fem minuter efter felet. I instrumentpanelen för valvet visar panelen Backup aviseringar händelser som kritiskt och varning på servernivå. Du kan visa alla händelser i inställningarna för säkerhetskopiering aviseringar. Men vad gör du om en varning visas när du arbetar på en separat problemet? Om du inte vet när aviseringen händer, det kan vara en mindre besvär eller det kan äventyra data. Kontrollera att rätt personer är medvetna om en avisering – när det uppstår genom att konfigurera tjänsten för att skicka aviseringar via e-post. Mer information om hur du konfigurerar e-postaviseringar finns i [konfigurera meddelanden](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Hur hittar jag information om aviseringarna?

Om du vill visa information om den händelse som utlöste en avisering, måste du öppna avsnittet aviseringar för säkerhetskopiering. Det finns två sätt att öppna avsnittet säkerhetskopiering aviseringar: från Backup-aviseringar panelen på instrumentpanelen för valvet eller från avsnittet aviseringar och händelser.

Öppna bladet säkerhetskopiering aviseringar från panel på Backup-aviseringar:

* På den **säkerhetskopiering aviseringar** panelen på instrumentpanelen för valvet, klickar du på **kritisk** eller **varning** visa drifthändelser för den allvarlighetsgraden.

    ![Aviseringar om säkerhetskopiering panelen](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Öppna bladet säkerhetskopiering aviseringar från avsnittet aviseringar och händelser:

1. Från instrumentpanelen för valvet klickar du på **alla inställningar**. ![Knappen för alla inställningar](./media/backup-azure-monitor-vms/all-settings-button.png)
2. På den **inställningar** bladet klickar du på **aviseringar och händelser**. ![Knappen för aviseringar och händelser](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. På den **aviseringar och händelser** bladet klickar du på **säkerhetskopiering aviseringar**. ![Säkerhetskopiera aviseringar knappen](./media/backup-azure-monitor-vms/backup-alerts.png)

    Den **säkerhetskopiering aviseringar** avsnittet öppnas och visar de filtrera aviseringarna.

    ![Aviseringar om säkerhetskopiering panelen](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Om du vill visa detaljerad information om en viss avisering från listan över händelser, klickar du på aviseringen för att öppna dess **information** avsnittet.

    ![Händelseinformation](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Om du vill anpassa de attribut som visas i listan, se [visa ytterligare händelse attribut](backup-azure-monitor-vms.md)

## <a name="configure-notifications"></a>Konfigurera meddelanden

 Du kan konfigurera tjänsten för att skicka e-postmeddelanden för aviseringar som inträffat den senaste timmen, eller när särskilda typer av händelser inträffar.

Att ställa in e-postmeddelanden för aviseringar

1. På menyn Backup aviseringar **konfigurera meddelanden**

    ![Säkerhetskopiera Alerts-menyn](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Avsnittet Konfigurera meddelanden öppnas.

    ![Konfigurera meddelanden bladet](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Klicka på avsnittet Konfigurera aviseringar för e-postmeddelanden **på**.

    Mottagare och allvarlighetsgrad dialogrutor har en stjärnan bredvid dem eftersom den här informationen krävs. Ange minst en e-postadress och Välj minst en allvarlighetsgrad.
3. I den **mottagare (e-post)** dialogrutan Skriv e-postadresserna för vem som får aviseringarna. Använd formatet: username@domainname.com. Avgränsa flera e-postadresser med semikolon (;).
4. I den **meddela** området, Välj **Per avisering** att skicka meddelande när den angivna signalen eller **sammanställning per timme** att skicka en sammanfattning för den senaste timmen.
5. I den **allvarlighetsgrad** dialogrutan Välj en eller flera nivåer som du vill ska utlösa e-postmeddelande.
6. Klicka på **Spara**.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Vilka aviseringstyper som är tillgängliga för Azure IaaS VM-säkerhetskopiering

   | Aviseringsnivå | Aviseringar skickas |
   | --- | --- |
   | Kritisk | för Backup-fel, Återställningsfel |
   | Varning | för säkerhetskopieringsjobb har slutförts med varningar (till exempel: vissa skrivare misslyckades vid skapande av en ögonblicksbild) |
   | Information | för närvarande är inga informationsaviseringar tillgängliga för virtuell Azure-säkerhetskopiering |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Situationer där det inte skickas e-postmeddelande även om meddelanden har konfigurerats

Det finns situationer där en avisering inte skickas, även om meddelanden har konfigurerats korrekt. I följande situationer e-postmeddelandet skickas inte meddelanden att undvika onödig avisering:

* Om aviseringarna är inställda på sammanställning per timme och en avisering skickas ut och löses inom en timme.
* Jobbet avbryts.
* En säkerhetskopiering utlöses och misslyckas och en annan säkerhetskopiering pågår.
* En schemalagd säkerhetskopiering för en Resource Manager-aktiverad virtuell dator startas, men den virtuella datorn inte längre finns.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Använda aktivitetsloggar för att få meddelanden efter säkerhetskopiering

> [!NOTE]
> Vi har flyttat till en ny modell för också aktivitetsloggar från Azure Backup på Recovery Services-valv. Det har tyvärr påverkas generering av aktivitetsloggar i Azure suveräna moln. Om Azure suveräna moln användare skapas/konfigurerats några aviseringar från aktivitetsloggar via Azure Monitor enligt anvisningarna här, skulle de inte aktiveras. I så fall rekommenderar vi dessa användare att använda diagnostikinställningar och LA arbetsyta eller [PowerBI reporting lösning](backup-azure-configure-reports.md) att hämta den relevanta informationen. I alla offentliga Azure-regioner, om en användare samlar in Recovery Services-aktivitetsloggar till Log Analytics-arbetsyta som vi redan nämnt [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity), dessa loggar också visades inte.

Om du vill få ett meddelande när säkerhetskopieringar har lyckats kan du använda aviseringar som bygger på den [aktivitetsloggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) på valvet.

### <a name="login-into-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure-portalen och gå vidare till relevanta Azure Recovery Services-valvet och klicka på ”aktivitetsloggavsnittet” i egenskaperna.

### <a name="identify-appropriate-log"></a>Identifiera relevant logg

Använda filter som visas i följande bild för att kontrollera om du tar emot aktivitetsloggar för säkerhetskopiering. Ändras tidsintervallet om du vill visa poster.

![Aktivitetsloggar](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Du kan klicka på ”JSON” segment för att få mer information och visa det genom att kopiera klistra in den till en textredigerare. Vault-information och artikeln som utlöste aktivitetsloggen d.v.s. säkerhetskopieringsobjekt bör visas.

Klicka på ”Lägg till aktivitetsloggavisering” genererar aviseringar för alla loggar.

### <a name="add-activity-log-alert"></a>Lägg till aktivitetsloggavisering

Klicka på ”Lägg till aktivitetsloggavisering” visas en skärm som visas nedan

![Aktivitetsloggavisering](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png) prenumerationen och resursgruppen för att spara aviseringen. Kriterierna är förifylld. Kontrollera att alla värden som är relevanta för dina behov.

För lyckade säkerhetskopieringar markeras nivån som ”information” och Status som ”lyckades”.

Om du väljer en ”resurs” ovan, genereras aviseringen när aktivitetsloggar för den resursen eller valvet har registrerats. Om du vill att regeln ska tillämpas på alla valv lämna ”resurs” för att vara tom.

### <a name="define-action-on-alert-firing"></a>Definiera åtgärd på den aviseringen utlösts

Använd åtgärdsgruppen ”” definiera åtgärd vid genererar en avisering. Du kan klicka på ”åtgärdstyp” vill veta mer om tillgängliga åtgärder sådana e-post/SMS/integrering med ITSM osv.

![Aktivitetsgrupp log-åtgärd](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

När du klickar på OK kommer att genereras en aktivitetsloggavisering och efterföljande aktivitetsloggar som registrerades för säkerhetskopiering utlöses åtgärden som definieras i åtgärdsgruppen.

### <a name="limitations-on-alerts"></a>Begränsningar vid aviseringar

Händelsebaserade aviseringar omfattas följande begränsningar:

1. Aviseringar har utlösts på alla virtuella datorer i Recovery Services-valvet. Du kan inte anpassa aviseringen för en delmängd av virtuella datorer i ett Recovery Services-valv.
2. Aviseringar skickas från ”alerts-noreply@mail.windowsazure.com”. För närvarande kan du ändra e-postavsändaren.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar en virtuell dator från en återställningspunkt igen [återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md).

Om du behöver information om hur du skyddar dina virtuella datorer, se [första titt: Säkerhetskopiera virtuella datorer till Recovery Services-valvet](backup-azure-vms-first-look-arm.md).

Mer information om hanteringsaktiviteter för VM-säkerhetskopieringar i artikeln [hantera Azure-säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md).
