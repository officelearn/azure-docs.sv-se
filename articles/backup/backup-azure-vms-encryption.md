---
title: Säkerhetskopiera och återställa krypterade virtuella datorer med hjälp av Azure Backup
description: Den här artikeln handlar om säkerhetskopiering och återställning av virtuella datorer krypteras med hjälp av Azure Disk Encryption.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: geetha
ms.openlocfilehash: 676c6a45f4a3930d350bbcbdcbb1a0fb47880407
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810005"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup
Den här artikeln handlar om stegen för att säkerhetskopiera och återställa virtuella datorer (VM) med hjälp av Azure Backup. Den innehåller också information om scenarier som stöds, nödvändiga komponenter och felsökningssteg för fel.

## <a name="supported-scenarios"></a>Scenarier som stöds

 Säkerhetskopiering och återställning av krypterade virtuella datorer stöds bara för virtuella datorer som använder Azure Resource Manager-distributionsmodellen. Det finns inte stöd för virtuella datorer som använder den klassiska distributionsmodellen. Säkerhetskopiering och återställning av krypterade virtuella datorer stöds för Windows och Linux-datorer som använder Azure Disk Encryption. Diskkryptering använder branschens standard BitLocker-funktion i Windows och dm-crypt i Linux för att tillhandahålla kryptering av diskar. I följande tabell visar krypteringstyp och stöd för virtuella datorer.

   |  | BEK och KEK virtuella datorer | BEK endast virtuella datorer |
   | --- | --- | --- |
   | **Icke-hanterade virtuella datorer**  | Ja | Ja  |
   | **Hanterade virtuella datorer**  | Ja | Ja  |

   > [!NOTE]
   > Azure Backup stöder virtuella datorer som har krypterats med fristående nycklar. Valfri tangent som är en del av ett certifikat som används för att kryptera en virtuell dator stöds inte i dag.
   >   

## <a name="prerequisites"></a>Förutsättningar
* Den virtuella datorn har krypterats med hjälp av [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Recovery Services-valvet har skapats och storage-replikering har angetts genom att följa stegen i [förbereda miljön för säkerhetskopiering](backup-azure-arm-vms-prepare.md).

* Säkerhetskopiering har gett behörighet att komma åt ett nyckelvalv som innehåller nycklar och hemligheter för krypterade virtuella datorer.

## <a name="backup-encrypted-vm"></a>Backup-krypterad virtuell dator
Använd följande steg för att ange ett säkerhetskopieringsmål, definiera en princip, konfigurera och utlösa en säkerhetskopia.

### <a name="configure-backup"></a>Konfigurera säkerhetskopiering
1. Om du redan har ett Recovery Services-valv öppnar du gå vidare till nästa steg. Om du inte har ett Recovery Services-valv som är öppna, men du befinner dig i Azure portal, Välj **alla tjänster**.

   a. I listan över resurser skriver du **Recovery Services**.

   b. När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv**, markera den.

      ![Recovery Services-valv](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Listan över Recovery Services-valv visas. Välj ett valv i listan.

     Instrumentpanelen för det valda valvet öppnas.
1. I listan över objekt som visas under valvet, väljer **Backup** att börja säkerhetskopiera krypterade virtuella datorn.

      ![Säkerhetskopiering bladet](./media/backup-azure-vms-encryption/select-backup.png)
1. På den **Backup** panelen, väljer **säkerhetskopieringsmål**.

      ![Bladet scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. Under **var körs din arbetsbelastning?** väljer **Azure**. Under **vad vill du säkerhetskopiera?** väljer **VM**. Välj sedan **OK**.

   ![Öppna bladet Scenario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. Under **Välj säkerhetskopieringspolicy**, Välj den säkerhetskopieringspolicy som du vill använda för valvet. Välj sedan **OK**.

      ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Information om standardprincipen visas. Om du vill skapa en princip väljer **Skapa ny** från den nedrullningsbara listan. När du har valt **OK**, principen för säkerhetskopiering är associerad med valvet.

1. Välj de krypterade virtuella datorerna som associeras med den angivna principen och välj **OK**.

      ![Välj krypterade virtuella datorer](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. Den här sidan visas ett meddelande om nyckelvalv som är kopplad till de krypterade virtuella datorer som du har valt. Backup kräver skrivskyddad åtkomst till nycklar och hemligheter i nyckelvalvet. De här behörigheterna används för att säkerhetskopiera nycklar och hemligheter, tillsammans med de associerade virtuella datorerna.<br>
Om du är en **medlemsanvändare**, aktivera säkerhetskopieringsprocessen kommer sömlöst få åtkomst till nyckelvalvet för att säkerhetskopiera krypterade virtuella datorer utan användarens ingripande.

   ![Meddelande för krypterade virtuella datorer](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   För en **gästanvändare**, måste du ange behörigheter till säkerhetskopieringstjänsten för att få åtkomst till nyckelvalvet för säkerhetskopior att fungera. Du kan ange dessa behörigheter genom att följa stegen som beskrivs i följande avsnitt

   ![Meddelande för krypterade virtuella datorer](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    Nu när du har definierat alla inställningar för valvet, Välj **Aktivera säkerhetskopiering** längst ned på sidan. **Aktivera säkerhetskopiering** distribueras principen till valvet och de virtuella datorerna.

1. Nästa fas i förberedelse installerar VM-agenten eller att se till att den Virtuella Datoragenten är installerad. Om du vill göra samma sak, följer du stegen i [förbereda miljön för säkerhetskopiering](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Utlösa ett säkerhetskopieringsjobb
Följ stegen i [säkerhetskopierade virtuella Azure-datorer till ett Recovery Services-valv](backup-azure-arm-vms.md) att utlösa ett säkerhetskopieringsjobb.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Fortsätt säkerhetskopiering av redan säkerhetskopierade virtuella datorer med kryptering är aktiverat  
Om du har virtuella datorer redan säkerhetskopieras i ett Recovery Services-valv som har aktiverats för kryptering senare, måste du ge behörigheter till Backup för att få åtkomst till nyckelvalvet för säkerhetskopior att fortsätta. Du kan ange dessa behörigheter genom att följa den [stegen i följande avsnitt](#provide-permissions-to-azure-backup). Du kan också följa stegen i avsnittet ”Aktivera säkerhetskopiering” i PowerShell på [PowerShell-dokumentationen](backup-azure-vms-automation.md).

## <a name="provide-permissions-to-azure-backup"></a>Ange behörigheter för säkerhetskopiering
Använd följande steg för att ange relevant behörighet till Backup för att få åtkomst till nyckelvalvet och utför säkerhetskopiering av krypterade virtuella datorer.
1. Välj **alla tjänster**, och Sök efter **viktiga valv**.

    ![Nyckelvalv](./media/backup-azure-vms-encryption/search-key-vault.png)

1. Listan över nyckelvalv, Välj det nyckelvalv som är associerade med den krypterade virtuella datorn som behöver säkerhetskopieras.

     ![Val av nyckelvalv](./media/backup-azure-vms-encryption/select-key-vault.png)

1. Välj **åtkomstprinciper**, och välj sedan **Lägg till ny**.

    ![Lägg till ny](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. Välj **väljer huvudnamn**, och skriv sedan **Backup Management-tjänsten** i sökrutan.

    ![Säkerhetskopieringstjänsten sökning](./media/backup-azure-vms-encryption/search-backup-service.png)

1. Välj **Backup Management-tjänsten**, och välj sedan **Välj**.

    ![Val av Backup-tjänsten](./media/backup-azure-vms-encryption/select-backup-service.png)

1. Under **konfigurera från mall (valfritt)** väljer **Azure Backup**. Behörigheterna som krävs är fylls i automatiskt för **Nyckelbehörigheter** och **hemliga behörigheter**. Om den virtuella datorn är krypterad med hjälp av **BEK endast**, endast för hemligheter krävs, så måste du ta bort valet för **Nyckelbehörigheter**.

    ![Azure backup val](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Välj **OK**. Observera att **Backup Management-tjänsten** läggs i **åtkomstprinciper**.

    ![Åtkomstprinciper](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Välj **spara** att ge behörigheterna som krävs för säkerhetskopiering.

    ![Säkerhetskopiering åtkomstprincip](./media/backup-azure-vms-encryption/save-access-policy.png)

När behörigheterna har har angetts, kan du fortsätta med att aktivera säkerhetskopiering för krypterade virtuella datorer.

## <a name="restore-an-encrypted-vm"></a>Återställa en krypterad virtuell dator
Azure Backup nu stöder återställning av [Azure krypterade virtuella datorer utan Azure AD](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad) utöver den tidigare versionen av återställning stöd till Azure krypterade virtuella datorer med Azure AD.<br>

Om du vill återställa en krypterad virtuell dator, återställa diskar genom att följa stegen i avsnittet ”återställa säkerhetskopierade diskar” i [väljer en konfiguration för återställning av virtuell dator](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Efter det kan använda du något av följande alternativ:

* Följ PowerShell steg i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) skapa en fullständig virtuell dator från återställda diskar.
* Eller, [anpassa en återställd virtuell dator med hjälp av mallar](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) att skapa virtuella datorer från återställda diskar. Mallar kan användas endast för återställningspunkter som skapats efter den 26 April 2017.

## <a name="troubleshooting-errors"></a>Felsöka fel
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
|Backup | Felkod: UserErrorKeyVaultPermissionsNotConfigured<br><br>Felmeddelande: Azure Backup-tjänsten har inte tillräcklig behörighet till Key Vault för säkerhetskopiering krypterade virtuella datorer. | Säkerhetskopiering ska tillhandahållas behörigheterna genom att följa den [stegen i föregående avsnitt](#provide-permissions-to-azure-backup). Du kan också följa stegen i avsnittet ”Aktivera skydd” i artikeln för PowerShell [Använd PowerShell för att säkerhetskopiera och återställa virtuella datorer](backup-azure-vms-automation.md#enable-protection). |  
| Återställ | Du kan inte återställa den här krypterade virtuella datorn eftersom det nyckelvalv som är associerade med den här virtuella datorn inte finns. |Skapa ett nyckelvalv med hjälp av [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md). Se [återställa en key vault-nyckeln och en hemlighet med hjälp av Azure Backup](backup-azure-restore-key-secret.md) att återställa en nyckel och en hemlighet, om de inte finns. |
| Återställ | Felkod: UserErrorKeyVaultKeyDoesNotExist<br><br> Felmeddelande: Du kan inte återställa den här krypterade virtuella datorn eftersom nyckeln som är associerade med den här virtuella datorn inte finns. |Se [återställa en key vault-nyckeln och en hemlighet med hjälp av Azure Backup](backup-azure-restore-key-secret.md) att återställa en nyckel och en hemlighet, om de inte finns. |
| Återställ | Felkod: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>Felmeddelande: Säkerhetskopieringstjänsten har inte åtkomstbehörighet till resurser i din prenumeration. |Som tidigare nämnts, Återställ diskar först genom att följa stegen i avsnittet ”återställa säkerhetskopierade diskar” i [väljer en konfiguration för återställning av virtuell dator](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Använd PowerShell för att [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
