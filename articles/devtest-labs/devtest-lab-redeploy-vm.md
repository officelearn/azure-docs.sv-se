---
title: Distribuera om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du distribuerar om en virtuell dator (flytta från en Azure-nod till en annan) i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da0edf13adaa0d7ecd84ee2c190f376c19b398db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480243"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Distribuera om en virtuell dator i ett labb i Azure DevTest Labs
Om du inte kan ansluta till en virtuell dator (VM) i ett labb via en fjärr skrivbords anslutning, distribuerar du om den virtuella datorn och försöker ansluta till den igen. När du distribuerar om en virtuell dator flyttar DevTest Labs den virtuella datorn från noden där den körs till en ny nod i Azure-infrastrukturen. Den startar sedan den virtuella datorn och behåller alla konfigurations alternativ och de associerade resurserna. Med den här funktionen slipper du den tid som krävs för att felsöka fjärr skrivbords anslutningen eller program åtkomst till Windows-baserade virtuella datorer i labbet. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Steg för att distribuera om en virtuell dator i ett labb 
Gör så här om du vill distribuera om en virtuell dator i ett labb i Azure DevTest Labs: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du labbet som innehåller den virtuella dator som du vill distribuera om.  
4. I den vänstra panelen väljer du **mina Virtual Machines**. 
5. Välj en virtuell dator i listan över virtuella datorer.
6. På sidan virtuell dator för den virtuella datorn väljer du **distribuera** om under **åtgärder** på den vänstra menyn.

    ![Omdistribuera](media/devtest-lab-redeploy-vm/redeploy.png)
7. Läs informationen på sidan och välj knappen **distribuera** om. 9. Kontrol lera statusen för omdistributions åtgärden i **aviserings** fönstret.

    ![Distribuera om status](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du ändrar storlek på en virtuell dator i Azure DevTest Labs finns i [ändra storlek på en virtuell dator](devtest-lab-resize-vm.md).


