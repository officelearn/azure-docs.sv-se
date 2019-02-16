---
title: Ta bort ett Recovery Services-valv i Azure.
description: Den här artikeln förklarar hur du tar bort ett Recovery Services-valv. Artikeln innehåller åtgärder för felsökning när du försöker ta bort ett valv, men det går inte att.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d15e3773a9b6e3dceb0799d206070730675c211d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310974"
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Den här artikeln beskriver hur du ta bort alla objekt från ett Recovery Services-valv och tar bort den. Du kan inte ta bort ett Recovery Services-valv om den är registrerad på en server och innehåller säkerhetskopierade data. Om du försöker ta bort ett valv, men inte kan konfigureras fortfarande valvet för att ta emot säkerhetskopierade data.

Lär dig hur du ta bort ett valv, finns i avsnittet [ta bort ett valv från Azure-portalen](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Om du inte vill behålla några data i Recovery Services-valvet och vill ta bort valvet, finns i avsnittet [ta bort valvet automatiskt](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Om du är osäker på vad som finns i valvet och du måste se till att du tar bort valvet, finns i avsnittet [ta bort valvet beroenden och ta bort valvet](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Ta bort ett valv från Azure-portalen

Om du redan har Recovery Services-valvet öppnar du vidare till det andra steget.

1. Öppna Azure-portalen och öppna det valv som du vill ta bort från instrumentpanelen.

   Om du inte har Recovery Services-valvet som är fäst på instrumentpanelen på navmenyn klickar du på **alla tjänster** i listan över resurser skriver **återställningstjänster**. När du börjar skriva filtreras listan baserat på det du skriver. Om du vill visa listan över valv i din prenumeration, klickar du på **Recovery Services-valv**.

   ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Listan över Recovery Services-valv visas.

   ![Välj valvet från lista](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. Välj det valv som du vill ta bort från listan. När du väljer valvet öppnas instrumentpanelen för valvet.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Om du vill ta bort ett valv i instrumentpanelen för valvet klickar du på **ta bort**. Du ombeds bekräfta att du vill ta bort valvet.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Om den **valv fel vid borttagning av** visas, du kan antingen ta bort beroenden från valvet eller du kan använda PowerShell för att ta bort valvet automatiskt. I följande avsnitt beskrivs hur du utför dessa uppgifter.

    ![Valvborttagningsfel](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Ta bort Recovery Services-valvet automatiskt

Du kan använda PowerShell för att ta bort ett Recovery Services-valv automatiskt. Framtvinga sätt raderas Recovery Services-valvet och alla associerade säkerhetskopieringsdata.

> [!Warning]
> När du använder PowerShell för att ta bort Recovery Services-valvet måste du vara säker på att du vill ta bort alla säkerhetskopierade data i valvet.
>

Ta bort ett Recovery Services-valv:

1. Logga in på ditt Azure-konto.

   Logga in på din Azure-prenumeration med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

   ```powershell
    Connect-AzureRmAccount
   ```
   Första gången du använder Azure Backup måste du registrera Azure Recovery Services-providern i din prenumeration med [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Öppna ett PowerShell-fönster med administratörsbehörighet.

1. Använd `Set-ExecutionPolicy Unrestricted` att ta bort eventuella begränsningar.

1. Kör följande kommando för att hämta Azure Resource Manager-klientpaketet från chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. Använd följande kommando för att installera Azure Resource Manager API-klient.

   `choco.exe install armclient`

1. Samla in prenumerations-ID och associerade resursgruppnamn för Recovery Services-valvet som du vill ta bort i Azure-portalen.

1. I PowerShell kör du följande kommando med ditt prenumerations-ID, resursgruppnamn och Recovery Services vault-namn. När du kör kommandot, tar den bort valvet och alla beroenden.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   Valvet måste vara tom innan du kan ta bort den. Annars kan du få ett felmeddelande som hänvisning till ”inte går att ta bort valvet eftersom det finns befintliga resurser i det här valvet”. Följande kommando visar hur du tar bort en behållare i ett valv:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

1. Logga in till din prenumeration i Azure-portalen och kontrollera att valvet tas bort.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Ta bort valvet beroenden och ta bort valv

Ta bort konfiguration mellan varje artikel eller servern och Recovery Services-valv för att ta bort valvet beroenden manuellt. När du går igenom följande procedur kan du använda den **Säkerhetskopieringsobjekt** menyn (se bild) för:

* Säkerhetskopieringar i Azure Storage (Azure Files)
* SQL Server i Virtuella Azure-säkerhetskopieringar
* Säkerhetskopior av virtuella Azure-datorer

Använd den **infrastruktur för säkerhetskopiering** menyn (se bild) för:

* Azure Backup Server-säkerhetskopieringar
* System Center DPM-säkerhetskopieringar

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. Rulla ned till avsnittet skyddade objekt i valvets instrumentpanel klicka sedan på **Säkerhetskopieringsobjekt**. I den här menyn kan du stoppa och ta bort Azure-filservrar, SQL-servrar i virtuella Azure-datorer och Azure virtual machines. I det här exemplet ska vi ta bort säkerhetskopierade data från en Azure-filserver.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Välj en typ av säkerhetskopiering att visa alla objekt av den typen.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. För alla objekt i listan, högerklicka på objektet och på snabbmenyn väljer **stoppa säkerhetskopiering**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/stop-backup-item.png)

    Menyn stoppa säkerhetskopiering öppnas.

1. På den **stoppa säkerhetskopiering** menyn från den **Välj ett alternativ** menyn och välj **ta bort säkerhetskopieringsdata**, skriver du namnet på objektet och klicka på **stoppa säkerhetskopiering**.

    Skriv namnet på objektet om du vill kontrollera att du vill ta bort den. Den **stoppa säkerhetskopiering** knappen aktiveras när du har kontrollerat objektet. Om du behåller data kan du inte ta bort valvet.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Om du vill ange en orsak till varför du tar bort data och lägga till kommentarer. Kontrollera att jobbet har slutförts genom att kontrollera Azure meddelandena ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet har slutförts tjänsten skickar ett meddelande: *säkerhetskopieringen har stoppats och säkerhetskopierade data skedde*.

1. När du tar bort ett objekt i listan på den **Säkerhetskopieringsobjekt** -menyn klickar du på **uppdatera** objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** fönstret i menyn Recovery Services-valvet. Det bör inte finnas någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om poster visas fortfarande i valvet, återgår till steg tre och välj en lista för typ av olika objekt.  

1. När det finns inga fler objekt i verktygsfältet valv, klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **New** tjänstens meny.

## <a name="removing-azure-backup-server-or-dpm"></a>Ta bort Azure Backup Server eller DPM

1. Rulla ned till avsnittet Hantera i valvets instrumentpanel klicka sedan på **infrastruktur för säkerhetskopiering**.

1. På undermenyn klickar du på **Säkerhetskopieringshanteringsservrar** att visa servrar för Azure Backup och System Center DPM-servern. Du kan stoppa och ta bort Azure-filservrar, SQL-servrar i virtuella Azure-datorer och Azure virtual machines.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Högerklicka på objektet som du vill ta bort och undermenyn, Välj **ta bort**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Menyn stoppa säkerhetskopiering öppnas.

1. På den **stoppa säkerhetskopiering** menyn från den **Välj ett alternativ** menyn och välj **ta bort säkerhetskopieringsdata**, skriver du namnet på objektet och klicka på **stoppa säkerhetskopiering**.

    Kontrollera att du vill ta bort, att skriva dess namn. Den **stoppa säkerhetskopiering** knappen aktiveras när du har kontrollerat objektet. Om du behåller data kan du ta bort valvet.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Alternativt kan du ange en orsak till varför du tar bort data och lägga till kommentarer. Kontrollera att jobbet har slutförts genom att kontrollera Azure meddelandena ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet är klart kommer tjänsten skickar ett meddelande: säkerhetskopieringen har stoppats och säkerhetskopierade data har tagits bort.

1. När du tar bort ett objekt i listan på den **Säkerhetskopieringsobjekt** -menyn klickar du på **uppdatera** återstående objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** fönstret i menyn Recovery Services-valvet. Det bör inte finnas någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om poster visas fortfarande i valvet, återgår till steg tre och välj en lista för typ av olika objekt.  
1. När det finns inga fler objekt i valvet, på instrumentpanelen för valvet klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **New** tjänstens meny.


## <a name="removing-azure-backup-agent-recovery-points"></a>Ta bort återställningspunkter för Azure Backup agent

1. Rulla ned till avsnittet Hantera i valvets instrumentpanel klicka sedan på **infrastruktur för säkerhetskopiering**.

1. I den underordnade menyn klickar du på **skyddade servrar** visa listan över skyddade servertyper, inklusive Azure Backup-agenten.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. I den **skyddade servrar** klickar du på Azure Backup-agenten.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Listan över servrar som skyddas med hjälp av Azure Backup-agenten, öppnas.

    ![Välj den specifika skyddade servern](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. I listan över servrar, klickar du på en för att öppna menyn.

    ![visa instrumentpanelen för den valda servern](./media/backup-azure-delete-vault/selected-protected-server.png)

1. På den valda servern instrumentpanelen menyn **ta bort**.

    ![ta bort den valda servern](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. På den **ta bort** menyn och Skriv namnet på objektet och klicka på **ta bort**.

    Skriv namnet på objektet om du vill kontrollera att du vill ta bort den. Den **ta bort** knappen aktiveras när du har kontrollerat objektet.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Alternativt kan du ange en orsak till varför du tar bort data och lägga till kommentarer. Kontrollera att jobbet har slutförts genom att kontrollera Azure meddelandena ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet är klart kommer tjänsten skickar ett meddelande: säkerhetskopieringen har stoppats och säkerhetskopierade data har tagits bort.

1. När du tar bort ett objekt i listan på den **Säkerhetskopieringsobjekt** -menyn klickar du på **uppdatera** återstående objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** fönstret i menyn Recovery Services-valvet. Det bör inte finnas någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om poster visas fortfarande i valvet, återgår till steg tre och välj en lista för typ av olika objekt.  
1. När det finns inga fler objekt i valvet, på instrumentpanelen för valvet klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **New** tjänstens meny.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Vad händer om jag stoppa säkerhetskopieringen men spara dina data?

Om du stoppar säkerhetskopieringsprocessen men av misstag *behålla* data, måste du ta bort säkerhetskopierade data innan du kan ta bort valvet. Ta bort säkerhetskopierade data:

1. På den **Säkerhetskopieringsobjekt** menyn, högerklicka på objektet och klickar på snabbmenyn **ta bort säkerhetskopieringsdata**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Den **ta bort säkerhetskopieringsdata** menyn öppnas.
1. På den **ta bort säkerhetskopieringsdata** menyn och Skriv namnet på objektet och klicka på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-retained-vault.png)

    När du har tagit bort data, återgår till steg 4c och fortsätta med processen.
