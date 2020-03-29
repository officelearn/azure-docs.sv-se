---
title: Säkerhetskopiera och återställa krypterade virtuella Azure-datorer
description: Beskriver hur du säkerhetskopierar och återställer krypterade virtuella Azure-datorer med Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206698"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Säkerhetskopiera och återställa krypterad Azure VM

I den här artikeln beskrivs hur du säkerhetskopierar och återställer virtuella Datorer [Azure Backup](backup-overview.md) med Windows eller Linux Azure (VMs) med krypterade diskar med hjälp av Azure Backup-tjänsten.

Om du vill veta mer om hur Azure Backup interagerar med virtuella Azure-datorer innan du börjar läser du dessa resurser:

- [Granska](backup-architecture.md#architecture-built-in-azure-vm-backup) azure VM-säkerhetskopieringsarkitekturen.
- [Läs mer](backup-azure-vms-introduction.md) Azure VM-säkerhetskopiering och Azure Backup-tillägget.

## <a name="encryption-support"></a>Stöd för kryptering

Azure Backup stöder säkerhetskopiering av virtuella Azure-datorer som har sina OS/datadiskar krypterade med Azure Disk Encryption (ADE). ADE använder BitLocker för kryptering av Virtuella Windows-datorer och dm-crypt-funktionen för virtuella Linux-datorer. ADE integreras med Azure Key Vault för att hantera diskkrypteringsnycklar och hemligheter. Key Vault Key Encryption Keys (KEKs) kan användas för att lägga till ytterligare ett lager av säkerhet, kryptera kryptering hemligheter innan du skriver dem till Key Vault.

Azure Backup kan säkerhetskopiera och återställa virtuella Azure-datorer med ADE med och utan Azure AD-appen, som sammanfattas i följande tabell.

**Disktyp för virtuell dator** | **ADE (BEK/dm-krypta)** | **ADE och KEK**
--- | --- | ---
**Ohanterade** | Ja | Ja
**Hanterade**  | Ja | Ja

- Läs mer om [ADE,](../security/azure-security-disk-encryption-overview.md) [Key Vault](../key-vault/key-vault-overview.md)och [KEKs](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Läs [vanliga frågor och svar](../security/azure-security-disk-encryption-faq.md) för diskkryptering för Azure VM.

### <a name="limitations"></a>Begränsningar

- Du kan säkerhetskopiera och återställa krypterade virtuella datorer inom samma prenumeration och region.
- Azure Backup stöder virtuella datorer krypterade med fristående nycklar. Alla nyckeler som är en del av ett certifikat som används för att kryptera en virtuell dator stöds för närvarande inte.
- Du kan säkerhetskopiera och återställa krypterade virtuella datorer inom samma prenumeration och region som säkerhetskopieringsvalvet för återställningstjänster.
- Krypterade virtuella datorer kan inte återställas på fil-/mappnivå. Du måste återställa hela den virtuella datorn för att återställa filer och mappar.
- När du återställer en virtuell dator kan du inte använda alternativet [ersätta befintlig virtuell dator](backup-azure-arm-restore-vms.md#restore-options) för krypterade virtuella datorer. Det här alternativet stöds bara för okrypterade hanterade diskar.

## <a name="before-you-start"></a>Innan du börjar

Gör följande innan du börjar:

1. Kontrollera att du har en eller flera [virtuella Windows-](../security/azure-security-disk-encryption-windows.md) eller [Linux-datorer](../virtual-machines/linux/disk-encryption-overview.md) med ADE aktiverat.
2. [Granska supportmatrisen](backup-support-matrix-iaas.md) för säkerhetskopiering av Azure VM
3. [Skapa](backup-azure-arm-vms-prepare.md#create-a-vault) ett säkerhetskopieringsvalv för återställningstjänster om du inte har något.
4. Om du aktiverar kryptering för virtuella datorer som redan är aktiverade för säkerhetskopiering behöver du bara ange säkerhetskopiering med behörighet för att komma åt Key Vault så att säkerhetskopior kan fortsätta utan avbrott. [Läs mer](#provide-permissions) om att tilldela dessa behörigheter.

Dessutom finns det ett par saker som du kan behöva göra under vissa omständigheter:

- **Installera VM-agenten på den virtuella datorn:** Azure Backup säkerhetskopierar Virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om din virtuella dator skapades från en Azure marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva [installera agenten manuellt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

1. Om du ännu inte har skapat ett [säkerhetskopieringsvalv](backup-azure-arm-vms-prepare.md#create-a-vault) för Återställningstjänster följer du dessa instruktioner
2. Öppna valvet i portalen och välj **Säkerhetskopiering** i avsnittet **Komma igång.**

    ![Bladet Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. I **Säkerhetskopieringsmål** > Var körs **Azure****din arbetsbelastning?**
4. I **Vad vill du säkerhetskopiera?** **Virtual machine** > **OK**

      ![Scenario blad](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Välj den princip som du vill associera med valvet i principer > för **säkerhetskopiering**Välj**säkerhetskopiering.** Klicka sedan på **OK**.
    - En princip för säkerhetskopiering anger när säkerhetskopieringar görs och hur lång tid de lagras.
    - Information om standardprincipen visas under den nedrullningsbara menyn.

    ![Öppna bladet Scenario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Om du inte vill använda standardprincipen väljer du **Skapa ny**och skapar en [anpassad princip](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Välj de krypterade virtuella datorer som du vill säkerhetskopiera med select-principen och välj **OK**.

      ![Välj krypterade virtuella datorer](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Om du använder Azure Key Vault visas ett meddelande om att Azure Backup behöver skrivskyddad åtkomst till nycklar och hemligheter i Nyckelvalvet på arkivsidan.

    - Om du får det här meddelandet krävs ingen åtgärd.

        ![Tillgång till OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Om du får det här meddelandet måste du ange behörigheter enligt beskrivningen i [proceduren nedan](#provide-permissions).

        ![Varning för åtkomst](./media/backup-azure-vms-encryption/access-warning.png)

9. Klicka på **Aktivera säkerhetskopiering** om du vill distribuera säkerhetskopieringsprincipen i valvet och aktivera säkerhetskopiering för de valda virtuella datorerna.

## <a name="trigger-a-backup-job"></a>Utlösa ett säkerhetskopieringsjobb

Den första säkerhetskopian körs i enlighet med schemat, men du kan köra den omedelbart enligt följande:

1. Klicka på **Säkerhetsobjekt**på arkivmenyn.
2. Klicka på **Azure Virtual Machine i** **säkerhetskopieringsobjekt**.
3. Klicka på ellipserna (...) i listan **Säkerhetsobjekt.**
4. Klicka på **Säkerhetskopiering nu**.
5. I **Säkerhetskopiering nu**använder du kalenderkontrollen för att välja den sista dagen då återställningspunkten ska behållas. Klicka sedan på **OK**.
6. Övervaka portalmeddelandena. Du kan övervaka jobbframsteget i instrumentpanelen för valvet >**pågående** **säkerhetskopieringsjobb** > . Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="provide-permissions"></a>Ange behörigheter

Azure VM behöver skrivskyddad åtkomst för att säkerhetskopiera nycklar och hemligheter, tillsammans med tillhörande virtuella datorer.

- Ditt Key Vault är associerat med Azure AD-klienten för Azure-prenumerationen. Om du är **medlemsanvändare**får Azure Backup åtkomst till nyckelvalvet utan ytterligare åtgärder.
- Om du är **gästanvändare**måste du ange behörigheter för Azure Backup för att komma åt nyckelvalvet.

Så här anger du behörigheter:

1. I Azure-portalen väljer du **Alla tjänster**och söker efter **nyckelvalv**.
2. Välj det nyckelvalv som är associerat med den krypterade virtuella datorn som du säkerhetskopierar.
3. Välj **Åtkomstprinciper** > **Lägg till nya**.
4. Välj **Huvudnamn**och skriv sedan **Säkerhetskopieringshantering**.
5. Välj**Tjänst för** **säkerhetskopieringshantering** > .

    ![Val av säkerhetskopiering av tjänsten](./media/backup-azure-vms-encryption/select-backup-service.png)

6. I **Lägg till åtkomstprincip** > **Konfigurera från mall (valfritt)** väljer du **Azure Backup**.
    - De behörigheter som krävs är förifyllda för **nyckelbehörigheter** och **hemliga behörigheter**.
    - Om den virtuella datorn endast är krypterad med **BEK**tar du bort valet för **nyckelbehörigheter** eftersom du bara behöver behörigheter för hemligheter.

    ![Val av Azure-säkerhetskopia](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Klicka på **OK**. **Tjänsten för säkerhetskopieringshantering** läggs till **i åtkomstprinciper**.

    ![Åtkomstprinciper](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Klicka på **Spara** om du vill ge Azure Backup behörigheterna.

## <a name="restore-an-encrypted-vm"></a>Återställa en krypterad virtuell dator

Du återställer krypterade virtuella datorer enligt följande:

1. [Återställ vm-disken](backup-azure-arm-restore-vms.md#restore-disks).
2. Återskapa instansen för den virtuella datorn genom att göra något av följande:
    1. Använd mallen som genereras under återställningen för att anpassa vm-inställningar och utlösa vm-distribution. [Läs mer](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Skapa en ny virtuell dator från de återställda diskarna med PowerShell. [Läs mer](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. För virtuella Linux-datorer installerar du om ADE-tillägget så att datadiskarna är öppna och monterade.

## <a name="next-steps"></a>Nästa steg

Om du stöter på några problem läser du dessa artiklar:

- Vanliga fel när du [säkerhetskopierar](backup-azure-vms-troubleshoot.md) och återställer krypterade virtuella Azure-datorer.
- [Problem med tillägg för Azure VM-agent/säkerhetskopiering.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
