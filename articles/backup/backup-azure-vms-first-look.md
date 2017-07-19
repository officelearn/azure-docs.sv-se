---
title: "En första titt: Säkerhetskopiera virtuella datorer i Azure med ett säkerhetskopieringsvalv | Microsoft Docs"
description: "Använd den klassiska portalen för att säkerhetskopiera virtuella datorer i Azure till ett Backup-valv. I den här kursen beskrivs alla skeden, inklusive att skapa säkerhetskopieringsvalvet, registrera de virtuella datorerna, skapa säkerhetskopieringsprincipen och köra det första säkerhetskopieringsjobbet."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/14/2017
ms.author: markgal;
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 61328e32763faea90074fc6d499e660c4109ab6d
ms.contentlocale: sv-se
ms.lasthandoff: 06/16/2017


---
# <a name="first-look-backing-up-azure-virtual-machines"></a>En första titt: Säkerhetskopiera virtuella datorer i Azure
> [!div class="op_single_selector"]
> * [Skydda virtuella datorer med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md)
> * [Skydda virtuella datorer i Azure med ett Backup-valv](backup-azure-vms-first-look.md)
>
>

de här självstudierna beskriver steg för steg hur du säkerhetskopierar en virtuell Azure-dator till Azure. Den här artikeln beskriver den klassiska modellen eller Service Manager-distributionsmodellen för att säkerhetskopiera virtuella datorer. Följande steg gäller endast säkerhetskopieringsvalv som har skapats i den klassiska portalen. Microsoft rekommenderar att Resource Manager-modellen används för nya distributioner.

Om du vill säkerhetskopiera en virtuell dator till ett Recovery Services-valv som hör till en resursgrupp, hittar du mer information i [En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md).

Följande krav måste vara uppfyllda för att du ska kunna genomföra den här självstudien:

* Du har skapat en virtuell dator i Azure-prenumerationen.
* Den virtuella datorn kan ansluta till Azures offentliga IP-adresser. Mer information finns i [Nätverksanslutningar](backup-azure-vms-prepare.md#network-connectivity).


> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Den här självstudiekursen handlar om virtuella datorer som har skapats i den klassiska portalen.
>
>

## <a name="create-a-backup-vault"></a>Skapa ett säkerhetskopieringsvalv
Ett säkerhetskopieringsvalv är en entitet som lagrar alla säkerhetskopior och återställningspunkter som har skapats med tiden. Säkerhetskopieringsvalvet innehåller även säkerhetskopieringspolicyerna som tillämpas på de virtuella datorer som säkerhetskopieras.

> [!IMPORTANT]
> Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv.
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> **Från den 1 november 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

## <a name="discover-and-register-azure-virtual-machines"></a>Identifiera och registrera virtuella datorer i Azure
Innan du registrerar den virtuella datorn med ett valv kör du identifieringsprocessen för att identifiera nya virtuella datorer. När du gör det returneras en lista över virtuella datorer i prenumerationen, jämte ytterligare information som molntjänstens namn och regionen.

1. Logga in på den [klassiska Azure-portalen](http://manage.windowsazure.com/).
2. Öppna listan över Recovery Services-valv genom att klicka på **Recovery Services** på den klassiska Azure-portalen.
    ![Välja arbetsbelastning](./media/backup-azure-vms-first-look/recovery-services-icon.png)
3. I listan med valv väljer du valvet för att säkerhetskopiera en virtuell dator.

    När du väljer valvet öppnas det på sidan **Snabbstart**.
4. Klicka på **Registrerade objekt** på valvmenyn.

    ![Välja arbetsbelastning](./media/backup-azure-vms-first-look/configure-registered-items.png)
5. Välj  **Virtuell Azure-dator** på **Typ**-menyn.

    ![Välja arbetsbelastning](./media/backup-azure-vms/discovery-select-workload.png)
6. Klicka på **Identifiera** längst ned på sidan.
    ![Knappen Identifiera](./media/backup-azure-vms/discover-button-only.png)

    Identifieringen kan ta några minuter medan de virtuella datorerna visas i tabellformat. Ett meddelande visas längst ned på skärmen som anger att processen körs.

    ![Identifiera virtuella datorer](./media/backup-azure-vms/discovering-vms.png)

    Meddelandet ändras när processen är klar.

    ![Identifieringen är klar](./media/backup-azure-vms-first-look/discovery-complete.png)
7. Klicka på **Registrera** längst ned på sidan.
    ![Knappen Registrera](./media/backup-azure-vms-first-look/register-icon.png)
8. På snabbmenyn **Registrera objekt** väljer du de virtuella datorer som du vill registrera.

   > [!TIP]
   > Flera virtuella datorer kan registreras samtidigt.
   >
   >

    Ett jobb skapas för varje virtuell dator som du har valt.
9. Klicka på **Visa jobb** i meddelandet för att gå till sidan **Jobb**.

    ![Registrera jobb](./media/backup-azure-vms/register-create-job.png)

    Den virtuella datorn visas också i listan över registrerade objekt, tillsammans med statusen för registreringsåtgärden.

    ![Registreringsstatus 1](./media/backup-azure-vms/register-status01.png)

    När åtgärden har slutförts ändras statusen för att återspegla tillståndet för *registrerade objekt*.

    ![Registreringsstatus 2](./media/backup-azure-vms/register-status02.png)

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
VM-agenten i Azure måste installeras på den virtuella Azure-datorn för att säkerhetskopieringstillägget ska fungera. Om den virtuella datorn skapades från Azure-galleriet finns VM-agenten redan på den virtuella datorn; du kan gå direkt till att [skydda dina virtuella datorer](backup-azure-vms-first-look.md#create-the-backup-policy).

Om en virtuell dator migreras från ett lokalt datacenter är VM-agenten antagligen inte installerad på den virtuella datorn. Du måste installera VM-agenten på den virtuella datorn innan du fortsätter att skydda den virtuella datorn. Detaljerade anvisningar om hur du installerar VM-agenten finns i [avsnittet om VM-agenten i artikeln om säkerhetskopiering av virtuella datorer](backup-azure-vms-prepare.md#vm-agent).

## <a name="create-the-backup-policy"></a>Skapa säkerhetskopieringsprincipen
Innan du initierar det första säkerhetskopieringsjobbet definierar du schemat som anger när ögonblicksbilder av säkerhetskopior ska tas. Schemat som definierar när ögonblicksbilder av säkerhetskopior tas och hur länge dessa ögonblicksbilder bevaras utgör säkerhetskopieringspolicyn. Bevarandeinformationen baseras på ett säkerhetsrotationsschema av typen ”farfar-far-son”.

1. Gå till säkerhetskopieringsvalvet under **Recovery Services** på den klassiska Azure-portalen och klicka på **Registrerade objekt**.
2. Välj **Virtuell Azure-dator** i listrutan.

    ![Välja arbetsbelastning på portalen](./media/backup-azure-vms/select-workload.png)
3. Klicka på **Skydda** längst ned på sidan.
    ![Klicka på Skydda](./media/backup-azure-vms-first-look/protect-icon.png)

    Guiden **Skydda objekt** visas och visar *endast* virtuella datorer som är registrerade och som inte skyddas.

    ![Konfigurera skydd i stor skala](./media/backup-azure-vms/protect-at-scale.png)
4. Välj de virtuella datorer som du vill skydda.

    Om det finns två eller flera virtuella datorer med samma namn använder du Cloud Services för att skilja mellan virtuella datorer.
5. På menyn **Konfigurera skydd** väljer du en befintlig princip eller skapar en ny princip för att skydda de virtuella datorer som du har identifierat.

    Nya säkerhetskopieringsvalv har en standardprincip som är associerad med valvet. Den här principen tar en daglig ögonblicksbild varje kväll som bevaras i 30 dagar. Flera virtuella datorer kan vara associerade med varje säkerhetskopieringspolicy. Men den virtuella datorn kan bara associeras med en princip i taget.

    ![Skydda med ny princip](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > En säkerhetskopieringspolicy innehåller ett kvarhållningsschema för de schemalagda säkerhetskopieringarna. Om du väljer en befintlig säkerhetskopieringspolicy kan du inte ändra kvarhållningsalternativen i nästa steg.
   >
   >
6. I **Kvarhållningsintervall** definierar du det dagliga, veckovisa, månatliga och årliga omfånget för de specifika säkerhetskopieringspunkterna.

    ![Virtuella datorer säkerhetskopieras med återställningspunkter](./media/backup-azure-vms/long-term-retention.png)

    Bevarandeprincipen anger hur länge en säkerhetskopia ska lagras. Du kan ange andra bevarandeprinciper baserat på när säkerhetskopieringen görs.
7. Klicka på **Jobb** för att visa en lista över jobb av typen **Konfigurera skydd**.

    ![Jobbet Konfigurera skydd](./media/backup-azure-vms/protect-configureprotection.png)

    Nu när du har skapat principen går du till nästa steg och kör den första säkerhetskopieringen.

## <a name="initial-backup"></a>Den första säkerhetskopieringen
När en virtuell dator har skyddats med en princip kan du visa den relationen på fliken **Skyddade objekt**. Innan den första säkerhetskopieringen har körts visas **Skyddsstatus** som **Skyddade – (första säkerhetskopiering väntar)**. Som standard är den första säkerhetskopieringen den *inledande säkerhetskopieringen*.

![Säkerhetskopiering väntar](./media/backup-azure-vms-first-look/protection-pending-border.png)

Så här startar du den första säkerhetskopieringen nu:

1. På sidan **Skyddade objekt** klickar du på **Säkerhetskopiera nu** längst ned på sidan.
    ![Ikonen Säkerhetskopiera nu](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Tjänsten Azure Backup skapar ett säkerhetskopieringsjobb för den första säkerhetskopieringen.
2. Klicka på fliken **Jobb** för att visa listan över jobb.

    ![Säkerhetskopiering pågår](./media/backup-azure-vms-first-look/protect-inprogress.png)

    När den första säkerhetskopieringen är klar ändras statusen för den virtuella datorn på fliken **Skyddade objekt** till *Skyddade*.

    ![Virtuella datorer säkerhetskopieras med återställningspunkter](./media/backup-azure-vms/protect-backedupvm.png)

   > [!NOTE]
   > Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera virtuella datorer från en region till ett säkerhetskopieringsvalv i en annan region. Så, för varje Azure-region som innehåller virtuella datorer som behöver säkerhetskopieras måste minst ett säkerhetskopieringsvalv skapas i den regionen.
   >
   >

## <a name="next-steps"></a>Nästa steg
Nu när du har säkerhetskopierat en virtuell dator kan du fortsätta med flera andra intressanta steg. Det mest logiska steget är att lära dig hur du återställer data till en virtuell dator. Det finns dock hanteringsaktiviteter som hjälper dig att förstå hur du skyddar dina data och minimerar kostnaderna.

* [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-restore-vms.md)
* [Felsökningsanvisningar](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

