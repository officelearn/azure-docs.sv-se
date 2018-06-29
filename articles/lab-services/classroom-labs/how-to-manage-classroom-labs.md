---
title: Hantera klassrum labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett klassrum labb kan visa alla de klassrum övningarna, fler registreringen länka med lab-användare eller ta bort ett labb.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: f8cf5a46e1d1e3242fd46900b56f7d8cf15d3fc5
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082532"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Hantera klassrum labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och konfigurerar ett klassrum labb, visa alla klassrum labs eller ta bort ett klassrum labb.

## <a name="prerequisites"></a>Förutsättningar
Om du vill konfigurera ett klassrum labb i ett labb-konto måste du vara medlem i den **Lab Creator** roll i labb-konto. En labbägaren kan lägga till en användare rollen Lab Creator med hjälp av stegen i följande artikel: [lägga till en användare i rollen Lab Creator](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com).
2. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på klassrumslabbet. 
    2. Välj **storlek** för den virtuella dator som du tänker använda i klassrummet.
    3. Välj **avbildning** som ska användas för att skapa den virtuella datorn.
    4. Ange den **standardautentiseringsuppgifter** ska användas för att logga in på virtuella datorer i labbet.
    7. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Du ser den **instrumentpanelen** för labbet. 
    
    ![Klassrum lab instrumentpanelen](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurera användningsprincip

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurera mallen
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Publicerar du mallen för att instanser av VM-mallen som är tillgängliga för användarna i labbet.  

### <a name="set-template-title-and-description"></a>Ange mall rubrik och beskrivning
1. I avsnittet **Mall** väljer du **Redigera** (pennikonen) för mallen. 
2. I fönstret **Användarvy** anger du en **rubrik** för mallen.
3. Ange en **beskrivning** för mallen.
4. Välj **Spara**.

    ![Beskrivning av klassrumslabb](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Konfigurera VM-mallen
 Du ansluter till VM-mallen och installera program som krävs på den innan du gör den tillgänglig för dina studenter. 

1. Vänta tills den virtuella datorn i mallen är klar. När det är klart, den **starta** knappen ska aktiveras. Om du vill starta den virtuella datorn, Välj **starta**.

    ![Starta VM-mallen](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. För att ansluta till den virtuella datorn, Välj **Anslut**, och följ instruktionerna. 

    ![Ansluta till VM-mallen](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Installera all programvara som krävs för studenter att göra labbet (till exempel Visual Studio, Azure Lagringsutforskaren osv.). 
2. Koppla från (Stäng fjärrskrivbordssessionen) i VM-mallen. 
3. **Stoppa** VM-mallen genom att välja **stoppa**. 

    ![Stoppa VM-mallen](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>Publicera mallen 
När du publicerar en mall, skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. 

1. Välj **publicera** i den **mallen** avsnitt. 

    ![Publicera VM-mallen](../media/tutorial-setup-classroom-lab/public-access.png)
1. I den **publicera** väljer den **publicerade** alternativet. 
2. Nu väljer den **publicera** knappen. Den här processen kan ta lite tid beroende på hur många virtuella datorer skapas, vilket är samma som antalet användare som tillåts i labbet.
    
    > [!IMPORTANT]
    > Det går inte att avpublicera när en mall har publicerats. 
4. Växla till den **virtuella datorer** sidan och bekräfta att du ser att virtuella datorer som finns i **otilldelade** tillstånd. Dessa virtuella datorer har inte tilldelats studenter ännu. 

    ![Virtuella datorer](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Vänta tills de virtuella datorerna har skapats. De måste vara i **stoppad** tillstånd. Du kan starta en student VM, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem i den här sidan eller låt eleverna starta de virtuella datorerna. 

    ![Virtuella datorer i ett stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna

1. Växla till den **instrumentpanelen** vyn. 
2. Välj panelen **Användarregistrering**.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. På den **användarregistrering** dialogrutan **Stäng**. 

## <a name="view-all-classroom-labs"></a>Visa alla klassrum labs
1. Gå till [Azure Lab Services-portalen](https://labs.azure.com).
2. Bekräfta att du ser alla labs i det valda labb-kontot. 

    ![Alla labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Använd den nedrullningsbara listrutan överst för att välja ett annat labb-konto. Du kan se labs i det valda labb-kontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrum labb
1. Välj tre punkter (...) i hörnet på panelen för labbet. 

    ![Välja labbet](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Välj **Ta bort**. 

    ![Knappen Ta bort](../media/how-to-manage-classroom-labs/delete-button.png)
3. På den **ta bort lab** dialogrutan **ta bort**. 

    ![Ta bort dialogruta](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Hantera virtuella datorer för studenter
När studenter registrera med Azure Lab tjänster som använder registreringen länka under förutsättning, visas de virtuella datorerna har tilldelats till studenter den **virtuella datorer** fliken. 

![Virtuella datorer som tilldelats studenter](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Du kan göra följande på en student VM: 

- Stoppa en virtuell dator om den virtuella datorn körs. 
- Starta en virtuell dator om den virtuella datorn har stoppats. 
- Anslut till den virtuella datorn. 
- Ta bort den virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](how-to-manage-classroom-labs.md)
- [Konfigurera ett labb](../tutorial-create-custom-lab.md)
