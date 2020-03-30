---
title: Hantera en mall för ett klassrumslabb i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du skapar och hanterar en labbmall i Klassrummet i Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539046"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Skapa och hantera en klassrumsmall i Azure Lab Services
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan publicerar du mallen för att göra instanser av mallen VM tillgängliga för dina labbanvändare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen.

I den här artikeln beskrivs hur du skapar och hanterar en virtuell malldator i ett klassrumslabb med Azure Lab Services. 

## <a name="set-or-update-template-title-and-description"></a>Ange eller uppdatera mallrubrik och beskrivning
Följ följande steg för att ange titel och beskrivning för första gången och uppdatera dem senare. 

1. På **sidan Mall** anger du den nya **titeln** för labbet.  
2. Ange den nya **beskrivningen** för mallen. När du flyttar fokus från textrutan sparas det automatiskt. 

    ![Mallnamn och beskrivning](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Uppdatera en virtuell mall
Följ följande steg för att uppdatera en virtuell mall.Use the following steps to update a template VM.  

1. Vänta tills mallen VM startas och välj sedan **Anslut till mall** i verktygsfältet för att ansluta till mallen VM och följ instruktionerna. Om det är en Windows-dator visas ett alternativ för att hämta RDP-filen. 
1. När du har anslutit till mallen och gjort ändringar har den inte längre samma inställningar som de virtuella datorerna som senast publicerades för användarna. Malländringar visas inte på användarnas befintliga virtuella datorer förrän du har publicerat igen.

    ![Ansluta till den virtuella malldatorn](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installera all programvara som krävs för studenterna ska kunna utföra labbet (till exempel Visual Studio, Azure Storage Explorer osv.). 
1. Koppla från (stäng fjärrskrivbordssessionen) den virtuella malldatorn. 
1. **Stoppa** mallen VM genom att välja **Stoppmall**. 
1. Följ stegen i nästa avsnitt för att **publicera** den uppdaterade mallen VM. 

## <a name="publish-the-template-vm"></a>Publicera den virtuella malldatorn  
I det här steget publicerar du mallen VM. När du publicerar mallen VM skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Alla virtuella datorer har samma konfiguration som mallen.


1. Välj **Publicera** i **verktygsfältet** på sidan Mall. 

    ![Knappen Publicera mall](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
2. På sidan **Publicera mall** anger du antalet virtuella datorer som du vill skapa i labbet och väljer sedan **Publicera**. 

    ![Publicera mall - antal virtuella datorer](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Vänta tills publiceringen är klar och växla sedan till **poolsidan för virtuella datorer** genom att välja **virtuella datorer** på den vänstra menyn eller genom att välja panelen **Virtuella datorer.** Bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labbanvändare får du tillgång till klassrumslabb](how-to-use-classroom-lab.md)
