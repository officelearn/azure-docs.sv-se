---
title: Azure Stack Quick Start - skapa en virtuell Windows-dator
description: "Azure Stack Quick Start - skapa en virtuell Windows-dator med hjälp av portalen"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: azure-stack
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: abca538f28bbc0a8f3f00311ca1a69d196f10272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Skapa en virtuell Windows-dator med Azure Stack-portalen

Du kan skapa en virtuell Windows-dator med hjälp av Azure Stack-portalen. Portalen är ett webbaserat användargränssnitt där du kan skapa, konfigurera och hantera resurser.

## <a name="sign-in-to-the-azure-stack-portal"></a>Logga in på Azure Stack-portalen

Logga in på Azure Stack-portalen. Adressen till stacken för Azure-portalen beror på vilken Azure Stack-produkt som du ansluter till:

* För Azure Stack Development Kit (ASDK) Gå till: https://portal.local.azurestack.external.
* Gå till den URL som tillhandahålls av Azure Stack-operatorn för ett Azure-stacken integrerat system.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **nya** > **Compute** > **Windows Server 2016 Datacenter Eval** > **skapa**. Om du inte ser **Windows Server 2016 Datacenter Eval** posten, kontakta din Azure Stack-operator. Be lägger till det till marketplace som beskrivs i den [lägga till Windows Server 2016 VM-avbildning i Azure Stack-marketplace](../azure-stack-add-default-image.md) artikel. 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. Under **grunderna**, ange ett **namn**, **användarnamn**, och **lösenord**. Välj en **prenumeration**. Skapa en **resursgruppen**, eller välj en befintlig en, Välj en **plats**, och klicka sedan på **OK**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. Under **välja en storlek**, klickar du på **D1 Standard** > **Välj**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. Under **inställningar**, acceptera standardinställningarna och klickar på **OK**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. Under **sammanfattning**, klickar du på **OK** att skapa den virtuella datorn. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Klicka för att visa den nya virtuella datorn **alla resurser**, Sök sedan efter den virtuella datorn och klickar på dess namn.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, virtuell dator och alla relaterade resurser när du inte längre behöver den virtuella datorn. Om du vill göra det, Välj resursgruppen från sidan virtuella datorn och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg
Du har distribuerat en enkel Windows virtuell dator i den här snabbstartsguide. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
