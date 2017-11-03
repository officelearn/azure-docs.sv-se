---
title: "Säkerhetskopiera virtuella Azure-datorer i skala | Microsoft Docs"
description: "Säkerhetskopiera flera virtuella datorer samtidigt till Azure"
services: backup
keywords: "säkerhetskopiering av virtuella datorer virtuella säkerhetskopiera; Säkerhetskopiera vm; säkerhetskopiering vm; Säkerhetskopiera Azure vm; säkerhetskopiering och katastrofåterställning"
author: markgalioto
ms.author: markgal
ms.date: 09/16/2017
ms.topic: article
ms.service: backup
ms.openlocfilehash: f144db921a8b2d01dbfe883a48574c4fabdae6f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Använd Azure-portalen för att säkerhetskopiera flera virtuella datorer

När du säkerhetskopierar data i Azure kan du lagra data i en Azure-resurs kallas Recovery Services-valvet. Recovery Services-valvet resursen är tillgänglig på menyn Inställningar i de flesta Azure-tjänster. Fördelen med att ha Recovery Services-valvet integrerats i menyn Inställningar i de flesta Azure-tjänster gör det mycket enkelt att säkerhetskopiera data. Individuellt arbeta med varje databas eller en virtuell dator i ditt företag är dock tråkigt. Vad händer om du vill säkerhetskopiera data för alla virtuella datorer på en avdelning eller på en plats? Det är enkelt att säkerhetskopiera flera virtuella datorer genom att skapa en princip för säkerhetskopiering och tillämpa principen på de önskade virtuella datorerna. Den här självstudiekursen beskrivs hur du:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv
> * Definiera en princip för säkerhetskopiering
> * Tillämpa principen för säkerhetskopiering för att skydda flera virtuella datorer
> * Utlösa ett säkerhetskopieringsjobb för skyddade virtuella datorer på begäran

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Recovery Services-valvet innehåller säkerhetskopierade data och säkerhetskopiera principen tillämpas på de skydda virtuella datorerna. Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera en virtuell dator från en plats till ett Recovery Services-valv i en annan plats. Så för varje Azure-plats som har virtuella datorer som ska säkerhetskopieras, minst en Recovery Services-valvet måste det finnas på den platsen.

1. På den vänstra menyn väljer **fler tjänster** och Skriv i listan över tjänster *återställningstjänster*. När du skriver filtreras listan med resurser. När du ser Recovery Services-valv i listan, väljer du den öppna menyn i Recovery Services-valv.

    ![Öppna Återställningstjänstvalvet-menyn](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. I den **Recovery Services-valv** -menyn klickar du på **Lägg till** att öppna menyn Recovery Services-valvet.

    ![Öppna valvet menyn](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. Valvet i Recovery Services-menyn 

    - Typen *myRecoveryServicesVault* i **namnet**,
    - Det aktuella prenumerations-ID visas i **prenumeration**. Om du har ytterligare prenumerationer kan du välja en annan prenumeration för det nya valvet.
    - För **resursgruppen** Välj **använda befintliga** och välj *myResourceGroup*. Om *myResourceGroup* inte finns, Välj **Skapa nytt** och skriv *myResourceGroup*.
    - Från den **plats** nedrullningsbara menyn, Välj *Västeuropa*.
    - Klicka på **skapa** att skapa Recovery Services-valvet.

Recovery Services-valvet måste vara på samma plats som de virtuella datorerna skyddas. Om du har virtuella datorer i flera regioner kan du skapa ett Recovery Services-valv i varje region. Den här guiden skapar ett Recovery Services-valv i *Västeuropa* eftersom där har *myVM* (den virtuella datorn skapas med Snabbstart) har skapats.

Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv.

När du skapar ett Recovery Services-valv har valvet standard geo-redundant lagring. För att tillhandahålla dataåterhämtning replikerar geo-redundant lagring data flera gånger mellan två Azure-regioner.

## <a name="set-backup-policy-to-protect-vms"></a>Ange princip för säkerhetskopiering att skydda virtuella datorer

Nästa steg är att konfigurera valvet för typ av data och för att ställa in principen för säkerhetskopiering när du har skapat Recovery Services-valvet. Princip för säkerhetskopiering är schemat för hur ofta och när återställningspunkter tas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna. I den här kursen antar vi att ditt företag är en komplex med ett hotell, stadium, och restaurang- och medgivanden sport och du skyddar data på de virtuella datorerna. Följande steg skapar en princip för säkerhetskopiering för finansiella data.

1. Välj i listan över Recovery Services-valv **myRecoveryServicesVault** att öppna dess instrumentpanel.

   ![Öppna menyn Scenario](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. Klicka på menyn valvet instrumentpanelen **säkerhetskopiering** öppna menyn i säkerhetskopian.

3. På menyn mål för säkerhetskopiering i den **var körs din arbetsbelastning** nedrullningsbara menyn, Välj *Azure*. Från den **vad vill du säkerhetskopiera** listrutan, Välj *virtuella*, och klicka på **säkerhetskopiering**.

    De här åtgärderna Förbered Recovery Services-valvet för att interagera med en virtuell dator. Recovery Services-valv har en standardprincip som skapar en återställningspunkt varje dag och behåller återställningspunkter under 30 dagar.

    ![Öppna menyn Scenario](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Skapa en ny princip på menyn säkerhetskopiering princip från den **Välj princip för säkerhetskopiering** nedrullningsbara menyn och väljer *Skapa nytt*.

    ![Välja arbetsbelastning](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. I den **säkerhetskopiera princip** menyn för **principnamn** typen *ekonomi*. Ange följande ändringar för princip för säkerhetskopiering: 
    - För **säkerhetskopieringsfrekvens** ange tidszonen för *Centraltid*. Eftersom sport komplex Texas, vill ägaren tidsinställning användas lokalt. Lämna den säkerhetskopieringsfrekvens som är inställd på varje dag kl 3:30.
    - För **kvarhållning av daglig säkerhetskopieringspunkt**, anger du tidsperioden till 90 dagar.
    - För **kvarhållning av veckovis säkerhetskopieringspunkt**, använda den *måndag* återställningspunkt och förvara dessa i 52 veckor.
    - För **kvarhållning av månatlig säkerhetskopieringspunkt**, använder återställningspunkt från första söndag i månaden och förvara dessa i 36 månader.
    - Avmarkera den **kvarhållning av årlig säkerhetskopieringspunkt** alternativet. Utfyllnad för ekonomi vill inte behålla data som är längre än 36 månader.
    - Skapa säkerhetskopieringspolicyn genom att klicka på **OK**.

    ![Välja arbetsbelastning](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    När du har skapat principen för säkerhetskopiering, associera principen med de virtuella datorerna.

6. I den **Välj virtuella datorer** dialogrutan Välj *myVM* och på **OK** att distribuera principen för säkerhetskopiering till de virtuella datorerna. 

    Alla virtuella datorer som finns på samma plats och inte är redan kopplad till en princip för säkerhetskopiering, visas. *myVMH1* och *myVMR1* som associeras med den *ekonomi* princip.

    ![Välja arbetsbelastning](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    När distributionen är klar får du ett meddelande distributionen har slutförts.

## <a name="initial-backup"></a>Den första säkerhetskopieringen

Du har aktiverat säkerhetskopiering för Recovery Services-valv men en första säkerhetskopiering har inte skapats. Det är disaster recovery bäst att utlösa den första säkerhetskopieringen så att dina data skyddas. 

Köra en säkerhetskopiering på begäran:

1. Klicka på instrumentpanelen i valvet **3** under **säkerhetskopiering objekt**, för att öppna menyn objekt för säkerhetskopiering.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Den **säkerhetskopiering objekt** menyn öppnas.

2. På den **säkerhetskopiering objekt** -menyn klickar du på **Azure virtuella** att öppna listan över virtuella datorer som är kopplad till valvet.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Listan **Säkerhetskopieringsobjekt** öppnas.

    ![Säkerhetskopieringsjobbet utlöses](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Öppna snabbmenyn genom att klicka på ellipserna **...** i listan **Säkerhetskopieringsobjekt**.

4. Välj på snabbmenyn **Säkerhetskopiera nu**.

    ![Snabbmeny](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Säkerhetskopiera nu-menyn öppnas.

5. Ange den sista dagen om du vill behålla återställningspunkten och klicka på Säkerhetskopiera nu-menyn **säkerhetskopiering**.

    ![Ange den sista dagen som återställningspunkten som skapas med Säkerhetskopiera nu ska behållas](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb. Beroende på storleken på den virtuella datorn, kan det ta en stund att skapa den första säkerhetskopian.

    När det första säkerhetskopieringsjobbet är klar visas dess status i menyn jobbet för säkerhetskopiering. Säkerhetskopieringsjobbet på begäran skapa den första återställningspunkten för *myVM*. Upprepa dessa steg för varje virtuell dator om du vill säkerhetskopiera andra virtuella datorer. 

    ![Panelen Säkerhetskopieringsjobb](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta på att arbeta med efterföljande självstudiekurser inte rensa upp de resurser som skapades i den här självstudiekursen. Om du inte planerar att fortsätta, Använd följande steg för att ta bort alla resurser som skapats av den här kursen i Azure-portalen.

1. På den **myRecoveryServicesVault** instrumentpanelen, klickar du på **3** under **säkerhetskopiering objekt**, för att öppna menyn objekt för säkerhetskopiering.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. På den **säkerhetskopiering objekt** -menyn klickar du på **Azure virtuella** att öppna listan över virtuella datorer som är kopplad till valvet.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Listan **Säkerhetskopieringsobjekt** öppnas.

3. I den **säkerhetskopiering objekt** -menyn, klicka på knappen för att öppna snabbmenyn.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. Välj på snabbmenyn **stoppa säkerhetskopiering** öppna stoppa Backup-menyn. 

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. I den **stoppa säkerhetskopiering** -menyn, Välj övre listrutan och välj **ta bort säkerhetskopierade Data**.

6. I den **skriver du namnet på objektet säkerhetskopiering** dialogrutan, ange *myVM*.
 
7. När säkerhetskopieringen objektet har verifierats (en markering visas) **stoppa säkerhetskopiering** knappen är aktiverad. Klicka på **stoppa säkerhetskopiering** stoppa principen och ta bort återställningspunkter. 

    ![Klicka på Stoppa säkerhetskopiering för att ta bort valvet](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png).

8. I den **myRecoveryServicesVault** -menyn klickar du på **ta bort**.

    ![Klicka på Stoppa säkerhetskopiering för att ta bort valvet](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    När valvet har tagits bort kan du gå tillbaka till listan över Recovery Services-valv.


## <a name="next-steps"></a>Nästa steg

I den här kursen används Azure portal:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv
> * Ange valvet för att skydda virtuella datorer
> * Skapa en anpassad princip för säkerhetskopiering och kvarhållning
> * Tilldela principen att skydda flera virtuella datorer
> * Utlös en på-begäran tillbaka för virtuella datorer

Fortsätta att i nästa kurs att återställa en virtuell Azure-dator från disken. 

> [!div class="nextstepaction"]
> [Återställa virtuella datorer med hjälp av CLI](./tutorial-restore-disk.md)
