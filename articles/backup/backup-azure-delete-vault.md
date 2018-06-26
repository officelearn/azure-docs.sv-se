---
title: Ta bort ett Recovery Services-valv i Azure '
description: Den här artikeln förklarar hur du tar bort Recovery Services-valvet. Artikeln innehåller åtgärder för felsökning när du försöker ta bort ett valv, men det går inte att.
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 6/21/2018
ms.author: markgal
ms.openlocfilehash: d8169eba6790e49a85d69434663faabe7430942e
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937608"
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Den här artikeln beskriver hur du ta bort alla objekt från en Recovery Services-valvet och tar bort den. Du kan inte ta bort Recovery Services-valvet om den är registrerad på en server och innehåller säkerhetskopierade data. Om du försöker ta bort ett valv, men inte kan konfigureras fortfarande valvet för att ta emot säkerhetskopierade data.

Information om hur du tar bort ett valv, finns i avsnittet [ta bort ett valv från Azure-portalen](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Om du inte vill att behålla alla data i Recovery Services-valvet vill du ta bort valvet, finns i avsnittet [ta bort valvet av kraft](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Om du inte vet vad som finns i valvet och du måste se till att du kan ta bort valvet, finns i avsnittet [ta bort valvet beroenden och ta bort valvet](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Ta bort ett valv från Azure-portalen

Om du redan har Recovery Services-valvet öppna går du vidare till nästa steg.

1. Öppna Azure portal och öppnar du vill ta bort valvet från instrumentpanelen.

   Om du inte har Recovery Services-valvet fäst på instrumentpanelen på navmenyn klickar du på **alla tjänster** och Skriv i listan över resurser, **återställningstjänster**. När du börjar skriva filtreras listan baserat på det du skriver. Om du vill visa listan över valv i din prenumeration, klickar du på **Recovery Services-valv**.

   ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Listan över Recovery Services-valv visas. 

   ![Välj valvet från lista](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

2. Välj valvet som du vill ta bort från listan. När du väljer valvet öppnas dess valvet instrumentpanelen.

    ![Välj din valvet för att öppna dess instrumentpanel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

3. Om du vill ta bort ett valv i valvet-instrumentpanelen klickar du på **ta bort**. Du ombeds bekräfta att du vill ta bort valvet.

    ![Välj din valvet för att öppna dess instrumentpanel](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Om den **valvet fel vid borttagning av** visas, kan du antingen ta bort beroenden valvet, eller du kan använda PowerShell för att ta bort valvet automatiskt. I följande avsnitt beskrivs hur du utför dessa uppgifter.

    ![Fel vid borttagning av valvet](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Ta bort Recovery Services-valvet automatiskt

Du kan använda PowerShell för att ta bort Recovery Services-valvet automatiskt. Tvingad sätt tas Recovery Services-valvet och alla tillhörande säkerhetskopierade data bort permanent. 

> [!Warning]
> När du använder PowerShell för att ta bort Recovery Services-valvet måste du vara säker på att du permanent vill ta bort alla säkerhetskopierade data i valvet.
>

Ta bort Recovery Services-valv:

1. Logga in på ditt Azure-konto.

   Logga in på Azure-prenumerationen med den `Connect-AzureRmAccount` kommandot och följa den på skärmen riktningar.

   ```powershell
    Connect-AzureRmAccount
   ```
   Första gången du använder Azure Backup måste du registrera Azure Recovery Services-providern i din prenumeration med [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

2. Öppna ett PowerShell-fönster med administratörsbehörighet.

3. Använd `Set-ExecutionPolicy Unrestricted` att ta bort eventuella begränsningar.

4. Kör följande kommando för att hämta Azure Resource Manager-klienten från chocolately.org.

    `iex ((New-Object System.Net.WebClient) DownloadString('https://chocolatey.org/install.ps1))`

5. Använd följande kommando för att installera Azure Resource Manager API-klient.

   `choco.exe install armclient`

6. Samla in prenumerations-ID och associerade resursgruppens namn för Recovery Services-valvet som du vill ta bort i Azure-portalen.

7. I PowerShell kör du följande kommando med ditt prenumerations-ID, resursgruppens namn och namn på Recovery Services-valvet. När du kör kommandot tas bort valvet och alla beroenden.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
8. Logga in till din prenumeration på Azure-portalen och kontrollera att valvet har tagits bort.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Ta bort valvet beroenden och ta bort valvet

Ta bort konfigurationen mellan varje element eller servern och Recovery Services-valvet och manuellt ta bort valvet beroenden. När du går igenom följande procedur kan du använda den **säkerhetskopiering objekt** menyn (se bild) för:

* Säkerhetskopieringar i Azure Storage (Azure-filer)
* SQL Server i Virtuella Azure-säkerhetskopieringar
* Virtuella datorer i Azure-säkerhetskopieringar
* Microsoft Azure Recovery Services-agenten säkerhetskopieringar

Använd den **säkerhetskopiering infrastruktur** menyn (se bild) för:

* Azure Backup Server-säkerhetskopieringar
* System Center DPM-säkerhetskopieringar

    ![Välj din valvet för att öppna dess instrumentpanel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. Rulla ned till avsnittet skyddade objekt i menyn valvet instrumentpanelen och klicka på **säkerhetskopiering objekt**. I den här menyn kan du stoppa och ta bort Azure-filservrar, SQL-servrar i virtuella Azure-datorn och virtuella Azure-datorer. I det här exemplet ska vi ta bort säkerhetskopierade data från en filserver i Azure.

    ![Välj din valvet för att öppna dess instrumentpanel](./media/backup-azure-delete-vault/selected-backup-items.png)

2. Välj en typ av säkerhetskopiering att visa alla objekt av den typen.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. För alla objekt i listan högerklickar du på objektet och på snabbmenyn väljer **stoppa säkerhetskopiering**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/stop-backup-item.png) 

    Stoppa säkerhetskopiering menyn öppnas.

4. På den **stoppa säkerhetskopiering** menyn från den **väljer du ett alternativ** väljer du **ta bort säkerhetskopieringsdata**, skriver du namnet på objektet och klicka på **stoppa säkerhetskopiering**.

    Skriv namnet på objektet om du vill kontrollera att du vill ta bort den. Den **stoppa säkerhetskopiering** knappen aktiveras när du har gjort objektet. Om du behåller dina data, kan du inte ta bort valvet.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Om du vill ange en orsak till varför du tar bort data och lägga till kommentarer. Kontrollera den Azure-meddelanden för att verifiera aktiviteten slutfördes ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet är slutfört tjänsten skickar ett meddelande: *säkerhetskopieringen har stoppats och den säkerhetskopiera informationen har tagits bort*.

5. När du tar bort ett objekt i listan på den **säkerhetskopiering objekt** -menyn klickar du på **uppdatera** objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** rutan i menyn Recovery Services-valvet. Det får inte finnas något **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt visas fortfarande i valvet, återgår till steg tre och välj ett annat objekt typen lista.  

6. När det finns inga fler objekt i verktygsfältet valvet, klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="removing-azure-backup-server-or-dpm"></a>Ta bort Azure Backup-Server eller DPM

1. Rulla ned till avsnittet Hantera valvet instrumentpanelen-menyn och klicka på **säkerhetskopiering infrastruktur**. 

2. Klicka på undermenyn **Säkerhetskopieringshanteringsservrar** att visa servrar för Azure-säkerhetskopiering och System Center DPM-servern. Du kan stoppa och ta bort Azure-filservrar, SQL-servrar i virtuella Azure-datorn och virtuella Azure-datorer. 

    ![Välj din valvet för att öppna dess instrumentpanel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Högerklicka på objektet som du vill ta bort och undermenyn, Välj **ta bort**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Stoppa säkerhetskopiering menyn öppnas.

4. På den **stoppa säkerhetskopiering** menyn från den **väljer du ett alternativ** väljer du **ta bort säkerhetskopieringsdata**, skriver du namnet på objektet och klicka på **stoppa säkerhetskopiering**.

    Kontrollera du vill ta bort att skriva dess namn. Den **stoppa säkerhetskopiering** knappen aktiveras när du har gjort objektet. Om du behåller dina data, kan du ta bort valvet.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Du kan också kan du ange en orsak till varför du vill ta bort data och lägga till kommentarer. Kontrollera den Azure-meddelanden för att verifiera att jobbet har slutförts, ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet är klart, tjänsten skickar ett meddelande: säkerhetskopieringen har stoppats och den säkerhetskopiera informationen har tagits bort.

5. När du tar bort ett objekt i listan på den **säkerhetskopiering objekt** -menyn klickar du på **uppdatera** kvarvarande objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** rutan i menyn Recovery Services-valvet. Det får inte finnas något **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt visas fortfarande i valvet, återgår till steg tre och välj ett annat objekt typen lista.  
6. När det finns inga fler objekt i valvet på valvet instrumentpanelen klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.


## <a name="removing-azure-backup-agent-recovery-points"></a>Ta bort återställningspunkter för Azure Backup agent

1. Rulla ned till avsnittet Hantera valvet instrumentpanelen-menyn och klicka på **säkerhetskopiering infrastruktur**.

2. Klicka på den underordnade menyn **skyddade servrar** visa listan över skyddade servertyper, inklusive Azure Backup-agenten.

    ![Välj din valvet för att öppna dess instrumentpanel](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. I den **skyddade servrar** klickar du på Azure Backup-agenten.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Listan över servrar som skyddas med Azure Backup-agenten öppnas.

    ![Välj den specifika skyddade servern](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

4. Klicka på en för att öppna menyn i listan över servrar.

    ![visa instrumentpanelen för den valda servern](./media/backup-azure-delete-vault/selected-protected-server.png)

5. På den valda servern instrumentpanelen menyn **ta bort**.

    ![ta bort den valda servern](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. På den **ta bort** menyn, skriver du namnet på objektet och klicka på **ta bort**.

    Skriv namnet på objektet om du vill kontrollera att du vill ta bort den. Den **ta bort** knappen aktiveras när du har gjort objektet.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Du kan också kan du ange en orsak till varför du vill ta bort data och lägga till kommentarer. Kontrollera den Azure-meddelanden för att verifiera att jobbet har slutförts, ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet är klart, tjänsten skickar ett meddelande: säkerhetskopieringen har stoppats och den säkerhetskopiera informationen har tagits bort.

7. När du tar bort ett objekt i listan på den **säkerhetskopiering objekt** -menyn klickar du på **uppdatera** kvarvarande objekt i valvet.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** rutan i menyn Recovery Services-valvet. Det får inte finnas något **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt visas fortfarande i valvet, återgår till steg tre och välj ett annat objekt typen lista.  
8. När det finns inga fler objekt i valvet på valvet instrumentpanelen klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

9. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Vad händer om jag stoppa säkerhetskopieringsprocessen men behålla data?

Om du avbryter säkerhetskopieringen men oavsiktligt *behålla* data, måste du ta bort den säkerhetskopiera informationen innan du kan ta bort valvet. Ta bort säkerhetskopierade data:

1. På den **Säkerhetskopieringsobjekt** -menyn högerklickar du på objektet och klickar på snabbmenyn **ta bort säkerhetskopieringsdata**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Den **ta bort säkerhetskopierade Data** menyn öppnas.
2. På den **ta bort säkerhetskopierade Data** menyn, skriver du namnet på objektet och klicka på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-retained-vault.png)

    När du har tagit bort data tillbaka till steg 4c och fortsätta med processen.
