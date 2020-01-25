---
title: Konfigurera ett klassrumslabb med Azure Lab Services | Microsoft Docs
description: I den här självstudien använder du Azure Lab Services för att skapa ett klass rums labb med virtuella datorer som används av studenter i din klass.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719185"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudie: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Lägga till användare i labbet
> * Ange schema för labbet
> * Skicka inbjudan via e-post till studenter

## <a name="prerequisites"></a>Krav
Om du vill konfigurera ett klass rums labb i ett labb konto måste du vara medlem i någon av dessa roller i labb kontot: ägare, labb skapare eller deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen.

En labbägare kan lägga till andra användare i rollen **Labbskaparen**. Labbägare lägger exempelvis till universitetslärare till rollen Labbskaparen. Sedan skapar universitetsläraren labbar med virtuella datorer för deras klasser. Elever använder registreringslänken som de får från universitetslärarna för att registrera sig i labbet. När de har registrerats kan de använda virtuella datorer i labbarna för att utföra klass- och hemarbete. Detaljerade anvisningar för att lägga till användare till rollen Labbskaparen finns i [Lägga till en användare till rollen Labbskaparen](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Välj **nytt labb**. 
    
    ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på labbet och välj **Nästa**.  

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Om du väljer en Linux-avbildning ser du ett alternativ för att aktivera anslutning till fjärr skrivbord för det. Mer information finns i [aktivera anslutning till fjärr skrivbord för Linux](how-to-enable-remote-desktop-linux.md).
    2. På sidan **autentiseringsuppgifter för virtuell dator** anger du standardautentiseringsuppgifterna för alla virtuella datorer i labbet. Ange **namn** och **lösen ord** för användaren och välj sedan **Nästa**.  

        ![Nytt labb fönster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. På sidan **labb principer** anger du det antal timmar som tilldelas för varje användare (**kvot för varje användare**) utanför den schemalagda tiden för labbet och väljer sedan **Slutför**. 

        ![Kvot för varje användare](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Du bör se följande skärm bild som visar status för den mall för att skapa mallen. Det tar upp till 20 minuter att skapa mallen i labbet. 

    ![Status för skapande av mall för virtuell dator](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Utför följande steg på sidan **mall** : de här stegen är **valfria** för självstudierna.

    2. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall kan du välja om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    1. Välj **Återställ lösen ord** för att återställa lösen ordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
10. Välj **publicera** i verktygsfältet på sidan **mall** . 

    ![Knappen publicera mall](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
8. På sidan **publicera mall** anger du det antal virtuella datorer som du vill skapa i labbet och väljer sedan **publicera**. 

    ![Publicera mall – antal virtuella datorer](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Växla till sidan **Virtual Machines-pool** genom att välja virtuella datorer på den vänstra menyn eller genom att välja panelen virtuella datorer. Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Du utför följande åtgärder på den här sidan (utför inte de här stegen för självstudierna. De här stegen är endast för din information.): 
    
    1. Om du vill ändra labb kapaciteten (antal virtuella datorer i labbet) väljer du **labb kapacitet** i verktygsfältet.
    2. Om du vill starta alla virtuella datorer samtidigt väljer du **starta alla** i verktygsfältet. 
    3. Om du vill starta en speciell virtuell dator väljer du nedpilen i **status**och väljer sedan **Starta**. Du kan också starta en virtuell dator genom att välja en virtuell dator i den första kolumnen och sedan välja **Starta** i verktygsfältet.

    Mer information om hur du skapar och hanterar mallar och konfigurerar och hanterar elevens virtuella datorer finns i följande artiklar: 
    
    - [Skapa och hantera klass rums labb mallar](how-to-create-manage-template.md)
    - [Konfigurera och hantera poolen för virtuella datorer](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

1. Välj **Användare** på den vänstra menyn. Som standard är alternativet **Begränsa åtkomst** aktiverat. När den här inställningen är aktiverad kan inte användaren registrera med labbet även om användaren har registreringslänken, såvida inte användaren finns i listan över användare. Endast användare i listan kan registreras med labbet genom att använda registreringslänken som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också stänga av **Begränsa åtkomst**, vilket gör att användare registrerar sig med labbet så länge de har registreringslänken. 
2. Välj **Lägg till användare** i verktygsfältet och välj sedan **Lägg till efter e-postadresser**. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

    Du ser namn på användare i listan när de har registrerats i labbet. 
    
## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid vissa tidpunkter. Användar kvoten du angav tidigare är den ytterligare tid som tilldelats varje användare utanför den schemalagda tiden. 

1. Växla till sidan **scheman** och välj **Lägg till schemalagd händelse** i verktygsfältet. 

    ![Knappen Lägg till schema på sidan scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. Utför följande steg på sidan **Lägg till schemalagd händelse** :
    1. Bekräfta att **standard** är valt **händelse typ**.  
    2. Ange **start datum** för klassen. 
    4. Ange **Start tiden** då du vill att de virtuella datorerna ska startas.
    5. Ange **stopp tiden** som de virtuella datorerna ska stängas av. 
    6. Ange **tids zonen** för start-och stopp tider som du har angett. 
3. På sidan **Lägg till schemalagd händelse** väljer du det aktuella schemat i avsnittet **Upprepa** .  

    ![Knappen Lägg till schema på sidan scheman](../media/how-to-create-schedules/select-current-schedule.png)
5. Utför följande steg i dialog rutan **Upprepa** :
    1. Bekräfta att **varje vecka** har angetts för fältet **Upprepa** . 
    2. Välj de dagar som du vill att schemat ska börja gälla. I följande exempel är måndag-fredag markerat. 
    3. Välj ett **slutdatum** för schemat.
    8. Välj **Spara**. 

        ![Ange upprepnings schema](../media/how-to-create-schedules/set-repeat-schedule.png)

3. På sidan **Lägg till schemalagd händelse** för **anteckningar (valfritt)** anger du eventuellt en beskrivning eller information om schemat. 
4. På sidan **Lägg till schemalagd händelse** väljer du **Spara**. 

    ![Vecko schema](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Kontrol lera att schemat har angetts genom att gå till Start datumet i kalendern.
    
    ![Schemalägg i kalendern](../media/how-to-create-schedules/schedule-calendar.png)

    Mer information om hur du skapar och hanterar scheman för en klass finns i [skapa och hantera schema för klass rum labb](how-to-create-schedules.md).

## <a name="send-invitation-emails-to-students"></a>Skicka inbjudningar via e-post till studenter

1. Växla till vyn **användare** om du inte redan är på sidan och välj **Bjud in alla** i verktygsfältet. 

    ![Välj studenter](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. På sidan **Skicka inbjudan via e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. E-postmeddelandet innehåller automatiskt registrerings länken. Du kan hämta den här registrerings länken genom att välja **... (tre punkter)** i verktygsfältet och **registrerings länken**. 

    ![Skicka registrerings länk via e-post](../media/tutorial-setup-classroom-lab/send-email.png)
4. Du ser status för **inbjudan** i listan **användare** . Statusen bör ändras till att **skickas** och sedan **skickas till &lt;datum&gt;** . 

    Mer information om hur du lägger till studenter i en klass och hur du hanterar användningen av labbet finns i [så här konfigurerar du elev användning](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett klassrumslabb och konfigurerade labbet. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

