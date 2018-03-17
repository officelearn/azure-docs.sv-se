---
title: "Lägg till ett Azure-stacken marketplace-objekt från Azure | Microsoft Docs"
description: "Beskriver hur du lägger till en avbildning av virtuell dator för Azure-baserade Windows Server på Azure Marketplace för stacken."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Självstudier: lägga till ett Azure-stacken marketplace-objekt från Azure

Som operatör Azure-stacken är det första du behöver göra så att användarna kan distribuera en virtuell dator (VM) att lägga till en VM-avbildning till stacken för Azure marketplace. Som standard ingenting publiceras på Azure-stacken Marketplace, men du kan ladda ned objekt från [en granskad lista över Azure marketplace-objekt](.\.\azure-stack-marketplace-azure-items.md) som har förhandstestade körs på Azure-stacken. Använd det här alternativet om du arbetar i ett scenario med anslutna och du har registrerat din Azure Stack-instans med Azure.

I den här självstudiekursen, lägger du till Windows Server 2016 VM-avbildning från Azure marketplace Stack för Azure marketplace.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Lägga till ett Windows Azure VM-stacken Server marketplace-objekt
> * Verifiera att VM-avbildning är tillgänglig 
> * Testa VM-avbildning

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Installera den [Azure Stack-kompatibla Azure PowerShell-moduler](asdk-post-deploy.md#install-azure-stack-powershell)
- Hämta den [Azure Stack-verktyg](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Registrera ASDK](asdk-register.md) med din Azure-prenumeration

## <a name="add-a-windows-server-vm-image"></a>Lägg till en Windows Server-VM-avbildning
Du kan lägga till en Windows Server 2016-avbildning till stacken för Azure marketplace genom att hämta bilden från Azure marketplace. Använd det här alternativet om du arbetar i ett scenario med anslutna och du har redan [registrerat din Azure-stacken instans](asdk-register.md) med Azure.

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**. 

   ![Lägg till från Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Hitta eller Sök efter den **Windows Server 2016 Datacenter** bild.

4. Välj den **Windows Server 2016 Datacenter** avbildningen och välj sedan **hämta**.

   ![Ladda ned avbildningen från Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Det tar ungefär en timme att hämta VM-avbildning och publicera den på Azure-stacken marketplace. 

När nedladdningen är klar bilden är publicerat och tillgängligt under **Marketplace Management**. Bilden är också tillgänglig under **Compute** att skapa nya virtuella datorer.

## <a name="verify-the-marketplace-item-is-available"></a>Kontrollera marketplace-objektet är tillgängligt
Följ dessa steg för att kontrollera att den nya VM-avbildningen är tillgängliga i stacken för Azure marketplace.

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **Marketplace Management**. 

3. Kontrollera att Windows Server 2016 Datacenter VM-avbildning har lagts till.

   ![Hämtade avbildning från Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Testa VM-avbildning
Som operatör Azure Stack kan du använda den [administratörsportal](https://adminportal.local.azurestack.external) för att skapa ett test VM att validera avbildningen har gjorts tillgängliga har. 

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).

2. Klicka på **nya** > **Compute** > **Windows Server 2016 Datacenter** > **skapa**.  
 ![Skapa virtuell dator från marketplace-avbildning](media/asdk-marketplace-item/new-compute.png)

3. I den **grunderna** bladet anger du följande information och klicka sedan på **OK**:
  - **Namnet**: test-vm-1
  - **Användarnamnet**: AdminTestUser
  - **Lösenordet**: AzS TestVM01
  - **Prenumerationen**: acceptera standardvärdet providern prenumeration
  - **Resursgruppen**: test-vm-rg
  - **Plats**: lokala

4. I den **välja en storlek** bladet, klickar du på **A1 Standard**, och klicka sedan på **Välj**.  

5. I den **inställningar** bladet acceptera standardinställningarna och klickar på **OK**

6. Klicka **OK** på bladet **Sammanfattning** för att skapa den virtuella datorn.  
> [!NOTE]
> Distributionen av virtuella datorer tar några minuter att slutföra.

7. Om du vill visa till den nya virtuella datorn, klickar du på **virtuella datorer**, Sök sedan efter **test-vm-1** och klicka på dess namn.
    ![Första test VM-bild som visas](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du har loggat in på den virtuella datorn, och verifiera att testa image fungerar korrekt, bör du ta bort resursgruppen test. Detta frigör begränsade resurser som är tillgängliga för nod ASDK installationer.

När det inte längre behövs tar du bort resursgruppen, virtuell dator och alla relaterade resurser genom att följa dessa steg:

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).
2. Klicka på **resursgrupper** > **test-vm-rg** > **ta bort resursgruppen**.
3. Typen **test-vm-rg** som resursgruppens namn och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett Windows Azure VM-stacken Server marketplace-objekt
> * Verifiera att VM-avbildning är tillgänglig 
> * Testa VM-avbildning

Gå vidare till nästa kurs att lära dig hur du skapar en Azure-stacken erbjudandet och planera.

> [!div class="nextstepaction"]
> [Erbjuder Azure Stack IaaS-tjänster](asdk-offer-services.md)