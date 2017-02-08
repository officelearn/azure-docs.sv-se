---
title: "En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv | Microsoft Docs"
description: "Skydda virtuella datorer i Azure med ett Recovery Services-valv. Använd säkerhetskopior av Resource Manager-distribuerade virtuella datorer och klassiskt distribuerade virtuella datorer samt virtuella datorer i Premium Storage för att skydda dina data. Skapa och registrera ett Recovery Services-valv. Registrera virtuella datorer, skapa en princip och skydda virtuella datorer i Azure."
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
ms.date: 11/10/2016
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: d18cd2c117ced64e407e87bcc96da38b0351a341
ms.openlocfilehash: 0b3409074e0b4929fdf1f5a6b915e3814facedf6


---
# <a name="first-look-protect-azure-vms-with-a-recovery-services-vault"></a>En första titt: Skydda virtuella datorer i Azure med ett Recovery Services-valv
> [!div class="op_single_selector"]
> * [Skydda virtuella datorer med ett Recovery Services-valv](backup-azure-vms-first-look-arm.md)
> * [Skydda virtuella datorer med ett säkerhetskopieringsvalv](backup-azure-vms-first-look.md)
>
>

Den här självstudierna beskriver steg för steg hur du skapar ett Recovery Services-valv och säkerhetskopierar en virtuell Azure-dator (VM). Recovery Services-valv skyddar:

* Azure Resource Manager-distribuerade virtuella datorer
* Klassiska virtuella datorer
* Virtuella datorer i standardlagring
* Virtuella datorer i Premium Storage
* Virtuella datorer som har krypterats med Azure Disk Encryption, med BEK och KEK

Mer information om hur du skyddar virtuella datorer i Premium Storage finns i [Säkerhetskopiera och återställa virtuella datorer i Premium Storage](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup)

> [!NOTE]
> I den här självstudiekursen förutsätter vi att du redan har en virtuell dator i din Azure-prenumeration och att du har utfört nödvändiga åtgärder för att ge säkerhetskopieringstjänsten åtkomst till den virtuella datorn.
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

Beroende på hur många virtuella datorer som du vill skydda kan du starta från olika startpunkter – om du vill säkerhetskopiera flera virtuella datorer i samma åtgärd går du till Recovery Services-valvet och startar säkerhetskopieringen från instrumentpanelen för valvet. Om du har en enda virtuell dator, som du vill säkerhetskopiera, kan du säkerhetskopiera direkt från bladet för hantering av virtuella datorer.

## <a name="configure-backup-from-vm-management-blade"></a>Konfigurera säkerhetskopiering från bladet för VM-hantering
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På navmenyn klickar du på **Fler tjänster** och skriver **Virtuella datorer** i listan över resurser.  Listan över virtuella datorer visas. Från listan med virtuella datorer väljer du en virtuell dator som du vill säkerhetskopiera. Nu öppnas bladet för hantering av virtuella datorer.
 ![VM-hanteringsbladet](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)

3. Klicka på alternativet ”Säkerhetskopiera” på vänster sida under Inställningar på VM-hanteringsbladet.
![Alternativet Säkerhetskopiera på VM-hanteringsbladet](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

4. Nu öppnas bladet Aktivera säkerhetskopiering. Det här bladet kräver två typer av indata: Recovery Services-valv – en Azure Backup-resurs som används för att lagra säkerhetskopior av de virtuella datorerna; En säkerhetskopieringspolicy – Säkerhetskopieringspolicyn anger schemat för säkerhetskopieringarna och hur länge säkerhetskopior ska behållas. Det här bladet tillhandahålls med standardalternativ. Du kan anpassa dem efter dina säkerhetskopieringsbehov.

  ![Guiden Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. För Recovery Services-valvet kan du välja ett befintligt valv eller skapa ett nytt. Om du skapar ett nytt valv skapas det i samma resursgrupp som den virtuella datorn och platsen är samma som den virtuella datorn. Om du vill skapa ett Recovery Services-valv med olika värden [skapar du ett Recovery Services-valv](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm) innan du klickar på säkerhetskopieringsalternativet i steg&3; och väljer det på det här bladet.

6. Välj bladet Säkerhetskopieringspolicy, välj den säkerhetskopieringspolicy som du vill använda för valvet och klicka på **OK**.
    ![Välj säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Informationen om standardpolicyn visas i informationsavsnittet. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Listrutan innehåller också ett alternativ för att byta tiden när ögonblicksbilden tas. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). När du klickar på **OK** associeras säkerhetskopieringspolicyn med den virtuella datorn.

7. Klicka på ”Aktivera säkerhetskopiering” för att konfigurera säkerhetskopiering på den virtuella datorn. Detta utlöser en distribution.
![Knappen Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. Du kan spåra konfigurationsförloppet med hjälp av aviseringar.
![Avisering för Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Om du klickar på alternativet ”Säkerhetskopiera” på VM-hanteringsbladet när distributionen för Konfigurera säkerhetskopiering är klar kommer du till bladet Säkerhetskopieringsobjekt för den säkerhetskopierade virtuella datorn.
![Vyn Säkerhetskopieringsobjekt för den virtuella datorn](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

## <a name="configure-backup-from-recovery-services-vault-view"></a>Konfigurera säkerhetskopiering från vyn Recovery Services-valv
Här är de generella steg som du utför.  

1. Skapa ett Recovery Services-valv för en virtuell dator.
2. Använd Azure-portalen för att välja ett scenario, ange en princip och identifiera objekt som ska skyddas.
3. Kör den första säkerhetskopieringen.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Skapa ett Recovery Services-valv för en virtuell dator
Ett Recovery Services-valv är en entitet som lagrar alla säkerhetskopior och återställningspunkter som har skapats med tiden. Recovery Services-valvet innehåller också säkerhetskopieringspolicyn som tillämpas på de skyddade virtuella datorerna.

> [!NOTE]
> Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera virtuella datorer från en plats till ett Recovery Services-valv på en annan plats. För varje Azure-plats som har virtuella datorer som ska säkerhetskopieras måste det finnas minst ett Recovery Services-valv på platsen.
>
>

Så här skapar du ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    Listan över Recovery Services-valv visas.
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa ett Recovery Services-valv (steg 5)](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.
5. Klicka på **Prenumeration** för att visa listan över prenumerationer. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.
6. Klicka på **Resursgrupp** för att visa listan över resursgrupper eller klicka på **Nytt** för att skapa en resursgrupp. För komplett information om resursgrupper, se [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Klicka på **Plats** för att välja en geografisk region för valvet. Valvet **måste** finnas i samma region som de virtuella datorer som du vill skydda.

   > [!IMPORTANT]
   > Om du är osäker på var din virtuella dator finns stänger du dialogrutan för valvgenerering och går till listan med virtuella datorer på portalen. Om du har virtuella datorer i flera regioner skapar du ett Recovery Services-valv i varje region. Skapa valvet på den första platsen innan du fortsätter till nästa plats. Du behöver inte ange lagringskonton för att lagra säkerhetskopierade data: Recovery Services-valvet och tjänsten Azure Backup hanterar detta automatiskt.
   >
   >
8. Klicka på **Skapa**. Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger på portalen. När valvet har skapats visas det i listan över Recovery Services-valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

Nu när du har skapat valvet ska vi se hur du konfigurerar lagringsreplikeringen.

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Lämna alternativet inställt på geo-redundant lagring om det här är din primära säkerhetskopia. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/storage-redundancy.md#locally-redundant-storage) lagring i [Översikt över Azure Storage-replikering](../storage/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrumentpanelen för valvet och bladet Inställningar. Om bladet **Inställningar** inte öppnas klickar du på **Alla inställningar** på instrumentpanelen för valvet.
2. På bladet **Inställningar** klickar du på **Infrastruktur för säkerhetskopiering** > **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**. På bladet **Konfiguration av säkerhetskopiering** väljer du alternativet för lagringsreplikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Välj ett säkerhetskopieringsmål, ange en princip och definiera objekt som ska skyddas
Innan du registrerar en virtuell dator med ett valv kör du identifieringsprocessen för att säkerställa att nya virtuella datorer som har lagts till i prenumerationen identifieras. Under processen uppmanas Azure att returnera listan med virtuella datorer i prenumerationen, tillsammans med ytterligare information som molntjänstens namn och regionen. På Azure-portalen refererar scenariot till vad du ska lägga till i Recovery Services-valvet. Principen är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna.

1. Om du redan har ett öppet Recovery Services-valv går du vidare till steg 2. Om inget Recovery Services-valv är öppet, men du befinner dig på Azure Portal klickar du på **Bläddra** på hubbmenyn.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

     ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

     Listan över Recovery Services-valv visas.
   * Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.

     ![Öppna bladet för valvet](./media/backup-azure-vms-first-look-arm/vault-settings.png)
2. På menyn på instrumentpanelen för valvet öppnar du bladet Säkerhetskopiering genom att klicka på **Säkerhetskopiering**.

    ![Öppna bladet Säkerhetskopiering](./media/backup-azure-vms-first-look-arm/backup-button.png)

    När bladet öppnas söker Backup-tjänsten efter nya virtuella datorer i prenumerationen.

    ![Identifiera virtuella datorer](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)
3. Öppna bladet Säkerhetskopieringsmål genom att klicka på **Säkerhetskopieringsmål** på bladet Säkerhetskopiering.

    ![Öppna bladet Scenario](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)
4. På bladet Säkerhetskopieringsmål väljer du Azure för **Var körs din arbetsbelastning?** och Virtuell dator för **Vad vill du säkerhetskopiera?** och klickar sedan på **OK**.

    Bladet Säkerhetskopieringsmål stängs och bladet Säkerhetskopieringspolicy öppnas.

    ![Öppna bladet Scenario](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)
5. Välj bladet Säkerhetskopieringspolicy, välj den säkerhetskopieringspolicy som du vill använda för valvet och klicka på **OK**.

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Informationen om standardpolicyn visas i informationsavsnittet. Om du vill skapa en ny policy väljer du **Skapa ny** i listrutan. Listrutan innehåller också ett alternativ för att byta tiden när ögonblicksbilden tas till kl. 19:00. Mer information om hur du definierar en säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). När du klickar på **OK** associeras säkerhetskopieringspolicyn med valvet.

    Välj sedan de virtuella datorer som du vill associera med valvet.
6. Välj de virtuella datorer som du vill associera med den angivna policyn och klicka på **Välj**.

    ![Välja arbetsbelastning](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    Om du inte ser den virtuella dator som du vill associera kontrollerar du att den finns på samma Azure-plats som Recovery Services-valvet.
7. Nu när du har definierat alla inställningar för valvet klickar du på **Aktivera säkerhetskopiering** längst ned på bladet Säkerhetskopiering. När du gör det distribueras policyn till valvet och de virtuella datorerna.

    ![Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

## <a name="initial-backup"></a>Den första säkerhetskopieringen
Att en säkerhetskopieringspolicy har distribuerats på den virtuella datorn betyder inte att dina data har säkerhetskopierats. Som standard är den första schemalagda säkerhetskopieringen (enligt konfigurationen av säkerhetskopieringspolicyn) den inledande säkerhetskopieringen. Innan den första säkerhetskopieringen har körts visas Status för senaste säkerhetskopiering på bladet **Säkerhetskopieringsjobb** som **Varning (första säkerhetskopiering väntar)**.

![Säkerhetskopiering väntar](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Såvida inte den första säkerhetskopieringen kommer att börja snart, rekommenderar vi att du kör **Säkerhetskopiera nu**.

Så här kör du **Säkerhetskopiera nu**:

1. På instrumentpanelen för valvet klickar du på **Azure Virtual Machines** på panelen **Säkerhetskopiering**. <br/>
    ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Bladet **Säkerhetskopieringsobjekt** öppnas.
2. På bladet **Säkerhetskopieringsobjekt** högerklickar du på det valv som du vill säkerhetskopiera och klickar på **Säkerhetskopiera nu**.

    ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Detta utlöser säkerhetskopieringsjobbet. <br/>

    ![Säkerhetskopieringsjobbet utlöses](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. Om du vill kontrollera att den första säkerhetskopieringen har slutförts klickar du på **Virtuella Azure-datorer** på panelen **Säkerhetskopieringsjobb** på instrumentpanelen för valvet.

    ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Bladet Säkerhetskopieringsjobb öppnas.
4. Du kan se statusen för alla jobb på bladet Säkerhetskopieringsjobb.

    ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > Som en del av säkerhetskopieringen skickar tjänsten Azure Backup ett kommando till säkerhetskopieringstillägget på varje virtuell dator som instruerar det att tömma alla skrivningar och använda en konsekvent ögonblicksbild.
   >
   >

    När säkerhetskopieringsjobbet är klart är statusen *Slutförd*.

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installera VM-agenten på den virtuella datorn
Den här informationen tillhandahålls om den behövs. VM-agenten i Azure måste installeras på den virtuella Azure-datorn för att säkerhetskopieringstillägget ska fungera. Men om din virtuella dator skapades från Azure-galleriet finns VM-agenten redan på den virtuella datorn. VM-agenten är inte installerad på virtuella datorer som migrerats från lokala datacenter. I dessa fall måste VM-agenten installeras. Om du har problem med att säkerhetskopiera den virtuella datorn i Azure kontrollerar du att VM-agenten i Azure är korrekt installerad på den virtuella datorn (se tabellen nedan). Om du skapar en anpassad virtuell dator [ser du till att kryssrutan **Installera VM-agenten** är markerad](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) innan den virtuella datorn etableras.

Lär dig mer om [VM-agenten](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) och [hur du installerar den](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Följande tabell innehåller ytterligare information om VM-agenten för virtuella Windows- och Linux-datorer.

| **Åtgärd** | **Windows** | **Linux** |
| --- | --- | --- |
| Installera VM-agenten |<li>Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen. <li>[Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |<li> Installera den senaste [Linux-agenten](https://github.com/Azure/WALinuxAgent) från GitHub. Du måste ha administratörsbehörighet för att slutföra installationen. <li> [Uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. |
| Uppdatera VM-agenten |Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |Följ anvisningarna för hur du [uppdaterar VM-agenten för Linux](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Kontrollera att ingen säkerhetskopieringsåtgärd körs medan VM-agenten uppdateras. |
| Bekräfta installationen av VM-agenten |<li>Gå till mappen *C:\WindowsAzure\Packages* på den virtuella datorn i Azure. <li>Du bör hitta filen WaAppAgent.exe.<li> Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet Produktversion ska vara 2.6.1198.718 eller högre. |Saknas |

### <a name="backup-extension"></a>Säkerhetskopieringstillägg
När VM-agenten har installerats på den virtuella datorn installerar Azure Backup-tjänsten säkerhetskopieringstillägget till VM-agenten. Azure Backup-tjänsten uppgraderar och korrigerar säkerhetskopieringstillägget utan ytterligare användarinteraktion.

Säkerhetskopieringstillägget installeras av Backup-tjänsten oavsett om den virtuella datorn körs eller inte. En virtuell dator som körs har bäst chans att tilldelas en programkonsekvent återställningspunkt. Azure Backup-tjänsten fortsätter dock att säkerhetskopiera den virtuella datorn även om den är avstängd och det inte gick att installera tillägget. Detta kallas för en virtuell offlinedator. I detta fall är återställningspunkten *kraschkonsekvent*.

## <a name="troubleshooting-information"></a>Felsökningsinformation
Om du har problem med att utföra vissa uppgifter i den här artikeln läser du [felsökningsanvisningarna](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Priser
Azure VM-säkerhetskopiering kommer att debiteras baserat på modellen för skyddade instanser. Läs mer under [priser för säkerhetskopiering](https://azure.microsoft.com/pricing/details/backup/)

## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).



<!--HONumber=Dec16_HO2-->


