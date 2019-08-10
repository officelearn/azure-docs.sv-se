---
title: Konfigurera ett klassrumslabb med Azure Lab Services | Microsoft Docs
description: I självstudien konfigurerar du ett labb som ska användas i ett klassrum.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 964ecca015e440439885bbbd85cb720a3abd10a9
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883520"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudier: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Lägga till användare i labbet
> * Skicka en registreringslänk till eleverna

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera ett klass rums labb i ett labb konto måste du vara medlem i någon av dessa roller i labb kontot: Ägare, Labbskaparen eller Deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen.

En labbägare kan lägga till andra användare i rollen **Labbskaparen**. Labbägare lägger exempelvis till universitetslärare till rollen Labbskaparen. Sedan skapar universitetsläraren labbar med virtuella datorer för deras klasser. Elever använder registreringslänken som de får från universitetslärarna för att registrera sig i labbet. När de har registrerats kan de använda virtuella datorer i labbarna för att utföra klass- och hemarbete. Detaljerade anvisningar för att lägga till användare till rollen Labbskaparen finns i [Lägga till en användare till rollen Labbskaparen](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Ange maximalt **antal virtuella datorer** i labbet. Du kan öka eller minska antalet virtuella datorer när du har skapat labbet eller i ett befintligt labb. Mer information finns i avsnittet om att [uppdatera antalet virtuella datorer i ett labb](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. På sidan för att **välja specifikationer för virtuell dator** utför du följande steg:
    1. Välj en **storlek** för virtuella datorer (VM) som skapas i labbet. För närvarande tillåts **små**, **medel**, **medium (virtualisering)** , **stor**och **GPU** -storlek.
    3. Välj den **VM-avbildning**  som ska användas för att skapa virtuella datorer i labbet. Om du väljer en Linux-avbildning ser du ett alternativ för att aktivera anslutning till fjärr skrivbord för det. Mer information finns i [aktivera anslutning till fjärr skrivbord för Linux](how-to-enable-remote-desktop-linux.md).
    4. Välj **Nästa**.

        ![Ange VM-specifikationer](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. På sidan **Ange autentiseringsuppgifter** anger du standardautentiseringsuppgifter för alla virtuella datorer i labbet. 
    1. Ange **namnet på användaren** för alla virtuella datorer i labbet.
    2. Ange **lösenordet** för användaren. 

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. Välj **Skapa**. 

        ![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. På sidan **Konfigurera mall** kan du se statusen för labbskapandeprocessen. Det tar upp till 20 minuter att skapa mallen i labbet. 

    ![Konfigurera mall](../media/tutorial-setup-classroom-lab/configure-template.png)
7. När konfigurationen av mallen har slutförts visas följande sida: 

    ![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. På sidan **Konfigurera mall** utför du följande steg: De här stegen är **valfria** för självstudien.
    2. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall kan du välja om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    1. Välj **Återställ lösen ord** för att återställa lösen ordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
9. Välj **Nästa** på mallsidan. 
10. På sidan **Publicera mallen** utför du någon av följande åtgärder. 
    1. Om du vill publicera mallen direkt väljer du **publicera**.  

        > [!WARNING]
        > När du väl har publicerat kan du inte ångra publiceringen. 
    2. Om du vill publicera senare väljer **Spara till senare**. Du kan publicera mallen VM när guiden har slutförts. Mer information om hur du konfigurerar och publicerar när guiden har slutförts finns i avsnittet [publicera mallen](how-to-create-manage-template.md#publish-the-template-vm) i artikeln [Hantera klass rums labb](how-to-manage-classroom-labs.md) .

        ![Publicera mall](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Du ser **förloppet för publiceringen**  av mallen. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Du ser följande sida när mallen har publicerats. Välj **Done** (Klar).

    ![Publicera mall – lyckades](../media/tutorial-setup-classroom-lab/publish-success.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Växla till sidan **Virtuella datorer** genom att välja Virtuella datorer på den vänstra menyn eller genom att välja panelen Virtuella datorer. Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

1. Välj **Användare** på den vänstra menyn. Som standard är alternativet **Begränsa åtkomst** aktiverat. När den här inställningen är aktiverad kan inte användaren registrera med labbet även om användaren har registreringslänken, såvida inte användaren finns i listan över användare. Endast användare i listan kan registreras med labbet genom att använda registreringslänken som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också stänga av **Begränsa åtkomst**, vilket gör att användare registrerar sig med labbet så länge de har registreringslänken. 
2. Välj **Lägg till användare** i verktygsfältet. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Ange kvoter för användare
Du kan ange kvoter per användare med hjälp av följande steg: 

1. Välj **användare** på den vänstra menyn om sidan inte redan är aktiv. 
2. Välj **kvot per användare: 10 timmar** i verktygsfältet. 
3. På sidan **kvot per användare** anger du antalet timmar som du vill ge varje användare (student): 
    1. **Totalt antal labb timmar per användare**. Användare kan använda sina virtuella datorer för det angivna antalet timmar (anges för det här fältet) **utöver den schemalagda tiden**. Om du väljer det här alternativet anger du **antalet timmar** i text rutan. 

        ![Antal timmar per användare](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 timmar (endast schema)** . Användare kan endast använda sina virtuella datorer under den schemalagda tiden eller när du som labb ägare aktiverar virtuella datorer för dem.

        ![Noll timmar – endast schemalagd tid](../media/how-to-configure-student-usage/zero-hours.png)
    4. Välj **Spara**. 
5. Nu visas ändrade värden i verktygsfältet: **Kvot per användare: &lt;antal timmar&gt;** . 

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Om du har konfigurerat kvot inställningen till **0 timmar (endast schema)** måste du ange ett schema för labbet. I den här självstudien ställer du in schemat som ett återkommande vecko schema.

1. Växla till sidan **scheman** och välj **Lägg till schema** i verktygsfältet. 

    ![Knappen Lägg till schema på sidan scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. På sidan **Lägg till schema** växlar du till **veckovis** överst. 
3. I **schema dagar (obligatoriskt)** väljer du de dagar som du vill att schemat ska börja gälla. I följande exempel är måndag-fredag markerat. 
4. I fältet **från** anger du schemats **start datum** eller väljer ett datum genom att välja knappen **kalender** . Det här fältet är obligatoriskt. 
5. För **schema slutdatum**anger eller väljer du ett slutdatum som de virtuella datorerna ska stängas av. 
6. För **Start tid**väljer du den tid då du vill att de virtuella datorerna ska startas. Start tiden måste anges om stopp tiden inte har angetts. Välj **ta bort start händelse** om du bara vill ange stopp tiden. Om **Start tiden** är inaktive rad väljer du **Lägg till Start händelse** bredvid den nedrullningsbara listan för att aktivera det. 
7. För **stopp tid**väljer du den tid då du vill att de virtuella datorerna ska stängas av. Stopp tiden måste anges om start tiden inte har angetts. Välj **ta bort stopp händelse** om du bara vill ange start tiden. Om **stopp tiden** är inaktive rad väljer du **Lägg till stopp händelse** bredvid List rutan för att aktivera det.
8. För **tidszon (krävs)** väljer du tids zonen för start-och stopp tider som du har angett.  
9. För **anteckningar**anger du en beskrivning eller information om schemat. 
10. Välj **Spara**. 

    ![Vecko schema](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Skicka ett e-postmeddelande med registrerings länken

1. Växla till **användarvyn** om du inte är på sidan redan. 
2. Välj vissa eller alla användare i listan. Markera kryss rutor i den första kolumnen i listan för att välja vissa användare. Markera kryss rutan framför rubriken för den första kolumnen (**namn**) eller markera alla kryss rutor för alla användare i listan för att markera alla användare. Du kan se status för Inbjudnings **tillståndet** i den här listan.  I följande bild har Inbjudnings läget för alla studenter angetts till inbjudan har **inte skickats**. 

    ![Välj studenter](../media/tutorial-setup-classroom-lab/select-students.png)
1. Välj **e-postikonen (kuvertet)** på en av raderna (eller) Välj **Skicka inbjudan** i verktygsfältet. Du kan också Hovra musen över ett elev namn i listan för att se e-postikonen. 

    ![Skicka registrerings länk via e-post](../media/tutorial-setup-classroom-lab/send-email.png)
4. Följ dessa steg på sidan **Skicka registrerings länk via e-post** : 
    1. Skriv ett **valfritt meddelande** som du vill skicka till eleverna. E-postmeddelandet innehåller automatiskt registrerings länken. 
    2. På sidan **Skicka registrerings länk via e-post** väljer du **Skicka**. Du ser status för inbjudan att ändra för att **Skicka inbjudan** och sedan för att **Skicka**inbjudan. 
        
        ![Inbjudningar har skickats](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett klassrumslabb och konfigurerade labbet. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

