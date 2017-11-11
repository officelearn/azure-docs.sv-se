---
title: "Hantera och övervaka virtuella Azure-datorsäkerhetskopieringar | Microsoft Docs"
description: "Lär dig att hantera och övervaka en virtuell dator i Azure-säkerhetskopieringar"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3d3de79c7f2465791ec68f850df2fc6317880f9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="manage-common-azure-backup-jobs-and-trigger-alerts-in-the-classic-portal"></a>Hantera vanliga Azure Backup-jobb och aviseringar för utlösaren i den klassiska portalen
> [!div class="op_single_selector"]
> * [Hantera Virtuella Azure-säkerhetskopiering](backup-azure-manage-vms.md)
> * [Hantera klassiska VM-säkerhetskopieringar](backup-azure-manage-vms-classic.md)
>
>

Den här artikeln innehåller information om vanliga hantering och övervakning uppgifter för klassiska modellen virtuella datorer som skyddas i Azure.  

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Se [förbereda din miljö för att säkerhetskopiera virtuella datorer i Azure](backup-azure-vms-prepare.md) mer information om hur du arbetar med klassisk distribution modellen virtuella datorer.
>
> [!IMPORTANT]
>Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv.
>
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter den 30 November 2017 kommer du inte att kunna använda PowerShell för att skapa säkerhetskopieringsvalv. **Med 30 November 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.

## <a name="manage-protected-virtual-machines"></a>Hantera skyddade virtuella datorer
Hantera skyddade virtuella datorer:

1. Visa och hantera inställningar för säkerhetskopiering för en virtuell dator klickar du på den **skyddade objekt** fliken.
2. Klicka på namnet på ett skyddat objekt att visa den **säkerhetskopiering information** fliken som innehåller information om den senaste säkerhetskopieringen.

    ![Säkerhetskopiering av virtuell dator](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. Visa och hantera inställningarna för säkerhetskopieringsprincip för en virtuell dator klickar du på den **principer** fliken.

    ![Princip för virtuell dator](./media/backup-azure-manage-vms/manage-policy-settings.png)

    Den **Säkerhetskopieringsprinciper** fliken visas den befintliga principen. Du kan ändra vid behov. Om du behöver skapa en ny genom att klicka på **skapa** på den **principer** sidan. Observera att det inte borde ha associerade med den virtuella datorer om du vill ta bort en princip.

    ![Princip för virtuell dator](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. Du kan få mer information om åtgärder eller status för en virtuell dator den **jobb** sidan. Klicka på ett jobb i listan för att få mer information eller filtrera jobb för en specifik virtuell dator.

    ![Jobb](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>Säkerhetskopiering på begäran för en virtuell dator
Du kan utföra en på-begäran säkerhetskopiering av en virtuell dator när den har konfigurerats för skydd. Om den första säkerhetskopian väntar för den virtuella datorn på begäran-säkerhetskopiering skapar en fullständig kopia av den virtuella datorn i Azure backup-valvet. Om första säkerhetskopieringen har slutförts är på begäran säkerhetskopiering kommer bara skicka ändringar från tidigare säkerhetskopiering till Azure backup valvet dvs. det alltid inkrementellt.

> [!NOTE]
> Kvarhållningsintervallet för en säkerhetskopiering på begäran har angetts till kvarhållningsvärde har angetts för dagliga kvarhållning i säkerhetskopieringsprincip för den virtuella datorn.  
>
>

Göra en på-begäran säkerhetskopiering av en virtuell dator:

1. Navigera till den **skyddade objekt** och välja **Azure virtuella** som **typen** (om det inte redan är vald) och klicka på **Välj** knappen.

    ![VM-typ](./media/backup-azure-manage-vms/vm-type.png)
2. Välj den virtuella dator som du vill göra en på-begäran säkerhetskopieringar och klicka på **Säkerhetskopiera nu** längst ned på sidan.

    ![Säkerhetskopiera nu](./media/backup-azure-manage-vms/backup-now.png)

    Detta skapar en säkerhetskopiering på den valda virtuella datorn. Kvarhållningsintervallet för återställningspunkt som skapats via det här jobbet ska vara detsamma som anges i principen som är associerad med den virtuella datorn.

    ![Skapa säkerhetskopieringsjobb](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > Om du vill visa principen som är associerad med en virtuell dator, öka detaljnivån till virtuell dator i den **skyddade objekt** sidan och gå till fliken princip för säkerhetskopiering.
   >
   >
3. När jobbet har skapats kan du klicka på **visa jobb** knappen i popup-fältet att motsvarande jobbet i sidan jobb.

    ![Säkerhetskopieringsjobbet har skapats](./media/backup-azure-manage-vms/created-job.png)
4. Efter slutförande av jobbet skapas en återställningspunkt som kan användas för att återställa den virtuella datorn. Detta ökar också kolumnvärde för återställning punkt 1 i **skyddade objekt** sidan.

## <a name="stop-protecting-virtual-machines"></a>Sluta skydda virtuella datorer
Du kan välja att stoppa framtida säkerhetskopieringar av en virtuell dator med följande alternativ:

* Behålla säkerhetskopierade data som är associerade med den virtuella datorn i Azure Backup-valvet
* Ta bort säkerhetskopierade data som är associerade med den virtuella datorn

Om du har valt för att behålla säkerhetskopierade data som är associerade med den virtuella datorn kan använda du säkerhetskopierade data för att återställa den virtuella datorn. Prisinformation för sådana virtuella datorer, klickar du på [här](https://azure.microsoft.com/pricing/details/backup/).

Ta bort skyddet för en virtuell dator:

1. Gå till **skyddade objekt** och välja **virtuella Azure-datorn** som filtertyp (om det inte redan är vald) och klicka på **Välj** knappen.

    ![VM-typ](./media/backup-azure-manage-vms/vm-type.png)
2. Välj den virtuella datorn och klicka på **stoppa skydd** längst ned på sidan.

    ![Stoppa skydd](./media/backup-azure-manage-vms/stop-protection.png)
3. Standard Azure Backup inte ta bort säkerhetskopierade data som är kopplade till den virtuella datorn.

    ![Bekräfta stoppskydd](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Markera kryssrutan om du vill ta bort säkerhetskopierade data.

    ![Kryssruta](./media/backup-azure-manage-vms/checkbox.png)

    Välj en orsak till att avbryta säkerhetskopieringen. När det här är valfritt hjälper att ange en orsak Azure Backup för att arbeta med din feedback och prioritera kundscenarier.
4. Klicka på **skicka** knapp för att skicka den **sluta skydda** jobb. Klicka på **visa jobb** att se motsvarande jobbet i **jobb** sidan.

    ![Stoppa skydd](./media/backup-azure-manage-vms/stop-protect-success.png)

    Om du inte har valt **ta bort associerade säkerhetskopieringsdata** alternativ under **stoppa skydd** guiden och sedan efter jobbet har slutförts, skydd status ändras till **skyddet avbrutits**. Data förblir med Azure Backup tills den raderas explicit. Du kan alltid ta bort data genom att välja den virtuella datorn i den **skyddade objekt** sidan och klicka på **ta bort**.

    ![Stoppad skydd](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Om du har valt den **ta bort associerade säkerhetskopieringsdata** alternativ, den virtuella datorn inte en del av den **skyddade objekt** sidan.

## <a name="re-protect-virtual-machine"></a>Skydda virtuella datorn på nytt
Om du inte har valt den **ta bort associera säkerhetskopieringsdata** alternativet i **stoppa skydd**, du kan skydda den virtuella datorn igen genom att följa stegen liknar säkerhetskopierar registrerade virtuella datorer. När skyddade, kommer den här virtuella datorn har säkerhetskopieringsdata behålls innan du avbryter du skyddet och återställningspunkter som skapats efter att skydda.

Efter att skydda den virtuella datorns skyddsstatus ändras till **skyddade** om det finns återställningspunkter före **stoppa skydd**.

  ![Redundansväxlade virtuella datorn](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> Du kan välja en annan princip än principen som virtuella datorn ursprungligen skyddades när skydda den virtuella datorn igen.
>
>

## <a name="unregister-virtual-machines"></a>Avregistrera virtuella datorer
Om du vill ta bort den virtuella datorn från valvet:

1. Klicka på den **AVREGISTRERA** längst ned på sidan.

    ![Inaktivera skydd](./media/backup-azure-manage-vms/unregister-button.png)

    Ett popup-meddelande visas längst ned på skärmen som uppmanar dig att bekräfta. Klicka på **Ja** att fortsätta.

    ![Inaktivera skydd](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopierade data
Du kan ta bort den säkerhetskopiera informationen som är kopplad till en virtuell dator antingen:

* Under stoppa Skyddsjobbet
* När en stoppskydd har jobbet slutförts på en virtuell dator

Ta bort säkerhetskopierade data på en virtuell dator som finns i den *skyddet avbrutits* tillstånd efter slutförande av en **stoppa säkerhetskopiering** jobb:

1. Navigera till den **skyddade objekt** och välja **Azure virtuella** som *typen* och klicka på den **Välj** knappen.

    ![VM-typ](./media/backup-azure-manage-vms/vm-type.png)
2. Välj den virtuella datorn. Den virtuella datorn kommer att vara i **skyddet avbrutits** tillstånd.

    ![Skyddet har stoppats](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. Klicka på den **ta bort** längst ned på sidan.

    ![Ta bort säkerhetskopia](./media/backup-azure-manage-vms/delete-backup.png)
4. I den **ta bort säkerhetskopieringsdata** guiden, Välj en orsak för att ta bort säkerhetskopieringsdata (rekommenderas) och klicka på **skicka**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-manage-vms/delete-backup-data.png)
5. Detta skapar ett jobb för att ta bort säkerhetskopierade data för den valda virtuella datorn. Klicka på **visa jobb** att motsvarande jobbet i sidan jobb.

    ![Data har tagits bort](./media/backup-azure-manage-vms/delete-data-success.png)

    När jobbet är slutfört den post som motsvarar den virtuella datorn tas bort från **skyddade objekt** sidan.

## <a name="dashboard"></a>Instrumentpanel
På den **instrumentpanelen** sidan som du kan granska information om virtuella Azure-datorer, lagring och jobb som är kopplade till dem i de senaste 24 timmarna. Du kan visa status för säkerhetskopiering och eventuella tillhörande säkerhetskopiering fel.

![Instrumentpanel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> Värdena i instrumentpanelen uppdateras var 24: e timme.
>
>

## <a name="auditing-operations"></a>Granska Operations
Azure backup är granska ”åtgärden loggar” av säkerhetskopieringsåtgärder utlöses av kunden, vilket gör det enkelt att se exakt vilka hanteringsåtgärder utfördes på säkerhetskopieringsvalvet. Operations loggar aktivera bra post före och gransknings-och stöd för säkerhetskopieringsåtgärder.

Följande åtgärder loggas i åtgärdsloggar:

* Registrera dig
* Avregistrera
* Konfigurera skydd
* Säkerhetskopiering (både schemalagd samt säkerhetskopiering på begäran via BackupNow)
* Återställ
* Stoppa skydd
* Ta bort säkerhetskopierade data
* Lägg till princip
* Ta bort princip
* Uppdatera principen
* Avbryta jobb

Visa åtgärdsloggar som motsvarar ett säkerhetskopieringsvalv:

1. Gå till **hanteringstjänster** i Azure-portalen och klicka sedan på **Åtgärdsloggar** fliken.

    ![Åtgärdsloggar](./media/backup-azure-manage-vms/ops-logs.png)
2. I filter, väljer **säkerhetskopiering** som *typen* och ange namnet på säkerhetskopieringsvalvet i *tjänstnamnet* och klicka på **skicka**.

    ![Åtgärden loggar Filter](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. Markera alla åtgärder i operations-loggar och klicka **information** att se information som motsvarar en åtgärd.

    ![Information om hämtning av loggar](./media/backup-azure-manage-vms/ops-logs-details.png)

    Den **information guiden** innehåller information om åtgärden utlöses, jobb-Id som den här åtgärden utlöses och starttid för åtgärden.

    ![Åtgärdsinformation](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>Varningsmeddelanden
Du kan hämta anpassade aviseringar för jobben i portalen. Detta uppnås genom att definiera PowerShell-baserade Varningsregler operativa loggar händelser. Vi rekommenderar att du använder *PowerShell version 1.3.0 eller senare*.

Om du vill definiera ett anpassat meddelande för att varna för Säkerhetskopieringsfel ser ett Exempelkommando ut:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: du kan hämta det från Operations Logs popup enligt ovan i avsnittet. ResourceUri i information popup-fönster för en åtgärd är ResourceId ska anges för denna cmdlet.

**OperationName**: detta ska vara i formatet ”Microsoft.Backup/backupvault/<EventName>” om EventName är en av registrera, avregistrera, ConfigureProtection, säkerhetskopiera, återställa, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy

**Status för**: värden är igång, som stöds har lyckats och misslyckats.

**ResourceGroup**: ResourceGroup för resursen som åtgärden utlöses. Du kan hämta den från ResourceId värde. Värdet mellan fält */resourceGroups/* och */providers/* i ResourceId värdet är värdet för ResourceGroup.

**Namnet**: namnet på regeln.

**CustomEmail**: Ange anpassade e-postadressen som du vill skicka aviseringsmeddelanden

**SendToServiceOwners**: det här alternativet skickas aviseringsmeddelanden till alla administratörer och medadministratörer i prenumerationen. Den kan användas i **ny AzureRmAlertRuleEmail** cmdlet

### <a name="limitations-on-alerts"></a>Begränsningar för aviseringar
Händelsebaserat aviseringar genomgår följande begränsningar:

1. Aviseringar aktiveras på alla virtuella datorer i valvet. Du kan anpassa den för att få aviseringar för en specifik uppsättning virtuella datorer i ett säkerhetskopieringsvalv.
2. Den här funktionen är i förhandsgranskningen. [Läs mer](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Du får aviseringar från ”alerts-noreply@mail.windowsazure.com”. För närvarande kan du ändra avsändarens e-post.

## <a name="next-steps"></a>Nästa steg
* [Återställa virtuella Azure-datorer](backup-azure-restore-vms.md)
