---
title: Använd Azure DevTest Labs för utbildning | Microsoft-dokument
description: Den här artikeln innehåller detaljerade steg som du kan följa för att konfigurera ett labb för utbildning i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 8f0a930d6e3c04548ade71f6d4e3294114eb60a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759985"
---
# <a name="use-azure-devtest-labs-for-training"></a>Använd Azure DevTest Labs för utbildning
Azure DevTest Labs kan användas för att implementera många viktiga scenarier förutom dev/test. Ett av dessa scenarier är att skapa ett labb för utbildning. Med Azure DevTest Labs kan du skapa ett labb där du kan tillhandahålla anpassade mallar som varje praktikant kan använda för att skapa identiska och isolerade miljöer för utbildning. Du kan tillämpa principer för att säkerställa att utbildningsmiljöer endast är tillgängliga för varje praktikant när de behöver dem och innehåller tillräckligt med resurser , till exempel virtuella datorer, som krävs för utbildningen. Slutligen kan du enkelt dela labbet med praktikanter, som de kan komma åt med ett klick.

![Använd DevTest Labs för utbildning](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs uppfyller följande krav som krävs för att genomföra utbildning i alla virtuella miljöer: 

* Praktikanter kan inte se virtuella datorer som skapats av andra praktikanter
* Varje träningsmaskin ska vara identisk
* Praktikanter kan snabbt tillhandahålla sina utbildningsmiljöer
* Kontrollera kostnaderna genom att se till att praktikanter inte kan få fler virtuella datorer än de behöver för utbildning och även avstängning virtuella datorer när de inte använder dem
* Dela enkelt utbildningslabbet med varje praktikant
* Återanvända träningslabbet om och om igen

I den här artikeln får du lära dig mer om olika Azure DevTest Labs-funktioner som kan användas för att uppfylla de tidigare beskrivna utbildningskraven och detaljerade steg som du kan följa för att konfigurera ett labb för utbildning.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementera utbildning med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs är utgångspunkten i Azure DevTest Labs. När du har skapat ett labb kan du utföra uppgifter som att lägga till användare (praktikanter) i labbet, ange principer för att styra kostnader, definiera VM-avbildningar som kan skapa snabbt och mer.   
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig hur du skapar ett labb i Azure DevTest Labs i Azure-portalen. |
2. **Skapa virtuella utbildnings-datorer på några minuter med färdiga marketplace-bilder och anpassade bilder** 
   
    Du kan välja färdiga avbildningar från en mängd olika avbildningar på Azure Marketplace och göra dem tillgängliga för praktikanterna i labbet. Om de färdiga avbildningarna inte uppfyller dina krav kan du skapa en anpassad avbildning genom att skapa en virtuell labbdator med hjälp av en färdig avbildning från Azure Marketplace, installera all programvara som du behöver för utbildningen och spara den virtuella datorn som anpassad avbildning i labbet. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Läs om hur du kan vitlista Azure Marketplace-avbildningar; göra tillgängliga för urval endast de bilder du vill ha för utbildningen. |
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att förinstallera den programvara du behöver för utbildningen så att praktikanter snabbt kan skapa en virtuell dator med den anpassade avbildningen. |
3. **Skapa återanvändbara mallar för träningsmaskiner** 
   
    En formel i Azure DevTest Labs är en lista över standardegenskapsvärden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en avbildning, en vm-storlek (en kombination av CPU och RAM) och ett virtuellt nätverk. Varje praktikant kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs-formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att plocka upp en avbildning, VM-storlek (kombination av CPU och RAM) och ett virtuellt nätverk. |
4. **Kontrollera kostnader**
   
    Med Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en praktikant i labbet. 
   
    Om du genomför flerdagarsträning och vill stoppa alla virtuella datorer vid en viss tidpunkt på dagen och sedan automatiskt starta om dem följande dag, kan du enkelt utföra det genom att ställa in principer för automatisk avstängning och automatisk start i labbet. 
   
    Slutligen, när utbildningen är klar kan du ta bort alla virtuella datorer på en gång genom att köra ett enda PowerShell-skript. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnaderna genom att ange principer i labbet. |
   | [Ta bort alla virtuella labbdamer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labb i en operation när utbildningen är klar. |
5. **Dela labbet med varje praktikant**
   
    Labs kan nås direkt via en länk som du delar med dina praktikanter. Dina praktikanter behöver inte ens ha ett Azure-konto, så länge de har ett [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Praktikanter kan inte se virtuella datorer som skapats av andra praktikanter.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägga till en praktikant i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure-portalen för att lägga till praktikanter i ditt utbildningslabb. |
   | [Lägga till praktikanter i labbet med ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera lägger till praktikanter i ditt utbildningslabb. |
   | [Få en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Läs om hur ett labb kan nås direkt via en hyperlänk. |
6. **Återanvänd labbet om och om igen** 
   
    Du kan automatisera skapande av labb, inklusive anpassade inställningar, genom att skapa en Resource Manager-mall och använda den för att skapa identiska labb om och om igen. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med hjälp av en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Hjälp av Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

