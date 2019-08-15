---
title: Ta bort ett Recovery Services-valv i Azure
description: Beskriver hur du tar bort ett Recovery Services-valv.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018874"
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

I den här artikeln beskrivs hur du tar bort ett [Azure Backup](backup-overview.md) Recovery Services-valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv.


## <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett Recovery Services-valv som har beroenden som skyddade servrar eller säkerhets kopierings hanterings servrar som är associerade med valvet.

- Det går inte att ta bort valvet som innehåller säkerhets kopierings data (det vill säga även om du har stoppat skyddet men behåller säkerhets kopierings data).

- Om du tar bort ett valv som innehåller beroenden, visas följande fel meddelande:

  ![Fel vid borttagning av valv](./media/backup-azure-delete-vault/error.png)

- Om du tar bort ett lokalt skyddat objekt (MARS, MABS eller DPM till Azure) från portal som innehåller beroenden visas ett varnings meddelande:

  ![Fel vid borttagning av skyddad Server](./media/backup-azure-delete-vault/error-message.jpg)

  
Om du vill ta bort valvet väljer du det scenario som matchar konfigurationen och följer de rekommenderade stegen:

Scenario | Steg för att ta bort beroenden för att ta bort valv |
-- | --
Jag har lokala filer och mappar som skyddas med hjälp av Azure Backup Agent (MARS) säkerhets kopiering till Azure | Utför stegen i ta bort säkerhets kopierings data och säkerhets kopierings objekt – [för mars](#delete-backup-items-from-mars-management-console) -agenten
Jag har lokala datorer som skyddas med MABS (Microsoft Azure Backup Server) eller DPM till Azure (System Center Data Protection Manager) | Utför stegen i ta bort säkerhets kopierings data och säkerhets kopierings objekt – [för Mabs-agent](#delete-backup-items-from-mabs-management-console)
Jag har skyddade objekt i molnet (t. ex. laaS VM, Azure-filresurs osv.)  | Utför stegen i ta bort säkerhets kopierings data och säkerhets kopierings objekt – [för skyddade objekt i molnet](#delete-protected-items-in-cloud)
Jag har skyddade objekt både lokalt och i molnet | Utför stegen i ta bort säkerhets kopierings data och säkerhets kopierings objekt i ordningen nedan: <br> - [För skyddade objekt i molnet](#delete-protected-items-in-cloud)<br> - [För MARS-agent](#delete-backup-items-from-mars-management-console) <br> - [För MABS-agent](#delete-backup-items-from-mabs-management-console)
Jag har inga skyddade objekt lokalt eller i molnet. men jag får fortfarande valvet borttagnings fel | Utför stegen i [ta bort Recovery Services valvet med Azure Resource Manager-klienten](#delete-the-recovery-services-vault-using-azure-resource-manager-client)


## <a name="delete-protected-items-in-cloud"></a>Ta bort skyddade objekt i molnet

Innan du fortsätter läsa **[det här](#before-you-start)** avsnittet för att förstå borttagnings processen för beroenden och valvet.

Gör så här om du vill stoppa skyddet och ta bort säkerhetskopierade data:

1. Från Portal > **Recovery Services valv** > **säkerhetskopiera objekt**, väljer du skyddade objekt i molnet (till exempel AzureVirtual Machine, Azure Storage (Azure Files), SQL på Azure VM och så vidare).

    ![Välj typ av säkerhets kopiering](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Högerklicka på objektet säkerhets kopia. Beroende på om säkerhets kopie posten är skyddad eller inte, visar menyn **Avbryt säkerhets kopiering** eller **ta bort säkerhetskopierade data**.

    - För **Avbryt säkerhets kopiering**väljer du **ta bort säkerhets kopierings data** från List rutan. Ange **namnet** på säkerhets kopierings objekt (Skift läges känsligt), Välj en **orsak**, ange **kommentarer**och klicka på **stoppa säkerhets kopiering**.

        ![Välj typ av säkerhets kopiering](./media/backup-azure-delete-vault/stop-backup-item.png)

    - För att **ta bort säkerhets kopierings data**anger du namnet på säkerhets kopierings objekt (Skift läges känsligt), väljer en **orsak**, anger **kommentarer**och klickar på **ta bort**. 

         ![ta bort säkerhets kopierings data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Kontrol lera **meddelandet** ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png). När tjänsten har slutförts visar tjänsten meddelandet: **Stoppar säkerhets kopiering och tar bort säkerhetskopierade data för*säkerhets kopierings objekt*** . **Åtgärden**har slutförts.
6. Klicka på **Uppdatera** på menyn **säkerhets kopierings objekt** för att kontrol lera om säkerhets kopierings objekt tas bort.

      ![ta bort säkerhets kopierings data](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Ta bort skyddade objekt lokalt

Innan du fortsätter att läsa **[det här](#before-you-start)** avsnittet för att förstå borttagnings processen för beroenden och valvet.

1. På instrument panelen för valv klickar du på **infrastruktur för säkerhets kopiering**.
2. Välj alternativet nedan beroende på ditt lokala scenario:

      - För **Azure Backup Agent**väljer du **skyddade servrar** > **Azure Backup Agent** och väljer den server som du vill ta bort. 

        ![Välj ditt valv för att öppna dess instrument panel](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - För **Azure Backup Server**/**DPM**väljer du **säkerhets kopierings hanterings servrar**. Välj den server som du vill ta bort. 


          ![öppna instrument panelen genom att välja valv](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Bladet **ta bort** visas med varnings meddelandet.

     ![ta bort säkerhets kopierings data](./media/backup-azure-delete-vault/delete-protected-server.png)

     Läs varnings meddelandet och de instruktioner som visas i kryss rutan medgivande.
    
    > [!NOTE]
    >- Om den skyddade servern är synkroniserad med Azure-tjänsten och det finns säkerhets kopierings objekt, visar kryss rutan medgivande antalet beroende säkerhets kopierings objekt och länken för att Visa säkerhets kopierings objekt.
    >- Om den skyddade servern inte är synkroniserad med Azure-tjänsten och säkerhets kopierings objekt finns visas antalet säkerhets kopierings objekt i kryss rutan medgivande.
    >- Om det inte finns några säkerhets kopierings objekt uppmanas du att ta bort kryss rutan.

4. Markera kryss rutan medgivande och klicka på **ta bort**.




5. Kontrol lera **meddelandet** ![ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/messages.png). När tjänsten har slutförts visar tjänsten meddelandet: **Stoppar säkerhets kopiering och tar bort säkerhetskopierade data för*säkerhets kopierings objekt*** . **Åtgärden**har slutförts.
6. Klicka på **Uppdatera** på menyn **säkerhets kopierings objekt** för att kontrol lera om säkerhets kopierings objekt tas bort.

Nu kan du fortsätta att ta bort säkerhets kopierings objekt från hanterings konsolen:
    
   - [Objekt som skyddas med MARS](#delete-backup-items-from-mars-management-console)
    - [Objekt som skyddas med MABS](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Ta bort säkerhets kopierings objekt från MARS Management Console

Ta bort säkerhets kopierings objekt från MARS Management Console

- Starta MARS Management-konsolen, gå till **Åtgärds** fönstret och välj **Schemalägg säkerhets kopiering**.
- Från **ändra eller stoppa en schemalagd säkerhets kopierings** guide väljer du alternativet **sluta använda säkerhets kopierings schema och tar bort alla lagrade säkerhets kopior** och klickar på **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Klicka på **Slutför**i guiden **stoppa en schemalagd säkerhets kopiering** .

    ![Stoppa en schemalagd säkerhets kopiering](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Du uppmanas att ange en säkerhets kod. Gör så här för att skapa en PIN-kod:
  - Logga in på Azure Portal.
  - Bläddra till **Recovery Services** > **Egenskaper**för valv**Inställningar** > .
  - Klicka på **generera**under **säkerhets-PIN**. Kopiera den här PIN-koden. (Den här PIN-koden är bara giltig i fem minuter.)
- I hanterings konsolen (klient app) klistrar du in PIN-koden och klickar på **OK**.

  ![Säkerhets kod](./media/backup-azure-delete-vault/security-pin.png)

- I *guiden **ändra säkerhets kopierings förlopp** visas borttagna säkerhets kopierings data i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent.*  

    ![Ta bort infrastruktur för säkerhets kopiering](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nu när du har tagit bort de säkerhetskopierade objekten från den lokala platsen slutför du nästa steg från portalen.

### <a name="delete-backup-items-from-mabs-management-console"></a>Ta bort säkerhets kopierings objekt från MABS Management Console

Ta bort säkerhets kopierings objekt från MABS Management Console

**Metod 1** Gör så här för att stoppa skyddet och ta bort säkerhetskopierade data:

1.  I DPM-administratörskonsol klickar du på **skydd** i navigerings fältet.
2.  I visningsfönstret väljer du den skydds grupps medlem som du vill ta bort. Högerklicka för att välja alternativet **stoppa skyddet av grupp medlemmar** .
3.  I dialog **rutan stoppa skydd** markerar du kryss **rutan ta bort skyddade data** > **ta bort lagring online** och klickar sedan på **stoppa skydd**.

    ![Ta bort lagring online](./media/backup-azure-delete-vault/delete-storage-online.png)

Den skyddade medlems statusen har nu ändrats till en **inaktiv replikering är tillgänglig**.

4. Högerklicka på den inaktiva skydds gruppen och välj **ta bort inaktivt skydd**.

    ![Ta bort inaktivt skydd](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Från fönstret **ta bort inaktivt skydd** väljer du **ta bort online-lagring** och klickar på **OK**.

    ![Ta bort repliker på disk och online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metod 2** Starta hanterings konsolen för **Mabs** . I avsnittet **Välj data skydds metod** avmarkerar **du jag vill ha onlineskydd**.

  ![Välj data skydds metod](./media/backup-azure-delete-vault/data-protection-method.png)

Nu när du har tagit bort de säkerhetskopierade objekten från den lokala platsen slutför du nästa steg från portalen.


## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

1. När alla beroenden har tagits bort bläddrar du till fönstret Essentials i menyn valv.
2. Kontrol lera att det inte finns några **säkerhets kopierings objekt**, **säkerhets kopierings hanterings servrar**eller **replikerade objekt** i listan. Om objekten fortfarande visas i valvet, se avsnittet [innan du börjar](#before-you-start) .

3. När det inte finns några fler objekt i valvet klickar du på **ta bort**på instrument panelen för valvet.

    ![ta bort säkerhets kopierings data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Klicka på **Ja**för att kontrol lera att du vill ta bort valvet. Valvet tas bort och portalen återgår till den **nya** tjänst menyn.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Ta bort Recovery Services valvet med Azure Resource Manager-klienten

Det här alternativet för att ta bort Recovery Servicess valvet rekommenderas endast när alla beroenden tas bort och du får fortfarande ett fel när du *tar*bort valvet.

- I fönstret **Essentials** i menyn valv kontrollerar du att det inte finns några **säkerhets kopierings objekt**, **säkerhets kopierings hanterings servrar**eller **replikerade objekt** i listan. Om det finns säkerhets kopierings objekt, se avsnittet [innan du börjar](#before-you-start) ..
- Försök [att ta bort valvet från portalen](#delete-the-recovery-services-vault)igen.
- Om alla beroenden tas bort och du fortfarande får borttagnings *felet*för valvet, använder du ARMClient-verktyget för att utföra stegen som anges nedan.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installera choklad härifrån och [](https://chocolatey.org/) installera ARMClient kör kommandot nedan:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Logga in på ditt Azure-konto och kör det här kommandot:

    `ARMClient.exe login [environment name]`

3. I Azure Portal kan du samla in prenumerations-ID och resurs gruppens namn för valvet som du vill ta bort.

Mer information om kommandot ARMClient finns i det här [dokumentet](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Använd Azure Resource Manager-klienten för att ta bort Recovery Services valv

1. Kör följande kommando med ditt prenumerations-ID, resurs gruppens namn och namnet på valvet. När du kör kommandot tas valvet bort om du inte har några beroenden.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Om valvet inte är tomt visas ett fel meddelande om att det inte går att ta bort valvet eftersom det finns befintliga resurser i valvet. Om du vill ta bort ett skyddat objekt/en behållare i ett valv gör du följande:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. I Azure Portal kontrollerar du att valvet har tagits bort.


## <a name="next-steps"></a>Nästa steg

[Läs mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services valv.<br/>
[Lär dig mer om](backup-azure-manage-windows-server.md) att övervaka och hantera Recovery Services valv.
