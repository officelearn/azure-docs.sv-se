---
title: Ta bort ett Recovery Services-valv i Azure
description: Beskriver hur du tar bort ett Recovery Services-valv.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: raynew
ms.openlocfilehash: 1cc86470b9e45469d633d47121869b3c2dc1b052
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439013"
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Den här artikeln beskrivs hur du tar bort en [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv och ta bort ett valv automatiskt.




## <a name="before-you-start"></a>Innan du börjar

Innan du börjar är det viktigt att förstå att du inte kan ta bort ett Recovery Services-valv som innehåller servrar som registrerats i den eller som innehåller säkerhetskopierade data.


- Ta bort ett valv utan problem, Avregistrerar servrar i den och tar bort vault-data.
- Om du inte vill behålla några data i Recovery Services-valvet och vill ta bort valvet, kan du ta bort valvet automatiskt.
- Om du försöker ta bort ett valv, men inte kan konfigureras fortfarande valvet för att ta emot säkerhetskopierade data.

Lär dig hur du ta bort ett valv, finns i avsnittet [ta bort ett valv från Azure-portalen](#delete-a-vault-from-the-azure-portal). Om avsnittet [ta bort valvet automatiskt](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Om du är osäker på vad som finns i valvet och du måste se till att du tar bort valvet, finns i avsnittet [ta bort valvet beroenden och ta bort valvet](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-the-azure-portal"></a>Ta bort ett valv i Azure Portal

1. Öppna listan över Recovery Services-valv i portalen.
2. Välj det valv som du vill ta bort från listan. Instrumentpanelen för valvet öppnas.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. I instrumentpanelen för valvet klickar du på **ta bort**. Kontrollera att du vill ta bort.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

2. Om det finns beroenden av valv, den **valv fel vid borttagning av** visas: 

    ![Valvborttagningsfel](./media/backup-azure-delete-vault/vault-delete-error.png)

    - Följ dessa instruktioner för att ta bort beroenden innan du tar bort valvet, granska
    - [Följ de här instruktionerna](#delete-the-recovery-services-vault-by-force) du använder PowerShell för att ta bort valvet automatiskt. 

## <a name="delete-the-recovery-services-vault-by-force"></a>Ta bort Recovery Services-valvet automatiskt

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda PowerShell för att ta bort ett Recovery Services-valv automatiskt. Det innebär att valvet och alla associerade säkerhetskopieringsdata raderas. 

> [!Warning]
> När du använder PowerShell för att ta bort ett Recovery Services-valv, måste du kontrollera att du vill ta bort alla säkerhetskopierade data i valvet.
>

Ta bort ett Recovery Services-valv:

1. Logga in på Azure-prenumerationen med den `Connect-AzAccount` , och följer den på skärmen riktningar.

   ```powershell
    Connect-AzAccount
   ```
2. Första gången du använder Azure Backup, måste du registrera Azure Recovery Services-providern i din prenumeration med [registrera AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Öppna ett PowerShell-fönster med administratörsbehörighet.
4. Använd `Set-ExecutionPolicy Unrestricted` att ta bort eventuella begränsningar.
5. Kör följande kommando för att hämta Azure Resource Manager-klientpaketet från chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Använd följande kommando för att installera Azure Resource Manager API-klient.

   `choco.exe install armclient`

7. Samla in prenumerations-ID och resurs gruppnamn för valvet som du vill ta bort i Azure-portalen.
8. I PowerShell kör du följande kommando med ditt prenumerations-ID, resursgruppnamn och valvnamnet. När du kör kommandot, tar den bort valvet och alla beroenden.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Om valvet inte är tom, får du felmeddelandet ”går inte att ta bort valvet eftersom det finns befintliga resurser i det här valvet”. Om du vill ta bort en behållare i ett valv, gör du följande:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Logga in till din prenumeration i Azure-portalen och kontrollera att valvet har tagits bort.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Ta bort valvet beroenden och ta bort valv

Du kan manuellt ta bort valvet beroenden, enligt följande:

- I den **Säkerhetskopieringsobjekt** -menyn, ta bort beroenden:
    - Säkerhetskopieringar i Azure Storage (Azure Files)
    - SQL Server i Virtuella Azure-säkerhetskopieringar
    - Säkerhetskopior av virtuella Azure-datorer
- I den **infrastruktur för säkerhetskopiering** menyn, ta bort beroenden:
    - Säkerhetskopior av Microsoft Azure Backup Server (MABS)
    - System Center DPM-säkerhetskopieringar

![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

### <a name="remove-backup-items"></a>Ta bort objekt att säkerhetskopiera

Den här proceduren ger ett exempel som visar hur du tar bort säkerhetskopierade data från Azure Files.

1. Klicka på **Säkerhetskopiera objekt** > **Azure Storage (Azure Files)**

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Högerklicka på varje Azure Files-objekt om du vill ta bort och klicka på **stoppa säkerhetskopiering**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/stop-backup-item.png)


3. I **stoppa säkerhetskopiering** > **Välj ett alternativ**väljer **ta bort säkerhetskopieringsdata**.
4. Skriv namnet på objektet och klicka på **stoppa säkerhetskopiering**. 
   - Detta verifierar att du vill ta bort objektet.
   - Den **stoppa säkerhetskopiering** knappen aktiveras när du har kontrollerat.
   - Om du behålla och ta inte bort data, kan du inte ta bort valvet.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Du kan också ange en orsak till varför du tar bort data och lägga till kommentarer.
6. Om du vill kontrollera att ta bort jobbet har slutförts, kontrollera den Azure-meddelanden ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png).
7. När jobbet har slutförts kan tjänsten skickar ett meddelande: **säkerhetskopieringen har stoppats och säkerhetskopierade data skedde**.
8. När du tar bort ett objekt i listan på den **Säkerhetskopieringsobjekt** -menyn klickar du på **uppdatera** objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Ta bort backup infrastrukturservrar

1. I valvets instrumentpanel klickar du på **infrastruktur för säkerhetskopiering**.
2. Klicka på **Säkerhetskopieringshanteringsservrar** att visa servrar. 

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Högerklicka på objektet > **ta bort**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . I **stoppa säkerhetskopiering** > **Välj ett alternativ**väljer **ta bort säkerhetskopieringsdata**.
4. Skriv namnet på objektet och klicka på **stoppa säkerhetskopiering**. 
   - Detta verifierar att du vill ta bort objektet.
   - Den **stoppa säkerhetskopiering** knappen aktiveras när du har kontrollerat.
   - Om du behålla och ta inte bort data, kan du inte ta bort valvet.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Du kan också ange en orsak till varför du tar bort data och lägga till kommentarer.
6. Om du vill kontrollera att ta bort jobbet har slutförts, kontrollera den Azure-meddelanden ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png).
7. När jobbet har slutförts kan tjänsten skickar ett meddelande: **säkerhetskopieringen har stoppats och säkerhetskopierade data skedde**.
8. När du tar bort ett objekt i listan på den **Säkerhetskopieringsobjekt** -menyn klickar du på **uppdatera** objekt i valvet.


### <a name="remove-azure-backup-agent-recovery-points"></a>Ta bort återställningspunkter för Azure Backup agent

1. I valvets instrumentpanel klickar du på **infrastruktur för säkerhetskopiering**.
2. Klicka på **Säkerhetskopieringshanteringsservrar** att visa infrastrukturservrar.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. I den **skyddade servrar** klickar du på Azure Backup-agenten.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Klicka på servern i listan över servrar som skyddas med hjälp av Azure Backup-agenten.

    ![Välj den specifika skyddade servern](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. På instrumentpanelen för valda servern klickar du på **ta bort**.

    ![ta bort den valda servern](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. På den **ta bort** menyn och Skriv namnet på objektet och klicka på **ta bort**.
   - Detta verifierar att du vill ta bort objektet.
   - Den **stoppa säkerhetskopiering** knappen aktiveras när du har kontrollerat.
   - Om du behålla och ta inte bort data, kan du inte ta bort valvet.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Du kan också ange en orsak till varför du tar bort data och lägga till kommentarer.
8. Om du vill kontrollera att ta bort jobbet har slutförts, kontrollera den Azure-meddelanden ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png).
7. När jobbet har slutförts kan tjänsten skickar ett meddelande: **säkerhetskopieringen har stoppats och säkerhetskopierade data skedde**.
8. När du tar bort ett objekt i listan på den **Säkerhetskopieringsobjekt** -menyn klickar du på **uppdatera** objekt i valvet.



### <a name="delete-the-vault-after-removing-dependencies"></a>Ta bort valvet när du tar bort beroenden

1. När alla beroenden har tagits bort, bläddra till den **Essentials** fönstret i menyn för valvet.

    - Det bör inte finnas någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas.
    - Om objekt visas fortfarande i valvet kan du ta bort dem.

2. När det finns inga fler objekt i valvet, på instrumentpanelen för valvet klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Kontrollera att du vill ta bort valvet, klicka på **Ja**. Valvet tas bort och portalen återgår till den **New** tjänstens meny.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Vad händer om jag stoppa säkerhetskopieringen men spara dina data?

Om du stoppa säkerhetskopieringen men behålla data, av misstag, måste du ta bort säkerhetskopierade data enligt beskrivningen i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services-valv.
