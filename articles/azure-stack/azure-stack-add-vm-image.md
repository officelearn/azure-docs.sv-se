---
title: Lägga till och ta bort en VM-avbildning till Azure-stacken | Microsoft Docs
description: Lägg till eller ta bort din organisations anpassade Windows eller Linux VM-avbildningar för klienter att använda.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/27/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 8dd77dd3431f1be2b8edd8b51929c21b1d5bcd88
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081358"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Göra en avbildning av virtuell dator som är tillgängliga i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

I Azure-stacken, kan du avbildningar av virtuella datorer kan dina användare. Dessa avbildningar kan refereras av Azure Resource Manager-mallar eller lägga till dem i Azure Marketplace-Gränssnittet som ett Marketplace-objekt. Du kan använda antingen en bild form globala Azure Marketplace eller lägga till en egen anpassad avbildning. Du kan lägga till en virtuell dator med hjälp av portalen eller Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Lägg till en VM-avbildning via portalen

> [!NOTE]
> Med den här metoden måste du skapa Marketplace-objektet separat.

Bilder måste kunna refereras av en URI för blob-lagring. Förbereda en Windows- eller Linux operativsystemavbildning i VHD-format (inte VHDX) och överför avbildningen till ett lagringskonto i Azure eller Azure-stacken. Om avbildningen har redan överförts till blob-lagring i Azure eller Azure-grupp, kan du hoppa över steg 1.

1. [Ladda upp en Windows VM-avbildning till Azure för Resource Manager distributioner](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) eller för en Linux-avbildning, följ instruktionerna i [distribuera Linux virtuella datorer på Azure-stacken](azure-stack-linux.md). Innan du laddar upp avbildningen är det viktigt att beakta följande faktorer:

   - Azure-stacken stöder fast disk VHD-format. Fast format strukturer den logiska disken linjärt i filen så att disken förskjutningen X lagras på blob-offset X. En liten sidfot i slutet av blobben beskriver egenskaperna för den virtuella Hårddisken. För att bekräfta om disken är löst kan du använda den [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell-kommando.  

    > [!IMPORTANT]
    >  Azure-stacken stöder inte dynamisk disk virtuella hårddiskar. Ändra storlek på en dynamisk disk som är kopplad till en virtuell dator lämnar den virtuella datorn i ett felaktigt tillstånd. Om du vill undvika det här problemet måste du ta bort den virtuella datorn utan att ta bort den Virtuella datorns disk, en VHD-blobben i ett lagringskonto. Kan konvertera VHD från en dynamisk disk till en fast disk och skapa den virtuella datorn igen.

   * Det är mer effektivt att ladda upp en bild till stacken Azure blob storage än till Azure blob storage eftersom det tar mindre tid att push-avbildningen till Azure-stacken avbildningslagringsplatsen.

   * När du överför den [Windows VM-avbildning](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), se till att ersätta den **inloggning till Azure** med den [konfigurera operatorn Stack Azure PowerShell-miljö](azure-stack-powershell-configure-admin.md) steg.  

   * Anteckna URI där du laddar upp avbildningen för blob-lagring. URI för blob-lagring har följande format: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * Om du vill göra blob anonymt tillgänglig, går du till kontot lagringsblobbehållare där VM-avbildning VHD överfördes. Välj **Blob**, och välj sedan **åtkomstprincip**. Alternativt kan kan du i stället Generera en signatur för delad åtkomst för behållaren och inkludera den som en del av blob-URI.

   ![Gå till storage-konto-blobbar](./media/azure-stack-add-vm-image/image1.png)

   ![Ange blobåtkomst till offentliga](./media/azure-stack-add-vm-image/image2.png)

2. Logga in på Azure-stacken som operator. Välj på menyn **fler tjänster**. Markera **Compute** > **VM-avbildningar** > **Lägg till**.

3. Under **lägga till en VM-avbildning**, ange utgivare, erbjudande, SKU och version för avbildningen av virtuella datorn. Dessa namn segment finns i VM-avbildning i Resource Manager-mallar. Se till att välja den **osType** värdet på rätt sätt. För **OS-diskens Blob-URI**, anger du Blob-URI där bilden överfördes. Markera **skapa** att börja skapa VM-avbildning.

   ![Börja skapa avbildningen](./media/azure-stack-add-vm-image/image4.png)

   När avbildningen har skapats, VM avbildningen status ändras till **lyckades**.

4. Om du vill att den virtuella datoravbildningen mer tillgänglig för användarna i Användargränssnittet, är det en bra idé att [skapa Marketplace-objektet](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Ta bort en VM-avbildning via portalen

1. Öppna administrationsportalen på [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Välj **Marketplace management**, och välj sedan den virtuella datorn som du vill ta bort.

3. Klicka på **Ta bort**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Lägg till en VM-avbildning till Marketplace med hjälp av PowerShell

1. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).  

2. Logga in på Azure-stacken som en operator. Instruktioner finns i [logga in på Azure-stacken som en operatör](azure-stack-powershell-configure-admin.md).

3. Öppna PowerShell med en förhöjd behörighet och kör:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  Den **Lägg till AzsPlatformimage** cmdlet anger värden som används av Azure Resource Manager-mallar för att referera till den Virtuella datoravbildningen. Värdena inkluderar:
  - **Publisher**  
    Exempel: `Canonical`  
    Utgivarens namn segment i VM-avbildning som användare använder när de distribuerar avbildningen. Ett exempel är **Microsoft**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **Erbjudande**  
    Exempel: `UbuntuServer`  
    Erbjudandet namnet segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **WindowsServer**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **sku**  
    Exempel: `14.04.3-LTS`  
    SKU namn segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **Datacenter2016**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **version**  
    Exempel: `1.0.0`  
    Versionen av VM-avbildning som användare använder när de distribuerar VM-avbildning. Den här versionen är i formatet *\#.\#.\#*. Ett exempel är **1.0.0**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **osType**  
    Exempel: `Linux`  
    OsType bildens måste vara antingen **Windows** eller **Linux**.  
  - **OSUri**  
    Exempel: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Du kan ange en URI för blob-lagring för ett `osDisk`.  

    Mer information finns i PowerShell-referens för den [Lägg till AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet och [ny DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Lägg till en anpassad VM-avbildning till Marketplace med hjälp av PowerShell

1. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).

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

3. Logga in på Azure-stacken som en operator. Instruktioner finns i [logga in på Azure-stacken som en operatör](azure-stack-powershell-configure-admin.md).

4. Skapa ett lagringskonto i globala Azure eller Azure-grupp för att lagra din anpassade VM-avbildning. Instruktioner finns i [Snabbstart: Överför, hämtning och listan blobar med hjälp av Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Förbereda en Windows- eller Linux operativsystemavbildning i VHD-format (inte VHDX), överför avbildningen till ditt lagringskonto och hämta URI där VM-avbildning kan hämtas av PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Valfritt) Du kan ladda upp en matris med datadiskar som en del av VM-avbildning. Skapa din datadiskar med hjälp av cmdlet New-DataDiskObject. Öppna PowerShell från en förhöjd behörighet och kör:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Öppna PowerShell med en förhöjd behörighet och kör:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Mer information om Lägg till AzsPlatformimage cmdlet och cmdlet New-DataDiskObject finns i Microsoft-PowerShell [Azure Stack operatorn dokumentation](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Ta bort en VM-avbildning med hjälp av PowerShell

När du behöver inte längre avbildningen av den virtuella dator som du överfört, kan du ta bort den från Marketplace med hjälp av följande cmdlet:

1. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).

2. Logga in på Azure-stacken som en operator.

3. Öppna PowerShell med en förhöjd behörighet och kör:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  Den **ta bort AzsPlatformImage** cmdlet anger värden som används av Azure Resource Manager-mallar för att referera till den Virtuella datoravbildningen. Värdena inkluderar:
  - **Publisher**  
    Exempel: `Canonical`  
    Utgivarens namn segment i VM-avbildning som användare använder när de distribuerar avbildningen. Ett exempel är **Microsoft**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **Erbjudande**  
    Exempel: `UbuntuServer`  
    Erbjudandet namnet segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **WindowsServer**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **sku**  
    Exempel: `14.04.3-LTS`  
    SKU namn segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **Datacenter2016**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **version**  
    Exempel: `1.0.0`  
    Versionen av VM-avbildning som användare använder när de distribuerar VM-avbildning. Den här versionen är i formatet *\#.\#.\#*. Ett exempel är **1.0.0**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
    
    Mer information om cmdlet Remove-AzsPlatformImage finns i Microsoft-PowerShell [Azure Stack operatorn dokumentation](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Nästa steg

[Etablera en virtuell dator](azure-stack-provision-vm.md)
