---
title: "En första titt: Skydda virtuella datorer i Azure med ett säkerhetskopieringsvalv | Microsoft Docs"
description: "Skydda virtuella datorer i Azure med Backup-valv. Den här självstudiekursen beskriver hur du skapar valv, registrerar virtuella datorer, skapar principer och skyddar virtuella datorer i Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/15/2016
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: d883cdc007beaf17118c6b6ddbc8345c3bfb5ef2
ms.openlocfilehash: 895eeb27b6050897575c5d6f20f16ea3f99fdcf3


---
# <a name="first-look-backing-up-azure-virtual-machines"></a>En första titt: Säkerhetskopiera virtuella datorer i Azure
> [!div class="op_single_selector"]
> * [Skydda virtuella datorer med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md)
> * [Skydda virtuella datorer i Azure med ett Backup-valv](backup-azure-vms-first-look.md)
>
>

de här självstudierna beskriver steg för steg hur du säkerhetskopierar en virtuell Azure-dator till Azure. Den här artikeln beskriver den klassiska modellen eller Service Manager-distributionsmodellen för att säkerhetskopiera virtuella datorer. Om du vill säkerhetskopiera en virtuell dator till ett Recovery Services-valv som hör till en resursgrupp, hittar du mer information i [En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md). Följande krav måste vara uppfyllda för att du ska kunna genomföra den här självstudien:

* Du har skapat en virtuell dator i Azure-prenumerationen.
* Den virtuella datorn kan ansluta till Azures offentliga IP-adresser. Mer information finns i [Nätverksanslutningar](backup-azure-vms-prepare.md#network-connectivity).

När du säkerhetskopierar en virtuell dator utför du fem huvudsteg:  

![Steg ett](./media/backup-azure-vms-first-look/step-one.png) Skapa ett säkerhetskopieringsvalv eller identifiera ett befintligt säkerhetskopieringsvalv. <br/>
![Steg två](./media/backup-azure-vms-first-look/step-two.png) Använda klassiska Azure-portalen för att identifiera och registrera virtuella datorer. <br/>
![Steg tre](./media/backup-azure-vms-first-look/step-three.png) Installera VM-agenten. <br/>
![step-four](./media/backup-azure-vms-first-look/step-four.png) Create the policy for protecting the virtual machines. <br/>
![Steg fem](./media/backup-azure-vms-first-look/step-five.png) Köra säkerhetskopieringen.

![En översikt över hur du säkerhetskopierar virtuella datorer](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Den här självstudiekursen handlar om virtuella datorer som kan skapas på den klassiska Azure-portalen. Azure Backup-tjänsten stödjer Resource Manager-baserade virtuella datorer. Mer information om hur du säkerhetskopierar virtuella datorer till ett Recovery Services-valv finns i [En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md).
>
>

## <a name="step-1---create-a-backup-vault-for-a-vm"></a>Steg 1 – Skapa ett säkerhetskopieringsvalv för en virtuell dator
Ett säkerhetskopieringsvalv är en entitet som lagrar alla säkerhetskopior och återställningspunkter som har skapats med tiden. Säkerhetskopieringsvalvet innehåller även säkerhetskopieringspolicyerna som tillämpas på de virtuella datorer som säkerhetskopieras.

1. Logga in på den [klassiska Azure-portalen](http://manage.windowsazure.com/).
2. I det nedre vänstra hörnet på Azure-portalen klickar du på **Nytt**

    ![Klicka på Nytt-menyn](./media/backup-azure-vms-first-look/new-button.png)
3. I guiden Snabbregistrering klickar du på **Data Services** > **Recovery Services** > **Säkerhetskopieringsvalv** > **Snabbregistrering**.

    ![Skapa säkerhetskopieringsvalv](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    I guiden uppmanas du att ange **namn** och **region**. Om du administrerar mer än en prenumeration visas en dialogruta där du kan välja prenumeration.
4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen.
5. I **Region** väljer du ett geografiskt område för valvet. Valvet **måste** finnas i samma region som de virtuella datorer som det skyddar.

    Om du inte vet vilken region din virtuella dator finns i stänger du den här guiden och klickar på **Virtual Machines** i listan över Azure-tjänster. Du hittar namnet på regionen i kolumnen Plats. Om du har virtuella datorer i flera regioner kan du skapa ett säkerhetskopieringsvalv i varje region.
6. Om dialogrutan **Prenumeration** inte visas i guiden går du vidare till nästa steg. Om du arbetar med flera prenumerationer väljer du en prenumeration som du vill associera med det nya säkerhetskopieringsvalvet.

    ![Popup-meddelande för valvgenerering](./media/backup-azure-vms-first-look/backup-vaultcreate.png)
7. Klicka på **Skapa valv**. Det kan ta en stund innan säkerhetskopieringsvalvet skapats. Övervaka statusmeddelandena längst ned på portalen.

    ![Popup-meddelande för valvgenerering](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Ett meddelande bekräftar att valvet har skapats. Det visas på sidan **Recovery Services** som **Aktivt**.

    ![Popup-meddelande för valvgenerering](./media/backup-azure-vms-first-look/create-vault-demo-success.png)
8. I listan över valv på sidan **Recovery Services** väljer du valvet som du skapade för att öppna sidan **Snabbstart**.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look/active-vault-demo.png)
9. På sidan **Snabbstart** klickar du på **Konfigurera** för att öppna alternativet för lagringsreplikering.
    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look/configure-storage.png)
10. I alternativet för **lagringsreplikering** väljer du replikeringsalternativet för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Valvet använder geo-redundant lagring som standard. Välj geo-redundant lagring om det här är din primära säkerhetskopia. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för geo-redundant och lokalt redundant lagring i [Översikt över Azure Storage-replikering](../storage/storage-redundancy.md).

När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. Du börjar kopplingen genom att identifiera och registrera de virtuella Azure-datorerna.

## <a name="step-2---discover-and-register-azure-virtual-machines"></a>Steg 2 – Identifiera och registrera virtuella datorer i Azure
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

## <a name="step-3---install-the-vm-agent-on-the-virtual-machine"></a>Steg 3 – Installera VM-agenten på den virtuella datorn
VM-agenten i Azure måste installeras på den virtuella Azure-datorn för att säkerhetskopieringstillägget ska fungera. Om den virtuella datorn skapades från Azure-galleriet finns VM-agenten redan på den virtuella datorn. Du kan gå vidare till [Skydda dina virtuella datorer](backup-azure-vms-first-look.md#step-4---create-the-backup-policy).

Om en virtuell dator migreras från ett lokalt datacenter är VM-agenten antagligen inte installerad på den virtuella datorn. Du måste installera VM-agenten på den virtuella datorn innan du fortsätter att skydda den virtuella datorn. Detaljerade anvisningar om hur du installerar VM-agenten finns i [avsnittet om VM-agenten i artikeln om säkerhetskopiering av virtuella datorer](backup-azure-vms-prepare.md#vm-agent).

## <a name="step-4---create-the-backup-policy"></a>Steg 4 – Skapa säkerhetskopieringspolicyn
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

## <a name="step-5---initial-backup"></a>Steg 5 – Den första säkerhetskopieringen
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
Nu när du har säkerhetskopierat en virtuell dator kan du fortsätta med flera andra intressanta steg. De mest logiska steget är att lära dig hur du återställer data till en virtuell dator. Det finns dock hanteringsaktiviteter som hjälper dig att förstå hur du skyddar dina data och minimerar kostnaderna.

* [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-restore-vms.md)
* [Felsökningsanvisningar](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).



<!--HONumber=Nov16_HO4-->


