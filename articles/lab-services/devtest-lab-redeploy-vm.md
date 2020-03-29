---
title: Distribuera om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du distribuerar om en virtuell dator (flytta från en Azure-nod till en annan) i Azure DevTest Labs.
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
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561632"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Distribuera om en virtuell dator i ett labb i Azure DevTest Labs
Om du inte kan ansluta till en virtuell dator (VM) i ett labb via en fjärrskrivbordsanslutning distribuerar du om den virtuella datorn och försöker ansluta till den igen. När du distribuerar om en virtuell dator flyttar DevTest Labs den virtuella datorn från den nod som den körs på till en ny nod i Azure-infrastrukturen. Den startar sedan den virtuella datorn samtidigt som alla dina konfigurationsalternativ och associerade resurser behålls. Den här funktionen sparar tid att felsöka anslutningen till fjärrskrivbord eller programåtkomst till Windows-baserade virtuella datorer i labbet. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Åtgärder för att distribuera om en virtuell dator i ett labb 
Så här om du vill distribuera om en virtuell dator i ett labb i Azure DevTest Labs: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj labbet som innehåller den virtuella datorn som du vill distribuera om i listan över labb.  
4. Välj Mina virtuella **datorer på**den vänstra panelen . 
5. Välj en virtuell dator i listan över virtuella datorer.
6. På sidan Virtuell dator för den virtuella datorn väljer du **Distribuera om** under **OPERATIONER** i menyn till vänster.

    ![Omdistribuera](media/devtest-lab-redeploy-vm/redeploy.png)
7. Läs informationen på sidan och välj **knappen Distribuera om.** 9. Kontrollera status för omdistributionen i fönstret **Meddelanden.**

    ![Omfördelningsstatus](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du ändrar storlek på en virtuell dator i Azure DevTest Labs, se [Ändra storlek på en virtuell dator](devtest-lab-resize-vm.md).


