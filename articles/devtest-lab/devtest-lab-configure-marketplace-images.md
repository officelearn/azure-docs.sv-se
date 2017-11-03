---
title: "Konfigurera inställningar för Azure Marketplace-avbildning i Azure DevTest Labs | Microsoft Docs"
description: "Konfigurera vilka Azure Marketplace-bilder som kan användas när du skapar en virtuell dator i Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: 5f888c9d92a9164cc7d3d1aed66c29a724b365d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurera inställningar för Azure Marketplace-avbildning i Azure DevTest Labs
DevTest Labs har stöd för att skapa virtuella datorer baserat på Azure Marketplace-bilder beroende på hur du har konfigurerat Azure Marketplace-bilder som ska användas i labbet. Den här artikeln visar hur du anger som eventuellt Azure Marketplace-bilder kan vara används när du skapar virtuella datorer i ett labb. Detta säkerställer att din grupp endast har åtkomst till Marketplace-avbildningar som de behöver. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Välj vilka Azure Marketplace-bilder tillåts när du skapar en virtuell dator
1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet. 
4. På den testmiljön bladet välj **konfiguration och principer**.
5. På lab's **konfiguration och principer** bladet under **virtuella Datorbaser**väljer **Marketplace-bilder**.
6. Ange om du vill att alla kvalificerade Azure Marketplace-bilder att användas som bas för en ny virtuell dator. Om du väljer **Ja**, och klicka sedan på Azure Marketplace-bilder som uppfyller alla följande villkor är tillåtna i labbet:
   
   * Avbildningen skapas en enda virtuell dator **och**
   * Bilden använder Azure Resource Manager för att etablera virtuella datorer, **och**
   * Bilden behöver inte köpa ett extra licensieringsplan
     
    Om du vill att inga bilder ska tillåtas eller du vill ange vilka bilder som kan användas, Välj **nr**.
     
     ![Alternativet för att tillåta alla Marketplace-bilder som ska användas som bas avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Om du väljer **nr** till föregående steg i **tillåtna bilder/Välj alla** kryssrutan aktiverad. 
   Du kan använda det här alternativet tillsammans med sökrutan för att snabbt markera eller avmarkera alla objekt som visas i listan.
   * Välj Azure Marketplace-bilder som du vill tillåta för att skapa Virtuella individuellt genom att kontrollera varje avbildning motsvarande kryssruta.
   * Välj ingenting i listan om du inte vill tillåta alla Azure Marketplace-bilder som ska användas i labbet.
   
    ![Du kan ange vilken Azure Marketplace-avbildningar kan användas som bas avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat hur Azure Marketplace-bilder tillåts när du skapar en virtuell dator, nästa steg är att [lägga till en virtuell dator i labbet](devtest-lab-add-vm-with-artifacts.md).

