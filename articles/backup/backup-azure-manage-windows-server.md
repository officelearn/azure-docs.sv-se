---
title: Hantera Azure Recovery Services valv och servrar
description: I den här artikeln får du lära dig hur du använder instrumentpanelen för översikt över Återställningstjänster för att övervaka och hantera dina Recovery Services-valv.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 1a4d23c157700f42422cfe7ca8fa1c49e2cf128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131978"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Övervaka och hantera Recovery Services-valv

I den här artikeln beskrivs hur du använder instrumentpanelen för översikt **över** Återställningstjänster för att övervaka och hantera dina Recovery Services-valv. När du öppnar ett Recovery Services-valv från listan öppnas **översiktsinstrumentpanelen** för det valda valvet. Instrumentpanelen innehåller olika detaljer om valvet. Det finns *paneler* som visar: status för kritiska aviseringar och varningsaviseringar, pågående och misslyckade säkerhetskopieringsjobb och mängden lokalt redundant lagring (LRS) och geo redundant lagring (GRS) som används. Om du säkerhetskopierar virtuella Azure-datorer till valvet visar panelen [ **Status för säkerhetskopiering** alla kritiska objekt eller varningsobjekt](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status). Följande bild är **översiktsinstrumentpanelen** för **Contoso-vault**. Panelen **Säkerhetskopieringsobjekt** visar att det finns nio objekt som är registrerade i valvet.

![instrumentpanelen för återställningstjänster valv](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Förutsättningarna för den här artikeln är: en Azure-prenumeration, ett Recovery Services-valv och att det finns minst ett säkerhetskopieringsobjekt konfigurerat för valvet.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Öppna ett Recovery Services-valv

Om du vill övervaka aviseringar eller visa hanteringsdata om ett Recovery Services-valv öppnar du valvet.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.

2. Klicka på **Alla tjänster**i portalen .

   ![Öppna en lista över Recovery Services-valv steg 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Skriv **Återställningstjänster** i dialogrutan **Recovery Services**Alla tjänster . När du börjar skriva filtreras listan baserat på det du skriver. När alternativet **Återställningstjänster valv** visas, klicka på den för att öppna listan över Recovery Services valv i din prenumeration.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Klicka på ett valv i listan över valv för att öppna **instrumentpanelen Översikt.**

    ![instrumentpanelen för återställningstjänster valv](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Översiktsinstrumentpanelen använder paneler för att tillhandahålla aviseringar och jobbdata för säkerhetskopiering.

## <a name="monitor-backup-jobs-and-alerts"></a>Övervaka säkerhetskopieringsjobb och aviseringar

Instrumentpanelen för översikt **över** Återställningstjänster ger paneler för övervaknings- och användningsinformation. Panelerna i avsnittet Övervakning visar kritiska aviseringar och varningsaviseringar samt pågående och misslyckade jobb. Klicka på en viss avisering eller ett visst jobb för att öppna menyn Säkerhetskopieringsvarningar eller säkerhetskopieringsjobb, filtrerat efter det jobbet eller aviseringen.

![Uppgifter för säkerhetskopiering av instrumentpaneler](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

Avsnittet Övervakning visar resultatet av fördefinierade **frågor om säkerhetskopiering** och jobb **för säkerhetskopiering.** Övervakningspanelerna ger aktuell information om:

* Kritiska varningar och varningsaviseringar för säkerhetskopieringsjobb (under de senaste 24 timmarna)
* Förkontrollstatus för virtuella Azure-datorer. Fullständig information om statusen för förkontroll finns i [Status för förvalskontroll för säkerhetskopiering](#backup-pre-check-status).
* Säkerhetskopieringsjobben pågår och jobb som har misslyckats (under de senaste 24 timmarna).

Användningspanelerna ger:

* Antalet säkerhetskopieringsobjekt som konfigurerats för valvet.
* Azure-lagringen (separerad av LRS och GRS) som förbrukas av valvet.

Klicka på panelerna (förutom säkerhetskopieringslagring) för att öppna den associerade menyn. I bilden ovan visar panelen Säkerhetskopieringsaviseringar tre kritiska aviseringar. Om du klickar på raden Kritiska aviseringar på panelen Säkerhetskopior öppnas säkerhetsaviseringar som filtrerats efter kritiska aviseringar.

![Menyn Säkerhetskopior filtreras efter kritiska aviseringar](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Menyn Säkerhetskopior i bilden ovan filtreras efter: Status är Aktiv, Allvarlighetsgraden är kritisk och tiden är de föregående 24 timmarna.

### <a name="backup-pre-check-status"></a>Status för förvalskontroll för säkerhetskopiering

Förkontroller av säkerhetskopiering kontrollerar konfigurationen av dina virtuella datorer för problem som kan påverka säkerhetskopiorna negativt. De sammanställer den här informationen så att du kan visa den direkt från instrumentpanelen för Recovery Services Vault och ge rekommendationer för korrigerande åtgärder för att säkerställa lyckade filkonsekventa eller programkonsekventa säkerhetskopior. De kräver ingen infrastruktur och har inga extra kostnader.  

Förkontroller för säkerhetskopiering körs som en del av de schemalagda säkerhetskopieringsåtgärderna för dina virtuella Azure-datorer. De avslutas med något av följande påstår:

* **Godkänd:** Det här tillståndet anger att den virtuella datorns konfiguration ska leda till lyckade säkerhetskopior och att inga korrigerande åtgärder behöver vidtas.
* **Varning:** Det här tillståndet anger ett eller flera problem i den virtuella datorns konfiguration som *kan* leda till säkerhetskopieringsfel. Det ger *rekommenderade* steg för att säkerställa lyckade säkerhetskopior. Om du till exempel inte har den senaste VM-agenten installerad kan säkerhetskopior misslyckas periodvis. Denna situation skulle ge en varning stat.
* **Kritisk**: Det här tillståndet anger ett eller flera kritiska problem i den virtuella datorns konfiguration som *leder* till säkerhetskopieringsfel och ger *nödvändiga* steg för att säkerställa lyckade säkerhetskopieringar. Ett nätverksproblem som orsakas av en uppdatering av NSG-reglerna för en virtuell dator kommer till exempel att orsaka att säkerhetskopior misslyckas, eftersom det förhindrar att den virtuella datorn kommunicerar med Azure Backup-tjänsten. Denna situation skulle ge ett kritiskt tillstånd.

Följ stegen nedan för att börja lösa eventuella problem som rapporterats av förkontroller av säkerhetskopiering för vm-säkerhetskopior i ditt Recovery Services Vault.

* Välj panelen **Status för förhandskontroll av säkerhetskopiering (Azure VMs)** på instrumentpanelen för Recovery Services Vault.
* Välj en virtuell dator med status för förkontroll för säkerhetskopiering av antingen **kritisk** eller **varning**. Den här åtgärden öppnar informationsfönstret **för virtuella datorer.**
* Markera fönsterrutan högst upp i fönstret för att visa beskrivningen av konfigurationsproblemet och hur du åtgärdar konfigurationsstegen.

## <a name="manage-backup-alerts"></a>Hantera aviseringar om säkerhetskopiering

Om du vill komma åt menyn Säkerhetssäkerhetsvarningar klickar du på **Säkerhetsvarningar**på Arkiv-menyn för Återställningstjänster .

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Rapporten Säkerhetskopior visar aviseringarna för valvet.

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Aviseringar

I listan Säkerhetskopior visas den valda informationen för de filtrerade aviseringarna. På menyn Säkerhetskopior kan du filtrera efter kritiska varningar eller varningsaviseringar.

| Aviseringsnivå | Händelser som genererar aviseringar |
| ----------- | ----------- |
| Kritisk | Du får kritiska aviseringar när: Säkerhetskopieringsjobb misslyckas, återställningsjobb misslyckas och när du stoppar skyddet på en server, men behåller data.|
| Varning | Varningsaviseringar när: Säkerhetskopieringsjobb slutförs med varningar, till exempel när färre än 100 filer inte säkerhetskopieras på grund av korruptionsproblem eller när fler än 1 000 000 filer har säkerhetskopierats). |
| Information | för närvarande används inga informationsvarningar. |

### <a name="viewing-alert-details"></a>Visa aviseringsinformation

Rapporten Säkerhetskopieringsvarningar spårar åtta detaljer om varje avisering. Använd knappen **Välj kolumner** för att redigera informationen i rapporten.

![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/backup-alerts.png)

Som standard visas alla detaljer, utom **Senaste förekomsttid,** i rapporten.

* Varning
* Objekt för säkerhetskopiering
* Skyddad server
* Severity
* Varaktighet
* Skapande tid
* Status
* Senaste förekomsttid

### <a name="change-the-details-in-alerts-report"></a>Ändra information i rapporten aviseringar

1. Om du vill ändra rapportinformationen klickar du på **Välj kolumner**på menyn **Säkerhetskopior.**

   ![Säkerhetskopieringsaviseringar](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Menyn **Välj kolumner** öppnas.

2. På menyn **Välj kolumner** väljer du den information som du vill ska visas i rapporten.

    ![Menyn Välj kolumner](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Klicka på **Klar** om du vill spara ändringarna och stänga menyn Välj kolumner.

   Om du gör ändringar, men inte vill behålla ändringarna, klickar du på **Återställ** för att återställa den markerade till den senast sparade konfigurationen.

### <a name="change-the-filter-in-alerts-report"></a>Ändra filtret i rapporten aviseringar

Använd **filtermenyn** om du vill ändra aviseringarnas allvarlighetsgrad, status, starttid och sluttid.

> [!NOTE]
> När du redigerar filtret Säkerhetskopior ändras inte varnings- eller varningsaviseringarna i instrumentpanelen för översikt för valvet.
>  

1. Om du vill ändra filtret Säkerhetskopior klickar du på **Filtrera**på menyn Säkerhetskopior på menyn Säkerhetskopior.

   ![Välj filtermeny](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Filter-menyn visas.

   ![Välj filtermeny](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Redigera allvarlighetsgrad, status, starttid eller sluttid och klicka på **Klar** för att spara ändringarna.

## <a name="configuring-notifications-for-alerts"></a>Konfigurera meddelanden för aviseringar

Konfigurera meddelanden för att generera e-postmeddelanden när en varning eller kritisk avisering inträffar. Du kan skicka e-postaviseringar varje timme eller när en viss avisering inträffar.

   ![Filtrera aviseringar](./media/backup-azure-manage-windows-server/configure-notification.png)

Som standard är e-postmeddelanden **på**. Klicka på **Av** om du vill stoppa e-postmeddelandena.

På kontrollen **Meddela** väljer du **Per avisering** om du inte vill gruppera eller inte har många objekt som kan generera aviseringar. Varje avisering resulterar i ett meddelande (standardinställningen) och ett e-postmeddelande för lösning skickas omedelbart.

Om du väljer Sammanfattning varje timme skickas ett e-postmeddelande till mottagarna som förklarar de olösta aviseringar som **genererats**under den senaste timmen. Ett e-postmeddelande med upplösning skickas ut i slutet av timmen.

Välj den allvarlighetsgrad för avisering (kritisk eller varning) som används för att generera e-post. För närvarande finns det inga informationsaviseringar.

## <a name="manage-backup-items"></a>Hantera objekt för säkerhetskopiering

Ett Recovery Services-valv innehåller många typer av säkerhetskopierade data. [Läs mer](backup-overview.md#what-can-i-back-up) om vad du kan säkerhetskopiera. Om du vill hantera de olika servrarna, datorerna, databaserna och arbetsbelastningarna klickar du på panelen **Säkerhetskopieringsobjekt** för att visa innehållet i valvet.

![Panel för säkerhetskopieringsobjekt](./media/backup-azure-manage-windows-server/backup-items.png)

Listan över säkerhetskopieringsobjekt, ordnade efter hanteringstyp för säkerhetskopiering, öppnas.

![lista över objekt för säkerhetskopiering](./media/backup-azure-manage-windows-server/list-backup-items.png)

Om du vill utforska en viss typ av skyddad instans klickar du på objektet i kolumnen Hantering av säkerhetskopiering. I ovanstående avbildning finns det till exempel två virtuella Azure-datorer som skyddas i det här valvet. Om du klickar på **Azure Virtual Machine**öppnas listan över skyddade virtuella datorer i det här valvet.

![lista över typ Säkerhetskopiering](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Listan över virtuella datorer har användbara data: den associerade resursgruppen, tidigare [förkontroll av säkerhetskopiering,](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status)senaste säkerhetskopieringsstatus och datum för den senaste återställningspunkten. Ellipsen i den sista kolumnen öppnar menyn för att utlösa vanliga uppgifter. De användbara data som tillhandahålls i kolumner, är olika för varje säkerhetskopia typ.

![lista över typ Säkerhetskopiering](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Hantera säkerhetskopieringsjobb

Panelen **Säkerhetskopieringsjobb** på instrumentpanelen för valvet visar antalet pågående jobb eller Misslyckades under de senaste 24 timmarna. Panelen ger en inblick i menyn Säkerhetskopieringsjobb.

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Om du vill se ytterligare information om jobben klickar du på **Pågår** eller **Om det inte gick** att öppna menyn Säkerhetskopieringsjobb filtrerat för det tillståndet.

### <a name="backup-jobs-menu"></a>Menyn Säkerhetskopieringsjobb

På menyn **Säkerhetskopieringsjobb** visas information om objekttypen, Åtgärden, Status, Starttid och Varaktighet.  

Om du vill öppna menyn Säkerhetskopieringsjobb klickar du på **Säkerhetskopieringsjobb**i valvets huvudmeny .

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Listan över säkerhetskopieringsjobb öppnas.

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Menyn Säkerhetskopieringsjobb visar status för alla åtgärder, på alla säkerhetskopieringstyper, under de senaste 24 timmarna. Använd **Filter** för att ändra filtren. Filtren förklaras i följande avsnitt.

Så här ändrar du filtren:

1. Klicka på **Filtrera**på menyn Säkerhetskopieringsjobb i valvet.

   ![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Filter-menyn öppnas.

   ![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Välj filterinställningarna och klicka på **Klar**. Den filtrerade listan uppdateras baserat på de nya inställningarna.

#### <a name="item-type"></a>Typ av artikel

Objekttypen är hanteringstypen för säkerhetskopiering för den skyddade instansen. Det finns fyra typer; se följande lista. Du kan visa alla objekttyper eller en artikeltyp. Du kan inte markera två eller tre objekttyper. De tillgängliga artikeltyperna är:

* Alla artikeltyper
* Virtuell Azure-dator
* Filer och mappar
* Azure Storage
* Azure-arbetsbelastning

#### <a name="operation"></a>Åtgärd

Du kan visa en eller alla åtgärder. Du kan inte välja två eller tre åtgärder. De tillgängliga operationerna är:

* Alla åtgärder
* Registrera dig
* Konfigurera säkerhetskopiering
* Säkerhetskopiering
* Återställ
* Inaktivera säkerhetskopiering
* Ta bort säkerhetskopieringsdata

#### <a name="status"></a>Status

Du kan visa All Status eller en. Du kan inte välja två eller tre statusar. De tillgängliga statusarna är:

* All Status
* Slutfört
* Pågår
* Misslyckades
* Avbrutna
* Kompletterat med varningar

#### <a name="start-time"></a>Starttid

Den dag och tid som frågan börjar. Standardär en 24-timmarsperiod.

#### <a name="end-time"></a>Sluttid

Den dag och tid då frågan avslutas.

### <a name="export-jobs"></a>Exportera jobb

Använd **Exportera jobb** för att skapa ett kalkylblad som innehåller all jobbmenyinformation. Kalkylbladet har ett blad som innehåller en sammanfattning av alla jobb och enskilda blad för varje jobb.

Om du vill exportera jobbinformationen till ett kalkylblad klickar du på **Exportera jobb**. Tjänsten skapar ett kalkylblad med namnet på valvet och datumet, men du kan ändra namnet.

## <a name="monitor-backup-usage"></a>Användning av säkerhetskopiering av bildskärmar

Panelen Säkerhetskopieringslagring på instrumentpanelen visar den lagring som förbrukas i Azure. Användning av lagring tillhandahålls för:

* Moln LRS-lagringsanvändning som är associerad med valvet
* Cloud GRS-lagringsanvändning som är associerad med valvet

## <a name="troubleshooting-monitoring-issues"></a>Felsöka övervakningsproblem

**Problem:** Jobb och/eller aviseringar från Azure Backup-agenten visas inte i portalen.

**Felsökningssteg:** Processen, ```OBRecoveryServicesManagementAgent```skickar jobbet och aviseringsdata till Azure Backup-tjänsten. Ibland kan den här processen fastna eller stängas av.

1. Om du vill kontrollera att processen inte körs ```OBRecoveryServicesManagementAgent``` öppnar du **Aktivitetshanteraren**och checken körs.

2. Om processen inte körs öppnar **du Kontrollpanelen**och bläddrar i listan över tjänster. Starta eller starta om **Microsoft Azure Recovery Services Management Agent**.

    För ytterligare information, bläddra i loggarna på:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Till exempel:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Nästa steg

* [Återställa Windows Server eller Windows-klienten från Azure](backup-azure-restore-windows-server.md)
* Mer information om Azure Backup finns i [Översikt över Azure Backup](backup-introduction-to-azure-backup.md)
