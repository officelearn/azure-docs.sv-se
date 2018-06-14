---
title: Använda Azure DevTest Labs för träning | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för utbildning-scenarier.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 85eddaaf101c3e85eca7514b04660163d23c1c80
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787999"
---
# <a name="use-azure-devtest-labs-for-training"></a>Använda Azure DevTest Labs för träning
Azure DevTest Labs kan användas för att implementera många viktiga scenarier utöver utveckling och testning. En av dessa scenarier är att skapa ett labb för träning. Azure DevTest Labs kan du skapa ett labb där du kan ange anpassade mallar som varje äga rum kan använda för att skapa identiska och isolerade miljöer för utbildning. Du kan tillämpa principer för att säkerställa att utbildning miljöer är tillgängliga för varje äga rum när de behöver dem och innehåller tillräckligt med resurser – till exempel virtuella datorer - krävs för utbildning. Slutligen kan du enkelt dela labbet med praktikanter som de kan komma åt med ett klick.

![Använda DevTest Labs för träning](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs uppfyller följande krav som krävs för träning i en virtuell miljö: 

* Praktikanter kan inte se virtuella datorer som skapats av andra praktikanter
* Varje dator utbildningen ska vara identiska
* Praktikanter kan snabbt tillhandahålla sina utbildning-miljöer
* Kontrollera kostnaden genom att säkerställa att praktikanter inte kan hämta flera virtuella datorer än de behöver för utbildning och även avstängning virtuella datorer när de inte använder dem
* Enkelt dela utbildning labbet med varje äga rum
* Återanvända labbet utbildning och om igen

I den här artikeln får du lära dig om olika funktioner i Azure DevTest Labs som kan användas för att uppfylla utbildningskrav som beskrivits tidigare och detaljerade anvisningar som du kan följa för att ställa in ett labb för träning.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementera utbildning med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs utgör startpunkten i Azure DevTest Labs. När du skapar ett labb kan du utföra uppgifter så som att lägga till användare (praktikanter) i laboratoriet kan ange principer för att kontrollera kostnader, definiera VM-avbildningar som kan snabbt skapa och mycket mer.   
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig mer om att skapa ett labb i Azure DevTest Labs i Azure-portalen. |
2. **Skapa utbildning virtuella datorer i minuter med hjälp av fördefinierade marketplace-bilder och anpassade avbildningar** 
   
    Du kan välja färdiga avbildningar från en mängd olika avbildningar i Azure Marketplace och göra dem tillgängliga för praktikanter i labbet. Om de färdiga bilderna inte uppfyller dina krav kan skapa du en anpassad avbildning genom att skapa ett labb VM som använder en färdiga avbildning från Azure Marketplace, installera den programvara som du behöver för utbildning och sparar den virtuella datorn som en anpassad avbildning i labbet. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-bilder](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan godkända Azure Marketplace-bilder. gör att välja endast avbildningarna du vill använda för utbildning. |
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att installera före den programvara du behöver för utbildningen så att praktikanter kan snabbt skapa en virtuell dator med hjälp av den anpassade avbildningen. |
3. **Skapa återanvändbara mallar för utbildning-datorer** 
   
    En formel i Azure DevTest Labs är en lista över egenskapen standardvärden används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en bild, en VM-storlek (en kombination av Processorn och RAM-minne) och ett virtuellt nätverk. Varje äga rum kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en bild, VM-storlek (kombination av Processorn och RAM-minne) och ett virtuellt nätverk. |
4. **Kontrollera kostnader**
   
    Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas med en ges i labbet. 
   
    Om du gör flera dagars utbildning och stoppa alla virtuella datorer på en viss tid på dagen och automatiskt starta om dem sedan följande dag, kan du enkelt göra det genom att ställa in automatisk avstängning och automatisk start principer i labbet. 
   
    Slutligen, när du har slutfört utbildningen du kan ta bort alla virtuella datorer samtidigt genom att köra en enda PowerShell-skript. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnader genom att ange principer i labbet. |
   | [Ta bort alla labbet virtuella datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labs i en enda åtgärd när utbildningen är klar. |
5. **Dela labbet med varje äga rum**
   
    Övningar kan nås direkt med en länk som du delar med din praktikanter. Din praktikanter behöver inte ha en Azure-konto så länge som de har en [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Praktikanter kan inte se virtuella datorer som skapats av andra praktikanter.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägg till en äga rum i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure-portalen för att lägga till praktikanter utbildning labbet. |
   | [Lägg till praktikanter i labbet använder ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera att lägga till praktikanter till din utbildning labbet. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur ett labb kan nås direkt via en hyperlänk. |
6. **Återanvända labbet och om igen** 
   
    Du kan automatisera genereringen av lab, inklusive anpassade inställningar genom att skapa en mall för hanteraren för filserverresurser och använder den för att skapa identiska labs och om igen. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb som använder en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

