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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666802"
---
# <a name="use-azure-devtest-labs-for-training"></a>Använda Azure DevTest Labs för träning
Azure DevTest Labs kan användas för att implementera många viktiga scenarier utöver utveckling och testning. En av dessa scenarier är att skapa ett labb för utbildning. Azure DevTest Labs kan du skapa ett labb där du kan ange anpassade mallar som varje praktikanten kan använda för att skapa identiska och isolerade miljöer för utbildning. Du kan tillämpa principer för att säkerställa att utbildning miljöer är tillgängliga för varje ges endast när de behöver dem och innehåller tillräckligt med resurser – till exempel virtuella datorer – krävs för utbildning. Slutligen kan du enkelt dela labbet med eleverna som de kan komma åt med ett klick.

![Använda labb för träning](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs uppfyller följande krav som krävs för att genomföra utbildningar i alla virtuella miljöer: 

* Eleverna kan inte se virtuella datorer som skapats av andra eleverna
* Alla datorer för utbildning ska vara identiska
* Eleverna kan snabbt etablera sina miljöer för utbildning
* Styra kostnaderna genom att se till att eleverna inte kan hämta flera virtuella datorer än de behöver för utbildning och även stänga av virtuella datorer när de inte använder dem
* Enkelt dela labb för utbildning med varje äga rum
* Återanvända labb för utbildning och om igen

I den här artikeln får du lära dig om olika Azure DevTest Labs-funktioner som kan användas för att uppfylla de tidigare beskrivna utbildningskrav och detaljerade steg som du kan följa om du vill konfigurera ett labb för utbildning.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementera utbildning med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs utgör startpunkten i Azure DevTest Labs. När du skapar ett labb kan du utföra uppgifter, till exempel lägga till användare (eleverna) till labbet, ange principer för att kontrollera kostnader, definiera VM-avbildningar som kan skapa snabbt och mycket mer.   
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig mer om att skapa ett labb i Azure DevTest Labs i Azure-portalen. |
2. **Skapa utbildning virtuella datorer på några minuter med hjälp av färdiga marketplace-avbildningar och anpassade avbildningar** 
   
    Du kan välja färdiga avbildningar från ett brett utbud av avbildningar i Azure Marketplace och göra dem tillgängliga för eleverna i laboratoriet. Om de färdiga avbildningarna inte uppfyller dina krav kan skapa du en anpassad avbildning genom att skapa ett labb virtuell dator med en färdig avbildning från Azure Marketplace, installera all programvara som du behöver för utbildning och sparar den virtuella datorn som anpassad avbildning i laboratoriet. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan godkänna Azure Marketplace-bilder. för att göra välja endast avbildningarna du för utbildning. |
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att installera före den programvara som du behöver för utbildningen så att eleverna kan snabbt skapa en virtuell dator med den anpassade avbildningen. |
3. **Skapa återanvändningsbara mallar för utbildning-datorer** 
   
    En formel i Azure DevTest Labs är en lista över standardegenskapsvärden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en bild, en VM-storlek (en kombination av processor och RAM-minne) och ett virtuellt nätverk. Varje praktikanten kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera labb formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en avbildning, VM-storlek (kombination av processor och RAM-minne) och ett virtuellt nätverk. |
4. **Kontrollera kostnader**
   
    Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas med en praktikanten i laboratoriet. 
   
    Om du gör flera dagars utbildning och vill du stoppa alla virtuella datorer på en viss tid på dagen och automatiskt starta om dem dagen, kan du enkelt göra det genom att ställa in automatisk avstängning och automatisk start principer i laboratoriet. 
   
    Slutligen när utbildning har slutförts du kan ta bort alla virtuella datorer samtidigt genom att köra en enda PowerShell-skript. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnader genom att ange principer i laboratoriet. |
   | [Ta bort alla labbets virtuella datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labs i en enda åtgärd när utbildningen har slutförts. |
5. **Dela labbet med varje äga rum**
   
    Labs kan nås direkt med en länk som du delar med din eleverna. Din eleverna behöver inte ha en Azure-konto, så länge som de har en [microsoftkonto](devtest-lab-faq.md#what-is-a-microsoft-account). Eleverna kan inte se virtuella datorer som skapats av andra eleverna.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägg till en praktikanten till ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure-portalen för att lägga till eleverna ditt labb för utbildning. |
   | [Lägg till eleverna i labb med hjälp av ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera att lägga till eleverna att ditt labb för utbildning. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur ett labb kan nås direkt via en hyperlänk. |
6. **Återanvända labbet och om igen** 
   
    Du kan automatisera lab skapas, inklusive anpassade inställningar genom att skapa en Resource Manager-mall och använder det för att skapa identiska labs och om igen. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med hjälp av en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med hjälp av Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

