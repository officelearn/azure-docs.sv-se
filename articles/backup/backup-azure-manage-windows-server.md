---
title: Hantera Azure Recovery Services-valv och servrar
description: Hantera jobb och aviseringar i ett Azure Recovery Services-valv.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/21/2018
ms.author: raynew
ms.openlocfilehash: 716ddcaf61c4d7db40821056dc759667f9376023
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871350"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Övervaka och hantera Recovery Services-valv

Den här artikeln förklarar hur du använder Recovery Services-valvet **översikt** instrumentpanelen för att övervaka och hantera dina Recovery Services-valv. När du öppnar ett Recovery Services-valv i listan i **översikt** instrumentpanelen för det valda valvet öppnas. Instrumentpanelen innehåller olika information om valvet. Det finns *paneler* som visar: status för kritiska och varningsaviseringar pågår och misslyckade säkerhetskopieringsjobb och mängden lokalt redundant lagring (LRS) och geografiskt redundant lagring (GRS) används. Om du säkerhetskopierar virtuella Azure-datorer till valvet, den [ **säkerhetskopiering statusen för Säkerhetskopieringskontrollen** panelen visar alla objekt som kritiskt eller varningshälsotillstånd](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). Följande bild är den **översikt** instrumentpanel för **Contoso-valvet**. Den **Säkerhetskopieringsobjekt** panelen visar det registreras nio till valvet.

![instrumentpanelen för Recovery services-valvet](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Krav för den här artikeln är: en Azure-prenumeration, Recovery Services-valvet och att det finns minst ett säkerhetskopieringsobjekt som konfigurerats för valvet.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Öppna Recovery Services-valvet

Du kan övervaka aviseringar eller visa hanteringsdata om Recovery Services-valvet genom att öppna valvet.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.

2. I portalen klickar du på **alla tjänster**.

   ![Öppna listan över Recovery Services-valv steg 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. I den **alla tjänster** skriver **återställningstjänster**. När du börjar skriva filtreras listan baserat på det du skriver. När den **Recovery Services-valv** visas klickar du på den för att öppna listan över Recovery Services-valv i prenumerationen.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Från listan över valv, klickar du på ett valv för att öppna dess **översikt** instrumentpanelen. 

    ![instrumentpanelen för Recovery services-valvet](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Översikt över instrumentpanelen använder paneler för att skicka aviseringar och data för säkerhetskopieringsjobbet.

## <a name="monitor-backup-jobs-and-alerts"></a>Övervaka säkerhetskopieringsjobb och aviseringar

Recovery Services-valvet **översikt** instrumentpanelen innehåller paneler för information om övervakning och användning. Paneler i övervakning avsnittet visning kritiskt och varningar och pågående och misslyckade jobb. Klicka på en viss avisering eller ett jobb för att öppna menyn säkerhetskopiering aviseringar eller säkerhetskopieringsjobb filtreras för jobbet eller avisering.

![Säkerhetskopiering instrumentpanelsuppgifter](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

Avsnittet övervakning visas resultatet av fördefinierade **säkerhetskopiering aviseringar** och **säkerhetskopieringsjobb** frågor. Paneler för övervakning innehåller uppdaterad information om:

* Kritiskt och varning aviseringar för säkerhetskopieringsjobb (under de senaste 24 timmarna)
* Statusen för säkerhetskopieringskontrollen för Azure virtuella datorer – fullständig information om statusen för säkerhetskopieringskontrollen finns i den [säkerhetskopiering blogg på statusen för säkerhetskopieringskontrollen för säkerhetskopiering](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* Säkerhetskopieringsjobb pågående och jobb som har misslyckats (under de senaste 24 timmarna).

Användning-paneler innehåller:

* Antal säkerhetskopieringsobjekt som konfigurerats för valvet.
* Azure storage (avgränsade med LRS och GRS) förbrukas av valvet.

Klicka på panelerna (utom Backup Storage) för att öppna menyn associerade. I bilden ovan visar panelen Backup aviseringar tre viktiga aviseringar. Klickar du på raden kritiska aviseringar i panelen Backup aviseringar, öppnas säkerhetskopiering aviseringarna filtreras för viktiga aviseringar.

![Aviseringar om säkerhetskopiering menyn filtreras för viktiga aviseringar](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Backup Alerts-menyn, i bilden ovan, filtreras efter: Status är aktiv, allvarlighetsgrad är kritiskt och föregående 24 timmar.

## <a name="manage-backup-alerts"></a>Hantera aviseringar för säkerhetskopiering

För att komma åt menyn aviseringar för säkerhetskopiering på menyn Recovery Services-valvet klickar du på **säkerhetskopiering aviseringar**.

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Aviseringsrapport för säkerhetskopiering visas aviseringar för valvet. 

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Aviseringar

Backup aviseringslistan visar den valda informationen för de filtrerade aviseringarna. Du kan filtrera aviseringar kritiskt eller varning på säkerhetskopiering Alerts-menyn.

| Aviseringsnivå | Händelser som genererar aviseringar |
| ----------- | ----------- |
| Kritisk | Du får viktiga aviseringar när: säkerhetskopiering jobb misslyckas återställningsjobb misslyckas och när du stoppar skyddet på en server, men behålla data.|
| Varning | Du får varning aviseringar när: säkerhetskopiering jobben har slutförts med varningar, till exempel när färre än 100 filer säkerhetskopieras inte på grund av problem med skador, eller när det är större än 1 000 000 filer har säkerhetskopierat). |
| Information | för närvarande används inga informationsaviseringar. |

### <a name="viewing-alert-details"></a>Visa aviseringsinformation

Backup aviseringsrapport spårar åtta information om varje avisering. Använd den **Välj kolumner** knappen för att redigera informationen i rapporten.

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts.png)

Som standard all information förutom **tidpunkt för senaste förekomst**, visas i rapporten.

* Varning
* Säkerhetskopieringsobjekt
* Skyddad Server
* Severity
* Varaktighet
* Skapad
* Status
* Tidpunkt för senaste förekomst

### <a name="change-the-details-in-alerts-report"></a>Ändra informationen i rapporten för aviseringar

1. Ändra rapportinformation i den **säkerhetskopiering aviseringar** -menyn klickar du på **Välj kolumner**.

   ![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Den **Välj kolumner** menyn öppnas.

2. I den **Välj kolumner** menyn väljer du de information som du vill ska visas i rapporten.

    ![Välj kolumner menyn](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Klicka på **klar** att spara ändringarna och stänga menyn Välj kolumner.

   Om du gör ändringar, men inte vill behålla ändringarna, klickar du på **återställa** att returnera den valda till den senaste sparat konfiguration.

### <a name="change-the-filter-in-alerts-report"></a>Ändra filtret i rapporten för aviseringar

Använd den **Filter** menyn för att ändra allvarlighetsgrad, Status, starttid och sluttid för aviseringar. 

> [!NOTE]
> Redigera säkerhetskopiering aviseringar ändras filter inte aviseringar kritiskt eller varning i valvet instrumentpanel.
>  

1. Om du vill ändra filtret säkerhetskopiering aviseringar, säkerhetskopiering Alerts-menyn klickar du på **Filter**.

   ![Välj filter-menyn](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Filtermenyn visas.

   ![Välj filter-menyn](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Redigera allvarlighetsgrad, Status, starttid och sluttid, och klicka på **klar** att spara dina ändringar.

## <a name="configuring-notifications-for-alerts"></a>Konfigurera meddelanden för aviseringar

Konfigurera meddelanden att skapa e-postmeddelanden när en varning eller kritisk varning visas. Du kan skicka e-postaviseringar varje timme eller när en viss avisering inträffar.

   ![Filtrera aviseringar](./media/backup-azure-manage-windows-server/configure-notification.png)

E-postmeddelanden är som standard **på**. Klicka på **av** att stoppa de e-postmeddelanden.

På den **meddela** kontrollen kan du välja **Per avisering** om inte vill att gruppering eller inte har många objekt som kan generera aviseringar. Alla aviseringar som resulterar i ett meddelande (standardinställningen) och en lösning i e-postmeddelandet skickas omedelbart.

Om du väljer **sammanställning per timme**, skickas ett e-postmeddelande till mottagarna som förklarar olösta aviseringar som genererats under den senaste timmen. En lösning i e-postmeddelande skickas ut i slutet av timmen.

Välj den allvarlighetsgrad för avisering (kritiskt eller varning) används för att generera e-post. Det finns för närvarande inga aviseringar för Information.

## <a name="manage-backup-items"></a>Hantera säkerhetskopieringsobjekt

Recovery Services-valvet innehåller många typer av säkerhetskopierade data. En fullständig lista över typer av säkerhetskopiering finns [som program och arbetsbelastningar kan säkerhetskopieras](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use). För att hantera de olika servrar, datorer, databaser och arbetsbelastningar, klickar du på den **Säkerhetskopieringsobjekt** panel för att visa innehållet i valvet.

![Objekt att säkerhetskopiera panel](./media/backup-azure-manage-windows-server/backup-items.png)

Listan med Säkerhetskopieringsobjekt, ordnade efter typ av Säkerhetskopieringshantering, öppnas.

![listan med säkerhetskopieringsobjekt](./media/backup-azure-manage-windows-server/list-backup-items.png)

Om du vill utforska en viss typ av skyddad instans, klickar du på objektet i fältet Typ av Säkerhetskopieringshantering. Till exempel i bilden ovan finns två Azure virtuella datorer som skyddas i det här valvet. Klicka på **Azure-dator**, öppnar du listan över skyddade virtuella datorer i det här valvet.

![lista över typ av säkerhetskopiering](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Listan med virtuella datorer innehåller användbara data: associerade resursgruppen, tidigare [Backup Förkontroll](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/), Status för senaste säkerhetskopiering och datumet för den senaste återställningspunkten. De tre punkterna öppnar i den sista kolumnen du menyn för att utlösa vanliga uppgifter. Användbara data i kolumner, är olika för varje typ av säkerhetskopiering.

![lista över typ av säkerhetskopiering](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Hantera säkerhetskopieringsjobb

Den **säkerhetskopieringsjobb** panel i instrumentpanelen för valvet visar antalet jobb som pågår eller misslyckades under de senaste 24 timmarna. Panelen erbjuder en glimt i menyn för säkerhetskopieringsjobb.

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Klicka för att visa ytterligare information om jobb som **pågår** eller **misslyckades** att öppna menyn säkerhetskopieringsjobb filtrerad för det aktuella tillståndet.

### <a name="backup-jobs-menu"></a>Menyn för säkerhetskopieringsjobb

Den **säkerhetskopieringsjobb** menyn visar information om objekt typ, åtgärden, Status, starttid och varaktighet.  

Klicka för att öppna menyn säkerhetskopieringsjobb på valvet huvudmenyn **säkerhetskopieringsjobb**. 

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Listan med säkerhetskopieringsjobb öppnas.

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Menyn för säkerhetskopieringsjobb visar status för alla åtgärder på alla typer av säkerhetskopiering, för de senaste 24 timmarna. Använd **Filter** ändra filtren. Filter som beskrivs i följande avsnitt.

Ändra filtren:

1. I valvet säkerhetskopieringsjobb menyn, klickar du på **Filter**.

   ![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Filter-menyn öppnas.

   ![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Välj inställningarna i filtret och klicka på **klar**. De filtrerade listan uppdateras baserat på de nya inställningarna.

#### <a name="item-type"></a>Typ av objekt

Objekttypen är typ av säkerhetskopieringshantering för den skyddade instansen. Det finns fyra typer. se listan nedan. Du kan visa alla typer av objekt, eller en objekttyp. Du kan inte välja två eller tre typer av objekt. Tillgängliga objekttyper är:

* Alla typer av objekt
* Virtuell Azure-dator
* Filer och mappar
* Azure Storage
* Azure-arbetsbelastning

#### <a name="operation"></a>Åtgärd

Du kan visa en åtgärd eller alla åtgärder. Du kan inte välja två eller tre åtgärder. De tillgängliga åtgärderna är:

* Alla åtgärder
* Registrera dig
* Konfigurera säkerhetskopiering
* Backup
* Återställ
* Inaktivera säkerhetskopiering
* ta bort säkerhetskopierade data

#### <a name="status"></a>Status

Du kan visa Status för alla eller en. Du kan inte välja två eller tre status. Tillgängliga status är:

* Alla statusar
* Slutfört
* Pågår
* Misslyckad
* Avbrutna
* Har slutförts med varningar

#### <a name="start-time"></a>Starttid

Dag och tid som frågan börjar. Standardvärdet är en 24-timmarsperiod.

#### <a name="end-time"></a>Sluttid

Den dag och tid när frågan slutar.

### <a name="export-jobs"></a>Export-jobb

Använd **exportera jobb** att skapa ett kalkylblad som innehåller alla jobb menyn uppgifter. Kalkylbladet har ett blad som innehåller en sammanfattning av alla jobb och enskilda ark för varje jobb.

Om du vill exportera jobb informationen till ett kalkylblad, klickar du på **exportera jobb**. Tjänsten skapar en speadsheet med namnet på valvet och datum, men du kan ändra namnet.

## <a name="monitor-backup-usage"></a>Övervaka säkerhetskopiering användning

Backup Storage-panelen på instrumentpanelen visar förbrukad lagring i Azure. Lagringsanvändningen har angetts för:

* LRS-lagring molnanvändning som är associerade med valvet
* GRS-lagring molnanvändning som är associerade med valvet


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Q1. Hur lång tid tar det för Azure backup-agenten jobbstatusen återspeglas i portalen?**

S1. Azure-portalen kan ta upp till 15 minuter att återspegla jobbstatusen Azure backup-agenten.

**Q2. När ett säkerhetskopieringsjobb misslyckas, hur lång tid tar det för att utlösa en avisering?**

S2. En avisering genereras i 20 minuter för Azure Säkerhetskopieringsfel.

**KVARTAL 3. Finns det fall där inte ett e-postmeddelande skickas om aviseringarna är inställda?**

S3. Ja. I följande situationer skickas inte meddelanden.

* Om meddelanden har konfigurerats för varje timme och en avisering skickas ut och löses inom en timme
* När ett jobb har avbrutits
* Om en andra säkerhetskopieringsjobb misslyckas eftersom det ursprungliga säkerhetskopieringsjobbet pågår

## <a name="troubleshooting-monitoring-issues"></a>Felsökning av problem med övervakning

**Problem:** jobb och/eller varningar från Azure Backup-agenten inte visas i portalen.

**Felsökning:** processen, ```OBRecoveryServicesManagementAgent```, skickar jobbet och avisering data till Azure Backup-tjänsten. Ibland den här processen kan fastna eller avstängning.

1. Kontrollera att processen inte körs, att öppna **Aktivitetshanteraren**, och kontrollera ```OBRecoveryServicesManagementAgent``` körs.

2. Om processen inte körs måste du öppna **Kontrollpanelen**, och bläddra i listan över tjänster. Starta eller starta om **Microsoft Azure Recovery Services Management Agent**.

    Bläddra i loggarna på för mer information:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Till exempel:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Nästa steg
* [Återställa Windows Server eller Windows Client från Azure](backup-azure-restore-windows-server.md)
* Läs mer om Azure Backup i [översikt över Azure Backup](backup-introduction-to-azure-backup.md)
* Gå till den [Azure Backup-Forum](https://go.microsoft.com/fwlink/p/?LinkId=290933)
