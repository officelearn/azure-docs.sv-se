---
title: Säkerhetskopiera virtuella Azure-datorer i skala
description: Säkerhetskopiera flera virtuella datorer samtidigt till Azure
services: backup
keywords: virtual machine backup; virtual machine back up; back up vm; backup vm; backup Azure vm; backup and disaster recovery
author: rayne-wiselman
ms.author: raynew
ms.date: 2/14/2018
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: 1d9daf5bdccb24db80eaf41597daa15a08ce1bd4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878313"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Använda Azure Portal till att säkerhetskopiera flera virtuella datorer

När du säkerhetskopierar data i Azure lagrar du dessa data i en Azure-resurs som kallas för ett Recovery Services-valv. Recovery Services-valvresursen är tillgänglig på menyn Inställningar i de flesta Azure-tjänster. Fördelen med att integrera Recovery Services-valvet i menyn Inställningar på de flesta Azure-tjänster gör det mycket enkelt att säkerhetskopiera data. Men det kan bli tröttsamt att arbeta med enskilda databaser eller virtuella datorer. Vad händer om du vill säkerhetskopiera data för alla virtuella datorer på en avdelning eller på en plats? Du kan enkelt säkerhetskopiera flera virtuella datorer genom att skapa en säkerhetskopieringspolicy och tillämpa den på de virtuella datorer du önskar. I den här självstudien beskrivs hur du:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * utlöser ett säkerhetskopieringsjobb på begäran för de skyddade virtuella datorerna

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Recovery Services-valvet innehåller säkerhetsdata och säkerhetskopieringspolicyn som tillämpas på de skyddade virtuella datorerna. Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan inte säkerhetskopiera en virtuell dator från en plats till ett Recovery Services-valv på en annan plats. För varje Azure-plats som har virtuella datorer som ska säkerhetskopieras måste det finnas minst ett Recovery Services-valv på platsen.

1. På menyn till vänster väljer du **Alla tjänster** och i listan över tjänster skriver du *Recovery Services*. När du skriver filtreras listan med resurser. När du ser Recovery Services-valv i listan markerar du det för att öppna menyn Recovery Services-valv.

    ![Öppna menyn Recovery Services-valv](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. På menyn **Recovery Services-valv** klickar du på **Lägg till** för att öppna menyn Recovery Services-valv.

    ![Öppna menyn för valvet](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. På menyn Recovery Services-valv

    - Skriv *myRecoveryServicesVault* i **Namn**.
    - Det aktuella prenumerations-ID:t visas i **Prenumeration**. Om du har flera prenumerationer kan du välja en annan prenumeration för det nya valvet.
    - För **Resursgrupp** väljer du **Använd befintlig** och sedan *myResourceGroup*. Om *myResourceGroup* inte finns väljer du **Skapa ny** och skriver *myResourceGroup*.
    - Från den nedrullningsbara menyn **Plats** väljer du *Europa, västra*.
    - Klicka på **Skapa** för att skapa ditt Recovery Services-valv.

Ett Recovery Services-valv måste vara på samma plats som de virtuella datorerna som skyddas. Om du har virtuella datorer i olika regioner skapar du ett Recovery Services-valv i varje region. I den här självstudien skapas ett Recovery Services-valv i *Europa, västra* eftersom det är där som *myVM* (den virtuella datorn som skapades med snabbstarten) skapades.

Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv.

När du skapar ett Recovery Services-valv får valvet som standard geo-redundant lagring. För att tillhandahålla dataåterhämtning replikerar geo-redundant lagring data flera gånger för två Azure-regioner.

## <a name="set-backup-policy-to-protect-vms"></a>Ställa in säkerhetskopieringspolicy för att skydda virtuella datorer

När du har skapat Recovery Services-valvet är nästa steg att konfigurera valvet för typ av data och att ange en säkerhetskopieringspolicy. Säkerhetskopieringspolicyn är schemat för hur ofta återställningspunkter skapas. Principen omfattar också kvarhållningsintervallet för återställningspunkterna. För den här självstudien antar vi att ditt företag är ett idrottskomplex med ett hotell, en stadium, restauranger och kiosker, och att du skyddar dina data på de virtuella datorerna. Med följande steg skapas en säkerhetskopieringspolicy för finansiella data.

1. Från listan med Recovery Services-valv väljer du **myRecoveryServicesVault** för att öppna valvets instrumentpanel.

   ![Öppna menyn Scenario](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. Öppna menyn Säkerhetskopiering på menyn på instrumentpanelen för valvet genom att klicka på **Säkerhetskopiering**.

3. På menyn Säkerhetskopieringsmål väljer du *Azure* i listrutan **Var körs din arbetsbelastning**. Från listrutan **Vad vill du säkerhetskopiera?** väljer du *Virtuell dator* och klickar sedan på **Säkerhetskopiera**.

    Dessa åtgärder förbereder Recovery Services-valvet för att interagera med en virtuell dator. Recovery Services-valv har en standardprincip som skapar en återställningspunkt varje dag och behåller återställningspunkterna under 30 dagar.

    ![Öppna menyn Scenario](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Om du vill skapa en ny policy går du till menyn Säkerhetskopieringspolicy och väljer *Skapa ny* i den nedrullningsbara menyn **Välj säkerhetskopieringspolicy**.

    ![Välja arbetsbelastning](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. På menyn **Säkerhetskopieringspolicy** skriver du *Finans* som **Policynamn**. Ange följande ändringar för säkerhetskopieringspolicyn:
    - För **Säkerhetskopieringsfrekvens** ställer du in tidszon på *Central Time*. Eftersom sportkomplexet ligger i Texas vill ägaren att tidsinställningen ska vara lokal. Ställ in säkerhetskopieringsfrekvensen på varje dag klockan 3:30.
    - För **Kvarhållning av daglig säkerhetskopieringspunkt** anger du 90 dagarsperiod.
    - För **Kvarhållning av veckovis säkerhetskopieringspunkt** använder du återställningspunkten *Måndag* och behåller den i 52 veckor.
    - För **Kvarhållning av månatlig säkerhetskopieringspunkt** använder du från den första söndagen i varje månad och behåller det i 36 månader.
    - Avmarkera alternativet **Kvarhållning av årlig säkerhetskopieringspunkt**. Den ansvariga för Finans vill inte behålla data längre än 36 månader.
    - Skapa säkerhetskopieringspolicyn genom att klicka på **OK**.

    ![Välja arbetsbelastning](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

    När du har skapat säkerhetskopieringspolicyn associerar du policyn med de virtuella datorerna.

6. I dialogrutan **Välj virtuella datorer** väljer du *myVM* och klicka på **OK** för att distribuera säkerhetskopieringspolicyn till de virtuella datorerna.

    Alla virtuella datorer som är på samma plats och som inte redan är associerade till en säkerhetskopieringspolicy visas. *myVMH1* och *myVMR1* markeras för att associeras till principen *Finans*.

    ![Välja arbetsbelastning](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    När distributionen slutförs får du ett meddelande om det.

## <a name="initial-backup"></a>Den första säkerhetskopieringen

Du har aktiverat säkerhetskopiering för Recovery Services-valven, men du har inte skapat någon säkerhetskopia. Det är en bästa metod för haveriberedskap för att utlösa den första säkerhetskopian, så dina data skyddas.

Så här kör du en säkerhetskopiering på begäran:

1. På instrumentpanelen för klickar du på **3** under **Säkerhetskopieringsobjekt** för att öppna menyn Säkerhetskopieringsobjekt.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Menyn **Säkerhetskopieringsobjekt** öppnas.

2. På menyn **Säkerhetskopiera objekt** klickar du på **Virtuell Azure-dator** för att öppna listan över virtuella datorer som är associerade till valvet.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Listan **Säkerhetskopieringsobjekt** öppnas.

    ![Säkerhetskopieringsjobbet utlöses](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Öppna snabbmenyn genom att klicka på ellipserna **...** i listan **Säkerhetskopieringsobjekt**.

4. Välj **Säkerhetskopiera nu** på snabbmenyn.

    ![Snabbmeny](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Menyn Säkerhetskopiera nu öppnas.

5. På menyn Säkerhetskopiera nu anger du den senaste dagen för att behålla återställningspunkten och klickar på **Säkerhetskopiera**.

    ![Ange den sista dagen som återställningspunkten som skapas med Säkerhetskopiera nu ska behållas](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

    När det första säkerhetskopieringsjobbet är klart visas dess status på menyn för säkerhetskopieringsjobb. Säkerhetskopieringsjobbet på begäran skapade den första återställningspunkten för *myVM*. Upprepa dessa steg för varje virtuell dator om du vill säkerhetskopiera andra virtuella datorer.

    ![Panelen Säkerhetskopieringsjobb](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här självstudien. Om du inte planerar att fortsätta följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudien på Azure-portalen.

1. På instrumentpanelen för **myRecoveryServicesVault** klickar du på **3** under **Säkerhetskopieringsobjekt** för att öppna menyn Säkerhetskopieringsobjekt.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. På menyn **Säkerhetskopiera objekt** klickar du på **Virtuell Azure-dator** för att öppna listan över virtuella datorer som är associerade till valvet.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Listan **Säkerhetskopieringsobjekt** öppnas.

3. Öppna snabbmenyn genom att klicka på ellipserna på menyn **Säkerhetskopieringsobjekt**.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. På snabbmenyn väljer du **Stoppa säkerhetskopiering** för att öppna menyn Stoppa säkerhetskopiering.

    ![Ikonen Inställningar](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. På menyn **Stoppa säkerhetskopiering** väljer du den övre nedrullningsbara menyn och sedan **Ta bort säkerhetskopieringsdata**.

6. I dialogrutan **Type the name of the Backup item** (Skriv namnet på säkerhetskopieringsobjektet) skriver du *myVM*.

7. När säkerhetskopieringsobjektet har verifierats (en kryssmarkering visas) aktiveras knappen **Stoppa säkerhetskopiering**. Klicka på **Stoppa säkerhetskopiering** för att stoppa policyn och ta bort återställningspunkterna.

    ![klicka på Stoppa säkerhetskopiering för att ta bort valvet](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. På menyn **myRecoveryServicesVault** klickar du på **Ta bort**.

    ![klicka på Stoppa säkerhetskopiering för att ta bort valvet](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    När valvet har tagits bort returnerar du listan med Recovery Services-valv.


## <a name="next-steps"></a>Nästa steg

I den här självstudien använder du Azure-portalen till att:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * Ställa in valvet för att skydda virtuella datorer
> * Skapa en anpassad policy för säkerhetskopiering och kvarhållning
> * Tilldela principen för att skydda flera virtuella datorer
> * Utlösa en säkerhetskopiering på begäran för virtuella datorer

Fortsätta till nästa kurs för att återställa en virtuell Azure-dator från disken.

> [!div class="nextstepaction"]
> [Återställa virtuella datorer med CLI](./tutorial-restore-disk.md)
