---
title: Konfigurera inställningarna för Azure Marketplace-avbildningar i Azure DevTest Labs
description: Konfigurera vilka Azure Marketplace-avbildningar som kan användas när du skapar en virtuell dator i Azure DevTest Labs
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896307"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurera inställningarna för Azure Marketplace-avbildningar i Azure DevTest Labs
DevTest Labs har stöd för att skapa virtuella datorer baserat på Azure Marketplace-avbildningar beroende på hur du har konfigurerat Azure Marketplace-avbildningar som ska användas i labbet. Den här artikeln visar hur du anger vilka Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb. Detta säkerställer att ditt team bara har åtkomst till de Marketplace-avbildningar som de behöver. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Välj vilka Azure Marketplace-avbildningar som ska tillåtas när du skapar en virtuell dator
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb. 
4. På labb bladet väljer du **konfiguration och principer**.
5. Välj **Marketplace-avbildningar**på bladet **konfiguration och principer** på labbet under **Virtual Machine Bases**.
6. Ange om du vill att alla kvalificerade Azure Marketplace-avbildningar ska vara tillgängliga för användning som bas för en ny virtuell dator. Om du väljer **Ja**tillåts alla Azure Marketplace-avbildningar som uppfyller alla följande kriterier i labbet:
   
   * Avbildningen skapar en enskild virtuell dator **och**
   * Avbildningen använder Azure Resource Manager för att etablera virtuella datorer **och**
   * Avbildningen kräver inte att du köper en extra licens plan
     
     Om du inte vill att några bilder ska tillåtas eller om du vill ange vilka bilder som kan användas väljer du **Nej**.
     
     ![Alternativ för att tillåta att alla Marketplace-avbildningar används som bas avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Om du väljer **Nej** till föregående steg aktive ras kryss rutan **tillåtna bilder/Markera alla** . 
   Du kan använda det här alternativet tillsammans med sökrutan för att snabbt välja eller avmarkera alla objekt som visas i listan.
   * Välj de Azure Marketplace-avbildningar som du vill tillåta för att skapa virtuella datorer individuellt genom att kontrol lera varje avbildnings motsvarande kryss ruta.
   * Välj ingenting från listan om du inte vill att några Azure Marketplace-avbildningar ska användas i labbet.
   
     ![Du kan ange vilka Azure Marketplace-avbildningar som kan användas som bas avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat hur Azure Marketplace-avbildningar tillåts när du skapar en virtuell dator är nästa steg att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

