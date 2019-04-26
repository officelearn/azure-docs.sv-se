---
title: Konfigurera inställningar för Azure Marketplace-avbildning i Azure DevTest Labs | Microsoft Docs
description: Konfigurera vilka Azure Marketplace-avbildningar kan användas när du skapar en virtuell dator i Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: d0375713c4881c0b73b91fc07bda3ceac2dbc620
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201898"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurera inställningar för Azure Marketplace-avbildning i Azure DevTest Labs
DevTest Labs stöder skapa virtuella datorer baserat på Azure Marketplace-avbildningar beroende på hur du har konfigurerat Azure Marketplace-avbildningar som ska användas i labbet. Den här artikeln visar hur du anger som, om sådana finns, Azure Marketplace-avbildningar kan vara används när du skapar virtuella datorer i ett labb. Detta garanterar att ditt team endast har åtkomst till Marketplace-avbildningar som de behöver. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Välj vilken Azure Marketplace-avbildningar är tillåtna när du skapar en virtuell dator
1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet. 
4. På den labb-bladet och välj **konfiguration och principer**.
5. På övningen **konfiguration och principer** bladet under **VM-baser**väljer **Marketplace-avbildningar**.
6. Ange om du vill att alla kvalificerade Azure Marketplace-avbildningar vara tillgängliga för användning som bas för en ny virtuell dator. Om du väljer **Ja**, och sedan på Azure Marketplace-avbildningar som uppfyller följande kriterier tillåts i laboratoriet:
   
   * Avbildningen skapas en enda virtuell dator **och**
   * Avbildningen använder Azure Resource Manager för att etablera virtuella datorer, **och**
   * Bilden du behöver köpa ett extra licensieringsplan
     
     Om du vill inga bilder som ska tillåtas, eller om du vill ange vilka bilder kan användas, Välj **nr**.
     
     ![Alternativet för att tillåta alla Marketplace-avbildningar som ska användas som grundläggande avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Om du väljer **nr** till föregående steg i **tillåtna bilder/välja alla** kryssrutan aktiveras. 
   Du kan använda det här alternativet tillsammans med sökrutan för att snabbt markera eller avmarkera alla objekt som visas i listan.
   * Välj Azure Marketplace-avbildningar som du vill tillåta individuellt för att skapa en virtuell dator genom att kontrollera motsvarande kryssruta för varje bild.
   * Välj något i listan om du inte vill att alla Azure Marketplace-avbildningar som ska användas i laboratoriet.
   
     ![Du kan ange vilken Azure Marketplace-avbildningar kan användas som grundläggande avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat hur Azure Marketplace-avbildningar är tillåtna när du skapar en virtuell dator, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

