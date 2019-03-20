---
title: Aktivera Konsekvens miljöer i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar en miljö med flera virtuella datorer i en mall för virtuell dator i ett klassrumslabb för Azure Lab Services.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190595"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Skapa en miljö med flera virtuella datorer i en mall för virtuell dator av ett klassrumslabb
För närvarande kan Azure Lab Services du konfigurera en mall för virtuell dator i ett labb och göra en enda kopia som är tillgängliga för var och en av dina användare. Men om du är en lärare som undervisar en IT-klass på hur du konfigurerar brandväggar eller servrar kan du behöva ge var och en av dina studenter med en miljö där flera virtuella datorer kan kommunicera med varandra via ett nätverk.

Kapslad virtualisering kan du skapa en miljö med flera virtuella datorer i ett labb mall för virtuell dator. Publicera mallen ger varje användare i labbet med en virtuell dator som har konfigurerats med flera virtuella datorer i den.

## <a name="what-is-nested-virtualization"></a>Vad är kapslad virtualisering?
Kapslad virtualisering kan du skapa virtuella datorer i en virtuell dator. Kapslad virtualisering görs via Hyper-V och är bara tillgängligt på Windows virtuella datorer.

Mer information om kapslad virtualisering finns i följande artiklar:

- [Kapslad virtualisering i Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Hur du aktiverar kapslad virtualisering i en Azure VM](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Använd kapslad virtualisering i Azure Lab Services
Det finns några viktiga steg:

1. Skapa en **stora** storlek **Windows** mall dator för labbet. 
2. Ansluta till den och [aktivera kapslad virtualisering](../../virtual-machines/windows/nested-virtualization.md).


Följande procedur ger detaljerade anvisningar: 

1. Skapa ett labbkonto om du inte redan har en. Anvisningar finns i [självstudien: Konfigurera ett labbkonto med Azure Lab Services](tutorial-setup-lab-account.md).
2. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
3. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
4. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Ange maximalt **antal virtuella datorer** i labbet. Du kan öka eller minska antalet virtuella datorer när du har skapat labbet eller i ett befintligt labb. Mer information finns i avsnittet om att [uppdatera antalet virtuella datorer i ett labb](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. På sidan för att **välja specifikationer för virtuell dator** utför du följande steg:
    1. Välj **stor** för storleken på virtuella datorer (VM) som ska skapas i labbet. För närvarande stöder endast stora kapslad virtualisering.
    2. Välj en avbildning av virtuell dator som är en **Windows-avbildning**. Kapslad virtualisering är endast tillgänglig på Windows-datorer. 
    3. Välj **Nästa**.

        ![Ange VM-specifikationer](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
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
8. På den **konfigurera mallen** väljer **Connect** att ansluta till mallen för virtuell dator för att konfigurera kapslad virtualisering. Du kan också konfigurera senare när du har slutfört stegen i den här guiden. 
9. Ställ in kapslad virtualisering på den virtuella datorn mall och konfigurera ett virtuellt nätverk med flera virtuella datorer. Detaljerade stegvisa anvisningar finns i [aktivera kapslad virtualisering i en Azure VM](../../virtual-machines/windows/nested-virtualization.md). Här är en snabb sammanfattning av stegen: 
    1. Aktivera Hyper-V-funktionen i mallen för virtuell dator.
    2. Konfigurera ett internt virtuellt nätverk med Internetanslutning för kapslade virtuella datorer
    3. Skapa virtuella datorer med Hyper-V Manager
    4. Tilldela en IP-adress till de virtuella datorerna
10. Välj **Nästa** på mallsidan. 
11. På sidan **Publicera mallen** utför du någon av följande åtgärder. 
    1. För att publicera mallen omedelbart väljer du **Publicera**.  

        > [!WARNING]
        > När du väl har publicerat kan du inte ångra publiceringen. 
    2. Om du vill publicera senare väljer **Spara till senare**. Du kan publicera mallen för den virtuella datorn när guiden har slutförts. Mer information om hur du konfigurerar och publicerar när guiden slutförts finns i avsnittet [Publicera mallen](how-to-create-manage-template.md#publish-the-template-vm) i artikeln om att [hantera klassrumslabb](how-to-manage-classroom-labs.md).

        ![Publicera mall](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Du ser **förloppet för publiceringen**  av mallen. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Du ser följande sida när mallen har publicerats. Välj **Done** (Klar).

    ![Publicera mall – lyckades](../media/tutorial-setup-classroom-lab/publish-success.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Nästa steg

Nu kan hämtar varje användare en enskild virtuell dator som innehåller en miljö med flera virtuella datorer i den. Om du vill lära dig mer om att lägga till användare i labbet och skicka registreringslänk till dem, finns i följande artikel: [Lägga till användare i labbet](tutorial-setup-classroom-lab.md#add-users-to-the-lab).