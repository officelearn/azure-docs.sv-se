---
title: I den här självstudien får du lära dig hur du prenumererar på ett Azure Stack-erbjudande | Microsoft Docs
description: Den här självstudien visar hur du skapar en ny prenumeration på Azure Stack-tjänster och testa erbjudandet genom att skapa en virtuell testdator.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 4cc2adc334ede3f2377722137d4a6c84544bf425
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251491"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Självstudie: skapa och testa en prenumeration

Den här självstudien visar hur du skapar en prenumeration som innehåller ett erbjudande och sedan testa den. För att testet du loggar in på användarportalen för Azure Stack som en molnadministratör prenumererar på erbjudandet och sedan skapa en virtuell dator.

> [!TIP]
> För mer en mer avancerade utvärderingsversionen, kan du [skapa en prenumeration för en viss användare](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) och logga sedan in som användaren i användarportalen. 

Den här självstudien visar hur du prenumererar på ett Azure Stack-erbjudande.

Vad du lära dig:

> [!div class="checklist"]
> * Prenumerera på ett erbjudande 
> * Testa erbjudandet

## <a name="subscribe-to-an-offer"></a>Prenumerera på ett erbjudande

För att du prenumererar på ett erbjudande som en användare kan logga du in på användarportalen för Azure Stack att identifiera de tjänster som har erbjudits av Azure Stack-operatör.

1. Logga in på portalen och Välj användaren **skaffa en prenumeration**.

   ![Skaffa en prenumeration](media/azure-stack-subscribe-services/get-subscription.png)

2. Skriv ett namn för prenumerationen i fältet **Visningsnamn**. Välj sedan **erbjuder** välja en av de tillgängliga erbjudandena i den **Välj ett erbjudande** avsnittet. Välj sedan **Skapa**.

   ![Skapa ett erbjudande](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Nu måste du uppdatera användarportalen så att börja använda din prenumeration.

3. Om du vill visa den prenumeration som du har skapat, Välj **alla tjänster**. Sedan, under den **Allmänt** kategori väljer **prenumerationer**, och välj sedan din nya prenumeration. När du prenumererar på ett erbjudande kan du uppdatera portalen om nya tjänster har gått med i den nya prenumerationen. I det här exemplet **virtuella datorer** har lagts till.

   ![Visa prenumeration](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testa erbjudandet

Loggat in på användarportalen, kan du testa erbjudandet genom att etablera en virtuell dator med de nya funktionerna i prenumerationen. 

> [!NOTE]
> Det här testet kräver att en Windows Server 2016 Datacenter VM först har lagts till i Azure Stack marketplace. 

1. Logga in på användarportalen.

2. Välj i användarportalen, **virtuella datorer**, sedan **Lägg till**, sedan **Windows Server 2016 Datacenter**, och klicka sedan på **skapa**.

3. I den **grunderna** Skriv en **namn**, **användarnamn**, och **lösenord**, Välj en **prenumeration**, Skapa en **resursgrupp** (eller välj en befintlig) och välj sedan **OK**.

4. I den **väljer du en storlek** väljer **Standard A1**, och klicka sedan på **Välj**.  

5. I den **inställningar** bladet godkänna standardvärdena och välj **OK**.

6. I den **sammanfattning** klickar du på **OK** att skapa den virtuella datorn.  

7. Om du vill se din nya virtuella dator **virtuella datorer**Sök sedan efter den nya virtuella datorn och klicka på dess namn.

    ![Alla resurser](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Distributionen av virtuella datorer tar några minuter att slutföra.


## <a name="next-steps"></a>Nästa steg

Vad du lärt dig i den här självstudien:

> [!div class="checklist"]
> * Prenumerera på ett erbjudande 
> * Testa erbjudandet


> [!div class="nextstepaction"]
> [Skapa en virtuell dator från en community-mall](azure-stack-create-vm-template.md)