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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 39683c89db57dbeefd190a51415c783d012785e0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303783"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudie: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med virtuella datorer som används av eleverna i klassrummet.  

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Konfigurera klassrumslabbet
> * Skicka en registreringslänk till eleverna

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Labbägaren kan lägga till en användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägg till en användare till rollen Lab Creator).


## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com).
2. Välj **Logga in** och ange dina autentiseringsuppgifter. 
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på klassrumslabbet. 
    2. Välj **storlek** för den virtuella dator som du tänker använda i klassrummet.
    3. Välj **avbildning** som ska användas för att skapa den virtuella datorn.
    4. Ange **standardautentiseringsuppgifter** för inloggning på de virtuella datorerna i labbet. 
    7. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurera användningsprincip

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Konfigurera mallen 
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Publicera mallen som offentlig så att instanser av den virtuella malldatorn är tillgängliga för labbanvändarna. 

### <a name="set-title-and-description"></a>Ange rubrik och beskrivning
1. I avsnittet **Mall** väljer du **Redigera** (pennikonen) för mallen. 
2. I fönstret **Användarvy** anger du en **rubrik** för mallen.
3. Ange en **beskrivning** för mallen.
4. Välj **Spara**.

    ![Beskrivning av klassrumslabb](../media/tutorial-setup-classroom-lab/lab-description.png)

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

### <a name="publish-the-template"></a>Publicera mallen 
När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. 

1. Välj **Publicera** i avsnittet **Mall**. 

    ![Publicera den virtuella malldatorn](../media/tutorial-setup-classroom-lab/public-access.png)
1. I fönstret **Publicera** väljer du alternativet **Publicerad**. 
2. Välj sedan knappen **Publicera**. Den här processen kan ta lite tid beroende på hur många virtuella datorer som skapas, vilket är samma som det antal användare som tillåts i labbet.
    
    > [!IMPORTANT]
    > När en mall har gjorts offentligt tillgänglig, kan åtkomsten inte ändras till privat. 
4. Växla till sidan **Virtuella datorer** och bekräfta att du ser fem virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. 

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


## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett klassrumslabb och konfigurerade labbet. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

