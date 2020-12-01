---
title: Konfigurera ett klassrumslabb med Azure Lab Services | Microsoft Docs
description: I den här självstudien använder du Azure Lab Services för att skapa ett klass rums labb med virtuella datorer som används av studenter i din klass.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8981a03b53b1cfb67b03d89f8a1468511d9b1b93
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434863"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudie: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Lägga till användare i labbet
> * Ange schema för labbet
> * Skicka inbjudan via e-post till studenter

## <a name="prerequisites"></a>Förutsättningar
I den här självstudien skapar du ett labb med virtuella datorer för din klass. Om du vill konfigurera ett klass rums labb i ett labb konto måste du vara medlem i någon av dessa roller i labb kontot: ägare, labb skapare eller deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen. Så du kan använda det användar konto som du använde för att skapa ett labb konto för att skapa ett klass rums labb. 

Här är det vanligaste arbets flödet när du använder Azure Lab Services:

1. Ett labb konto skapare lägger till andra användare i **labb skaparen** -rollen. Labb kontots skapare/administratör lägger till exempel till lärare till **labb skapare** rollen så att de kan skapa labb för sina klasser. 
2. Sedan skapar läraren labb med virtuella datorer för sina klasser och skickar registrerings länkar till studenter i klassen. 
3. Eleverna använder registrerings länken som de får från lärare att registrera sig på labbet. När de har registrerats kan de använda virtuella datorer i labbarna för att utföra klass- och hemarbete. 

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb
I det här steget skapar du ett labb för din klass i Azure. 

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Välj **nytt labb**. 
    
    ![Skärm bild som visar "Azure Lab Services" med knappen "nytt labb" valt.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på labbet och välj **Nästa**.  

        ![Skapa ett klassrumslabb](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. På sidan **autentiseringsuppgifter för virtuell dator** anger du standardautentiseringsuppgifterna för alla virtuella datorer i labbet. Ange **namn** och **lösen ord** för användaren och välj sedan **Nästa**.  

        ![Nytt labb fönster](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. På sidan **labb principer** väljer du **Slutför**. 

        ![Kvot för varje användare](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Du bör se följande skärm bild som visar status för den mall för att skapa mallen. Den här åtgärden tar upp till 20 minuter. 

    ![Status för skapande av mall för virtuell dator](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Utför följande steg på sidan **mall** : de här stegen är **valfria** för självstudierna.

    1. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall kan du välja om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    3. Installera och konfigurera program vara som krävs för din klass på den virtuella dator mal len. 
    4. **Stoppa** mallen VM.  

    > [!NOTE]
    > Mall VM: ar **kostar** att köra, så se till att den virtuella datorns mall stängs av när du inte behöver den för att köras. 

## <a name="publish-the-template-vm"></a>Publicera den virtuella malldatorn
I det här steget publicerar du mallen VM. När du publicerar mallen VM skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Alla virtuella datorer har samma konfiguration som mallen.

1. På sidan **mall** väljer du **publicera** i verktygsfältet. 

    ![Knappen publicera mall](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
2. På sidan **publicera mall** anger du det antal virtuella datorer som du vill skapa i labbet och väljer sedan **publicera**. 

    ![Publicera mall – antal virtuella datorer](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Vänta tills publiceringen är klar och växla sedan till sidan **Virtual Machines-pool** genom att välja **virtuella datorer** på den vänstra menyn eller genom att välja panelen **virtuella datorer** . Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > När en lärare aktive ras på en elev-VM påverkas inte kvoten för studenten. Kvoten för en användare anger antalet Labb timmar som är tillgängliga för användaren utanför den schemalagda klass tiden. Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid vissa tidpunkter. Användar kvoten (standard 10 timmar) som du angav tidigare är den ytterligare tid som tilldelats varje användare utanför den schemalagda tiden. 

1. Växla till sidan **scheman** och välj **Lägg till schemalagd händelse** i verktygsfältet. 

    ![Skärm bild som visar knappen "Lägg till schemalagd händelse" på sidan "scheman".](./media/how-to-create-schedules/add-schedule-button.png)
2. Utför följande steg på sidan **Lägg till schemalagd händelse** :
    1. Bekräfta att **standard** är valt **händelse typ**.  
    2. Välj **start datum** för klassen. 
    4. Välj **Start tiden** då du vill att de virtuella datorerna ska startas.
    5. Välj **stopp tiden** som de virtuella datorerna ska stängas av. 
    6. Välj tidszon **för start** -och stopp tider som du har angett. 
3. På sidan **Lägg till schemalagd händelse** väljer du det aktuella schemat i avsnittet **Upprepa** .  

    ![Knappen Lägg till schema på sidan scheman](./media/how-to-create-schedules/select-current-schedule.png)
5. Utför följande steg i dialog rutan **Upprepa** :
    1. Bekräfta att **varje vecka** har angetts för fältet **Upprepa** . 
    2. Välj de dagar som du vill att schemat ska börja gälla. I följande exempel är Monday-Friday markerat. 
    3. Välj ett **slutdatum** för schemat.
    8. Välj **Spara**. 

        ![Ange upprepnings schema](./media/how-to-create-schedules/set-repeat-schedule.png)

3. På sidan **Lägg till schemalagd händelse** för **anteckningar (valfritt)** anger du eventuellt en beskrivning eller information om schemat. 
4. På sidan **Lägg till schemalagd händelse** väljer du **Spara**. 

    ![Vecko schema](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Kontrol lera att schemat har angetts genom att gå till Start datumet i kalendern.
    
    ![Schemalägg i kalendern](./media/how-to-create-schedules/schedule-calendar.png)

    Mer information om hur du skapar och hanterar scheman för en klass finns i [skapa och hantera schema för labb](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

1. Välj **Användare** på den vänstra menyn. Som standard är alternativet **Begränsa åtkomst** aktiverat. När den här inställningen är aktiverad kan inte användaren registrera med labbet även om användaren har registreringslänken, såvida inte användaren finns i listan över användare. Endast användare i listan kan registreras med labbet genom att använda registreringslänken som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också stänga av **Begränsa åtkomst**, vilket gör att användare registrerar sig med labbet så länge de har registreringslänken. 
2. Välj **Lägg till användare** i verktygsfältet och välj sedan **Lägg till efter e-postadress**. 

    ![Knappen Lägg till användare](./media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](./media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](./media/how-to-configure-student-usage/users-list-new.png)

    Du ser namn på användare i listan när de har registrerats i labbet. 
    

## <a name="send-invitation-emails-to-users"></a>Skicka inbjudningar via e-post till användare

1. Växla till vyn **användare** om du inte redan är på sidan och välj **Bjud in alla** i verktygsfältet. 

    ![Välj studenter](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. På sidan **Skicka inbjudan via e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. E-postmeddelandet innehåller automatiskt registrerings länken. Du kan hämta den här registrerings länken genom att välja **... (tre punkter)** i verktygsfältet och **registrerings länken**. 

    ![Skicka registrerings länk via e-post](./media/tutorial-setup-classroom-lab/send-email.png)
4. Du ser status för **inbjudan** i listan **användare** . Statusen bör ändras till att **skickas** och sedan **skickas till &lt; datum &gt;**. 

    Mer information om hur du lägger till studenter i en klass och hur du hanterar användningen av labbet finns i [så här konfigurerar du elev användning](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat ett labb för din klass i Azure. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

