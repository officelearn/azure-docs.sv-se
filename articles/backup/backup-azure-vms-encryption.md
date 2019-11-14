---
title: Säkerhetskopiera och återställa krypterade virtuella Azure-datorer med Azure Backup
description: Beskriver hur du säkerhetskopierar och återställer krypterade virtuella Azure-datorer med tjänsten Azure Backup.
ms.reviewer: geg
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: dacurwin
ms.openlocfilehash: 004f15a1af11e3ed27f792e245888671b94fbb1a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074934"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Säkerhetskopiera och återställa krypterade virtuella Azure-datorer

Den här artikeln beskriver hur du säkerhetskopierar och återställer virtuella Windows-eller Linux Azure-datorer (VM: ar) med krypterade diskar med hjälp av tjänsten [Azure Backup](backup-overview.md) .

Om du vill veta mer om hur Azure Backup interagerar med virtuella Azure-datorer innan du börjar, granskar du följande resurser:

- [Granska](backup-architecture.md#architecture-direct-backup-of-azure-vms) arkitekturen för säkerhets kopiering av virtuella Azure-datorer.
- [Läs mer om](backup-azure-vms-introduction.md) Azure VM-säkerhetskopiering och Azure Backup-tillägget.

## <a name="encryption-support"></a>Krypterings stöd

Azure Backup stöder säkerhets kopiering av virtuella Azure-datorer som har sina operativ system/data diskar krypterade med Azure Disk Encryption (ADE). ADE använder BitLocker för kryptering av virtuella Windows-datorer och dm-crypt-funktionen för virtuella Linux-datorer. ADE integreras med Azure Key Vault för att hantera nycklar och hemligheter för disk kryptering. Key Vault nyckel krypterings nycklar (KeyExchange) kan användas för att lägga till ett extra säkerhets lager, kryptera krypterings hemligheter innan de skrivs till Key Vault.

Azure Backup kan säkerhetskopiera och återställa virtuella Azure-datorer med hjälp av ADE med och utan Azure AD-appen, som sammanfattas i följande tabell.

**Typ av virtuell datordisk** | **ADE (BEK/dm-crypt)** | **ADE och KEK**
--- | --- | ---
**Ohanterade** | Ja | Ja
**Leda**  | Ja | Ja

- Lär dig mer om [ade](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md)och [KeyExchange](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Läs [vanliga frågor och svar](../security/azure-security-disk-encryption-faq.md) om Azure VM Disk Encryption.

### <a name="limitations"></a>Begränsningar

- Du kan säkerhetskopiera och återställa krypterade virtuella datorer inom samma prenumeration och region.
- Azure Backup stöder virtuella datorer som har krypterats med fristående nycklar. Alla nycklar som ingår i ett certifikat som används för att kryptera en virtuell dator stöds inte för närvarande.
- Du kan säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region som Recovery Services säkerhets kopierings valv.
- Det går inte att återställa krypterade virtuella datorer på nivån fil/mapp. Du måste återställa hela den virtuella datorn för att återställa filer och mappar.
- När du återställer en virtuell dator kan du inte använda alternativet [Ersätt befintlig virtuell dator](backup-azure-arm-restore-vms.md#restore-options) för krypterade virtuella datorer. Det här alternativet stöds bara för okrypterade hanterade diskar.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar gör du följande:

1. Se till att du har en eller flera virtuella [Windows](../security/azure-security-disk-encryption-windows.md) -eller [Linux](../virtual-machines/linux/disk-encryption-overview.md) -datorer med ade aktiverat.
2. [Granska support matrisen](backup-support-matrix-iaas.md) för säkerhets kopiering av virtuella Azure-datorer
3. [Skapa](backup-azure-arm-vms-prepare.md#create-a-vault) ett Recovery Services säkerhets kopierings valv om du inte har något.
4. Om du aktiverar kryptering för virtuella datorer som redan har Aktiver ATS för säkerhets kopiering behöver du bara ange säkerhets kopiering med behörigheter för att få åtkomst till Key Vault så att säkerhets kopior kan fortsätta utan avbrott. [Läs mer](#provide-permissions) om att tilldela dessa behörigheter.

Dessutom finns det några saker som du kan behöva göra i vissa fall:

- **Installera VM-agenten på den virtuella datorn**: Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva [Installera agenten manuellt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Tillåt uttryckligen utgående åtkomst**: vanligt vis behöver du inte uttryckligen tillåta utgående nätverks åtkomst för en virtuell Azure-dator för att den ska kunna kommunicera med Azure Backup. Vissa virtuella datorer kan dock drabbas av anslutnings problem, vilket visar **ExtensionSnapshotFailedNoNetwork** -fel vid försök att ansluta. Om detta händer bör du [uttryckligen tillåta utgående åtkomst](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), så Azure Backup tillägget kan kommunicera med offentliga Azure-IP-adresser för säkerhets kopierings trafik.

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

1. Om du ännu inte har skapat ett Recovery Services säkerhets kopierings valv följer du [dessa anvisningar](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Öppna valvet i portalen och välj **säkerhets kopiering** i avsnittet **komma igång** .

    ![Säkerhets kopierings blad](./media/backup-azure-vms-encryption/select-backup.png)

3. I **säkerhets kopierings mål** > **var körs din arbets belastning?** Välj **Azure**.
4. I **vad vill du säkerhetskopiera?** Välj **virtuell dator** > **OK**.

      ![Bladet scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. I **säkerhets kopierings policy** > **väljer du säkerhets kopierings princip**väljer du den princip som du vill associera med valvet. Klicka på **OK**.
    - En säkerhets kopierings princip anger när säkerhets kopieringar görs och hur länge de lagras.
    - Information om standardprincipen visas under den nedrullningsbara menyn.

    ![Öppna bladet Scenario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Om du inte vill använda standard principen väljer du **Skapa ny**och [skapar en anpassad princip](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Välj de krypterade virtuella datorer som du vill säkerhetskopiera med hjälp av Välj princip och välj **OK**.

      ![Välj krypterade virtuella datorer](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Om du använder Azure Key Vault visas ett meddelande på sidan valv som Azure Backup behöver skrivskyddad åtkomst till nycklarna och hemligheterna i Key Vault.

    - Om du får det här meddelandet krävs ingen åtgärd.

        ![Åtkomst OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Om du får det här meddelandet måste du ange behörigheter enligt beskrivningen i [proceduren nedan](#provide-permissions).

        ![Åtkomst varning](./media/backup-azure-vms-encryption/access-warning.png)

9. Klicka på **Aktivera säkerhets kopiering** för att distribuera säkerhets kopierings principen i valvet och aktivera säkerhets kopiering för de valda virtuella datorerna.

## <a name="trigger-a-backup-job"></a>Utlösa ett säkerhets kopierings jobb

Den första säkerhets kopieringen kommer att köras enligt schemat, men du kan köra den direkt på följande sätt:

1. I menyn valv klickar du på **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt**klickar du på **virtuell Azure-dator**.
3. I listan **säkerhets kopierings objekt** klickar du på ellipserna (...).
4. Klicka på **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Klicka på **OK**.
6. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb** > **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="provide-permissions"></a>Ange behörigheter

Azure VM behöver skrivskyddad åtkomst för att säkerhetskopiera nycklar och hemligheter, tillsammans med de associerade virtuella datorerna.

- Din Key Vault är kopplad till Azure AD-klienten för Azure-prenumerationen. Om du är **medlems användare**får Azure Backup få åtkomst till Key Vault utan ytterligare åtgärder.
- Om du är **gäst användare**måste du ange behörigheter för Azure Backup för att få åtkomst till nyckel valvet.

Ange behörigheter:

1. I Azure Portal väljer du **alla tjänster**och söker efter **nyckel valv**.
2. Välj nyckel valvet som är associerat med den krypterade virtuella datorn som du säkerhetskopierar.
3. Välj **åtkomst principer** > **Lägg till ny**.
4. Välj **Välj huvud konto**och skriv sedan **säkerhets kopierings hantering**.
5. Välj **säkerhets kopierings hanterings tjänst** > **väljer**.

    ![Val av säkerhets kopierings tjänst](./media/backup-azure-vms-encryption/select-backup-service.png)

6. I **Lägg till åtkomst princip** > **Konfigurera från mall (valfritt)** väljer du **Azure Backup**.
    - De behörigheter som krävs är förifyllda för **nyckel behörigheter** och **hemliga behörigheter**.
    - Om den virtuella datorn är krypterad med **endast Bek**tar du bort valet för **nyckel behörigheter** eftersom du bara behöver behörighet för hemligheter.

    ![Val av Azure Backup](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Klicka på **OK**. **Säkerhets kopierings hanterings tjänsten** har lagts till i **åtkomst principer**.

    ![Åtkomstprinciper](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Klicka på **Spara** för att ange Azure Backup med behörigheterna.

## <a name="restore-an-encrypted-vm"></a>Återställa en krypterad virtuell dator

Du återställer krypterade virtuella datorer på följande sätt:

1. [Återställa den virtuella dator disken](backup-azure-arm-restore-vms.md#restore-disks).
2. Gör sedan något av följande:
    - Använd mallen som genereras under återställnings åtgärden för att anpassa VM-inställningar och utlösa VM-distribution. [Läs mer](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Skapa en ny virtuell dator från de återställda diskarna med PowerShell. [Läs mer](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
    - För virtuella Linux-datorer återställer du ADE-tillägget så att data diskarna är öppna och monterade.

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem kan du läsa följande artiklar:

- [Vanliga fel](backup-azure-vms-troubleshoot.md) vid säkerhets kopiering och återställning av krypterade virtuella Azure-datorer.
- Problem med [Azure VM-agent/säkerhets kopierings tillägg](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .
