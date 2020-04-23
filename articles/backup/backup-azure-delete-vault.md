---
title: Ta bort ett Microsoft Azure Recovery Services-valv
description: I den här artikeln kan du läsa om hur du tar bort beroenden och sedan tar bort ett Azure Backup Recovery Services-valv.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025143"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Ta bort ett Azure Backup Recovery Services-valv

I den här artikeln beskrivs hur du tar bort ett [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv.

## <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett Recovery Services-valv som har beroenden, till exempel skyddade servrar eller servrar för säkerhetskopieringshantering, som är associerade med det.

- Valv som innehåller säkerhetskopierade data kan inte tas bort (även om du har stoppat skyddet men behållit säkerhetskopieringsdata).

- Om du tar bort ett valv som innehåller beroenden visas följande meddelande:

  ![Ta bort valvfelet.](./media/backup-azure-delete-vault/error.png)

- Om du tar bort ett lokalt skyddat objekt från en portal som innehåller beroenden visas ett varningsmeddelande:

  ![Ta bort det skyddade serverfelet.](./media/backup-azure-delete-vault/error-message.jpg)

- Om säkerhetskopieringsobjekt är i mjukt borttaget visas varningsmeddelandet nedan och du måste vänta tills de tas bort permanent. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Ta bort valvfelet.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Valv som har registrerade lagringskonton kan inte tas bort. Mer information om hur du avregistrerar kontot finns i [Avregistrera ett lagringskonto](manage-afs-backup.md#unregister-a-storage-account).
  
Om du vill ta bort valvet väljer du det scenario som matchar din konfiguration och följer de rekommenderade stegen:

Scenario | Åtgärder för att ta bort beroenden för att ta bort valvet |
-- | --
Jag har lokala filer och mappar skyddade med hjälp av Azure Backup agent, säkerhetskopiera till Azure | Utför stegen i [Ta bort säkerhetskopieringsobjekt från MARS-hanteringskonsolen](#delete-backup-items-from-the-mars-management-console)
Jag har lokala datorer som skyddas med hjälp av MABS (Microsoft Azure Backup Server) eller DPM (System Center Data Protection Manager) till Azure | Utför stegen i [Ta bort säkerhetskopior från MABS-hanteringskonsolen](#delete-backup-items-from-the-mabs-management-console)
Jag har skyddade objekt i molnet (till exempel en virtuell laaS-dator eller en Azure Files-resurs)  | Utföra stegen i [Ta bort skyddade objekt i molnet](#delete-protected-items-in-the-cloud)
Jag har skyddade objekt både lokalt och i molnet | Utför stegen i alla följande avsnitt i följande ordning: <br> 1. [Ta bort skyddade objekt i molnet](#delete-protected-items-in-the-cloud)<br> 2. [Ta bort säkerhetskopieringsobjekt från MARS-hanteringskonsolen](#delete-backup-items-from-the-mars-management-console) <br> 3. [Ta bort säkerhetskopieringsobjekt från MABS Management-konsolen](#delete-backup-items-from-the-mabs-management-console)
Jag har inga skyddade objekt lokalt eller i molnet. Jag får dock fortfarande felmeddelandet borttagning av Arkiv | Utför stegen i [Ta bort recovery services-valvet med hjälp av Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> Kontrollera att det inte finns några lagringskonton registrerade i valvet. Mer information om hur du avregistrerar kontot finns i [Avregistrera ett lagringskonto](manage-afs-backup.md#unregister-a-storage-account).

## <a name="delete-protected-items-in-the-cloud"></a>Ta bort skyddade objekt i molnet

Läs först avsnittet **[Innan du börjar](#before-you-start)** för att förstå beroenden och borttagningsprocessen för valvet.

Så här stoppar du skyddet och tar bort säkerhetskopierade data:

1. Från portalen går du till **Valvet**för Återställningstjänster och går sedan till **Säkerhetskopieringsobjekt**. Välj sedan skyddade objekt i molnet (till exempel Virtuella Azure-datorer, Azure Storage [Azure Files-tjänsten] eller SQL Server på virtuella Azure-datorer).

    ![Välj säkerhetskopia.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Högerklicka för att markera säkerhetskopian. Beroende på om säkerhetskopieringsobjektet är skyddat eller inte visas antingen fönstret **Stoppa säkerhetskopiering** eller fönstret **Ta bort säkerhetskopierade data** på menyn.

    - Om fönstret **Stoppa säkerhetskopiering** visas väljer du **Ta bort säkerhetskopierade data** på den nedrullningsbara menyn. Ange namnet på säkerhetskopieringsobjektet (det här fältet är skiftlägeskänsligt) och välj sedan en orsak på den nedrullningsbara menyn. Skriv in dina kommentarer, om du har några. Välj sedan **Stoppa säkerhetskopiering**.

        ![Fönstret Stoppa säkerhetskopiering.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Om fönstret **Ta bort säkerhetskopierade data** visas anger du namnet på säkerhetskopieringsobjektet (det här fältet är skiftlägeskänsligt) och väljer sedan en orsak på den nedrullningsbara menyn. Skriv in dina kommentarer, om du har några. Välj sedan **Ta bort**.

         ![Fönstret Ta bort säkerhetskopierade data.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Kontrollera **ikonen Meddelande:** ![Ikonen Meddelande.](./media/backup-azure-delete-vault/messages.png) När processen är klar visas följande meddelande: *Stoppa säkerhetskopiering och ta bort säkerhetskopierade data för "* Säkerhetskopieringsobjekt *"*. *Åtgärden har slutförts*.
4. Välj **Uppdatera** på menyn **Säkerhetsobjekt** för säkerhetskopiering om du vill kontrollera att säkerhetskopieringsobjektet har tagits bort.

      ![Sidan Ta bort säkerhetskopieringsobjekt.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Ta bort skyddade objekt lokalt

Läs först avsnittet **[Innan du börjar](#before-you-start)** för att förstå beroenden och borttagningsprocessen för valvet.

1. Välj **Infrastruktur för säkerhetskopiering på**instrumentpanelen i valvet .
2. Beroende på ditt lokala scenario väljer du något av följande alternativ:

      - För MARS väljer du **Skyddade servrar** och sedan Azure **Backup Agent**. Välj sedan den server som du vill ta bort.

        ![För MARS väljer du valvet för att öppna instrumentpanelen.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - För MABS eller DPM väljer du **Servrar för säkerhetskopieringshantering**. Välj sedan den server som du vill ta bort.

          ![För MABS väljer du valvet för att öppna instrumentpanelen.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **Fönstret Ta bort** visas med ett varningsmeddelande.

     ![Borttagningsfönstret.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Läs varningsmeddelandet och instruktionerna i kryssrutan Medgivande.
    > [!NOTE]
    >- Om den skyddade servern synkroniseras med Azure-tjänster och säkerhetskopieringsobjekt finns, visar kryssrutan medgivande antalet beroende säkerhetskopieringsobjekt och länken för att visa säkerhetskopieringsobjekten.
    >- Om den skyddade servern inte synkroniseras med Azure-tjänster och säkerhetskopieringsobjekt finns, visar kryssrutan medgivande endast antalet säkerhetskopieringsobjekt.
    >- Om det inte finns några säkerhetskopior kommer kryssrutan medgivande att be om borttagning.

4. Markera kryssrutan Medgivande och välj sedan **Ta bort**.

5. Kontrollera **ikonen** ![Meddelande ta](./media/backup-azure-delete-vault/messages.png)bort säkerhetskopierade data . När åtgärden är klar visar tjänsten meddelandet: *Stoppa säkerhetskopiering och ta bort säkerhetskopierade data för "Säkerhetskopieringsobjekt".* *Åtgärden har slutförts*.
6. Välj **Uppdatera** på menyn **Säkerhetsobjekt** för säkerhetskopiering om du vill kontrollera att säkerhetskopieringsobjektet tas bort.

När den här processen är klar kan du ta bort säkerhetskopiorna från hanteringskonsolen:

- [Ta bort säkerhetskopieringsobjekt från MARS-hanteringskonsolen](#delete-backup-items-from-the-mars-management-console)
- [Ta bort säkerhetskopieringsobjekt från MABS-hanteringskonsolen](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Ta bort säkerhetskopieringsobjekt från MARS-hanteringskonsolen

1. Öppna HANTERINGSKONSOLEN FÖR MARS, gå till **åtgärdsfönstret** och välj **Schemalägg säkerhetskopiering**.
2. På sidan **Ändra eller Stoppa en schemalagd säkerhetskopiering** väljer du Sluta använda det här **säkerhetskopieringsschemat och tar bort alla lagrade säkerhetskopior**. Välj sedan **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhetskopia.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Välj **Slutför**på sidan **Stoppa en schemalagd säkerhetskopiering** .

    ![Stoppa en schemalagd säkerhetskopia.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Du uppmanas att ange en säkerhets-PIN -kod (personnummer), som du måste generera manuellt. Det gör du genom att logga in på Azure-portalen.
5. Gå till**Settings** > **Egenskaper**för inställningar för **återställningstjänsters valv** > .
6. Under **Säkerhets-PIN**väljer du **Generera**. Kopiera den här PIN-koden. PIN-koden är giltig i endast fem minuter.
7. Klistra in PIN-koden i hanteringskonsolen och välj sedan **OK**.

    ![Generera en säkerhets-PIN-kod.](./media/backup-azure-delete-vault/security-pin.png)

8. På sidan **Ändra för säkerhetskopiering** visas följande meddelande: *Borttagna säkerhetskopierade data sparas i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent.*  

    ![Ta bort infrastrukturen för säkerhetskopiering.](./media/backup-azure-delete-vault/deleted-backup-data.png)

När du har tagit bort de lokala säkerhetskopieringsobjekten följer du nästa steg från portalen.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Ta bort säkerhetskopieringsobjekt från MABS-hanteringskonsolen

Det finns två metoder som du kan använda för att ta bort säkerhetskopieringsobjekt från MABS-hanteringskonsolen.

#### <a name="method-1"></a>Metod 1

Så här stoppar du skyddet och tar bort säkerhetskopierade data:

1. Öppna DPM-administratörskonsolen och välj sedan **Skydd** i navigeringsfältet.
2. Markera den skyddsgruppmedlem som du vill ta bort i visningsfönstret. Högerklicka för att välja alternativet **Stoppa skydd för gruppmedlemmar.**
3. I dialogrutan **Stoppa skydd** väljer du Ta bort **skyddade data**och markerar sedan kryssrutan Ta bort **lagring online.** Välj sedan **Stoppskydd**.

    ![Välj Ta bort skyddade data från fönstret Stoppa skydd.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Den skyddade medlemsstatusen ändras till *Inaktiv replik tillgänglig*.

4. Högerklicka på den inaktiva skyddsgruppen och välj **Ta bort inaktivt skydd**.

    ![Ta bort inaktivt skydd.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Markera kryssrutan **Ta bort onlinelagring i** fönstret Ta bort **inaktivt skydd** och välj sedan **OK**.

    ![Ta bort onlinelagring.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metod 2

Öppna **MABS-hanteringskonsolen.** Avmarkera kryssrutan Jag vill **ha onlineskydd** under **Välj dataskyddsmetod.**

  ![Välj dataskyddsmetod.](./media/backup-azure-delete-vault/data-protection-method.png)

När du har tagit bort de lokala säkerhetskopieringsobjekten följer du nästa steg från portalen.

## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

1. När alla beroenden har tagits bort bläddrar du till fönstret **Essentials** på arkivmenyn.
2. Kontrollera att det inte finns några säkerhetskopieringsobjekt, säkerhetskopieringshanteringsservrar eller replikerade objekt i listan. Om objekt fortfarande visas i valvet läser du avsnittet [Innan du startar.](#before-you-start)

3. När det inte finns fler objekt i valvet väljer du **Ta bort** på instrumentpanelen för valvet.

    ![Välj Ta bort på instrumentpanelen för valvet.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Välj **Ja** för att kontrollera att du vill ta bort valvet. Valvet tas bort. Portalen återgår till menyn **Ny** tjänst.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Ta bort valvet för återställningstjänster med PowerShell

Läs först avsnittet **[Innan du börjar](#before-you-start)** för att förstå beroenden och borttagningsprocessen för valvet.

Så här stoppar du skyddet och tar bort säkerhetskopierade data:

- Om du använder SQL i Azure-virtuella datorer säkerhetskopiering och aktiverat automatiskt skydd för SQL-instanser inaktiverar du först det automatiska skyddet.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Läs mer](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) om hur du inaktiverar skydd för ett Azure Backup-skyddat objekt.

- Stoppa skyddet och ta bort data för alla säkerhetskopierade skyddade objekt i molnet (t.ex. laaS VM, Azure File Share etc.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Läs mer](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) om att inaktivera skydd för ett säkerhetskopierat skyddat objekt.

- För lokala filer och mappar som skyddas med stöd av AZURE Backup Agent (MARS) säkerhetskopiera till Azure använder du följande PowerShell-kommando för att ta bort säkerhetskopierade data från varje MARS PowerShell-modul:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post där följande fråga skulle visas:

    *Microsoft Azure Backup Vill du ta bort den här säkerhetskopieringsprincipen? Borttagna säkerhetskopieringsdata kommer att behållas i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent. <br/> [Y] Ja [A] Ja till alla [N] Nej [L] Nej till alla [S] Suspend [?] Hjälp (standard är "Y"):*

- För lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM till Azure (System Center Data Protection Manager) använder du följande kommando för att ta bort säkerhetskopierade data i Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post där följande fråga skulle visas:

   *Microsoft Azure-säkerhetskopia* Vill du ta bort den här säkerhetskopieringsprincipen? Borttagna säkerhetskopieringsdata kommer att behållas i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent. <br/>
   [Y] Ja [A] Ja till alla [N] Nej [L] Nej till alla [S] Suspend [?] Hjälp (standard är "Y"):*

När du har tagit bort säkerhetskopierade data avmarkerar du lokala behållare och hanteringsservrar.

- För lokala filer och mappar som skyddas med hjälp av Azure Backup Agent (MARS) säkerhetskopiera till Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Läs mer](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) om att avregistrera en Windows-server eller annan behållare från valvet.

- För lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM till Azure (System Center Data Protection Manage:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Läs mer](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) om att avregistrera en behållare för säkerhetskopieringshantering från valvet.

När du har tagit bort säkerhetskopierade data permanent och avregistrerat alla behållare fortsätter du att ta bort valvet.

Så här tar du bort ett Recovery Services-valv:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Läs mer](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) om hur du tar bort ett valv för återställningstjänster.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Ta bort recovery services-valvet med CLI

Läs först avsnittet **[Innan du börjar](#before-you-start)** för att förstå beroenden och borttagningsprocessen för valvet.

> [!NOTE]
> För närvarande stöder Azure Backup CLI hantera endast Azure VM-säkerhetskopior, så följande kommando för att ta bort valvet fungerar bara om valvet innehåller Azure VM-säkerhetskopior. Du kan inte ta bort ett valv med Azure Backup CLI, om valvet innehåller något säkerhetskopieringsobjekt av annan typ än virtuella Azure-datorer.

Om du vill ta bort det befintliga valvet för återställningstjänster utför du nedanstående:

- Så här stoppar du skyddet och tar bort säkerhetskopierade data

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Mer information finns i den här [artikeln](/cli/azure/backup/protection#az-backup-protection-disable).

- Ta bort ett befintligt valv för återställningstjänster:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Mer information finns i den här [artikeln](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Ta bort valvet för Återställningstjänster med hjälp av Azure Resource Manager

Det här alternativet för att ta bort valvet för Återställningstjänster rekommenderas endast om alla beroenden tas bort och du fortfarande får *felet borttagning*av Arkiv . Prova något eller alla av följande tips:

- Kontrollera att det inte finns några säkerhetskopieringsobjekt, säkerhetskopieringshanteringsservrar eller replikerade objekt i listan från fönstret **Essentials** på arkivmenyn. Om det finns säkerhetskopieringsobjekt läser du avsnittet [Innan du startar.](#before-you-start)
- Försök [ta bort valvet från portalen](#delete-the-recovery-services-vault) igen.
- Om alla beroenden tas bort och du fortfarande får *felet borttagning*av arkiv använder du verktyget ARMClient för att utföra följande steg (efter anteckningen).

1. Gå till [chocolatey.org](https://chocolatey.org/) för att ladda ner och installera Chocolatey. Installera sedan ARMClient genom att köra följande kommando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Logga in på ditt Azure-konto och kör sedan följande kommando:

    `ARMClient.exe login [environment name]`

3. Samla prenumerations-ID: t och resursgruppsnamnet för det valv som du vill ta bort i Azure-portalen.

Mer information om kommandot ARMClient finns i [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Använda Azure Resource Manager-klienten för att ta bort ett Recovery Services-valv

1. Kör följande kommando med hjälp av prenumerations-ID, resursgruppsnamn och valvnamn. Om du inte har några beroenden tas valvet bort när du kör följande kommando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Om valvet inte är tomt visas följande felmeddelande: *Arkiv kan inte tas bort eftersom det finns befintliga resurser i det här valvet.* Om du vill ta bort ett skyddat objekt eller en behållare i ett valv kör du följande kommando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Kontrollera att valvet tas bort i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Recovery Services-valv](backup-azure-recovery-services-vault-overview.md)<br/>
[Lär dig mer om övervakning och hantering av Recovery Services-valv](backup-azure-manage-windows-server.md)
