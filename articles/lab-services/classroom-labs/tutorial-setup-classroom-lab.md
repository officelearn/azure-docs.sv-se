---
title: Konfigurera ett klassrumslabb med Azure Lab Services | Microsoft Docs
description: I den här självstudien använder du Azure Lab Services för att konfigurera ett klassrumslabb med virtuella datorer som används av elever i din klass.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 166ec4db2a2891d25a1e80526f8c1bd9770f9eef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77592228"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudie: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Lägga till användare i labbet
> * Ange schema för labbet
> * Skicka e-post till elever

## <a name="prerequisites"></a>Krav
I den här självstudien konfigurerar du ett labb med virtuella datorer för din klass. Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i en av dessa roller i labbkontot: Ägare, Labbskapare eller Deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen. Du kan därför använda användarkontot som du använde för att skapa ett labbkonto för att skapa ett klassrumslabb. 

Här är det vanliga arbetsflödet när du använder Azure Lab Services:

1. En labbkontoskapare lägger till andra användare i rollen **Lab Creator.** Labbkontoskapare/administratör lägger till till exempel professorer i rollen **Lab Creator** så att de kan skapa labb för sina klasser. 
2. Sedan skapar professorerna labb med virtuella datorer för sina klasser och skickar registreringslänkar till studenter i klassen. 
3. Elever använder registreringslänken som de får från universitetslärarna för att registrera sig i labbet. När de har registrerats kan de använda virtuella datorer i labbarna för att utföra klass- och hemarbete. 

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb
I det här steget skapar du ett labb för din klass i Azure. 

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Välj **Nytt labb**. 
    
    ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** för labbet och välj **Nästa**.  

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. På sidan **Autentiseringsuppgifter för virtuella** datorer anger du standardreferenser för alla virtuella datorer i labbet. Ange **namnet** och **lösenordet** för användaren och välj sedan **Nästa**.  

        ![Nytt labbfönster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. På sidan **Labbprinciper** väljer du **Slutför**. 

        ![Kvot för varje användare](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Du bör se följande skärm som visar status för skapandet av den virtuella mallen. Den här åtgärden tar upp till 20 minuter. 

    ![Status för skapandet av den virtuella mallen](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Gör följande steg på **sidan Mall:** De här stegen är **valfria** för självstudien.

    1. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall väljer du om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    3. Installera och konfigurera programvara som krävs för klassen på mallen VM. 
    4. **Stoppa** mallen VM.  

## <a name="publish-the-template-vm"></a>Publicera den virtuella malldatorn
I det här steget publicerar du mallen VM. När du publicerar mallen VM skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Alla virtuella datorer har samma konfiguration som mallen.

1. Välj **Publicera** i **verktygsfältet** på sidan Mall. 

    ![Knappen Publicera mall](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
2. På sidan **Publicera mall** anger du antalet virtuella datorer som du vill skapa i labbet och väljer sedan **Publicera**. 

    ![Publicera mall - antal virtuella datorer](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Vänta tills publiceringen är klar och växla sedan till **poolsidan för virtuella datorer** genom att välja **virtuella datorer** på den vänstra menyn eller genom att välja panelen **Virtuella datorer.** Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > När en lärare slår på en deltagare VM påverkas inte kvoten för eleven. Kvot för en användare anger antalet labbtimmar som är tillgängliga för användaren utanför den schemalagda klasstiden. Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid specifika tidpunkter. Användarkvoten (standard: 10 timmar) som du angav tidigare är den extra tid som tilldelats varje användare utanför den här schemalagda tiden. 

1. Växla till sidan Scheman och välj **Lägg till schemalagd händelse** i **verktygsfältet.** 

    ![Knappen Lägg till schema på sidan Scheman](../media/how-to-create-schedules/add-schedule-button.png)
2. Gör följande på sidan **Lägg till schemalagd händelse:**
    1. Bekräfta att **Standard** har valt **händelsetypen**.  
    2. Välj **startdatum** för klassen. 
    4. Välj den **starttid** då du vill att de virtuella datorerna ska startas.
    5. Välj den **stopptid** då de virtuella datorerna ska stängas av. 
    6. Välj **tidszon** för de start- och stopptider som du har angett. 
3. På samma **sidan Lägg till schemalagd händelse** väljer du det aktuella schemat i avsnittet **Upprepa.**  

    ![Knappen Lägg till schema på sidan Scheman](../media/how-to-create-schedules/select-current-schedule.png)
5. Gör följande i dialogrutan **Upprepa:**
    1. Bekräfta att **varje vecka** är inställt för fältet **Upprepa.** 
    2. Välj de dagar då du vill att schemat ska börja gälla. I följande exempel väljs måndag-fredag. 
    3. Välj ett **slutdatum** för schemat.
    8. Välj **Spara**. 

        ![Ange upprepat schema](../media/how-to-create-schedules/set-repeat-schedule.png)

3. På sidan **Lägg till schemalagd händelse** för **Anteckningar (valfritt)** anger du nu en beskrivning eller några anteckningar för schemat. 
4. På sidan **Lägg till schemalagd händelse** väljer du **Spara**. 

    ![Veckoschema](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navigera till startdatumet i kalendern för att kontrollera att schemat är inställt.
    
    ![Schema i kalendern](../media/how-to-create-schedules/schedule-calendar.png)

    Mer information om hur du skapar och hanterar scheman för en klass finns i [Skapa och hantera schema för klassrumslabb](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

1. Välj **Användare** på den vänstra menyn. Som standard är alternativet **Begränsa åtkomst** aktiverat. När den här inställningen är aktiverad kan inte användaren registrera med labbet även om användaren har registreringslänken, såvida inte användaren finns i listan över användare. Endast användare i listan kan registreras med labbet genom att använda registreringslänken som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också stänga av **Begränsa åtkomst**, vilket gör att användare registrerar sig med labbet så länge de har registreringslänken. 
2. Välj **Lägg till användare** i verktygsfältet och välj sedan Lägg till via **e-postadress**. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

    Du kommer att se namn på användare i listan när de har registrerats i labbet. 
    

## <a name="send-invitation-emails-to-users"></a>Skicka e-postmeddelanden med inbjudningar till användare

1. Växla till **vyn Användare** om du redan redan är på sidan och välj **Bjud in alla** i verktygsfältet. 

    ![Välj elever](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. På sidan **Skicka inbjudan via e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. E-postmeddelandet innehåller automatiskt registreringslänken. Du kan få denna registrering länk genom att välja **... (ellips)** i verktygsfältet och **registreringslänken**. 

    ![Skicka registreringslänk via e-post](../media/tutorial-setup-classroom-lab/send-email.png)
4. Du ser statusen **för inbjudan** i listan **Användare.** Statusen ska ändras till **Skicka** och sedan skickas ** &lt;på datum&gt;**. 

    Mer information om hur du lägger till elever i en klass och hanterar deras användning av labbet finns i [Så här konfigurerar du elevanvändning](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat ett labb för din klass i Azure. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

