---
title: Hantera klassrum labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett klassrumslabb kan visa alla klassrum labbarna, fler registreringen länka till en lab-användare eller ta bort ett labb.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: eebdc98db5ecdf518d3b0b58e6757a2b7ecd5dd7
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043812"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Hantera klassrum labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och konfigurerar ett klassrumslabb, visa alla klassrum labb eller ta bort ett klassrumslabb.

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i den här rollen. Labbägaren kan lägga till andra användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägg till en användare till rollen Lab Creator).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange en **namn** för övningen. 
    2. Ange maximal **antalet användare som** åtkomst till labbet. 
    6. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. På den **Välj specifikationer för virtuell dator** gör du följande steg:
    1. Välj en **storlek** för virtuella datorer (VM) som skapas i labbet. 
    2. Välj den **region** som du vill att de virtuella datorerna som ska skapas. 
    3. Välj den **VM-avbildning** som används för att skapa virtuella datorer i labbet. 
    4. Välj **Nästa**.

        ![Ange VM-specifikationer](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. På den **ange autentiseringsuppgifter** anger standardautentiseringsuppgifter för alla virtuella datorer i labbet. 
    1. Ange den **användarens namn** för alla virtuella datorer i labbet.
    2. Ange den **lösenord** för användaren. 

        > [!IMPORTANT]
        > Anteckna användarnamnet och lösenordet. De kommer inte att visas igen.
    3. Välj **Skapa**. 

        ![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. På den **konfigurera mallen** kan du se statusen för labbprocessen. Skapandet av mallen i labbet tar upp till 20 minuter. 

    ![Konfigurera mall](../media/tutorial-setup-classroom-lab/configure-template.png)
7. När konfigurationen av mallen har slutförts, visas följande sida: 

    ![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Följande steg är valfria steg i den här självstudien: 
    1. Starta VM-mallen genom att välja **starta**.
    2. Anslut till VM-mallen genom att välja **Connect**. 
    3. Installera och konfigurera programvara på din mall för virtuell dator. 
    4. **Stoppa** den virtuella datorn.  
    5. Ange en **beskrivning** för mallen

        ![Nästa på konfigurationssidan för mallen](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Välj **nästa** på mallsidan. 
10. På **publicerar du mallen** gör du följande åtgärder. 
    1. Om du vill publicera mallen omedelbart, markerar du kryssrutan för *jag förstår att jag inte kan ändra mallen efter publicering. Den här processen kan bara göras en gång och kan ta upp till en timme*, och välj **publicera**.  

        > [!WARNING]
        > När du publicerar det går inte att du avpublicera. 
    2. Om du vill publicera senare väljer **spara till senare**. Du kan publicera VM-mallen när guiden har slutförts. Mer information om hur du konfigurerar och publicerar när guiden slutförts, se mer information om hur du konfigurerar och publicerar när guiden slutförts finns i [publicerar du mallen](#publish-the-template) i avsnittet den [hantera klassrum labs ](how-to-manage-classroom-labs.md) artikeln.

        ![Publicera mall](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Du ser den **förloppet för publicering** mallen. Den här processen kan ta upp till en timme. 

    ![Publicera mall - förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Du ser följande sida när mallen är har publicerats. Välj **Done** (Klar).

    ![Publicera mall – lyckades](../media/tutorial-setup-classroom-lab/publish-success.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Växla till sidan **Virtuella datorer** och bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="configure-usage-policy"></a>Konfigurera användningsprincip

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurera mallen
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Publicera mallen så att instanser av den virtuella malldatorn är tillgängliga för labbanvändarna.  

### <a name="set-template-title-and-description"></a>Ange mall rubrik och beskrivning
1. I avsnittet **Mall** väljer du **Redigera** (pennikonen) för mallen. 
2. I fönstret **Användarvy** anger du en **rubrik** för mallen.
3. Ange en **beskrivning** för mallen.
4. Välj **Spara**.

    ![Beskrivning av klassrumslabb](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Konfigurera den virtuella malldatorn
 Du ansluter till den virtuella malldatorn och installerar program som krävs på den innan du gör den tillgänglig för dina studenter. 

1. Vänta tills den virtuella malldatorn är klar. När den är klar bör knappen **Starta** aktiveras. För att starta den virtuella datorn väljer du **Starta**.

    ![Starta den virtuella malldatorn](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. För att ansluta till den virtuella datorn väljer du **Anslut** och följer instruktionerna. 

    ![Ansluta till den virtuella malldatorn](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Installera all programvara som krävs för studenterna ska kunna utföra labbet (till exempel Visual Studio, Azure Storage Explorer osv.). 
2. Koppla från (stäng fjärrskrivbordssessionen) den virtuella malldatorn. 
3. **Stoppa** den virtuella malldatorn genom att välja **Stoppa**. 

    ![Stoppa den virtuella malldatorn](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


## <a name="publish-the-template"></a>Publicera mallen 
När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. 

1. Välj **Publicera** i avsnittet **Mall**. 

    ![Publicera den virtuella malldatorn](../media/tutorial-setup-classroom-lab/public-access.png)
1. I fönstret **Publicera** väljer du alternativet **Publicerad**. 
2. Välj sedan knappen **Publicera**. Den här processen kan ta lite tid beroende på hur många virtuella datorer som skapas, vilket är samma som det antal användare som tillåts i labbet.
    
    > [!IMPORTANT]
    > När en mall har publicerats kan publiceringen inte tas bort. 
4. Växla till sidan **Virtuella datorer** och bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. 

    ![Virtuella datorer](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Vänta tills de virtuella datorerna har skapats. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna

1. Växla till vyn **Instrumentpanel**. 
2. Välj panelen **Användarregistrering**.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. I dialogrutan **Användarregistrering** väljer du **Stäng**. 

## <a name="view-all-classroom-labs"></a>Visa alla klassrum labs
1. Gå till [Azure Lab Services portal](https://labs.azure.com).
2. Kontrollera att du ser alla labs i den valda labbkonto. 

    ![Alla labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Använd den nedrullningsbara listan högst upp för att välja ett annat labbkonto. Du kan se labs i den valda labbkonto. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrumslabb
1. Välj tre punkter (...) i hörnet på panelen för övningen. 

    ![Välja labbet](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Välj **Ta bort**. 

    ![Ta bort knapp](../media/how-to-manage-classroom-labs/delete-button.png)
3. På den **Delete lab** dialogrutan **ta bort**. 

    ![Ta bort dialogrutan](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Hantera virtuella studentdatorer
När studenter register med Azure Lab Services med hjälp av registreringen länka angivna dem ska du se de virtuella datorerna har tilldelats till studenter på **virtuella datorer** fliken. 

![Virtuella datorer som tilldelats studenter](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Du kan utföra följande uppgifter på en student VM: 

- Stoppa en virtuell dator om Virtuellt datorn körs. 
- Starta en virtuell dator om den virtuella datorn har stoppats. 
- Anslut till den virtuella datorn. 
- Ta bort den virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](how-to-manage-classroom-labs.md)
- [Konfigurera ett labb](../tutorial-create-custom-lab.md)
