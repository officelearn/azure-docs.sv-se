---
title: Säkerhetskopiera och återställa krypterade virtuella datorer med hjälp av Azure Backup
description: Den här artikeln handlar om säkerhetskopiering och återställning upplevelsen för virtuella datorer som har krypterats med hjälp av Azure Disk Encryption.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c788720e046c2efef954ef77f7b52854439b7515
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Säkerhetskopiera och återställa den krypterade virtuella datorer med Azure Backup
Den här artikeln handlar om stegen för att säkerhetskopiera och återställa virtuella datorer (VM) med hjälp av Azure Backup. Det ger också information om scenarier som stöds, krav och felsökningssteg för fel.

## <a name="supported-scenarios"></a>Scenarier som stöds

 * Säkerhetskopiering och återställning av krypterade virtuella datorer stöds bara för virtuella datorer som använder Azure Resource Manager-distributionsmodellen. Det finns inte stöd för virtuella datorer som använder den klassiska distributionsmodellen. <br>
 * Säkerhetskopiering och återställning av krypterade virtuella datorer stöds för både Windows- och Linux virtuella datorer som använder Azure Disk Encryption. Diskkryptering använder funktionen industry standard BitLocker i Windows och funktionen dm-crypt i Linux för att tillhandahålla kryptering av diskar. <br>
 
 I följande tabell visas de scenarier som stöds för BitLocker-krypteringsnyckeln (BEK) - endast och viktiga krypteringsnyckel (KEK) - krypterad virtuella datorer:
 
 
   |  | BEK + KEK virtuella datorer | Endast BEK virtuella datorer |
   | --- | --- | --- |
   | **Icke-hanterade virtuella datorer**  | Ja | Ja  |
   | **Hanterade virtuella datorer**  | Ja | Ja  |

## <a name="prerequisites"></a>Förutsättningar
* Den virtuella datorn har krypterats med hjälp av [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Recovery Services-valvet har skapats och storage-replikering har angetts genom att följa stegen i [förbereda miljön för säkerhetskopiering](backup-azure-arm-vms-prepare.md).

* Säkerhetskopiering har angett [behörighet att komma åt ett nyckelvalv](#provide-permissions-to-backup) som innehåller nycklar och hemligheter för krypterade virtuella datorer.

## <a name="backup-encrypted-vm"></a>Krypterad säkerhetskopiering VM
Använd följande steg för att ange ett mål för säkerhetskopiering, definiera en princip, konfigurera objekt och utlösa en säkerhetskopia.

### <a name="configure-backup"></a>Konfigurera säkerhetskopiering
1. Om du redan har ett Recovery Services-valv öppna vidare till nästa steg. Om du inte har ett Recovery Services-valv öppna men du befinner dig i Azure portal, Välj **alla tjänster**.

   a. I listan över resurser skriver du **Recovery Services**.

   b. När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv**, markerar du den.

      ![Recovery Services-valv](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Listan över Recovery Services-valv visas. Välj ett valv i listan.

     Instrumentpanelen för det valda valvet öppnas.
2. Listan över objekt som visas under valvet, Välj **säkerhetskopiering** att starta säkerhetskopiering av krypterade VM.

      ![Säkerhetskopiering bladet](./media/backup-azure-vms-encryption/select-backup.png)
3. På den **säkerhetskopiering** panelen, väljer **säkerhetskopiering målet**.

      ![Scenario-bladet](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Under **var körs din arbetsbelastning?** väljer **Azure**. Under **vad vill du säkerhetskopiera?** väljer **virtuella**. Välj sedan **OK**.

   ![Öppna bladet Scenario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Under **Välj säkerhetskopieringsprincip**, Välj den säkerhetskopieringsprincip som du vill koppla till valvet. Välj sedan **OK**.

      ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Information om standardprincipen visas. Om du vill skapa en princip, väljer **Skapa nytt** från den nedrullningsbara listan. När du har valt **OK**, principen för säkerhetskopiering är kopplad till valvet.

6. Välj de kryptera virtuella datorerna som ska associeras med den angivna principen och välj **OK**.

      ![Välj krypterade virtuella datorer](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Den här sidan visas ett meddelande om nyckelvalv som är associerade med de krypterade virtuella datorer som du har valt. Skrivskyddad åtkomst till nycklar och hemligheter i nyckelvalvet krävs. Dessa behörigheter används för att säkerhetskopiera nycklar och hemligheter, tillsammans med associerade virtuella datorer.<br>
Om du är en **medlem användaren**, aktivera säkerhetskopieringen kommer sömlöst få åtkomst till nyckelvalvet till säkerhetskopiering krypterade virtuella datorer utan användarens ingripande.

   ![Krypterat meddelande för virtuella datorer](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   För en **gästanvändare**, måste du ange behörigheter till säkerhetskopieringstjänsten för att få åtkomst till nyckelvalvet för säkerhetskopiering ska fungera. Du kan ange behörigheterna genom att följa den [anvisningarna i följande avsnitt](#provide-permissions-to-backup)

   ![Krypterat meddelande för virtuella datorer](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Nu när du har definierat alla inställningar för valvet, Välj **Aktivera säkerhetskopiering** längst ned på sidan. **Aktivera säkerhetskopiering** distribueras principen till valvet och de virtuella datorerna.
  
8. Nästa fas förberedelse installeras den Virtuella Datoragenten eller kontrollera att den Virtuella Datoragenten är installerad. Om du vill göra samma följer du stegen i [förbereda miljön för säkerhetskopiering](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Utlösa ett säkerhetskopieringsjobb
Följ stegen i [säkerhetskopiering virtuella Azure-datorer till ett Recovery Services-valv](backup-azure-arm-vms.md) att utlösa ett säkerhetskopieringsjobb.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Fortsätt säkerhetskopior av virtuella datorer redan säkerhetskopierade med kryptering aktiverat  
Om du har virtuella datorer redan säkerhetskopieras i Recovery Services-valvet som är aktiverade för kryptering senare, måste du ge behörigheter till Backup för att komma åt nyckelvalvet för säkerhetskopieringar att fortsätta. Du kan ange behörigheterna genom att följa den [stegen i följande avsnitt](#provide-permissions-to-azure-backup). Du kan följa PowerShell anvisningarna i avsnittet ”Aktivera säkerhetskopiering” i den [PowerShell dokumentationen](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Ger behörighet att säkerhetskopiera
Använd följande steg för att ange relevant behörighet till Backup för att komma åt nyckelvalvet och utför säkerhetskopiering av krypterade virtuella datorer.
1. Välj **alla tjänster**, och Sök efter **nyckeln valv**.

    ![Nyckelvalv](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Välj i listan över nyckelvalv nyckelvalvet som är associerade med den krypterade VM som behöver säkerhetskopieras.

     ![Val av nyckelvalv](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Välj **åtkomstprinciper**, och välj sedan **Lägg till ny**.

    ![Lägg till ny](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Välj **väljer principal**, och skriv sedan **säkerhetskopiering hanteringstjänsten** i sökrutan. 

    ![Säkerhetskopieringstjänsten sökning](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Välj **säkerhetskopiering hanteringstjänsten**, och välj sedan **Välj**.

    ![Val av säkerhetskopieringstjänsten](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Under **konfigurera från mall (valfritt)** väljer **Azure Backup**. Behörigheterna som krävs fylls i automatiskt för **nyckeln behörigheter** och **hemliga behörigheterna**. Om den virtuella datorn är krypterad med hjälp av **BEK endast**, bara behörigheter för hemligheter krävs, så måste du ta bort markeringen för **nyckeln behörigheter**.

    ![Val av Azure backup](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Välj **OK**. Observera att **säkerhetskopiering hanteringstjänsten** hämtar lades till i **åtkomstprinciper**. 

    ![Åtkomstprinciper](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Välj **spara** att ge behörigheterna som krävs för säkerhetskopiering.

    ![Princip för säkerhetskopiering åtkomst](./media/backup-azure-vms-encryption/save-access-policy.png)

När du har behörigheter tillhandahålls kan du fortsätta med att aktivera säkerhetskopiering för krypterade virtuella datorer.

## <a name="restore-an-encrypted-vm"></a>Återställa en krypterad VM
Om du vill återställa en virtuell dator i krypterade återställa diskar genom att följa stegen i avsnittet ”återställa säkerhetskopierade diskar” i [väljer en konfiguration för återställning av Virtuella](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Sedan kan använda du något av följande alternativ:

* Följ PowerShell stegen i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) att skapa en fullständig virtuell dator från återställda diskar.
* Eller, [använda mallar för att anpassa en återställd VM](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) att skapa virtuella datorer från återställda diskar. Mallar kan användas endast för återställningspunkter som skapats efter 26 April 2017.

## <a name="troubleshooting-errors"></a>Felsöka fel
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
|Backup | Säkerhetskopiering har inte tillräcklig behörighet för att nyckelvalvet för säkerhetskopiering av krypterade virtuella datorer. | Säkerhetskopiering ska tillhandahållas behörigheterna genom att följa den [stegen i föregående avsnitt](#provide-permissions-to-azure-backup). Du kan följa PowerShell anvisningarna i avsnittet ”Aktivera skydd” i PowerShell-dokumentationen på [Använd AzureRM.RecoveryServices.Backup-cmdletar för att säkerhetskopiera virtuella datorer](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Återställ |Du kan inte återställa den kryptera virtuella datorn eftersom nyckelvalvet som är associerade med den här virtuella datorn inte finns. |Skapa ett nyckelvalv med [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md). Se [återställa en nyckelvalv nyckel och en hemlighet med hjälp av Azure Backup](backup-azure-restore-key-secret.md) att återställa en nyckel och en hemlighet om de inte finns. |
| Återställ |Du kan inte återställa den kryptera virtuella datorn eftersom nyckeln och den hemlighet som är associerade med den här virtuella datorn inte finns. |Se [återställa en nyckelvalv nyckel och en hemlighet med hjälp av Azure Backup](backup-azure-restore-key-secret.md) att återställa en nyckel och en hemlighet om de inte finns. |
| Återställ |Säkerhetskopiering har inte behörighet att komma åt resurser i din prenumeration. |Som tidigare nämnts återställa diskar först genom att följa stegen i avsnittet ”återställa säkerhetskopierade diskar” i [väljer en konfiguration för återställning av Virtuella](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Sedan kan använda PowerShell för att [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
