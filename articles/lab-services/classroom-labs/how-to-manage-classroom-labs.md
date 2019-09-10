---
title: Hantera klass rums labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett klass rums labb, visar alla klass rums labb, delar registrerings länken med en labb användare eller tar bort ett labb.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873577"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Hantera klass rums labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och tar bort ett klass rums labb. Det visar också hur du visar alla klass rums labb i ett labb konto. 

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i den här rollen. Labbägaren kan lägga till andra användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägga till en användare till rollen Lab Creator).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga**in. Välj eller ange ett **användar-ID** som är medlem i rollen **labb skapare** i labb kontot och ange lösen ord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Ange maximalt **antal virtuella datorer** i labbet. Du kan öka eller minska antalet virtuella datorer i labbet senare. 
    6. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. På sidan för att **välja specifikationer för virtuell dator** utför du följande steg:
    1. Välj en **storlek** för virtuella datorer (VM) som skapas i labbet. För närvarande tillåts **små**, **medel**, **medium (virtualisering)** , **stor**och **GPU** -storlek. Mer information finns i avsnittet [VM-storlekar](#vm-sizes) .
    1. Välj den **region** där du vill att de virtuella datorerna ska skapas. 
    1. Välj den **VM-avbildning**  som ska användas för att skapa virtuella datorer i labbet. Om du väljer en Linux-avbildning ser du ett alternativ för att aktivera anslutning till fjärr skrivbord för det. Mer information finns i [aktivera anslutning till fjärr skrivbord för Linux](how-to-enable-remote-desktop-linux.md).
    1. Välj **Nästa**.

        ![Ange VM-specifikationer](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. På sidan **Ange autentiseringsuppgifter** anger du standardautentiseringsuppgifter för alla virtuella datorer i labbet. 
    1. Ange **namnet på användaren** för alla virtuella datorer i labbet.
    2. Ange **lösenordet** för användaren. 

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. Inaktivera alternativet **Använd samma lösen ord för alla virtuella datorer** om du vill att eleverna ska ange sina egna lösen ord. Det här steget är **valfritt**. 

        En lärare kan välja att använda samma lösen ord för alla virtuella datorer i labbet eller tillåta studenter att ange lösen ord för sina virtuella datorer. Som standard är den här inställningen aktive rad för alla Windows-och Linux-avbildningar utom Ubuntu. När du väljer **Ubuntu** VM är den här inställningen inaktive rad, så att eleverna uppmanas att ange ett lösen ord när de loggar in för första gången.
    1. Välj **Skapa**. 

        ![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. På sidan **Konfigurera mall** kan du se statusen för labbskapandeprocessen. Det tar upp till 20 minuter att skapa mallen i labbet. En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med.  

    ![Konfigurera mall](../media/tutorial-setup-classroom-lab/configure-template.png)
7. När konfigurationen av mallen har slutförts visas följande sida: 

    ![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Följande steg är valfria i den här självstudien: 
    2. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en virtuell Linux-mall kan du välja om du vill ansluta med SSH eller RDP (om RDP är aktiverat).
    1. Välj **Återställ lösen ord** för att återställa lösen ordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
9. Välj **Nästa** på mallsidan. 
10. På sidan **Publicera mallen** utför du någon av följande åtgärder. 
    1. Om du vill publicera mallen omedelbart markerar du kryssrutan för *I understand I can't modify the template after publishing (Jag förstår att jag inte kan ändra mallen efter publicering). Den här processen kan bara göras en gång och den kan ta upp till en timme*. Välj sedan **Publicera**.  Publicera mallen så att instanser av den virtuella malldatorn är tillgängliga för labbanvändarna.

        > [!WARNING]
        > När du väl har publicerat kan du inte ångra publiceringen. 
    2. Om du vill publicera senare väljer **Spara till senare**. Du kan publicera mallen för den virtuella datorn när guiden har slutförts. Information om hur du konfigurerar och publicerar när guiden har slutförts finns i avsnittet om hur du konfigurerar och publicerar när guiden har slutförts finns i avsnittet publicera mallen i artikeln [så här hanterar du klass rums labb](how-to-manage-classroom-labs.md) .

        ![Publicera mall](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Du ser **förloppet för publiceringen**  av mallen. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Du ser följande sida när mallen har publicerats. Välj **Done** (Klar).

    ![Publicera mall – lyckades](../media/tutorial-setup-classroom-lab/publish-success.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Växla till sidan **Virtuella datorer** och bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>VM-storlekar  

| Size | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten | 2 | 3,5 GB | Den här storleken passar bäst för kommando rad, öppna webbläsare, webb servrar med låg trafik, små till medel stora databaser. |
| Medel | 4 | 7 GB | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys | 
| Medium (kapslad virtualisering) | 4 | 16 GB | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys. Den här storleken stöder även kapslad virtualisering. <p>Den här storleken kan användas i scenarier där varje student behöver flera virtuella datorer. Lärare kan använda kapslad virtualisering för att konfigurera ett fåtal kapslade virtuella datorer med liten storlek inuti den virtuella datorn. </p> |
| Stor | 8 | 32 GB | Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen. Den här storleken stöder även kapslad virtualisering |  
| Liten GPU (visualisering) | 6 | 56 GB | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. | 
| Liten GPU (Compute) | 6 | 56 GB | Den här storleken passar bäst för beräknings intensiva och nätverks intensiva program som artificiell intelligens och djup inlärnings program. | 
| Medelhög GPU (visualisering) | 12 | 112 GB | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. | 

## <a name="view-all-classroom-labs"></a>Visa alla klass rum labb
1. Navigera till [Azure Lab Services Portal](https://labs.azure.com).
2. Välj **Logga**in. Välj eller ange ett **användar-ID** som är medlem i rollen **labb skapare** i labb kontot och ange lösen ord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Bekräfta att du ser alla labb i det valda labb kontot. 

    ![Alla labb](../media/how-to-manage-classroom-labs/all-labs.png)
3. Använd List rutan längst upp för att välja ett annat labb konto. Du ser labb i det valda labb kontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klass rums labb
1. I rutan för labbet väljer du tre punkter (...) i hörnet. 

    ![Välja labbet](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Välj **Ta bort**. 

    ![Ta bort knapp](../media/how-to-manage-classroom-labs/delete-button.png)
3. I dialog rutan **ta bort labb** väljer du **ta bort**. 

    ![Ta bort dialog ruta](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Växla till ett annat klass rums labb
Om du vill växla till ett annat klass rums labb från den aktuella väljer du den nedrullningsbara listan med labb i labb kontot längst upp.

![Välj labbet i list rutan längst upp](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)

