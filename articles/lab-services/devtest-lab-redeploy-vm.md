---
title: Distribuera om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att distribuera om en virtuell dator (flytta från en Azure-nod till ett annat) i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 273b0f1105d8b71b90a06e2627e201b97f12a754
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095403"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Distribuera om en virtuell dator i ett labb i Azure DevTest Labs
Om du inte kan ansluta till en virtuell dator (VM) i ett labb via en anslutning till fjärrskrivbord, distribuera om den virtuella datorn och försök conencting till den igen. När du distribuerar om en virtuell dator i DevTest Labs flyttar den virtuella datorn från noden där den körs till en ny nod i Azure-infrastrukturen. Därefter startar den virtuella datorn samtidigt som du behåller alla konfigurationsalternativ och associerade resurser. Den här funktionen sparar du tid då felsöka din anslutning till fjärrskrivbord eller programmet åtkomst till Windows-baserade virtuella datorer i labbet. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Steg för att distribuera om en virtuell dator i ett labb 
Om du vill distribuera om en virtuell dator i ett labb i Azure DevTest Labs, gör du följande: 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. I listan över labbar Välj labb som innehåller den virtuella datorn som du vill distribuera om.  
4. I den vänstra panelen, väljer **Mina virtuella datorer**. 
5. Välj en virtuell dator från listan över virtuella datorer.
6. På sidan virtuell dator för den virtuella datorn, Välj **omdistribuera** under **OPERATIONS** på den vänstra menyn.

    ![Omdistribuera](media/devtest-lab-redeploy-vm/redeploy.png)
7. Läs informationen på sidan och välj **omdistribuera** knappen. 9. Kontrollera status för omdistributionsåtgärden i den **meddelanden** fönster.

    ![Distribuera om status](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du ändrar storlek på en virtuell dator i Azure DevTest Labs finns i [ändra storlek på en virtuell dator](devtest-lab-resize-vm.md).


