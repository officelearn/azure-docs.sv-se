---
title: "Hantera Azure-säkerhetskopieringsvalv och servrar Azure med hjälp av den klassiska distributionsmodellen | Microsoft Docs"
description: "Använd den här självstudiekursen för att lära dig att hantera Azure-säkerhetskopieringsvalv och servrar."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: markgal;
ms.openlocfilehash: 91451b2cdc42ed05ef7c1ba9c66ad5b4b45dd788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Hantera Azure Backup Vault och servrar med hjälp av den klassiska distributionsmodellen
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-manage-windows-server.md)
> * [Klassisk](backup-azure-manage-windows-server-classic.md)
>
>

I den här artikeln hittar du en översikt över säkerhetskopiering hanteringsuppgifter som är tillgängliga via den klassiska Azure-portalen och Microsoft Azure Backup-agenten.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

> [!IMPORTANT]
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter den 15 oktober 2017 kan du inte längre använda PowerShell för att skapa säkerhetskopieringsvalv. **Från den 1 november 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

## <a name="management-portal-tasks"></a>Portalen hanteringsuppgifter
1. Logga in på den [hanteringsportalen](https://manage.windowsazure.com).
2. Klicka på **återställningstjänster**, klicka på namnet på valvet för att visa sidan Snabbstart.

    ![Återställningstjänster](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Du kan se de tillgängliga hanteringsuppgifterna genom att välja alternativen överst på sidan Snabbstart.

![Hantera flikar](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Instrumentpanel
Välj **instrumentpanelen** att se en översikt för användning för servern. Den **användningsöversikten** omfattar:

* Antal Windows-servrar som är registrerade i molnet
* Antalet virtuella datorer i Azure skyddad i molnet
* Det totala lagringsutrymmet som används i Azure
* Status för senaste jobb

Längst ned i instrumentpanelen kan du utföra följande uppgifter:

* **Hantera certifikat** – om ett certifikat som användes för att registrera servern och sedan använda detta för att uppdatera certifikatet. Om du använder autentiseringsuppgifter för valv kan inte använda **hantera certifikat**.
* **Ta bort** -tar bort det aktuella säkerhetskopieringsvalvet. Om ett säkerhetskopieringsvalv används inte längre kan du ta bort den att frigöra lagringsutrymme. **Ta bort** aktiveras först när alla registrerade servrar har tagits bort från valvet.

![Säkerhetskopiering instrumentpanelsuppgifter](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Registrerade artiklar
Välj **registrerade objekt** att visa namnen på de servrar som är registrerade för det här valvet.

![Registrerade artiklar](./media/backup-azure-manage-windows-server-classic/registered-items.png)

Den **typen** filtrera standardvärden för virtuell dator i Azure. Om du vill visa namnen på de servrar som är registrerade för valvet, Välj **Windows server** från nedrullningsbara menyn.

Härifrån kan du utföra följande uppgifter:

* **Tillåt omregistrering** – när det här alternativet väljs för en server som du kan använda den **Registreringsguiden** i Microsoft Azure Backup agent lokalt för att registrera servern med backup valvet en andra gång. Du kan behöva registrera igen på grund av ett fel i certifikatet eller om en server som var tvungen att återskapas.
* **Ta bort** -tar bort en server från säkerhetskopieringsvalvet. Alla lagrade data som är associerade med servern tas bort omedelbart.

    ![Registrerade artiklar uppgifter](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Skyddade objekt
Välj **skyddade objekt** att visa objekt som har säkerhetskopierats från servrar.

![Skyddade objekt](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Konfigurera
Från den **konfigurera** fliken som du kan välja det lämpliga lagringsalternativet för redundans. Den bästa tidpunkten att välja lagringsalternativ för redundans är rätt när du har skapat ett valv och alla datorer som är registrerade på den.

> [!WARNING]
> När ett objekt har registrerats i valvet, redundans lagringsalternativ är låst och kan inte ändras.
>
>

![Konfigurera](./media/backup-azure-manage-windows-server-classic/configure.png)

Se den här artikeln för mer information om [lagring redundans](../storage/common/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Microsoft Azure Backup agentuppgifter
### <a name="console"></a>Konsolen
Öppna den **Microsoft Azure Backup-agenten** (du kan hitta den genom att söka igenom din dator för *Microsoft Azure Backup*).

![Backup-agenten](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Från den **åtgärder** tillgängliga längst till höger i konsolen backup-agenten kan du utföra följande hanteringsaktiviteter:

* Registrera Server
* Schema för säkerhetskopiering
* Säkerhetskopiera nu
* Ändra egenskaper för

![Konsolen agentåtgärder](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> Att **återställa Data**, se [återställer filer till en Windows server eller Windows-klientdatorn](backup-azure-restore-windows-server.md).
>
>

### <a name="modify-an-existing-backup"></a>Ändra en befintlig säkerhetskopia
1. Klicka i Microsoft Azure Backup-agenten **schemalägga säkerhetskopiering**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. I den **guiden schemalägga säkerhetskopiering** lämna den **göra ändringar i säkerhetskopiering objekt eller gånger** alternativ som valts och klickar på **nästa**.

    ![Ändra en schemalagd säkerhetskopiering](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Om du vill lägga till eller ändra objekt på den **markerar objekt att säkerhetskopiera** skärmen klickar du på **Lägg till objekt**.

    Du kan också ange **Undantagsinställningar** från den här sidan i guiden. Om du vill undanta filer eller filtyper Läs proceduren för att lägga till [undantagsinställningar](#exclusion-settings).
4. Välj de filer och mappar som du vill säkerhetskopiera och klicka på **okej**.

    ![Lägg till artiklar](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Ange den **Säkerhetskopieringsschemat** och på **nästa**.

    Du kan schemalägga varje dag (högst 3 gånger per dag) eller veckovisa säkerhetskopior.

    ![Ange schema för säkerhetskopiering](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Ange schemat för säkerhetskopiering är beskrivs i detalj i detta [artikel](backup-azure-backup-cloud-as-tape.md).
   >
   >
6. Välj den **bevarandeprincip** för säkerhetskopia och klickar på **nästa**.

    ![Välj din bevarandeprincip](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. På den **bekräftelse** granska informationen och klicka på **Slutför**.
8. När guiden är klar att skapa den **Säkerhetskopieringsschemat**, klickar du på **Stäng**.

    Ändrar skydd måste du bekräfta att säkerhetskopieringar utlöser korrekt genom att gå till den **jobb** fliken och bekräftar att ändringarna visas i alla säkerhetskopieringsjobb.

### <a name="enable-network-throttling"></a>Aktivera begränsning
Azure Backup-agenten ger en begränsning flik där du kan styra hur nätverksbandbredden används när data överfördes. Den här kontrollen kan vara användbart om du behöver säkerhetskopiera data under arbetstid, men inte vill säkerhetskopieringsprocessen störa andra internet-trafik. Begränsning av data gäller transfer för att säkerhetskopiera och återställa aktiviteter.  

Aktivera begränsning:

1. I den **säkerhetskopieringsagenten**, klickar du på **ändra egenskaper för**.
2. Välj den **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder** kryssrutan.

    ![Nätverksbegränsning](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. När du har aktiverat begränsning, ange tillåtna bandbredd för överföring av säkerhetskopierade data under **arbetstid** och **icke-arbetstid**.

    Värdena bandbredd börja på 512 kilobit per sekund (Kbps) och gå upp till 1 023 megabyte per sekund (Mbps). Du kan också ange början och avslutas för **arbetstid**, och vilka dagar i veckan betraktas arbete dagar. Tid utanför arbetstid avsedda betraktas som icke-arbetstid.
4. Klicka på **OK**.

## <a name="exclusion-settings"></a>Undantagsinställningar
1. Öppna den **Microsoft Azure Backup-agenten** (du kan hitta den genom att söka igenom din dator för *Microsoft Azure Backup*).

    ![Öppna säkerhetskopieringsagent](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. Klicka i Microsoft Azure Backup-agenten **schemalägga säkerhetskopiering**.

    ![Schemalägga en Windows Serverbackup](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. I guiden schemalägga säkerhetskopiering lämna den **göra ändringar i säkerhetskopiering objekt eller gånger** alternativ som valts och klickar på **nästa**.

    ![Ändra ett schema](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Klicka på **undantag inställningar**.

    ![Välj objekt som ska undantas](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Klicka på **Lägg till undantag**.

    ![Lägg till undantag](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Välj platsen och klicka sedan på **OK**.

    ![Välj en plats för undantag](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Lägga till filnamnstillägget i den **filtyp** fältet.

    ![Exkludera efter filtyp](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Lägger till en .mp3-tillägg

    ![Exempel på en typ.](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Lägg till ett annat filtillägg, klicka på **Lägg till undantag** och ange en annan filtyp (lägga till filnamnstillägget .jpeg).

    ![Exempel på en annan typ](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. När du har lagt till alla tillägg, klickar du på **OK**.
9. Fortsätta med guiden Schemalägg säkerhetskopiering genom att klicka på **nästa** tills den **bekräftelsesidan**, klicka på **Slutför**.

    ![Bekräftelse av undantag](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Nästa steg
* [Återställa Windows Server eller Windows-klienten från Azure](backup-azure-restore-windows-server.md)
* Mer information om Azure Backup finns [översikt över Azure-säkerhetskopiering](backup-introduction-to-azure-backup.md)
* Besök den [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933)
