---
title: Hantera klassrumslabb i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du skapar och konfigurerar ett klassrumslabb, visar alla klassrumslabb, delar registreringslänken med en labbanvändare eller tar bort ett labb.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: cc937589b2fc4f394b44cf6890a352d770751d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502035"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Hantera klassrumslabb i Azure Lab Services 
I den här artikeln beskrivs hur du skapar och tar bort ett klassrumslabb. Den visar också hur du visar alla klassrumslabb i ett labbkonto. 

## <a name="prerequisites"></a>Krav
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i den här rollen. Labbägaren kan lägga till andra användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägg till en användare till rollen Lab Creator).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Internet Explorer 11 stöds inte ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Välj eller ange ett **användar-ID** som är medlem i rollen **Labbskapare** i labbkontot och ange lösenord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Välj **Nytt labb**. 
    
    ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Välj **storleken på de virtuella datorer** du behöver för klassen. En lista över tillgängliga storlekar finns i avsnittet [VM-storlekar.](#vm-sizes) 
    3. Välj den **avbildning för virtuella datorer** som du vill använda för klassrumslabbet. Om du väljer en Linux-avbildning visas ett alternativ för att aktivera fjärrskrivbordsanslutning för den. Mer information finns i [Aktivera fjärrskrivbordsanslutning för Linux](how-to-enable-remote-desktop-linux.md).
    4. Granska det **totala priset per timme** som visas på sidan. 
    6. Välj **Spara**.

        ![Nytt labbfönster](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Du ser ett alternativ för att välja en plats för ditt labb om labbkontot har konfigurerats så att [labbskaparen kan välja labbplatsalternativ.](allow-lab-creator-pick-lab-location.md) 
4. På sidan **Autentiseringsuppgifter för virtuella** datorer anger du standardreferenser för alla virtuella datorer i labbet.
    1. Ange **namnet på användaren** för alla virtuella datorer i labbet.
    2. Ange **lösenordet** för användaren. 

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. Inaktivera **Använd samma lösenord för alla virtuella datorer** om du vill att eleverna ska ange sina egna lösenord. Det här steget är **valfritt**. 

        En lärare kan välja att använda samma lösenord för alla virtuella datorer i labbet, eller tillåta elever att ange lösenord för sina virtuella datorer. Som standard är den här inställningen aktiverad för alla Windows- och Linux-avbildningar utom Ubuntu. När du väljer **Ubuntu** VM inaktiveras den här inställningen, så eleverna uppmanas att ange ett lösenord när de loggar in för första gången.  

        ![Nytt labbfönster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Välj sedan **Nästa** på **autentiseringssidan för den virtuella datorn.** 
5. Gör följande på sidan **Labbprinciper:**
    1. Ange antalet tilldelade timmar för varje användare (**kvot för varje användare)** utanför den schemalagda tiden för labbet. 
    2. För alternativet **Automatisk avstängning av virtuella datorer** anger du om du vill att den virtuella datorn ska stängas av automatiskt när användaren kopplar från. Du kan också ange hur länge den virtuella datorn ska vänta på att användaren ska återansluta innan den stängs av automatiskt.. Mer information finns i [Aktivera automatisk avstängning av virtuella datorer vid frånkoppling](how-to-enable-shutdown-disconnect.md).
    3. Välj sedan **Slutför**. 

        ![Kvot för varje användare](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Du bör se följande skärm som visar status för skapandet av den virtuella mallen. Det tar upp till 20 minuter att skapa mallen i labbet. 

    ![Status för skapandet av den virtuella mallen](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Gör följande steg på **sidan Mall:** De här stegen är **valfria** för självstudien.

    2. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall väljer du om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    1. Välj **Återställ lösenord** om du vill återställa lösenordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
10. På **sidan Mall** väljer du **Publicera** i verktygsfältet. 

    ![Knappen Publicera mall](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
8. På sidan **Publicera mall** anger du antalet virtuella datorer som du vill skapa i labbet och väljer sedan **Publicera**. 

    ![Publicera mall - antal virtuella datorer](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Växla till **poolsidan för virtuella datorer** genom att välja virtuella datorer på den vänstra menyn eller genom att välja panelen Virtuella datorer. Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Du gör följande uppgifter på den här sidan (gör inte dessa steg för självstudien. Dessa steg är endast till för din information.): 
    
    1. Om du vill ändra labbkapaciteten (antal virtuella datorer i labbet) väljer du **Labbkapacitet** i verktygsfältet.
    2. Om du vill starta alla virtuella datorer samtidigt väljer du **Starta alla** i verktygsfältet. 
    3. Om du vill starta en viss virtuell dator markerar du nedpilen i **status**och väljer sedan **Start**. Du kan också starta en virtuell dator genom att välja en virtuell dator i den första kolumnen och sedan genom att välja **Start** i verktygsfältet.                

### <a name="vm-sizes"></a>VM-storlekar  

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten | 2 | 3,5 GB | Den här storleken passar bäst för kommandoraden, öppna webbläsare, låg trafik webbservrar, små till medelstora databaser. |
| Medel | 4 | 7 GB | Den här storleken passar bäst för relationsdatabaser, cachelagring i minnet och analys | 
| Medium (kapslad virtualisering) | 4 | 16 GB | Den här storleken passar bäst för relationsdatabaser, cachelagring i minnet och analyser. Den här storleken stöder också kapslad virtualisering. <p>Den här storleken kan användas i scenarier där varje deltagare behöver flera virtuella datorer. Lärare kan använda kapslad virtualisering för att ställa in några kapslade virtuella datorer i liten storlek inuti den virtuella datorn. </p> |
| Stor | 8 | 32 GB | Den här storleken passar bäst för program som behöver snabbare processorer, bättre lokal diskprestanda, stora databaser, stora minnescacheminnen. Den här storleken stöder även kapslad virtualisering |  
| Liten GPU (visualisering) | 6 | 56 GB | Den här storleken passar bäst för fjärrvisualisering, streaming, spel, kodning med ramverk som OpenGL och DirectX. | 
| Liten GPU (beräkning) | 6 | 56 GB | Den här storleken passar bäst för beräkningsintensiva och nätverksintensiva program som artificiell intelligens och djupinlärningsprogram. | 
| Medium GPU (visualisering) | 12 | 112 GB | Den här storleken passar bäst för fjärrvisualisering, streaming, spel, kodning med ramverk som OpenGL och DirectX. | 

> [!NOTE]
> Azure Lab Services installerar och konfigurerar automatiskt nödvändiga GPU-drivrutiner för dig när du skapar ett labb med GPU-avbildningar.  

## <a name="view-all-classroom-labs"></a>Visa alla klassrumslabb
1. Navigera till [Azure Lab Services-portalen](https://labs.azure.com).
2. Välj **Logga in**. Välj eller ange ett **användar-ID** som är medlem i rollen **Labbskapare** i labbkontot och ange lösenord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Bekräfta att du ser alla labb i det valda labbkontot. På labbets panel ser du antalet virtuella datorer i labbet och kvoten för varje användare (utanför den schemalagda tiden).

    ![Alla labb](../media/how-to-manage-classroom-labs/all-labs.png)
3. Använd listrutan högst upp för att välja ett annat labbkonto. Du ser labb i det valda labbkontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrumslabb
1. På panelen för labbet väljer du tre punkter (...) i hörnet och väljer sedan **Ta bort**. 

    ![Knappen Ta bort](../media/how-to-manage-classroom-labs/delete-button.png)
3. I dialogrutan **Ta bort labb** väljer du Ta **bort** för att fortsätta med borttagningen. 

## <a name="switch-to-another-classroom-lab"></a>Växla till ett annat klassrumslabb
Om du vill växla till ett annat klassrumslabb från den aktuella väljer du listrutan med labb i labbkontot högst upp.

![Välj labbet i listrutan högst upp](../media/how-to-manage-classroom-labs/switch-lab.png)

Du kan också skapa ett nytt labb med det **nya labbet** i den här listrutan. 

> [!NOTE]
> Du kan också använda Az.LabServices PowerShell-modulen (förhandsversion) för att hantera labb. Mer information finns [på Az.LabServices hemsida på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Om du vill växla till ett annat labbkonto väljer du listrutan bredvid labbkontot och väljer det andra labbkontot. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labbanvändare får du tillgång till klassrumslabb](how-to-use-classroom-lab.md)

