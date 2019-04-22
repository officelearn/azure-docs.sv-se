---
title: Säkerhetskopiera och återställa krypterade virtuella Azure-datorer med Azure Backup
description: Beskriver hur du säkerhetskopiera och återställa krypterade virtuella Azure-datorer med Azure Backup-tjänsten.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358225"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Säkerhetskopiera och återställa krypterade virtuella Azure-datorer

Den här artikeln beskriver hur du säkerhetskopierar och återställer Windows eller Linux Azure-datorer (VM) med krypterade diskar med hjälp av den [Azure Backup](backup-overview.md) service.

Granska dessa resurser om du vill lära dig mer om hur Azure Backup samverkar med Azure virtuella datorer innan du börjar:

- [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitektur för Azure VM-säkerhetskopiering.
- [Lär dig mer om](backup-azure-vms-introduction.md) säkerhetskopiering av Azure virtuella datorer och Azure Backup-tillägget.

## <a name="encryption-support"></a>Stöd för kryptering

Azure Backup stöder säkerhetskopiering av Azure virtuella datorer som har sina OS/datadiskar krypterats med Azure Disk Encryption (ADE). ADE använder BitLocker för kryptering av Windows virtuella datorer och funktionen dm-crypt för Linux-datorer. ADE integreras med Azure Key Vault för att hantera diskkrypteringsnycklar och hemligheter. Krypteringsnycklar (KeyExchange-nycklar) för Key Vault nyckeln kan användas att lägga till ett extra lager av säkerhet, kryptering av kryptering hemligheter innan du skriver dem till Key Vault.

Azure Backup kan säkerhetskopiera och återställa virtuella Azure-datorer med hjälp av ADE med och utan Azure AD-app som sammanfattas i tabellen nedan.

**Typ av virtuell datordisk** | **ADE (BEK/dm-crypt)** | **ADE och KEK**
--- | --- | ---
**Ohanterade** | Ja | Ja
**Hanterad**  | Ja | Ja

- Läs mer om [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md), och [KeyExchange-nycklar](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Läs den [vanliga frågor och svar](../security/azure-security-disk-encryption-faq.md) för Virtuella Azure-diskkryptering.



### <a name="limitations"></a>Begränsningar

- Du kan säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region.
- Azure Backup stöder virtuella datorer som är krypterat med nycklar som fristående. Valfri tangent som är en del av ett certifikat som används för att kryptera en virtuell dator stöds inte för närvarande.
- Du kan säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region som Recovery Services-Backup-valvet.
- Inte att återställa krypterade virtuella datorer på nivån fil/mapp. Du behöver återställa en hel virtuell dator för att återställa filer och mappar.
- När du återställer en virtuell dator, du kan inte använda den [Ersätt befintlig virtuell dator](backup-azure-arm-restore-vms.md#restore-options) alternativet för krypterade virtuella datorer. Det här alternativet stöds bara för okrypterade hanterade diskar.




## <a name="before-you-start"></a>Innan du börjar

Innan du börjar måste du göra följande:

1. Kontrollera att du har en eller flera [Windows](../security/azure-security-disk-encryption-windows.md) eller [Linux](../security/azure-security-disk-encryption-linux.md) virtuella datorer med ADE aktiverat.
2. [Granska av stödmatrisen](backup-support-matrix-iaas.md) för virtuell Azure-säkerhetskopiering
3. [Skapa](backup-azure-arm-vms-prepare.md#create-a-vault) ett Recovery Services-Backup-valv om du inte har något.
4. Om du aktiverar kryptering för virtuella datorer som redan har aktiverats för säkerhetskopiering, behöver du bara ange säkerhetskopiering med behörighet till Key Vault så att säkerhetskopieringar kan fortsätta utan avbrott. [Läs mer](#provide-permissions) om hur du tilldelar dessa behörigheter.

Det finns dessutom några saker som du kan behöva göra i vissa fall:

- **Installera VM-agenten på den virtuella datorn**: Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn har skapats från en Azure marketplace-avbildning, är agenten installerad och körs. Om du skapar en anpassad virtuell dator eller om du migrerar en lokal virtuell dator, kan du behöva [installerar du agenten manuellt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Uttryckligen tillåta utgående åtkomst**: Normalt behöver du inte uttryckligen tillåta utgående nätverksåtkomst för en Azure-dator att kommunicera med Azure Backup. Men vissa virtuella datorer kan uppleva anslutningsproblem, som visar den **ExtensionSnapshotFailedNoNetwork** fel vid försök att ansluta. Om detta inträffar bör du [uttryckligen tillåta utgående åtkomst](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), så att tillägget Azure Backup kan kommunicera med Azure offentliga IP-adresser för säkerhetskopieringen.



## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

1. Om du inte har skapat ett Recovery Services-säkerhetskopieringsvalv, följer du [dessa instruktioner](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Öppna valvet i portalen och välj **Backup** i den **komma igång** avsnittet.

    ![Säkerhetskopiering bladet](./media/backup-azure-vms-encryption/select-backup.png)

3. I **säkerhetskopieringsmål** > **var körs din arbetsbelastning?** Välj **Azure**.
4. I **vad vill du säkerhetskopiera?** Välj **VM** > **OK**.

      ![Bladet scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. I **säkerhetskopieringspolicy** > **Välj säkerhetskopieringspolicy**, väljer du den princip som du vill associera med valvet. Klicka sedan på **OK**.
    - En princip för säkerhetskopiering anger när säkerhetskopior tas och hur länge de lagras.
    - Information om standardprincipen visas under den nedrullningsbara menyn.

    ![Öppna bladet Scenario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Om du inte vill använda standardprincipen väljer **Skapa ny**, och [skapar en anpassad princip](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. De krypterade virtuella datorer du vill säkerhetskopiera använder Välj principen och väljer **OK**.

      ![Välj krypterade virtuella datorer](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Om du använder Azure Key Vault, på sidan valv, visas ett meddelande om att Azure Backup behöver skrivskyddad åtkomst till nycklar och hemligheter i Key Vault.

    - Om du får det här meddelandet, krävs ingen åtgärd.

        ![Åtkomst OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Om du får det här meddelandet måste du ange behörigheter som beskrivs i den [proceduren nedan](#provide-permissions).

        ![Åtkomst-varning](./media/backup-azure-vms-encryption/access-warning.png)

9. Klicka på **Aktivera säkerhetskopiering** att distribuera principen för säkerhetskopiering i valvet och aktivera säkerhetskopiering för de valda virtuella datorerna.


## <a name="trigger-a-backup-job"></a>Utlösa ett säkerhetskopieringsjobb

Den första säkerhetskopieringen kommer att köras i enlighet med schemat, men du kan köra den direkt enligt följande:

1. I menyn valvet klickar du på **Säkerhetskopiera objekt**.
2. I **Säkerhetskopieringsobjekt** klickar du på **Azure-dator**.
3. I den **Säkerhetskopieringsobjekt** klickar du på ellipserna (...).
4. Klicka på **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**, använder kalenderkontrollen för att välja den sista dagen som återställningspunkten ska behållas. Klicka sedan på **OK**.
6. Meddelandena som portalen. Du kan övervaka jobbförloppet i instrumentpanelen för valvet > **säkerhetskopieringsjobb** > **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.


## <a name="provide-permissions"></a>Ange behörigheter

Azure virtuella datorn behöver skrivskyddad åtkomst för säkerhetskopiering av nycklar och hemligheter, tillsammans med de associerade virtuella datorerna.

- Key Vault är associerad med Azure AD-klient för Azure-prenumerationen. Om du är en **medlemsanvändare**, Azure Backup får åtkomst till Key Vault utan ytterligare åtgärder.
- Om du är en **gästanvändare**, måste du ange behörigheter för Azure Backup för att få åtkomst till nyckelvalvet.

Att ange behörigheter:

1. I Azure-portalen väljer du **alla tjänster**, och Sök efter **viktiga valv**.
2. Välj det nyckelvalv som är associerade med den krypterade virtuella datorn du säkerhetskopierar.
3. Välj **åtkomstprinciper** > **Lägg till ny**.
4. Välj **väljer huvudnamn**, och skriv sedan **säkerhetskopiering Management**.
5. Välj **säkerhetskopiera hanteringstjänsten** > **Välj**.

    ![Val av Backup-tjänsten](./media/backup-azure-vms-encryption/select-backup-service.png)

6. I **Lägg till åtkomstprincip** > **konfigurera från mall (valfritt)** väljer **Azure Backup**.
    - Behörigheterna som krävs är fylls i automatiskt för **Nyckelbehörigheter** och **hemliga behörigheter**.
    - Om din virtuella dator har krypterats med **BEK endast**, ta bort valet för **Nyckelbehörigheter** eftersom du bara behöver behörigheter för hemligheter.

    ![Azure backup val](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Klicka på **OK**. **Säkerhetskopiera hanteringstjänsten** läggs till i **åtkomstprinciper**.

    ![Åtkomstprinciper](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Klicka på **spara** att ge Azure Backup med behörigheter.

## <a name="restore-an-encrypted-vm"></a>Återställa en krypterad virtuell dator

Du återställa krypterade virtuella datorer på följande sätt:

1. [Återställa den Virtuella datordisken](backup-azure-arm-restore-vms.md#restore-disks).
2. Gör sedan något av följande:
    - Med mallen som genereras under återställningen att anpassa inställningarna för virtuella datorer och utlösa distribution av virtuella datorer. [Läs mer](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Skapa en ny virtuell dator från återställda diskar med Powershell. [Läs mer](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem, granska

- [Vanliga fel](backup-azure-vms-troubleshoot.md) när säkerhetskopiera och återställa krypterade virtuella Azure-datorer.
- [Azure VM-agenten/säkerhetskopieringstillägget](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problem.
