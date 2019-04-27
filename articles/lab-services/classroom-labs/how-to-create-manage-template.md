---
title: Hantera en mall med ett klassrumslabb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och hanterar en klassrum labbmall i Azure Lab Services.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: b287a67c470cc1697065838e52916c285a2233a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704466"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Skapa och hantera en klassrum-mall i Azure Lab Services
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan kan publicera du mallen för att tillgängliggöra instanser av VM-mallen för dina labbanvändare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen.

Den här artikeln beskriver hur du skapar och hanterar en mall för virtuell dator i ett klassrumslabb för Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publicera en mall när du skapar ett klassrumslabb
Först, kan du konfigurera och publicera en mall när du skapar ett klassrumslabb.

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
    2. Om du vill publicera senare väljer **Spara till senare**. Du kan publicera mallen för den virtuella datorn när guiden har slutförts. Mer information om hur du konfigurerar och publicerar när guiden slutförts, finns mer information om hur du konfigurerar och publicerar när guiden slutförts, finns i mallen i avsnittet Publicera den [hantera klassrum labs](how-to-manage-classroom-labs.md) artikeln.

        ![Publicera mall](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Du ser **förloppet för publiceringen**  av mallen. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Du ser följande sida när mallen har publicerats. Välj **Done** (Klar).

    ![Publicera mall – lyckades](../media/tutorial-setup-classroom-lab/publish-success.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Ange eller uppdatera mallen rubrik och beskrivning
Använd följande steg för att ange rubriken och beskrivningen för första gången och uppdatera dem senare. 

1. I den **mall** avsnittet musen över **namn** för mallen eller **beskrivning** för mallen, och markera den. 
2. Ange den **nytt namn** eller **ny beskrivning** för mallen och tryck på **RETUR**.

    ![Mallens namn och beskrivning](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Ställ in eller uppdatera en mall för virtuell dator
 Du ansluter till den virtuella malldatorn och installerar program som krävs på den innan du gör den tillgänglig för dina studenter. Använd följande steg för att ställa in en mall för virtuell dator för första gången eller för att uppdatera den virtuella datorn. 

1. Vänta tills den virtuella malldatorn är klar. När den är klar bör knappen **Starta** aktiveras. För att starta den virtuella datorn väljer du **Starta**.

    ![Starta den virtuella malldatorn](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Granska varningen och välj **starta**. 

    ![Starta mall - varning](../media/how-to-create-manage-template/start-template-warning.png)
2. Du ser status på labbet panelen i den **mall** avsnittet.

    ![Starta mall - status](../media/how-to-create-manage-template/template-start-status.png)
1. När den har startats för att ansluta till den virtuella datorn, Välj **Connect**, och följ anvisningarna. 

    ![Ansluta till eller stoppa VM-mallen](../media/how-to-create-manage-template/connect-stop-vm.png)
1. Installera all programvara som krävs för studenterna ska kunna utföra labbet (till exempel Visual Studio, Azure Storage Explorer osv.). 
2. Koppla från (stäng fjärrskrivbordssessionen) den virtuella malldatorn. 
3. **Stoppa** den virtuella malldatorn genom att välja **Stoppa**. 

## <a name="publish-the-template-vm"></a>Publicera den virtuella malldatorn  
Om du inte publicerar mallen när du har skapat labbet kan publicera du den senare. Innan du publicerar, kanske du vill ansluta till VM-mallen och uppdatera den med programvara. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. 

1. Välj **Publicera** i avsnittet **Mall**. 

    ![Publicera den virtuella malldatorn](../media/tutorial-setup-classroom-lab/public-access.png)
1. På den **publicerar du mallen** meddelanderutan, granska meddelandet och välj **publicera**. Den här processen kan ta lite tid beroende på hur många virtuella datorer skapas.
    
    > [!IMPORTANT]
    > När en mall har publicerats kan publiceringen inte tas bort. Du kan publicera om mallen dock. 
4. Vänta tills statusen för mallen för att ändra till **publicerad**. 

    ![Publiceringsstatus](../media/how-to-create-manage-template/publish-status.png)
1. Växla till sidan **Virtuella datorer** och bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. Vänta tills de virtuella datorerna har skapats. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta dina studenter starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Publicera om mallen 
När du har publicerat en mall kan du fortfarande ansluta till VM-mallen, uppdatera den och sedan publicera den igen. När du publicerar en mall för virtuell dator, återskapas alla användare VMs avbrytas och de baserat på den uppdaterade mallen. 

1. På instrumentpanelsidan över ditt labb väljer **publicera** i mallavsnittet. 

    ![Publicera knappen](../media/how-to-create-manage-template/republish-button.png)
2. På den **publicera om mallen** meddelanderutan, läser du igenom texten och väljer **publicera** att fortsätta. Annars väljer **Avbryt**. 

    ![Publicera meddelandet mall](../media/how-to-create-manage-template/republish-template-message.png)
3. Du ser status för den publicera på nytt på panelen i den **mall** avsnittet.

    ![Status för att publicera](../media/how-to-create-manage-template/republish-status-publishing.png)
4. När mallen har publicerats kan den här inställningen **publicerad**. 

    ![Publicerar om den lyckades](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)
