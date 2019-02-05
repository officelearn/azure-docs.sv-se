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
ms.date: 01/16/2019
ms.author: spelluru
ms.openlocfilehash: 3b425af972b0983db076ab103a33c57f7a127210
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095761"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudier: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Konfigurera klassrumslabbet
> * Skicka en registreringslänk till eleverna

## <a name="prerequisites"></a>Nödvändiga komponenter
För att konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i någon av dessa roller i labbkontot: Ägare, Labbskaparen eller Deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen.

En labbägare kan lägga till andra användare i rollen **Labbskaparen**. Labbägare lägger exempelvis till universitetslärare till rollen Labbskaparen. Sedan skapar universitetsläraren labbar med virtuella datorer för deras klasser. Elever använder registreringslänken som de får från universitetslärarna för att registrera sig i labbet. När de har registrerats kan de använda virtuella datorer i labbarna för att utföra klass- och hemarbete. Detaljerade anvisningar för att lägga till användare till rollen Labbskaparen finns i [Lägga till en användare till rollen Labbskaparen](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Ange det högsta **antalet användare** som är tillåtet i labbet. 
    6. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. På sidan för att **välja specifikationer för virtuell dator** utför du följande steg:
    1. Välj en **storlek** för virtuella datorer (VM) som skapas i labbet. 
    2. Välj den **region** där du vill att de virtuella datorerna ska skapas. 
    3. Välj den **VM-avbildning**  som ska användas för att skapa virtuella datorer i labbet. 
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
8. Följande steg är valfria i den här självstudien: 
    1. Starta mallen för den virtuella datorn genom att välja **Starta**.
    2. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. 
    3. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    4. **Stoppa** den virtuella datorn.  
    5. Ange en **beskrivning** för mallen

        ![Nästa på sidan Konfigurera mall](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Välj **Nästa** på mallsidan. 
10. På sidan **Publicera mallen** utför du någon av följande åtgärder. 
    1. Om du vill publicera mallen omedelbart markerar du kryssrutan för *I understand I can't modify the template after publishing (Jag förstår att jag inte kan ändra mallen efter publicering). Den här processen kan bara göras en gång och den kan ta upp till en timme*. Välj sedan **Publicera**.  

        > [!WARNING]
        > När du väl har publicerat kan du inte ångra publiceringen. 
    2. Om du vill publicera senare väljer **Spara till senare**. Du kan publicera mallen för den virtuella datorn när guiden har slutförts. Mer information om hur du konfigurerar och publicerar när guiden slutförts finns i avsnittet [Publicera mallen](how-to-create-manage-template.md#publish-the-template-vm) i artikeln om att [hantera klassrumslabb](how-to-manage-classroom-labs.md).

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
3. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)


## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna

1. Växla till **användarvyn** om du inte är på sidan redan. 
2. Välj **Hämta registreringslänk** i verktygsfältet.
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp.

    ![Registreringslänk](../media/tutorial-setup-classroom-lab/registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du **Stäng**. 
2. Dela registreringslänken med en student så att studenten kan registrera sig för klassen.

## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett klassrumslabb och konfigurerade labbet. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

