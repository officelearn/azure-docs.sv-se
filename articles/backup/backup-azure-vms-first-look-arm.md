---
title: 'En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv'
description: Skydda virtuella datorer i Azure med ett Recovery Services-valv. Använd säkerhetskopior av Resource Manager-distribuerade virtuella datorer och klassiskt distribuerade virtuella datorer samt virtuella datorer med Premium Storage, krypterade virtuella datorer, virtuella datorer på hanterade diskar för att skydda dina data. Skapa och registrera ett Recovery Services-valv. Registrera virtuella datorer, skapa en princip och skydda virtuella datorer i Azure.
services: backup
author: markgalioto
manager: carmonm
keyword: backups; vm backup
ms.service: backup
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
keywords: säkerhetskopiering. säkerhetskopiering
ms.openlocfilehash: feae4ac83b637588fe0979a82ce05a56c2d339ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606043"
---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer till Recovery Services-valvet

Den här artikeln förklarar hur du konfigurerar skydd för en virtuell dator från virtuella datorer Arkiv-menyn eller Recovery Services-valvet. Recovery Services-valv skyddar:

* Azure Resource Manager-distribuerade virtuella datorer
* Klassiska virtuella datorer
* Virtuella datorer i standardlagring
* Virtuella datorer i Premium Storage
* Virtuella datorer som körs på Managed Disks
* Virtuella datorer som har krypterats med Azure Disk Encryption
* Programkonsekvent säkerhetskopiering av virtuella Windows-datorer med VSS och virtuella Linux-datorer med anpassade skript som körs före och efter ögonblicksbilder

Mer information om hur du skyddar virtuella datorer med Premium Storage finns i artikeln [Säkerhetskopiera och återställa virtuella datorer i Premium Storage](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Mer information om stöd för hanterade virtuella datordiskar finns i [Säkerhetskopiering och återställning av virtuella datorer på hanterade diskar](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Mer information om ramverket för förskript och efterskript för säkerhetskopiering av virtuella Linux-datorer finns i [Programkonsekvent säkerhetskopiering av virtuella Linux-datorer med förskript och efterskript](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Du hittar mer information om vad du kan se och går inte att säkerhetskopiera [förbereda din miljö för att säkerhetskopiera virtuella datorer i Azure](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

> [!NOTE]
> Tjänsten säkerhetskopiering skapar en separat resursgrupp än resursgruppen för den virtuella datorn att lagra återställning punkt samling. Kunder bör inte låsa resursgruppen som skapats för användning av Backup-tjänsten.
Namnformatet för resursgruppen som skapats av Backup-tjänsten är: AzureBackupRG_`<Geo>`_`<number>`
<br>Exempel: AzureBackupRG_northeurope_1
>
>

Beroende på hur många virtuella datorer du vill skydda kan du börja från olika startpunkter. Om du vill säkerhetskopiera flera virtuella datorer i en enda åtgärd går du till Recovery Services-valvet och [initierar säkerhetskopieringsjobbet från valvets instrumentpanel](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Om du vill säkerhetskopiera en enskild virtuell dator [initiera säkerhetskopieringsjobbet VM operations menyn](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>Konfigurera säkerhetskopieringsjobbet VM operations-menyn

Använd följande steg för att konfigurera säkerhetskopieringsjobbet virtuella operations-menyn. Stegen gäller bara för virtuella datorer i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Alla tjänster** på navmenyn och skriv **Virtuella datorer** i dialogrutan Filter. När du skriver filtreras listan med resurser. När du ser Virtuella datorer väljer du det alternativet.

  ![Skärmbild som visar hur du navigerar till virtuella datorer från Alla tjänster](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  Lista över virtuella datorer (VM) i prenumerationen visas.

  ![Listan över virtuella datorer i prenumerationen visas.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Välj en virtuell dator som du vill säkerhetskopiera i listan.

  ![Listan över virtuella datorer i prenumerationen visas.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  När du markerar den virtuella datorn, öppna listan över virtuella datorer SKIFT till vänster och virtual machine management-menyn och virtuella-instrumentpanelen.

4. På menyn VM management i den **Operations** klickar du på **säkerhetskopiering**. </br>

  ![Alternativ för säkerhetskopiering i menyn VM](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  Aktivera säkerhetskopiering menyn öppnas.

  ![Alternativ för säkerhetskopiering i menyn VM](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. Klicka i området Recovery Services-valvet **Välj befintlig** och välj ett valv i den nedrullningsbara listan.

  ![Guiden Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  Om det inte finns några Recovery Services-valv, eller om du vill använda ett nytt valv, klickar du på **Skapa nytt** och anger namnet för det nya valvet. Ett nytt valv skapas i samma resursgrupp och i samma region som den virtuella datorn. Om du vill skapa ett Recovery Services-valv med andra värden läser du avsnittet om hur du [skapar ett Recovery Services-valv](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. Välj en princip på menyn Välj princip för säkerhetskopiering. Information för den markerade principen visas under den nedrullningsbara menyn.

  Om du vill skapa en ny princip eller redigerar den befintliga principen, klickar på **skapa (eller redigera) en ny princip** att öppna Redigeraren för säkerhetskopiering. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Om du vill spara ändringarna i principen för säkerhetskopiering och återgå till menyn Aktivera säkerhetskopiering klickar du på **OK**.

  ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Om du vill tillämpa principen Recovery Services-valvet och säkerhetskopiering till den virtuella datorn, klickar du på **Aktivera säkerhetskopiering** vill distribuera principen. När principen distribueras så associeras den med valvet och de virtuella datorerna.

  ![Knappen Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Du kan följa konfigurationsförloppet med hjälp av meddelandena som visas på portalen. I följande exempel ser du att distributionen har startat.

  ![Avisering för Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. När förloppet konfigurationen har slutförts på VM management-menyn klickar du på **säkerhetskopiering** öppna menyn säkerhetskopiering och visa tillgängliga detaljer.

  ![Vyn Säkerhetskopieringsobjekt för den virtuella datorn](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  Innan den första säkerhetskopieringen har slutförts visas **Status för senaste säkerhetskopiering** som **Varning (första säkerhetskopiering väntar)**. Att se när nästa schemalagda säkerhetskopieringsjobb inträffar under **sammanfattning** klickar du på namnet på principen. Princip för säkerhetskopiering-menyn öppnas och visar tidpunkten för den schemalagda säkerhetskopieringen.

10. För att skydda den virtuella datorn, klickar du på **Säkerhetskopiera nu**. 

  ![Klicka på Säkerhetskopiera nu om du vill köra den första säkerhetskopieringen](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Menyn Säkerhetskopiera nu öppnas. 

  ![Visar bladet Säkerhetskopiera nu](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. På menyn säkerhetskopiering nu klickar du på kalenderikonen, Använd kalender för att välja den sista dagen i den här återställningspunkten behålls Klicka på **OK**.

  ![Ange den sista dagen som återställningspunkten som skapas med Säkerhetskopiera nu ska behållas](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Konfigurera säkerhetskopieringsjobbet från Recovery Services-valvet
Med de här stegen kan du konfigurera säkerhetskopieringsjobbet.

1. Skapa ett Recovery Services-valv för en virtuell dator.
2. Använd Azure Portal till att välja ett scenario, ange en säkerhetskopieringspolicy och identifiera vilka objekt som ska skyddas.
3. Kör den första säkerhetskopieringen.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Skapa ett Recovery Services-valv för en virtuell dator
Ett Recovery Services-valv är en entitet som lagrar alla säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery Services-valvet innehåller också säkerhetskopieringspolicyn som tillämpas på de skyddade virtuella datorerna.

> [!NOTE]
> Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera virtuella datorer från en region till ett Recovery Services-valv i en annan region. För varje Azure-region som har virtuella datorer som ska säkerhetskopieras måste det finnas minst ett Recovery Services-valv i regionen.
>
>

Så här skapar du ett Recovery Services-valv:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/) med din Azure-prenumeration.
2. På navmenyn **Alla tjänster** och i dialogrutan Filter skriver du **Recovery Services**. När du skriver filtreras listan med resurser. När du ser Recovery Services-valvet i listan klickar du på det.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Om det finns Recovery Services-valv i prenumerationen så visas de.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Recovery Services valvet menyn öppnas, där du uppmanas att ange en **namn**, **prenumeration**, **resursgruppen**, och **plats**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.

5. I avsnittet **Prenumeration** använder du listrutan för att välja Azure-prenumerationen. Om du bara använder en prenumeration visas den och du kan gå vidare till nästa steg. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.

6. Gör följande i avsnittet **Resursgrupp**:

    * Välj **Skapa ny** om du vill skapa en resursgrupp.
    Eller
    * Välj **Använd befintlig** och klicka på listrutan om du vill se listan över tillgängliga resursgrupper.

  Fullständig information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Klicka på **Plats** för att välja en geografisk region för valvet. Det här alternativet anger den geografiska region som dina säkerhetskopierade data skickas till.

  > [!IMPORTANT]
  > Om du är osäker på var din virtuella dator finns stänger du dialogrutan för valvgenerering och går till listan med virtuella datorer på portalen. Om du har virtuella datorer i olika regioner skapar du ett Recovery Services-valv i varje region. Skapa valvet i den första regionen innan du fortsätter till nästa region. Du behöver inte ange vilka lagringskonton som används för säkerhetskopierade data. Recovery Services-valvet och tjänsten Azure Backup hanterar lagringen automatiskt.
  >

8. Klicka på längst ned i menyn Recovery Services-valvet **skapa**.

    Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv efter ett par minuter klickar du på **Uppdatera**.

    ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    När du ser valvet i listan över Recovery Services-valv kan du ange lagringsredundansen.

Nu när du har skapat valvet ska vi se hur du konfigurerar lagringsreplikeringen.

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Om Recovery Services-valvet är din primära plats för säkerhetskopiering ska du lämna alternativet för lagringsreplikering på geo-redundant lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Från den **Recovery Services-valv** -menyn, Välj det nya valvet.

  ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  När du väljer valvet, menyn Inställningar (*som har namnet på valvet överst*) och öppna valvet instrumentpanelen.

  ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. I det nya valvet Management menyn använder du den lodräta bilden och rulla ned till avsnittet Hantera klickar du på **säkerhetskopiering infrastruktur** öppna menyn i infrastrukturen för säkerhetskopiering.
 
   ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. I menyn infrastrukturen för säkerhetskopiering klickar du på **konfigurering av säkerhetskopiering** att öppna den **konfigurering av säkerhetskopiering** menyn.

    ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-open-infra.png)
4. Välj lämpligt alternativ för lagringsreplikering för valvet.

    ![alternativ för lagringskonfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som primär slutpunkt för lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) i denna [översikt av lagringsredundans](../storage/common/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Välj ett säkerhetskopieringsmål, ange en princip och definiera objekt som ska skyddas
Innan du registrerar en virtuell dator med ett valv kör du identifieringsprocessen för att säkerställa att nya virtuella datorer som har lagts till i prenumerationen identifieras. Under processen uppmanas Azure att returnera listan med virtuella datorer i prenumerationen, tillsammans med ytterligare information som molntjänstens namn och regionen. På Azure-portalen refererar scenariot till vad du ska lägga till i Recovery Services-valvet. Principen är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna.

1. Om du redan har ett öppet Recovery Services-valv går du vidare till steg 2. Klicka annars på **Alla tjänster**. Skriv **Recovery Services** och klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Listan över Recovery Services-valv visas.

    ![Vy över listan med Recovery Services-valv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Från listan med Recovery Services-valv väljer du ett valv för att öppna valvets instrumentpanel.

     ![Öppna menyn för valvet](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Öppna menyn Säkerhetskopiering på menyn på instrumentpanelen för valvet genom att klicka på **Säkerhetskopiering**.

    ![Öppna menyn för säkerhetskopiering](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Öppna menyer säkerhetskopierings- och mål för säkerhetskopian.

    ![Öppna menyn Scenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. På menyn mål för säkerhetskopian från den **var körs din arbetsbelastning** nedrullningsbara menyn, Välj Azure. Från listrutan **Vad vill du säkerhetskopiera?** väljer du Virtuell dator och klickar sedan på **OK**.

    Nu registreras tillägget för den virtuella datorn i valvet. Mål för säkerhetskopian menyn stängs och **säkerhetskopiera princip** menyn öppnas.

    ![Öppna menyn Scenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. På menyn säkerhetskopiering principen väljer du den säkerhetskopieringsprincip som du vill koppla till valvet.

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Information om standardprincipen visas under den nedrullningsbara menyn. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Klicka på **OK** för att associera säkerhetskopieringspolicyn med valvet.

    Säkerhetskopiering princip menyn stängs och **Välj virtuella datorer** menyn öppnas.
5. I den **Välj virtuella datorer** -menyn väljer du de virtuella datorerna som associeras med den angivna principen och klicka på **OK**.

    ![Välja arbetsbelastning](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Den valda virtuella datorn verifieras. Om du inte ser de virtuella datorer som du förväntar dig, kontrollerar du att de finns på samma Azure-plats som Recovery Services-valvet samt att de inte redan är skyddade. Platsen för Recovery Services-valvet visas på instrumentpanelen för valvet.

6. Nu när du har definierat alla inställningar för valvet, Backup-menyn, klicka på **Aktivera säkerhetskopiering** att distribuera principen till valvet och de virtuella datorerna. När du distribuerar säkerhetskopieringspolicyn skapas inte den första återställningspunkten för den virtuella datorn.

    ![Aktivera säkerhetskopiering](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopieringen körs säkerhetskopieringspolicyn enligt schemat. Fortsätt för att initiera det första säkerhetskopieringsjobbet.

## <a name="initial-backup"></a>Den första säkerhetskopieringen
Att en säkerhetskopieringspolicy har distribuerats på den virtuella datorn betyder inte att dina data har säkerhetskopierats. Som standard är den första schemalagda säkerhetskopieringen (enligt konfigurationen av säkerhetskopieringspolicyn) den inledande säkerhetskopieringen. Tills den första säkerhetskopian inträffar, senaste Status för säkerhetskopiering på den **säkerhetskopieringsjobb** menyn visas som **varning (första säkerhetskopian väntande)**.

![Säkerhetskopiering väntar](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Såvida inte den första säkerhetskopieringen kommer att börja snart, rekommenderar vi att du kör **Säkerhetskopiera nu**.

Så här kör du det första säkerhetskopieringsjobbet:

1. På instrumentpanelen för valvet klickar du på numret under **Säkerhetskopieringsobjekt**, eller på panelen **Säkerhetskopieringsobjekt**. <br/>
  ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Menyn **Säkerhetskopieringsobjekt** öppnas.

  ![Säkerhetskopieringsobjekt](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. På den **säkerhetskopiering objekt** -menyn väljer du det.

  ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Listan **Säkerhetskopieringsobjekt** öppnas. <br/>

  ![Säkerhetskopieringsjobbet utlöses](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Öppna snabbmenyn genom att klicka på ellipserna **...** i listan **Säkerhetskopieringsobjekt**.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Snabbmenyn visas.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Klicka på **Säkerhetskopiera nu** på snabbmenyn.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Menyn Säkerhetskopiera nu öppnas.

  ![Visar menyn säkerhetskopiering nu](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. På menyn säkerhetskopiering nu klickar du på kalenderikonen, Använd kalender för att välja den sista dagen i den här återställningspunkten behålls Klicka på **säkerhetskopiering**.

  ![Ange den sista dagen som återställningspunkten som skapas med Säkerhetskopiera nu ska behållas](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

6. Du kan visa eller spåra statusen för den första säkerhetskopieringen genom att klicka på **Pågår** på panelen **Säkerhetskopieringsjobb** på instrumentpanelen för valvet.

  ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Säkerhetskopieringsjobb menyn öppnas.

  ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  I den **säkerhetskopiera jobb** -menyn kan du se status för alla jobb. Kontrollera om säkerhetskopieringsjobbet för den virtuella datorn fortfarande körs, eller om det har slutförts. När säkerhetskopieringsjobbet är klart visas statusen *Slutfört*.

  > [!NOTE]
  > Som en del av säkerhetskopieringen skickar tjänsten Azure Backup ett kommando till säkerhetskopieringstillägget på varje virtuell dator som instruerar det att tömma alla skrivningar och använda en konsekvent ögonblicksbild.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
Den här informationen tillhandahålls om den behövs. VM-agenten i Azure måste installeras på den virtuella Azure-datorn för att säkerhetskopieringstillägget ska fungera. Men om din virtuella dator skapades från Azure-galleriet finns VM-agenten redan på den virtuella datorn. VM-agenten är inte installerad på virtuella datorer som migrerats från lokala datacenter. I dessa fall måste VM-agenten installeras. Om du har problem med att säkerhetskopiera den virtuella datorn i Azure kontrollerar du att VM-agenten i Azure är korrekt installerad på den virtuella datorn (se följande tabell). Om du skapar en anpassad virtuell dator ska du installera VM-agenten innan den virtuella datorn etableras.

Lär dig mer om [VM-agenten](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) och [hur du installerar den](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Följande tabell innehåller ytterligare information om VM-agenten för virtuella Windows- och Linux-datorer.

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera VM-agenten |<li>Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. <li>[Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |<li> Installera den senaste [Linux-agenten](https://github.com/Azure/WALinuxAgent) från GitHub. Du måste ha administratörsbehörighet för att slutföra installationen. <li> [Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |
| Uppdatera VM-agenten |Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ anvisningarna för hur du [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Bekräfta installationen av VM-agenten |<li>Gå till mappen *C:\WindowsAzure\Packages* på den virtuella datorn i Azure. <li>Du bör hitta filen WaAppAgent.exe.<li> Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet Produktversion ska vara 2.6.1198.718 eller högre. |Gäller inte |

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
När VM-agenten har installerats på den virtuella datorn installerar Azure Backup-tjänsten säkerhetskopieringstillägget till VM-agenten. Azure Backup-tjänsten uppgraderar och korrigerar säkerhetskopieringstillägget utan ytterligare användarinteraktion.

Backup-tjänsten installerar tillägget för säkerhetskopiering även om den virtuella datorn inte är igång. En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Azure Backup-tjänsten fortsätter dock att säkerhetskopiera den virtuella datorn även om den är avstängd och det inte gick att installera tillägget. Den här typen av säkerhetskopiering kallas för offline-VM och återställningspunkten är *kraschkonsekvent*.

## <a name="troubleshooting-information"></a>Felsökningsinformation
Om du har problem med att utföra vissa uppgifter i den här artikeln läser du [felsökningsanvisningarna](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Prissättning
Kostnaden för att säkerhetskopiera virtuella datorer i Azure baseras på hur många instanser som skyddas. I [Vad är en skyddad instans](backup-introduction-to-azure-backup.md#what-is-a-protected-instance) definieras begreppet skyddad instans. I avsnittet om att [beräkna skyddade instanser](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances) ges ett exempel på hur du kan beräkna kostnaden för säkerhetskopiering av en virtuell dator. På sidan med prissättning för Azure-säkerhetskopiering finns information om [priser för säkerhetskopiering](https://azure.microsoft.com/pricing/details/backup/).

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).
