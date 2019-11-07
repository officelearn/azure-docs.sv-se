---
title: Hantera en mall för ett klass rums labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och hanterar en labb-mall för klass rum i Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: fd3396a98359aef966cd8bb2f984e556fc6abcc7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584806"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Skapa och hantera en klass rums mall i Azure Lab Services
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan publicerar du mallen för att skapa instanser av den virtuella mallen som är tillgänglig för dina labb användare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen.

Den här artikeln beskriver hur du skapar och hanterar en mall för virtuella datorer i ett klass rum labb med Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publicera en mall när du skapar ett klass rums labb
Information om hur du publicerar en mall när du skapar ett klass rums labb finns i [skapa ett klass rums labb](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Ange eller uppdatera mallens rubrik och beskrivning
Använd följande steg för att ange titel och beskrivning för första gången och uppdatera dem senare. 

1. Ange den nya **rubriken** för labbet på sidan **mall** .  
2. Ange den nya **beskrivningen** för mallen. När du flyttar fokus från text rutan sparas den automatiskt. 

    ![Mallens namn och beskrivning](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Uppdatera en mall för virtuell dator
Använd följande steg för att uppdatera en mall-VM.  

1. På sidan **mall** väljer du **Anpassa mall** i verktygsfältet. 
2. I dialog rutan **Anpassa mall** väljer du **Fortsätt**. När du har startat mallen och gjort ändringar kommer den inte längre att ha samma konfiguration som de virtuella datorer som senast publicerades till användarna. Ändringar i mallen visas inte på användarnas befintliga virtuella datorer förrän du har publicerat dem igen.

    ![Dialog rutan Anpassa](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Vänta tills mallen VM startas och välj sedan **Anslut till mall** i verktygsfältet för att ansluta till mallen VM och följ instruktionerna. Om det är en Windows-dator visas ett alternativ för att ladda ned RDP-filen. 

    ![Ansluta till den virtuella malldatorn](../media/how-to-create-manage-template/connect-template-vm.png)
1. Installera all programvara som krävs för studenterna ska kunna utföra labbet (till exempel Visual Studio, Azure Storage Explorer osv.). 
2. Koppla från (stäng fjärrskrivbordssessionen) den virtuella malldatorn. 
3. **Stoppa** mallen VM genom att välja **stoppa mall**. 
4. Följ stegen i nästa avsnitt för att **publicera** den uppdaterade mallen VM. 

## <a name="publish-the-template-vm"></a>Publicera den virtuella malldatorn  
Om du inte publicerar mallen när du skapar labbet kan du publicera den senare. Innan du publicerar kanske du vill ansluta till mallen VM och uppdatera den med valfri program vara. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är antalet virtuella datorer som du angav när du publicerade det för första gången eller vad du angav på sidan för den virtuella datorn. Alla virtuella datorer har samma konfiguration som mallen. 

1. På sidan **mall** väljer du **publicera** i verktygsfältet. 
1. I rutan **publicera mallens** meddelande granskar du meddelandet och väljer **publicera**. Den här processen kan ta lite tid beroende på hur många virtuella datorer som skapas.

    ![Knappen Publicera](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > När en mall har publicerats kan publiceringen inte tas bort. Du kan publicera mallen på om. 
4. Du kan se status för publicerings processen på mallsidan. Vänta tills status för mallen har ändrats till **publicerad**. 

    ![Publicerings status](../media/how-to-create-manage-template/publish-status.png)
1. Växla till sidan **Virtuella datorer** och bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. Vänta tills de virtuella datorerna har skapats. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta dina studenter starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)
