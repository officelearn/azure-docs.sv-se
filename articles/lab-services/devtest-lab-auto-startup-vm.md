---
title: Konfigurera inställningar för automatisk start för en virtuell dator i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du konfigurerar inställningar för automatisk start för virtuella datorer i ett labb. Med den här inställningen kan virtuella datorer i labbet startas automatiskt enligt ett schema.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807919"
---
# <a name="auto-startup-lab-virtual-machines"></a>Virtuella datorer för automatiskt startlabb  
Med Azure DevTest Labs kan du konfigurera virtuella datorer i labbet så att de startas och stängs av automatiskt baserat på ett schema. Information om hur du konfigurerar inställningar för automatisk nedskalning finns i [Hantera principer för automatisk nedskrivning för ett labb i Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Till skillnad från automatisk nedskalning, där alla virtuella datorer inkluderas när principen är aktiverad, kräver principen för automatisk start en labbanvändare att uttryckligen välja en virtuell dator och välja det här schemat. På så sätt kommer du inte lätt att stöta på situationen där oönskade virtuella datorer startas av misstag automatiskt och orsakar oväntade utgifter.

Den här artikeln visar hur du konfigurerar principen för automatisk start för ett labb.

## <a name="configure-autostart-settings-for-a-lab"></a>Konfigurera inställningar för automatisk start för ett labb 
1. Navigera till startsidan för ditt labb. 
2. Välj **Konfiguration och principer** på den vänstra menyn. 

    ![Menyn Konfiguration och principer](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Gör följande på sidan **Konfiguration och principer:**
    
    1. Välj **På** för **Tillåt att virtuella datorer schemaläggs för automatisk start för** att aktivera funktionen för automatisk start för det här labbet. 
    2. Välj en starttid (till exempel: 08:00:00) för **fältet Schemastart.** 
    3. Välj en **tidszon** som ska användas. 
    4. Välj **veckodagar** då virtuella datorer måste startas automatiskt. 
    5. Välj sedan **Spara** i verktygsfältet för att spara inställningarna. 

        ![Inställningar för automatisk start](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Den här principen gäller inte automatiskt automatisk start på några virtuella datorer i labbet. Om du vill **välja** enskilda virtuella datorer går du till sidan för den virtuella datorn och aktiverar **automatisk start** för den virtuella datorn.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Aktivera automatisk start för en virtuell dator i labbet
Följande procedur ger dig steg för att välja en virtuell dator i autostart-principen för labbet. 

1. På startsidan för ditt labb väljer du den **virtuella datorn** i listan Mina **virtuella datorer.** 

    ![Menyn Konfiguration och principer](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. På sidan **Virtuell dator** väljer du Starta **automatiskt på** den vänstra menyn eller i listan **Scheman.** 

    ![Välj autostart-menyn](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. På sidan **Autostart** väljer du **På** för alternativet Tillåt att den **här virtuella datorn schemaläggs för automatisk start.**

    ![Aktivera automatisk start för den virtuella datorn](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Välj sedan **Spara** i verktygsfältet för att spara inställningen. 


## <a name="next-steps"></a>Nästa steg
Mer information om principen för automatisk nedskrivning av konfiguration för ett labb finns i [Hantera principer för automatisk nedskrivning för ett labb i Azure DevTest Labs](devtest-lab-auto-shutdown.md)
