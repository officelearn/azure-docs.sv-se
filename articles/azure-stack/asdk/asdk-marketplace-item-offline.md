---
title: Lägg till ett Azure-stacken marketplace-objekt från en lokal källa | Microsoft Docs
description: Beskriver hur du lägger till en lokal operativsystemsavbildning Stack Azure Marketplace.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: a093e60718881b2fe9ca70df7596e8963dc55d9f
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808051"
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Självstudier: lägga till ett Azure-stacken marketplace-objekt från en lokal källa

Som operatör Azure-stacken är det första du behöver göra så att användarna kan distribuera en virtuell dator (VM) att lägga till en VM-avbildning till stacken för Azure marketplace. Inget publiceras på Azure-stacken Marketplace som standard, men du kan ladda upp VM ISO-avbildningar för att göra dem tillgängliga för användarna. Använd det här alternativet om du har distribuerat Azure Stack i ett scenario med frånkopplade eller i scenarier med begränsad anslutning.

I kursen får du ladda ned en Windows Server 2016 VM-avbildning från den Windows Server-utvärderingar sidan och överföra den till stacken för Azure marketplace.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till en Windows Server 2016 VM-avbildning
> * Verifiera att VM-avbildning är tillgänglig 
> * Testa VM-avbildning

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

- Installera den [Azure Stack-kompatibla Azure PowerShell-moduler](asdk-post-deploy.md#install-azure-stack-powershell)
- Hämta den [Azure Stack-verktyg](asdk-post-deploy.md#download-the-azure-stack-tools)
- Hämta den [ISO-avbildning för Windows Server 2106 virtuella](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) från utvärderingar för Windows Server-sida

## <a name="add-a-windows-server-vm-image"></a>Lägg till en Windows Server-VM-avbildning
Du kan publicera Windows Server 2016-avbildningen till Azure-stacken marketplace genom att lägga till en tidigare hämtade ISO-avbildning med hjälp av PowerShell. 

Använd det här alternativet om du har distribuerat Azure Stack i ett scenario med frånkopplade eller i scenarier med begränsad anslutning.

1. [Installera PowerShell för Azure-stacken](../azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Om du använder **Active Directory Federation Services**, använder du följande cmdlet:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Logga in på Azure-stacken som en operator. Instruktioner finns i [logga in på Azure-stacken som en operatör](../azure-stack-powershell-configure-admin.md).

   ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

4. Lägg till Windows Server 2016-bild till stacken för Azure marketplace.

    Den **Lägg till AzsPlatformimage** cmdlet som används för att lägga till avbildningen anger värden som används av Azure Resource Manager-mallar för att referera till den Virtuella datoravbildningen.
    
    Värdena inkluderar:
    
  - **Publisher**  
    Exempel: `Microsoft`  
    Utgivarens namn segment i VM-avbildning som användare använder när de distribuerar avbildningen. Ett exempel är **Microsoft**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **Erbjudande**  
    Exempel: `WindowsServer`  
    Erbjudandet namnet segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **WindowsServer**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **sku**  
    Exempel: `Datacenter2016`  
    SKU namn segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **Datacenter2016**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **Version**  
    Exempel: `1.0.0`  
    Versionen av VM-avbildning som användare använder när de distribuerar VM-avbildning. Den här versionen är i formatet *\#.\#.\#*. Ett exempel är **1.0.0**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **osType**  
    Exempel: `Windows`  
    OsType bildens måste vara antingen **Windows** eller **Linux**. Ersätt *fully_qualified_path_to_ISO* med sökvägen till Windows Server 2016 ISO som du hämtat. 
  - **OSUri**  
    Exempel: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Du kan ange en URI för blob-lagring för ett `osDisk`. I hans fall ska du ange den plats där du sparade den bild som du har hämtat.

    Mer information finns i PowerShell-referens för den [Lägg till AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet.

    Öppna PowerShell med en förhöjd behörighet och kör:

      ````PowerShell  
        Add-AzsPlatformimage -publisher "Microsoft" -offer "WindowsServer" -sku "Datacenter2016" -version "1.0.0” -OSType "Windows" -OSUri "<fully_qualified_path_to_ISO>"
      ````  

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
> * Lägg till en Windows Server 2016 VM-avbildning
> * Verifiera att VM-avbildning är tillgänglig 
> * Testa VM-avbildning

Gå vidare till nästa kurs att lära dig hur du skapar en Azure-stacken erbjudandet och planera.

> [!div class="nextstepaction"]
> [Erbjuder Azure Stack IaaS-tjänster](asdk-offer-services.md)
