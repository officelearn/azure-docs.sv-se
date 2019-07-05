---
title: Ta bort ett Recovery Services-valv i Azure
description: Beskriver hur du tar bort ett Recovery Services-valv.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508433"
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Den här artikeln beskrivs hur du tar bort en [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv och ta bort ett valv automatiskt.


## <a name="before-you-start"></a>Innan du börjar

Innan du börjar är det viktigt att förstå att du inte kan ta bort ett Recovery Services-valv som innehåller servrar som registrerats i den eller som innehåller säkerhetskopierade data.

- Om du vill ta bort ett valv utan problem, avregistrera servrar som den innehåller, ta bort valvet data och ta sedan bort valvet.
- Om du försöker ta bort ett valv som fortfarande har beroenden, utfärdas ett felmeddelande visas och du måste manuellt ta bort valv-beroenden, inklusive:
    - Säkerhetskopierade objekt
    - Skyddade servrar
    - Säkerhetskopiera hanteringsservrar (Azure Backup Server, DPM) ![väljer du ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Om du inte vill behålla några data i Recovery Services-valvet och vill ta bort valvet, kan du ta bort valvet automatiskt.
- Om du försöker ta bort ett valv, men inte kan konfigureras fortfarande valvet för att ta emot säkerhetskopierade data.


## <a name="delete-a-vault-from-the-azure-portal"></a>Ta bort ett valv i Azure Portal

1. Öppna instrumentpanelen för valvet.  
2. I instrumentpanelen, klickar du på **ta bort**. Kontrollera att du vill ta bort.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Om du får ett felmeddelande, ta bort [Säkerhetskopiera objekt](#remove-backup-items), [infrastrukturservrar](#remove-azure-backup-management-servers), och [återställningspunkter](#remove-azure-backup-agent-recovery-points), och ta sedan bort valvet.

![ta bort vault-fel](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Ta bort Recovery Services-valvet med hjälp av Azure Resource Manager-klienten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installera chocolatey från [här](https://chocolatey.org/) och för att installera ARMClient kör i kommandot nedan:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Logga in på ditt Azure-konto och kör det här kommandot:

    `ARMClient.exe login [environment name]`

3. Samla in prenumerations-ID och resurs gruppnamn för valvet som du vill ta bort i Azure-portalen.

Mer information om ARMClient kommando finns [dokumentet](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Använd Azure Resource Manager-klienten att ta bort Recovery Services-valv

1. Kör följande kommando med ditt prenumerations-ID, resursgruppnamn och valvnamnet. När du kör kommandot tas bort valvet om du inte har några beroenden.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Om valvet inte är tom, får du felmeddelandet ”går inte att ta bort valvet eftersom det finns befintliga resurser i det här valvet”. Ta bort skyddade objekt / behållare i ett valv, gör du följande:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Kontrollera att valvet tas bort i Azure-portalen.


## <a name="remove-vault-items-and-delete-the-vault"></a>Ta bort valvet objekt och ta bort valvet

Ta bort alla beroenden innan Recovery Services-valvet tas bort.

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

## <a name="deleting-backup-items-from-management-console"></a>Ta bort objekt att säkerhetskopiera från konsolen Grupprinciphantering

Gå till din lokala server-hanteringskonsolen (MARS, Azure Backup Server eller SC DPM beroende på där dina tillbaka objekt skyddas) för att ta bort säkerhetskopieringsobjekten från en infrastruktur för säkerhetskopiering.

### <a name="for-mars-agent"></a>För MARS-agenten

- Starta MARS Management-konsolen, gå till den **åtgärder** rutan och välj **Schemalägg säkerhetskopiering**.
- Från **ändra eller avbryta en schemalagd säkerhetskopiering** guiden väljer du alternativet **sluta använda det här schemat för säkerhetskopiering och ta bort alla lagrade säkerhetskopior** och klicka på **nästa**.

    ![Ändra eller avbryta en schemalagd säkerhetskopiering](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Från **stoppa en schemalagd säkerhetskopiering** guiden klickar du på **Slutför**.

    ![Stoppa en schemalagd säkerhetskopiering](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Du uppmanas att ange en säkerhetskod. Om du vill generera PIN-koden utför i stegen nedan:
  - Logga in på Azure Portal.
  - Bläddra till **Recovery Services-valv** > **inställningar** > **egenskaper**.
  - Under **SÄKERHETSKODEN**, klickar du på **generera**. Kopiera den här PIN-kod. (Den här PIN-kod är giltig i fem minuter)
- Klistra in PIN-koden i konsolen för hantering (klientappen) och klicka på **Ok**.

  ![Säkerhetskod](./media/backup-azure-delete-vault/security-pin.png)

- I den **ändra säkerhetskopiering** guiden visas *borttagna säkerhetskopierade data bevaras i 14 dagar. Därefter tas tas säkerhetskopierade data bort permanent.*  

    ![Ta bort en infrastruktur för säkerhetskopiering](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nu när du har tagit bort säkerhetskopieringsobjekten från en lokal plats, utföra de nedanstående steg från portalen:
- För MARS följer du stegen i [återställningspunkter för ta bort Azure Backup agent](#remove-azure-backup-agent-recovery-points)

### <a name="for-mabs-agent"></a>För MABS-agent

Det finns olika metoder för att stoppa/ta bort onlineskydd, utföra någon av de metoderna nedan:

**Metod 1**

Starta den **MABS Management** konsolen. I den **Välj dataskyddsmetod** icke väljer **jag vill ha ett onlineskydd**.

  ![Välj dataskyddsmetod](./media/backup-azure-delete-vault/data-protection-method.png)

**Metod 2**

Om du vill ta bort en skyddsgrupp måste du först stoppa skyddet av gruppen. Använd följande procedur för att avbryta skyddet och aktivera borttagning av en skyddsgrupp.

1.  I DPM-administratörskonsolen klickar du på **Protection** i navigeringsfältet.
2.  I visningsfönstret väljer du den skyddsgruppsmedlem du vill ta bort. Högerklicka för att välja **stoppa skydd av gruppmedlemmar** alternativet.
3.  Från den **sluta skydda** dialogrutan **ta bort skyddade data** > **ta bort lagringen** kryssrutan och klicka sedan på **stoppa Skydd**.

    ![Ta bort lagringen online](./media/backup-azure-delete-vault/delete-storage-online.png)

Skyddad medlem status har ändrats till **inaktiv replikering är tillgänglig**.

5. Högerklicka på gruppen inaktivt skydd och välj **ta bort inaktivt skydd**.

    ![Ta bort inaktivt skydd](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Från den **ta bort inaktivt skydd** väljer **ta bort onlinelagring** och klicka på **Ok**.

    ![Ta bort repliker på disk och online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

Nu när du har tagit bort säkerhetskopieringsobjekten från en lokal plats, utföra de nedanstående steg från portalen:
- MABS och DPM att följa stegen i [ta bort Azure Backup-hanteringsservrar](#remove-azure-backup-management-servers).


### <a name="remove-azure-backup-management-servers"></a>Ta bort Azure Backup-hanteringsservrar

Innan du tar bort Azure backup management-servern, se till att utföra stegen i [Radera säkerhetskopierade objekt från hanteringskonsolen](#deleting-backup-items-from-management-console).

1. I valvets instrumentpanel klickar du på **infrastruktur för säkerhetskopiering**.
2. Klicka på **Säkerhetskopieringshanteringsservrar** att visa servrar.

    ![Välj valvet för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Högerklicka på objektet > **ta bort**.
4. På den **ta bort** menyn skriver du namnet på servern och på **ta bort**.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  Du kan också ange en orsak till varför du tar bort data och lägga till kommentarer.

> [!NOTE]
> Om du ser nedan fel och sedan första följer du instruktionerna i [Radera säkerhetskopierade objekt från hanteringskonsolen](#deleting-backup-items-from-management-console).
>
>![Borttagningen misslyckades](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Om det inte går att utföra stegen för att ta bort säkerhetskopior från hanteringskonsolen, till exempel på grund av att servern med hanteringskonsolen, kontakta Microsoft support.

6. Om du vill kontrollera att ta bort jobbet har slutförts, kontrollera den Azure-meddelanden ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png).
7. När jobbet har slutförts kan tjänsten skickar ett meddelande: **säkerhetskopieringen har stoppats och säkerhetskopierade data skedde**.
8. När du tar bort ett objekt i listan på den **infrastruktur för säkerhetskopiering** -menyn klickar du på **uppdatera** objekt i valvet.


### <a name="remove-azure-backup-agent-recovery-points"></a>Ta bort återställningspunkter för Azure Backup agent

Innan du tar bort Azure backup återställningspunkten, se till att utföra stegen i [Radera säkerhetskopierade objekt från hanteringskonsolen](#deleting-backup-items-from-management-console).

1. I valvets instrumentpanel klickar du på **infrastruktur för säkerhetskopiering**.
2. Klicka på **skyddade servrar** att visa infrastrukturservrar.

    ![Välj ditt valv för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. I den **skyddade servrar** klickar du på Azure Backup-agenten.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Klicka på servern i listan över servrar som skyddas med hjälp av Azure Backup-agenten.

    ![Välj den specifika skyddade servern](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. På instrumentpanelen för valda servern klickar du på **ta bort**.

    ![ta bort den valda servern](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. På den **ta bort** menyn skriver du namnet på servern och på **ta bort**.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Du kan också ange en orsak till varför du tar bort data och lägga till kommentarer.

> [!NOTE]
> Om du ser nedan fel och sedan första följer du instruktionerna i [Radera säkerhetskopierade objekt från hanteringskonsolen](#deleting-backup-items-from-management-console).
>
>![Borttagningen misslyckades](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Om det inte går att utföra stegen för att ta bort säkerhetskopior från hanteringskonsolen, till exempel på grund av att servern med hanteringskonsolen, kontakta Microsoft support. 

8. Om du vill kontrollera att ta bort jobbet har slutförts, kontrollera den Azure-meddelanden ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png).
9. När du tar bort ett objekt i listan på den **infrastruktur för säkerhetskopiering** -menyn klickar du på **uppdatera** objekt i valvet.


### <a name="delete-the-vault-after-removing-dependencies"></a>Ta bort valvet när du tar bort beroenden

1. När alla beroenden har tagits bort, bläddra till den **Essentials** fönstret i menyn för valvet.
2. Kontrollera att det inte finns någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt visas fortfarande i valvet kan du ta bort dem.

3. När det finns inga fler objekt i valvet, på instrumentpanelen för valvet klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Kontrollera att du vill ta bort valvet, klicka på **Ja**. Valvet tas bort och portalen återgår till den **New** tjänstens meny.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Vad händer om jag stoppa säkerhetskopieringen men spara dina data?

Om du stoppa säkerhetskopieringen men behålla data, av misstag, måste du ta bort säkerhetskopierade data enligt beskrivningen i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services-valv.
