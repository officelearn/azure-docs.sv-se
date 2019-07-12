---
title: Ta bort ett Recovery Services-valv i Azure
description: Beskriver hur du tar bort ett Recovery Services-valv.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827881"
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Den här artikeln beskrivs hur du tar bort en [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och ta bort ett valv.


## <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett Recovery Services-valv som har beroenden, till exempel skyddade servrar eller säkerhetskopieringshanteringsservrar kopplade till valvet.<br/>
Kan inte tas bort valv som innehåller säkerhetskopierade data (dvs, även om du har stoppat skyddet men bevaras säkerhetskopierade data).

Om du tar bort ett valv som innehåller beroenden, visas följande fel:

![ta bort vault-fel](./media/backup-azure-delete-vault/error.png)

Om du vill ta bort ett smidigt sätt ett valv följer du stegen i sekvensen nedan:
- Stoppa skyddet och ta bort säkerhetskopierade data
- Ta bort skyddade servrar eller säkerhetskopieringshanteringsservrar
- Ta bort valvet


## <a name="delete-backup-data-and-backup-items"></a>Ta bort säkerhetskopierade data och objekt att säkerhetskopiera

Innan du fortsätter ytterligare Läs **[detta ](#before-you-start)** avsnitt för att förstå beroenden och valv borttagningsprocessen.

### <a name="for-protected-items-in-cloud"></a>För skyddade objekt i molnet

Om du vill stoppa skyddet och ta bort säkerhetskopierade data, utföra den nedan:

1. Från portalen > Recovery Services-valv > Säkerhetskopieringsobjekt väljer de skyddade objekten i molnet.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. För varje objekt, måste du högerklicka och välj **stoppa säkerhetskopiering**.

    ![Välj typ av säkerhetskopiering](./media/backup-azure-delete-vault/stop-backup-item.png)

3. I **stoppa säkerhetskopiering** > **Välj ett alternativ**väljer **ta bort säkerhetskopieringsdata**.
4. Skriv namnet på objektet och klicka på **stoppa säkerhetskopiering**.
   - Detta verifierar att du vill ta bort objektet.
   - Den **stoppa säkerhetskopiering** knappen aktiveras när du har kontrollerat.
   - Om du behålla och ta inte bort data, kan du inte ta bort valvet.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Kontrollera den **meddelande** ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). Är färdig visar tjänsten meddelandet: **Stoppar säkerhetskopiering och ta bort säkerhetskopiera data för ”*Säkerhetskopieringsobjekt*”** . **Åtgärden har slutförts**.
6. Klicka på **uppdatera** på den **Säkerhetskopieringsobjekt** meny för att kontrollera om säkerhetskopieringsobjekt tas bort.

      ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>För MARS-agenten

För att stoppa skyddet och ta bort säkerhetskopierade data, utför du stegen i den ordning som anges nedan:

- [Steg 1: Ta bort objekt att säkerhetskopiera från MARS-hanteringskonsol](#step-1-delete-backup-items-from-mars-management-console)
- [Steg 2: Ta bort Azure Backup-agenten från portalen](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Steg 1: Ta bort objekt att säkerhetskopiera från MARS-hanteringskonsol

Om du inte utföra det här steget på grund av att servern du sedan kontakta Microsoft support.

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

Nu när du har tagit bort säkerhetskopieringsobjekten från en lokal plats, utföra nästa steg från portalen.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Steg 2: Ta bort Azure Backup-agenten från portalen

Se till att [steg 1](#step-1-delete-backup-items-from-mars-management-console) har slutförts innan du fortsätter:

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

> [!NOTE]
> Om du ser nedan fel och sedan första följer du instruktionerna i [Radera säkerhetskopierade objekt från hanteringskonsolen](#step-1-delete-backup-items-from-mars-management-console).
>
>![Borttagningen misslyckades](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Om det inte går att utföra stegen för att ta bort säkerhetskopior från hanteringskonsolen, till exempel på grund av att servern med hanteringskonsolen, kontakta Microsoft support.

7. Kontrollera den **meddelande** ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). Är färdig visar tjänsten meddelandet: **Stoppar säkerhetskopiering och ta bort säkerhetskopiera data för ”*Säkerhetskopieringsobjekt*”** . **Åtgärden har slutförts**.
8. Klicka på **uppdatera** på den **Säkerhetskopieringsobjekt** meny för att kontrollera om säkerhetskopieringsobjekt tas bort.


### <a name="for-mabs-agent"></a>För MABS-agent

För att stoppa skyddet och ta bort säkerhetskopierade data, utför du stegen i den ordning som anges nedan:

- [Steg 1: Ta bort objekt att säkerhetskopiera från MABS-hanteringskonsol](#step-1-delete-backup-items-from-mabs-management-console)
- [Steg 2: Ta bort Azure Backup-hanteringsservrar från portalen](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Steg 1: Ta bort objekt att säkerhetskopiera från MABS-hanteringskonsol

Om du inte utföra det här steget på grund av att servern du sedan kontakta Microsoft support.

**Metod 1** för att stoppa skyddet och ta bort säkerhetskopierade data, utföra i stegen nedan:

1.  I DPM-administratörskonsolen klickar du på **Protection** i navigeringsfältet.
2.  I visningsfönstret väljer du den skyddsgruppsmedlem du vill ta bort. Högerklicka för att välja **stoppa skydd av gruppmedlemmar** alternativet.
3.  Från den **sluta skydda** dialogrutan **ta bort skyddade data** > **ta bort lagringen** kryssrutan och klicka sedan på **stoppa Skydd**.

    ![Ta bort lagringen online](./media/backup-azure-delete-vault/delete-storage-online.png)

Skyddad medlem status har ändrats till **inaktiv replikering är tillgänglig**.

5. Högerklicka på gruppen inaktivt skydd och välj **ta bort inaktivt skydd**.

    ![Ta bort inaktivt skydd](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Från den **ta bort inaktivt skydd** väljer **ta bort onlinelagring** och klicka på **Ok**.

    ![Ta bort repliker på disk och online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metod 2** starta den **MABS Management** konsolen. I den **Välj dataskyddsmetod** icke väljer **jag vill ha ett onlineskydd**.

  ![Välj dataskyddsmetod](./media/backup-azure-delete-vault/data-protection-method.png)

Nu när du har tagit bort säkerhetskopieringsobjekten från en lokal plats, utföra nästa steg från portalen.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Steg 2: Ta bort Azure Backup-hanteringsservrar från portalen

Se till att [steg 1](#step-1-delete-backup-items-from-mabs-management-console) har slutförts innan du fortsätter:

1. I valvets instrumentpanel klickar du på **infrastruktur för säkerhetskopiering**.
2. Klicka på **Säkerhetskopieringshanteringsservrar** att visa servrar.

    ![Välj valvet för att öppna valvets instrumentpanel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Högerklicka på objektet > **ta bort**.
4. På den **ta bort** menyn skriver du namnet på servern och på **ta bort**.

     ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Om du ser nedan fel och sedan första följer du instruktionerna i [Radera säkerhetskopierade objekt från hanteringskonsolen](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![Borttagningen misslyckades](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Om det inte går att utföra stegen för att ta bort säkerhetskopior från hanteringskonsolen, till exempel på grund av att servern med hanteringskonsolen, kontakta Microsoft support.

5. Kontrollera den **meddelande** ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). Är färdig visar tjänsten meddelandet: **Stoppar säkerhetskopiering och ta bort säkerhetskopiera data för ”*Säkerhetskopieringsobjekt*”** . **Åtgärden har slutförts**.
6. Klicka på **uppdatera** på den **Säkerhetskopieringsobjekt** meny för att kontrollera om säkerhetskopieringsobjekt tas bort.


## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

1. När alla beroenden har tagits bort, bläddra till den **Essentials** fönstret i menyn för valvet.
2. Kontrollera att det inte finns någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt som fortfarande visas i valvet, [ta bort dem](#delete-backup-data-and-backup-items).

3. När det finns inga fler objekt i valvet, på instrumentpanelen för valvet klickar du på **ta bort**.

    ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Kontrollera att du vill ta bort valvet, klicka på **Ja**. Valvet tas bort och portalen återgår till den **New** tjänstens meny.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Ta bort Recovery Services-valvet med hjälp av Azure Resource Manager-klienten

Det här alternativet om du vill ta bort Recovery Services-valvet rekommenderas endast när alla beroenden tas bort och du fortfarande får den *valv fel vid borttagning av*.



- Från den **Essentials** fönstret på menyn vault kontrollerar du att det inte finns någon **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om det finns objekt att säkerhetskopiera, genomför du stegen i [ta bort säkerhetskopierade data och objekt att säkerhetskopiera](#delete-backup-data-and-backup-items).
- Försök [tar bort valvet från portalen](#delete-the-recovery-services-vault).
- Om alla beroenden tas bort och du fortfarande får den *valv fel vid borttagning av* använder ARMClient för att utföra stegen som anges nedan;

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
2. Om valvet inte är tom, får du felet ”går inte att ta bort valvet eftersom det finns befintliga resurser i det här valvet”. Ta bort skyddade objekt / behållare i ett valv, gör du följande:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Kontrollera att valvet tas bort i Azure-portalen.


## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services-valv.<br/>
[Lär dig mer om](backup-azure-manage-windows-server.md) övervaka och hantera Recovery Services-valv.
