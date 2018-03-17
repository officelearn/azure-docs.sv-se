---
title: Hantera Azure Recovery Services-valv och servrar | Microsoft Docs
description: "Använd den här artikeln för att hantera Azure Recovery Services-valv och servrar."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 18ab960c3d86f4154f7cbbaa3dd4909b9b16b697
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Övervaka och hantera Azure Recovery Services-valv och servrar för Windows-datorer

Den här artikeln innehåller en översikt över säkerhetskopiering övervakaren och hantering av uppgifter som är tillgängliga via Azure portal och Microsoft Azure Backup-agenten. Den här artikeln förutsätter att du redan har en Azure-prenumeration och har skapat minst ett Recovery Services-valvet.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Öppna ett Recovery Services-valv

Recovery Services-valvet instrumentpanelen visar information eller attribut för Recovery Services-valvet.

1. Logga in på den [Azure Portal](https://portal.azure.com/) med din Azure-prenumeration.
2. Klicka på **Alla tjänster**. 

3. Du vill öppna en Recovery Services-valvet. I dialogrutan för att börja skriva **återställningstjänster**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv** att visa listan över Recovery Services-valv i din prenumeration.

     ![Öppna en lista över Recovery Services-valv steg 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

    Listan över Recovery Services-valv öppnas.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Välj namnet på Recovery Services-valvet som du vill öppna i listan över valv. Recovery Services-valvet instrumentpanelen menyn öppnas.

    ![Recovery services-valvet instrumentpanelen](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Nu när du har öppnat Recovery Services-valvet, försöker du med övervakning eller hantering av uppgifter.

## <a name="monitor-backup-jobs-and-alerts"></a>Övervakaren säkerhetskopieringsjobb och -varningar

Du kan övervaka jobb och aviseringar från Recovery Services-valvet instrumentpanel där du ser:

* Information om aviseringar om säkerhetskopiering
* Filer och mappar samt Azure virtuella datorer som skyddas i molnet
* Totalt lagringsutrymme som förbrukas i Azure
* Status för säkerhetskopieringsjobb

![Säkerhetskopiering instrumentpanelsuppgifter](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Klicka på informationen i dessa brickor öppnar associerade-menyn där du hanterar relaterade aktiviteter.

Högst upp på instrumentpanelen:

* Inställningar – ger åtkomst till tillgängliga aktiviteter för säkerhetskopiering.
* Säkerhetskopian – kan du säkerhetskopiera nya filer och mappar (eller virtuella Azure-datorer) till Recovery Services-valvet.
* Ta bort – om en recovery services-valvet är inte längre används, ta bort den att frigöra lagringsutrymme. Ta bort aktiveras först när alla skyddade servrar har tagits bort från valvet.

![Säkerhetskopiering instrumentpanelsuppgifter](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Aviseringar för säkerhetskopiering med Azure backup-agenten:
| Aviseringsnivå | Aviseringar skickas |
| --- | --- |
| Kritiska | för säkerhetskopiering fel, Återställningsfel och uppskjutna delete d.v.s. när någon stoppar skydd med radera data |
| Varning | för säkerhetskopiering har slutförts med varningar (när < 100 filerna säkerhetskopieras inte på grund av problem med skadade och > 1 000 000 filer har säkerhetskopierats) |
| Information | finns för närvarande inga informationsaviseringar för Azure backup agent |

## <a name="manage-backup-alerts"></a>Hantera aviseringar för säkerhetskopiering
Klicka på den **säkerhetskopiering aviseringar** öppna den **säkerhetskopiering aviseringar** menyn och hantera aviseringar.

![Aviseringar om säkerhetskopiering](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

Panelen Backup aviseringar visar antalet:

* kritiska aviseringar som inte matchas med de senaste 24 timmarna
* varningsaviseringar som inte matchas med de senaste 24 timmarna

Klicka på länken om du vill visa den **säkerhetskopiering aviseringar** -menyn med en filtrerad vy av aviseringarna (kritiskt eller varning).

Från menyn säkerhetskopiering aviseringar du:

* Välj lämplig information för att inkludera med aviseringarna.

    ![Välj kolumner](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtrera aviseringar efter allvarlighetsgrad, status, och börja/sluta gånger.

    ![Filtrera aviseringar](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Konfigurera aviseringar för allvarlighetsgrad, frekvens och mottagare, samt aktivera och inaktivera aviseringar.

    ![Filtrera aviseringar](./media/backup-azure-manage-windows-server/configure-notifications.png)

Om **Per avisering** är valt som den **Avisera** frekvens, ingen gruppering eller minskning av e-post inträffar. Alla aviseringar som resulterar i en avisering (standardinställning) och en lösning i e-postmeddelande skickas omedelbart.

Om **timvis sammanfattad** är valt som den **Avisera** frekvens, skickas ett e-postmeddelande till användaren förklarar olösta aviseringar har genererats under den senaste timmen. En lösning i e-postmeddelande skickas i slutet av en timme.

Aviseringar kan skickas för följande allvarlighetsgrader:

* kritiska
* Varning
* information

Du inaktivera avisering med den **inaktivera** knappen i jobbet information menyn. När du klickar på Inaktivera, kan du ange upplösning anteckningar.

Du kan välja de kolumner som du vill ska visas som en del av avisering med den **Välj kolumner** knappen.

> [!NOTE]
> Från den **inställningar** menyn du hanterar aviseringar om säkerhetskopiering genom att välja **övervakning och rapporter > aviseringar och händelser > Säkerhetskopieringsaviseringar** och sedan klicka på **Filter** eller  **Konfigurera meddelanden**.
>
>

## <a name="manage-backup-items"></a>Hantera säkerhetskopiering objekt
Hantera lokala säkerhetskopiorna är nu tillgängligt i hanteringsportalen. I avsnittet säkerhetskopiering på instrumentpanelen i **Säkerhetskopieringsobjekt** panelen visar antalet Säkerhetskopiera objekt som skyddas i valvet.

Klicka på **-mappar** i objekt för säkerhetskopiering i panelen.

![Säkerhetskopiera objekt sida vid sida](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Säkerhetskopieringsobjekt-menyn öppnas med filtret till fil / mapp där du vill se varje säkerhetskopieringen post i listan.

![Säkerhetskopiera objekt](./media/backup-azure-manage-windows-server/backup-item-list.png)

Om du väljer ett specifikt säkerhetskopiering objekt i listan kan du se de viktigaste uppgifterna för objektet.

> [!NOTE]
> Från den **inställningar** menyn du hanterar filer och mappar genom att välja **skyddade objekt > säkerhetskopiering objekt** och sedan välja **-mappar** från nedrullningsbara menyn.
>
>

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Hantera säkerhetskopieringsjobb
Säkerhetskopiering jobb för både lokala (när den lokala servern säkerhetskopiering till Azure) och Azure-säkerhetskopieringar visas i instrumentpanelen.

Panelen Backup jobbet visar antalet jobb i avsnittet säkerhetskopiering på instrumentpanelen:

* pågår
* Det gick inte under de senaste 24 timmarna.

Om du vill hantera säkerhetskopieringsjobb, klickar du på den **säkerhetskopieringsjobb** panelen som öppnar menyn säkerhetskopieringsjobb.

![Säkerhetskopiera objekt från inställningar](./media/backup-azure-manage-windows-server/backup-jobs.png)

Du ändrar informationen i menyn säkerhetskopieringsjobb med den **Välj kolumner** längst upp på sidan.

Använd den **Filter** för att välja mellan filer och mappar och säkerhetskopiering av Azure virtuella datorer.

Om du inte ser dina säkerhetskopierade filer och mappar, klickar du på **Filter** längst upp på sidan och välj **filer och mappar** objekttypen-menyn.

> [!NOTE]
> Från den **inställningar** menyn du hantera säkerhetskopieringsjobb genom att välja **övervakning och rapporter > jobb > säkerhetskopieringsjobb** och sedan välja **-mappar** i listrutan menyn.
>
>

## <a name="monitor-backup-usage"></a>Övervaka användningen av säkerhetskopiering
I avsnittet säkerhetskopiering i instrumentpanelen visar ikonen för användning i säkerhetskopiering lagringsutrymme som förbrukas i Azure. Lagringskvoten tillhandahålls för:

* Lagringskvoten på molnet LRS som är associerade med valvet
* Lagringskvoten på molnet GRS som är associerade med valvet

## <a name="manage-your-production-servers"></a>Hantera produktionsservrarna
Klicka på att hantera produktionsservrarna **inställningar**.

Klicka på under hantera **säkerhetskopiering infrastruktur > produktionsservrar**.

Menyn produktionsservrar listor över alla tillgängliga produktionsservrar. Klicka på en server i listan för att öppna serverinformationen.

![Skyddade objekt](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Öppna Azure Backup-agenten
Öppna den **Microsoft Azure Backup-agenten** (du hittar det genom att söka igenom din dator för *Microsoft Azure Backup*).

![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/snap-in-search.png)

Från den **åtgärder** tillgängliga längst till höger i konsolen backup-agenten du utföra följande hanteringsaktiviteter:

* Registrera Server
* Schema för säkerhetskopiering
* Säkerhetskopiera nu
* Ändra egenskaper för

![Microsoft Azure Backup agent konsolen åtgärder](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Att **återställa Data**, se [återställer filer till en Windows server eller Windows-klientdatorn](backup-azure-restore-windows-server.md).
>
>

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="modify-the-backup-schedule"></a>Ändra schemat för säkerhetskopiering
1. Klicka i Microsoft Azure Backup-agenten **schemalägga säkerhetskopiering**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. I den **guiden schemalägga säkerhetskopiering** lämna den **göra ändringar i säkerhetskopiering objekt eller gånger** alternativ som valts och klickar på **nästa**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Om du vill lägga till eller ändra objekt på den **markerar objekt att säkerhetskopiera** skärmen klickar du på **Lägg till objekt**.

    Du kan också ange **Undantagsinställningar** från den här sidan i guiden. Om du vill undanta filer eller filtyper Läs proceduren för att lägga till [undantagsinställningar](#manage-exclusion-settings).
4. Välj de filer och mappar som du vill säkerhetskopiera och klicka på **okej**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Ange den **Säkerhetskopieringsschemat** och på **nästa**.

    Du kan schemalägga varje dag (högst 3 gånger per dag) eller veckovisa säkerhetskopior.

    ![Windows Server Backup-objekt](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Ange schemat för säkerhetskopiering är beskrivs i detalj i detta [artikel](backup-azure-backup-cloud-as-tape.md).
   >

6. Välj den **bevarandeprincip** för säkerhetskopia och klickar på **nästa**.

    ![Windows Server Backup-objekt](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. På den **bekräftelse** granska informationen och klicka på **Slutför**.
8. När guiden är klar att skapa den **Säkerhetskopieringsschemat**, klickar du på **Stäng**.

    Ändrar skydd måste du bekräfta att säkerhetskopieringar utlöser korrekt genom att gå till den **jobb** fliken och bekräftar att ändringarna visas i alla säkerhetskopieringsjobb.

## <a name="enable-network-throttling"></a>Aktivera begränsning

Azure Backup-agenten ger en begränsning flik där du kan styra hur nätverksbandbredden används när data överfördes. Den här kontrollen kan vara användbart om du behöver säkerhetskopiera data under arbetstid, men inte vill säkerhetskopieringsprocessen störa andra internet-trafik. Begränsning av data gäller transfer för att säkerhetskopiera och återställa aktiviteter.  

Aktivera begränsning:

1. I den **säkerhetskopieringsagenten**, klickar du på **ändra egenskaper för**.
2. På den ** begränsning fliken Välj **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**.

    ![Nätverksbegränsning](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    När du har aktiverat begränsning, ange tillåtna bandbredd för överföring av säkerhetskopierade data under **arbetstid** och **icke-arbetstid**.

    Värdena bandbredd börja på 512 kilobit per sekund (Kbps) och gå upp till 1 023 megabyte per sekund (Mbps). Du kan också ange början och avslutas för **arbetstid**, och vilka dagar i veckan betraktas arbete dagar. Tid utanför arbetstid avsedda betraktas som icke-arbetstid.
3. Klicka på **OK**.

## <a name="manage-exclusion-settings"></a>Hantera undantagsinställningar
1. Öppna den **Microsoft Azure Backup-agenten** (du kan hitta den genom att söka igenom din dator för *Microsoft Azure Backup*).

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. Klicka i Microsoft Azure Backup-agenten **schemalägga säkerhetskopiering**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. I guiden schemalägga säkerhetskopiering lämna den **göra ändringar i säkerhetskopiering objekt eller gånger** alternativ som valts och klickar på **nästa**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Klicka på **undantag inställningar**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Klicka på **Lägg till undantag**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Välj platsen och klicka sedan på **OK**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Lägga till filnamnstillägget i den **filtyp** fältet.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Lägger till en .mp3-tillägg

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Lägg till ett annat filtillägg, klicka på **Lägg till undantag** och ange en annan filtyp (lägga till filnamnstillägget .jpeg).

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. När du har lagt till alla tillägg, klickar du på **OK**.
9. Fortsätta med guiden Schemalägg säkerhetskopiering genom att klicka på **nästa** tills den **bekräftelsesidan**, klicka på **Slutför**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**F1. Status för säkerhetskopieringsjobb visas som slutförd i Azure backup agent, varför inte den hämta visas omedelbart i portal?**

S1. Det avspeglas på Maximal fördröjning på 15 minuter mellan status för säkerhetskopieringsjobb i Azure backup-agenten och Azure portal.

**Q.2 när ett säkerhetskopieringsjobb misslyckas, hur lång tid tar det för att aktivera en varning?**

A.2 en avisering utlöses inom 20 minuter för att Azure säkerhetskopieringen har misslyckats.

**KV. 3. Finns det fall där inte ett e-postmeddelande skickas om meddelanden har konfigurerats?**

S3. Nedan visas fall när meddelandet inte skickas för att minska bruset avisering:

* Om meddelanden konfigureras varje timme och en avisering aktiveras som lösts inom samma timme
* Jobbet har avbrutits.
* Andra säkerhetskopieringsjobbet misslyckades eftersom ursprungliga säkerhetskopiering pågår.

## <a name="troubleshooting-monitoring-issues"></a>Felsökning av problem med övervakning
**Problem:** jobb och/eller varningar från Azure Backup-agenten inte visas i portalen.

**Felsökningssteg:** processen ```OBRecoveryServicesManagementAgent```, skickar jobbet och avisering data till Azure Backup-tjänsten. Ibland den här processen kan fastna eller avstängning.

1. Om du vill kontrollera processen inte körs öppna **Aktivitetshanteraren** och kontrollera om den ```OBRecoveryServicesManagementAgent``` processen körs.
2. Förutsatt att processen inte körs, öppna **Kontrollpanelen** och bläddra i listan över tjänster. Starta eller starta om **Microsoft Azure Recovery Services-hanteringsagenten**.

    Bläddra på loggfilerna på för mer information:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Exempel:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Nästa steg
* [Återställa Windows Server eller Windows-klienten från Azure](backup-azure-restore-windows-server.md)
* Mer information om Azure Backup finns [översikt över Azure-säkerhetskopiering](backup-introduction-to-azure-backup.md)
* Besök den [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933)
