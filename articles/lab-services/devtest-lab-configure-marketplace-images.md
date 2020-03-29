---
title: Konfigurera Azure Marketplace-avbildningsinställningar i Azure DevTest Labs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169557"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurera Azure Marketplace-avbildningsinställningar i Azure DevTest Labs
DevTest Labs har stöd för att skapa virtuella datorer baserat på Azure Marketplace-avbildningar beroende på hur du har konfigurerat Azure Marketplace-avbildningar som ska användas i labbet. Den här artikeln visar hur du anger vilka, om några, Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb. Detta säkerställer att ditt team bara har tillgång till marketplace-avbildningar de behöver. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Välj vilka Azure Marketplace-avbildningar som är tillåtna när du skapar en virtuell dator
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb. 
4. På labbets blad väljer du **Konfiguration och principer**.
5. I labbets **konfigurations- och principblad** under Baser för virtuella datorer väljer du **Marketplace-avbildningar**. **Virtual Machine Bases**
6. Ange om du vill att alla kvalificerade Azure Marketplace-avbildningar ska vara tillgängliga för användning som bas för en ny virtuell dator. Om du väljer **Ja**tillåts alla Azure Marketplace-avbildningar som uppfyller alla följande villkor i labbet:
   
   * Avbildningen skapar en enda virtuell dator **och**
   * Avbildningen använder Azure Resource Manager för att etablera virtuella datorer **och**
   * Bilden kräver inte att du köper en extra licensplan
     
     Om du inte vill att inga bilder ska tillåtas, eller om du vill ange vilka bilder som kan användas, väljer du **Nej**.
     
     ![Möjlighet att tillåta att alla Marketplace-avbildningar används som basavbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Om du väljer **Nej** till föregående steg är kryssrutan **Tillåten bild/Markera alla** aktiverad. 
   Du kan använda det här alternativet tillsammans med sökrutan för att snabbt markera eller avmarkera alla objekt som visas i listan.
   * Markera de Azure Marketplace-avbildningar som du vill tillåta att den virtuella datorn skapas individuellt genom att kontrollera varje avbildnings motsvarande kryssruta.
   * Välj ingenting i listan om du inte vill tillåta att några Azure Marketplace-avbildningar används i labbet.
   
     ![Du kan ange vilka Azure Marketplace-avbildningar som kan användas som basavbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat hur Azure Marketplace-avbildningar tillåts när du skapar en virtuell dator är nästa steg att lägga till [en virtuell dator i labbet](devtest-lab-add-vm.md).

