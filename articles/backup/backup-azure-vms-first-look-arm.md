---
title: "En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv | Microsoft Docs"
description: "Skydda virtuella datorer i Azure med ett Recovery Services-valv. Använd säkerhetskopior av Resource Manager-distribuerade virtuella datorer och klassiskt distribuerade virtuella datorer samt virtuella datorer med Premium Storage, krypterade virtuella datorer, virtuella datorer på hanterade diskar för att skydda dina data. Skapa och registrera ett Recovery Services-valv. Registrera virtuella datorer, skapa en princip och skydda virtuella datorer i Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/15/2017
ms.author: markgal;jimpark
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d39678bb7e7d2263b6b2f8d36da1bd86dcfff271
ms.lasthandoff: 04/03/2017


---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vaults"></a>Säkerhetskopiera virtuella Azure-datorer till Recovery Services-valv
> [!div class="op_single_selector"]
> * [Skydda virtuella datorer med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md)
> * [Skydda virtuella datorer med ett säkerhetskopieringsvalv](backup-azure-vms-first-look.md)
>
>

Den här självstudiekursen beskriver steg för steg hur du skapar ett Recovery Services-valv och säkerhetskopierar en virtuell Azure-dator (VM). Recovery Services-valv skyddar:

* Azure Resource Manager-distribuerade virtuella datorer
* Klassiska virtuella datorer
* Virtuella datorer i standardlagring
* Virtuella datorer i Premium Storage
* Virtuella datorer som körs på Managed Disks
* Virtuella datorer som har krypterats med Azure Disk Encryption, med BEK och KEK
* Programkonsekvent säkerhetskopiering av virtuella Windows-datorer med VSS och virtuella Linux-datorer med anpassade skript som körs före och efter ögonblicksbilder

Mer information om hur du skyddar virtuella datorer med Premium Storage finns i artikeln [Säkerhetskopiera och återställa virtuella datorer i Premium Storage](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Mer information om stöd för hanterade virtuella datordiskar finns i [Säkerhetskopiering och återställning av virtuella datorer på hanterade diskar](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Mer information om ramverket för förskript och efterskrift för säkerhetskopiering av virtuella Linux-datorer finns i [Programkonsekvent säkerhetskopiering av virtuella Linux-datorer med förskript och efterskript] (https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) (på engelska)

> [!NOTE]
> I den här självstudiekursen förutsätter vi att du redan har en virtuell dator i din Azure-prenumeration och att du har utfört nödvändiga åtgärder för att ge säkerhetskopieringstjänsten åtkomst till den virtuella datorn.
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

Beroende på hur många virtuella datorer du vill skydda kan du börja från olika startpunkter. Om du vill säkerhetskopiera flera virtuella datorer i en enda åtgärd går du till Recovery Services-valvet och [initierar säkerhetskopieringsjobbet från valvets instrumentpanel](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Om du vill säkerhetskopiera en virtuell dator kan du initiera säkerhetskopieringsjobbet från bladet VM-hantering.

## <a name="configure-the-backup-job-from-the-vm-management-blade"></a>Konfigurera säkerhetskopieringsjobbet från bladet VM-hantering

Gör så här när du ska konfigurera säkerhetskopieringsjobbet från bladet för hantering av virtuella datorer i Azure Portal. Dessa steg gäller inte för virtuella datorer på den klassiska portalen.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Fler tjänster** på navmenyn och skriv **Virtuella datorer** i dialogrutan Filter. När du skriver filtreras listan med resurser. När du ser Virtuella datorer väljer du det alternativet.

  ![Öppna textdialogrutan genom att klicka på Fler tjänster på navmenyn och skriv Virtuella datorer](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  Listan över virtuella datorer (VM) i prenumerationen visas.

  ![Listan över virtuella datorer i prenumerationen visas.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Välj en virtuell dator som du vill säkerhetskopiera i listan.

  ![Listan över virtuella datorer i prenumerationen visas.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  När du väljer den virtuella datorn flyttas listan över virtuella datorer till vänster och bladet för hantering av virtuella datorer och instrumentpanelen för virtuella datorer öppnas. </br>
 ![Bladet för hantering av virtuell dator](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)

4. Klicka på **Säkerhetskopiering** i avsnittet **Inställningar** på bladet VM-hantering. </br>

  ![Säkerhetskopieringsalternativet på bladet VM-hantering](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

  Bladet Aktivera säkerhetskopiering öppnas.

  ![Säkerhetskopieringsalternativet på bladet VM-hantering](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. För Recovery Services-valvet klickar du på **Välj befintlig** och väljer valvet i den nedrullningsbara listan.

  ![Guiden Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

  Om det inte finns några Recovery Services-valv, eller om du vill använda ett nytt valv, klickar du på **Skapa nytt** och anger namnet för det nya valvet. Ett nytt valv skapas i samma resursgrupp och på samma plats som den virtuella datorn. Om du vill skapa ett Recovery Services-valv med andra värden läser du avsnittet om hur du [skapar ett Recovery Services-valv](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. Om du vill visa mer information om säkerhetskopieringspolicyn klickar du på **Säkerhetskopieringspolicy**.

  Bladet **Säkerhetskopieringspolicy** öppnas och innehåller information om den valda principen. Om det finns andra principer använder du den nedrullningsbara menyn för att välja en annan säkerhetskopieringspolicy. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Om du vill spara ändringarna i säkerhetskopieringspolicyn och återgå till bladet Aktivera säkerhetskopiering klickar du på **OK**.

  ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new-2.png)

7. Klicka på **Aktivera säkerhetskopiering** på bladet Aktivera säkerhetskopiering så att principen distribueras. När principen distribueras så associeras den med valvet och de virtuella datorerna.

  ![Knappen Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. Du kan följa konfigurationsförloppet med hjälp av meddelandena som visas på portalen. I följande exempel ser du att distributionen har startat.

  ![Avisering för Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. När konfigurationsförloppet har slutförts på bladet VM-hantering klickar du på **Säkerhetskopiering** för att öppna bladet Säkerhetskopieringsobjekt och visa informationen.

  ![Vyn Säkerhetskopieringsobjekt för den virtuella datorn](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

  Innan den första säkerhetskopieringen har slutförts visas **Status för senaste säkerhetskopiering** som **Varning (första säkerhetskopiering väntar)**. Du kan se när nästa schemalagda säkerhetskopieringsjobb körs under **Säkerhetskopieringspolicy** genom att klicka på namnet på principen. Bladet Säkerhetskopieringspolicy öppnas och visar tiden för den schemalagda säkerhetskopieringen.

10. Om du vill köra ett säkerhetskopieringsjobb och skapa den första återställningspunkten klickar du på **Säkerhetskopiera nu** på bladet Säkerhetskopieringsvalv.

  ![Klicka på Säkerhetskopiera nu om du vill köra den första säkerhetskopieringen](./media/backup-azure-vms-first-look-arm/backup-now.png)

  Bladet Säkerhetskopiera nu öppnas.

  ![Visar bladet Säkerhetskopiera nu](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. På bladet Säkerhetskopiera nu klickar du på kalenderikonen, använder kalenderkontrollen för att välja den sista dagen som den här återställningspunkten ska behållas och klickar sedan på **Säkerhetskopiera**.

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
> Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera virtuella datorer från en plats till ett Recovery Services-valv på en annan plats. För varje Azure-plats som har virtuella datorer som ska säkerhetskopieras måste det finnas minst ett Recovery Services-valv på platsen.
>
>

Så här skapar du ett Recovery Services-valv:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/) med din Azure-prenumeration.
2. På navmenyn **Fler tjänster** och i dialogrutan Filter skriver du **Recovery Services**. När du skriver filtreras listan med resurser. När du ser Recovery Services-valvet i listan klickar du på det.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Om det finns Recovery Services-valv i prenumerationen så visas de.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

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
  > Om du är osäker på var din virtuella dator finns stänger du dialogrutan för valvgenerering och går till listan med virtuella datorer på portalen. Om du har virtuella datorer i olika regioner skapar du ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Du behöver inte ange vilka lagringskonton som används för säkerhetskopierade data. Recovery Services-valvet och tjänsten Azure Backup hanterar lagringen automatiskt.
  >

8. Längst ned på bladet för Recovery Services-valvet klickar du på **Skapa**.

    Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv efter ett par minuter klickar du på **Uppdatera**.

    ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    När du ser valvet i listan över Recovery Services-valv kan du ange lagringsredundansen.

Nu när du har skapat valvet ska vi se hur du konfigurerar lagringsreplikeringen.

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Om Recovery Services-valvet är din primära plats för säkerhetskopiering ska du lämna alternativet för lagringsreplikering på geo-redundant lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/storage-redundancy.md#locally-redundant-storage) lagring i [Översikt över Azure Storage-replikering](../storage/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj det nya valvet på bladet **Recovery Services-valv**.

  ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  När du väljer valvet minimeras bladet Inställningar (*där namnet på valvet står överst*) och bladet med valvinformation öppnas.

  ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. På det nya valvets inställningsblad använder du det lodräta reglaget och bläddrar ned till avsnittet Hantera. Där klickar du på **Infrastruktur för säkerhetskopiering**.
    Bladet Infrastruktur för säkerhetskopiering öppnas.
3. På bladet Infrastruktur för säkerhetskopiering klickar du på **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**.

    ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Välj lämpligt alternativ för lagringsreplikering för valvet.

    ![alternativ för lagringskonfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som primär slutpunkt för lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/storage-redundancy.md#locally-redundant-storage) i denna [översikt av lagringsredundans](../storage/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Välj ett säkerhetskopieringsmål, ange en princip och definiera objekt som ska skyddas
Innan du registrerar en virtuell dator med ett valv kör du identifieringsprocessen för att säkerställa att nya virtuella datorer som har lagts till i prenumerationen identifieras. Under processen uppmanas Azure att returnera listan med virtuella datorer i prenumerationen, tillsammans med ytterligare information som molntjänstens namn och regionen. På Azure-portalen refererar scenariot till vad du ska lägga till i Recovery Services-valvet. Principen är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna.

1. Om du redan har ett öppet Recovery Services-valv går du vidare till steg 2. Annars klickar du på **Fler tjänster** på navmenyn och skriver **Recovery Services** i listan över resurser och klickar sedan på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Listan över Recovery Services-valv visas.

    ![Vy över listan med Recovery Services-valv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Från listan med Recovery Services-valv väljer du ett valv för att öppna valvets instrumentpanel.

     ![Öppna bladet för valvet](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Öppna bladet Säkerhetskopiering på menyn på instrumentpanelen för valvet genom att klicka på **Säkerhetskopiering**.

    ![Öppna bladet Säkerhetskopiering](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Bladen Säkerhetskopiering och Säkerhetskopieringsmål öppnas.

    ![Öppna bladet Scenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. På bladet Säkerhetskopieringsmål väljer du Azure i listrutan **Var körs din arbetsbelastning?** Från listrutan **Vad vill du säkerhetskopiera?** väljer du Virtuell dator och klickar sedan på **OK**.

    Nu registreras tillägget för den virtuella datorn i valvet. Bladet Säkerhetskopieringsmål stängs och bladet **Säkerhetskopieringspolicy** öppnas.

    ![Öppna bladet Scenario](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. På bladet Säkerhetskopieringspolicy väljer du den säkerhetskopieringspolicy som du vill använda för valvet.

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Information om standardprincipen visas under den nedrullningsbara menyn. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Klicka på **OK** för att associera säkerhetskopieringspolicyn med valvet.

    Bladet Säkerhetskopieringspolicy stängs och bladet **Välj virtuella datorer** öppnas.
5. På bladet **Välj virtuella datorer** väljer du de virtuella datorer som du vill associera med den angivna principen och klickar på **OK**.

    ![Välja arbetsbelastning](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Den valda virtuella datorn verifieras. Om du inte ser de virtuella datorer som du förväntar dig kontrollerar du att de finns på samma Azure-plats som Recovery Services-valvet. Platsen för Recovery Services-valvet visas på instrumentpanelen för valvet.

6. Nu när du har definierat alla inställningar för valvet klickar du på **Aktivera säkerhetskopiering** på bladet Säkerhetskopiering så att principen distribueras till valvet och de virtuella datorerna. När du distribuerar säkerhetskopieringspolicyn skapas inte den första återställningspunkten för den virtuella datorn.

    ![Aktivera säkerhetskopiering](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopieringen körs säkerhetskopieringspolicyn enligt schemat. Fortsätt för att initiera det första säkerhetskopieringsjobbet.

## <a name="initial-backup"></a>Den första säkerhetskopieringen
Att en säkerhetskopieringspolicy har distribuerats på den virtuella datorn betyder inte att dina data har säkerhetskopierats. Som standard är den första schemalagda säkerhetskopieringen (enligt konfigurationen av säkerhetskopieringspolicyn) den inledande säkerhetskopieringen. Innan den första säkerhetskopieringen har körts visas Status för senaste säkerhetskopiering på bladet **Säkerhetskopieringsjobb** som **Varning (första säkerhetskopiering väntar)**.

![Säkerhetskopiering väntar](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Såvida inte den första säkerhetskopieringen kommer att börja snart, rekommenderar vi att du kör **Säkerhetskopiera nu**.

Så här kör du det första säkerhetskopieringsjobbet:

1. På instrumentpanelen för valvet klickar du på numret under **Säkerhetskopieringsobjekt**, eller på panelen **Säkerhetskopieringsobjekt**. <br/>
  ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Bladet **Säkerhetskopieringsobjekt** öppnas.

  ![Säkerhetskopieringsobjekt](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Välj objektet på bladet **Säkerhetskopieringsobjekt**.

  ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Listan **Säkerhetskopieringsobjekt** öppnas. <br/>

  ![Säkerhetskopieringsjobbet utlöses](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Öppna snabbmenyn genom att klicka på ellipserna **...** i listan **Säkerhetskopieringsobjekt**.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Snabbmenyn visas.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Klicka på **Säkerhetskopiera nu** på snabbmenyn.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Bladet Säkerhetskopiera nu öppnas.

  ![Visar bladet Säkerhetskopiera nu](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. På bladet Säkerhetskopiera nu klickar du på kalenderikonen, använder kalenderkontrollen för att välja den sista dagen som den här återställningspunkten ska behållas och klickar sedan på **Säkerhetskopiera**.

  ![Ange den sista dagen som återställningspunkten som skapas med Säkerhetskopiera nu ska behållas](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

6. Du kan visa eller spåra statusen för den första säkerhetskopieringen genom att klicka på **Pågår** på panelen **Säkerhetskopieringsjobb** på instrumentpanelen för valvet.

  ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Bladet Säkerhetskopieringsjobb öppnas.

  ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Du kan se statusen för alla jobb på bladet **Säkerhetskopieringsjobb**. Kontrollera om säkerhetskopieringsjobbet för den virtuella datorn fortfarande körs, eller om det har slutförts. När säkerhetskopieringsjobbet är klart visas statusen *Slutfört*.

  > [!NOTE]
  > Som en del av säkerhetskopieringen skickar tjänsten Azure Backup ett kommando till säkerhetskopieringstillägget på varje virtuell dator som instruerar det att tömma alla skrivningar och använda en konsekvent ögonblicksbild.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
Den här informationen tillhandahålls om den behövs. VM-agenten i Azure måste installeras på den virtuella Azure-datorn för att säkerhetskopieringstillägget ska fungera. Men om din virtuella dator skapades från Azure-galleriet finns VM-agenten redan på den virtuella datorn. VM-agenten är inte installerad på virtuella datorer som migrerats från lokala datacenter. I dessa fall måste VM-agenten installeras. Om du har problem med att säkerhetskopiera den virtuella datorn i Azure kontrollerar du att VM-agenten i Azure är korrekt installerad på den virtuella datorn (se följande tabell). Om du skapar en anpassad virtuell dator [ser du till att kryssrutan **Installera VM-agenten** är markerad](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) innan den virtuella datorn etableras.

Lär dig mer om [VM-agenten](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) och [hur du installerar den](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Följande tabell innehåller ytterligare information om VM-agenten för virtuella Windows- och Linux-datorer.

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera VM-agenten |<li>Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. <li>[Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |<li> Installera den senaste [Linux-agenten](https://github.com/Azure/WALinuxAgent) från GitHub. Du måste ha administratörsbehörighet för att slutföra installationen. <li> [Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |
| Uppdatera VM-agenten |Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ anvisningarna för hur du [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Bekräfta installationen av VM-agenten |<li>Gå till mappen *C:\WindowsAzure\Packages* på den virtuella datorn i Azure. <li>Du bör hitta filen WaAppAgent.exe.<li> Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet Produktversion ska vara 2.6.1198.718 eller högre. |Saknas |

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
När VM-agenten har installerats på den virtuella datorn installerar Azure Backup-tjänsten säkerhetskopieringstillägget till VM-agenten. Azure Backup-tjänsten uppgraderar och korrigerar säkerhetskopieringstillägget utan ytterligare användarinteraktion.

Backup-tjänsten installerar tillägget för säkerhetskopiering även om den virtuella datorn inte är igång. En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Azure Backup-tjänsten fortsätter dock att säkerhetskopiera den virtuella datorn även om den är avstängd och det inte gick att installera tillägget. Den här typen av säkerhetskopiering kallas för offline-VM och återställningspunkten är *kraschkonsekvent*.

## <a name="troubleshooting-information"></a>Felsökningsinformation
Om du har problem med att utföra vissa uppgifter i den här artikeln läser du [felsökningsanvisningarna](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Priser
Kostnaden för att säkerhetskopiera virtuella datorer i Azure baseras på hur många instanser som skyddas. I [Vad är en skyddad instans](backup-introduction-to-azure-backup.md#what-is-a-protected-instance) definieras begreppet skyddad instans. I avsnittet om att [beräkna skyddade instanser](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances) ges ett exempel på hur du kan beräkna kostnaden för säkerhetskopiering av en virtuell dator. På sidan med prissättning för Azure-säkerhetskopiering finns information om [priser för säkerhetskopiering](https://azure.microsoft.com/pricing/details/backup/).

## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

