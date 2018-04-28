---
title: Azure Stack Quick Start - skapa en virtuell Windows-dator
description: Azure Stack Quick Start - skapa en virtuell Windows-dator med hjälp av portalen
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Snabbstart: skapa en virtuell dator för Windows server med Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan skapa en virtuell dator i Windows Server 2016 med hjälp av Azure Stack-portalen. Följ stegen i den här artikeln för att skapa och använda en virtuell dator.

## <a name="sign-in-to-the-azure-stack-portal"></a>Logga in på Azure Stack-portalen

Logga in på Azure Stack-portalen. Adressen till stacken för Azure-portalen beror på vilken Azure Stack-produkt som du vill ansluta till:

* För Azure Stack Development Kit (ASDK) Gå till: https://portal.local.azurestack.external.
* Gå till den URL som tillhandahålls av Azure Stack-operatorn för ett Azure-stacken integrerat system.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **nya** > **Compute** > **Windows Server 2016 Datacenter Eval** > **skapa**. Om du inte ser **Windows Server 2016 Datacenter Eval** posten, kontakta din Azure Stack-operator. Be lägger till det till marketplace som beskrivs i den [lägga till Windows Server 2016 VM-avbildning i Azure Stack-marketplace](../azure-stack-add-default-image.md) artikel.

    ![Steg för att skapa en virtuell Windows-dator i portalen](media/azure-stack-quick-windows-portal/image01.png)
2. Under **grunderna**, ange ett **namn**, **användarnamn**, och **lösenord**. Välj en **prenumeration**. Skapa en **resursgruppen**, eller välj en befintlig en, Välj en **plats**, och klicka sedan på **OK**.

    ![Konfigurera grundläggande inställningar](media/azure-stack-quick-windows-portal/image02.png)
3. Under **välja en storlek**, klickar du på **D1 Standard** > **Välj**.
    ![Välj storleken på virtuella datorn](media/azure-stack-quick-windows-portal/image03.png)
4. Under **inställningar**, acceptera standardinställningarna och klickar på **OK**.
    ![Konfigurera inställningar för virtuell dator](media/azure-stack-quick-windows-portal/image04.png)
5. Under **sammanfattning**, klickar du på **OK** att skapa den virtuella datorn.
    ![Visa sammanfattning och skapa virtuell dator](media/azure-stack-quick-windows-portal/image05.png)
6. Klicka för att visa den nya virtuella datorn **alla resurser**, söka efter namnet på virtuella datorn och klicka sedan på namnet i sökresultaten.
    ![Se virtuell dator](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den virtuella datorn kan du ta bort den virtuella datorn och dess resurser. Om du vill göra det, Välj resursgruppen på sidan virtuella datorn och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en grundläggande Windows Server-datorn i den här snabbstartsguide. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
