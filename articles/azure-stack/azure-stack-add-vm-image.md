---
title: Lägga till och ta bort en VM-avbildning till Azure Stack | Microsoft Docs
description: Lägg till eller ta bort din organisations anpassade Windows eller Linux VM-avbildning för att klienterna ska använda.
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
ms.openlocfilehash: 5c2088ab39e32c049ce867698e84efba759c9a87
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447344"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Göra en avbildning av virtuell dator som är tillgängliga i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

I Azure Stack, kan du tillgängliggöra avbildningar av virtuella datorer till dina användare. Dessa avbildningar kan refereras av Azure Resource Manager-mallar eller du kan lägga till dem till Azure Marketplace-Gränssnittet som ett Marketplace-objekt. Du kan använda antingen en bild form global Azure Marketplace eller lägga till en egen anpassad avbildning. Du kan lägga till en virtuell dator med portalen eller med Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Lägg till en VM-avbildning via portalen

> [!NOTE]
> Med den här metoden måste du skapa Marketplace-objekt separat.

Avbildningar måste kunna refereras till av en URI för blob-lagring. Förbereda en Windows- eller Linux operativsystemavbildning i VHD-format (inte VHDX) och sedan ladda upp avbildningen till ett lagringskonto i Azure eller Azure Stack. Om din avbildning har redan överförts till bloblagringen i Azure eller Azure Stack, kan du hoppa över steg 1.

1. [Överför en Windows VM-avbildning till Azure för Resource Manager-distributioner](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) eller för en Linux-avbildning, följer du instruktionerna som beskrivs i [distribuera Linux-datorer på Azure Stack](azure-stack-linux.md). Innan du laddar upp avbildningen är det viktigt att tänka på följande faktorer:

   - Azure Stack har stöd för VHD-formatet fast disk. Det fasta formatet strukturer den logiska disken linjärt i filen så att diskförskjutning X lagras vid blob-förskjutning X. En liten sidfot i slutet av bloben beskriver egenskaperna för den virtuella Hårddisken. För att bekräfta om disken är fast, använda den [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell-kommando.  

    > [!IMPORTANT]
    >  Azure Stack har inte stöd för dynamisk disk virtuella hårddiskar. Ändra storlek på en dynamisk disk som är kopplad till en virtuell dator lämnar den virtuella datorn i ett felaktigt tillstånd. Om du vill minimera detta problem genom att ta bort den virtuella datorn utan att ta bort den Virtuella datorns disk, en VHD-blob i ett lagringskonto. Konvertera den virtuella Hårddisken från en dynamisk disk till en fast disk och återskapa den virtuella datorn i.

   * Det är mer effektivt att överföra en avbildning till Azure Stack blob-lagring än till Azure blob-lagring eftersom det tar mindre tid att överföra avbildningen till Azure Stack-avbildningslagringsplatsen.

   * När du laddar upp den [Windows VM-avbildning](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), se till att ersätta den **logga in på Azure** steg med den [konfigurera PowerShell-miljö för Azure Stack-operatör](azure-stack-powershell-configure-admin.md) steg.  

   * Anteckna URI där du laddar upp avbildningen för blob-lagring. URI för blob-lagring har följande format: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * För att göra blob anonymt tillgänglig, går du till blob-behållaren storage-konto där den Virtuella datoravbildningen VHD har överförts. Välj **Blob**, och välj sedan **åtkomstprincip**. Du kan i stället skapar en signatur för delad åtkomst för behållaren, och lägger till den som en del av blob-URI.

   ![Gå till lagringskontoblobbar](./media/azure-stack-add-vm-image/image1.png)

   ![Ange blob-åtkomst till offentliga](./media/azure-stack-add-vm-image/image2.png)

2. Logga in på Azure Stack som operatör. I menyn, Välj **fler tjänster**. Välj **Compute** > **VM-avbildningar** > **Lägg till**.

3. Under **lägga till en virtuell datoravbildning**, ange utgivare, erbjudande, SKU och version av avbildningen av virtuella datorn. Dessa namn segment avser VM-avbildning i Resource Manager-mallar. Se till att välja den **osType** värde korrekt. För **OS diskens Blob-URI**, anger du Blob-URI där avbildningen laddades upp. Välj **skapa** att börja skapa avbildningen.

   ![Börja skapa avbildningen](./media/azure-stack-add-vm-image/image4.png)

   När avbildningen har skapats, VM-avbildning status ändras till **lyckades**.

4. Om du vill göra avbildningen av virtuell dator mer tillgänglig för användarna i Användargränssnittet, är det en bra idé att [skapa ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Ta bort en VM-avbildning via portalen

1. Öppna administrationsportalen på [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Välj **Marketplace management**, och välj sedan den virtuella datorn som du vill ta bort.

3. Klicka på **Ta bort**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Lägga till en VM-avbildning i Marketplace med hjälp av PowerShell

> [!Note]  
> När du lägger till en bild som det ska bara är tillgänglig för Azure Resource Manager-baserade mallar och PowerShell-distributioner. Att göra en avbildning som är tillgängliga för en användarna som ett marketplace-objekt, publicera i marketplace-objekt med hjälp av stegen i artikeln [skapa och publicera ett Marketplace-objekt](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-and-publish-marketplace-item)

1. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).  

2. Logga in på Azure Stack som en operatör. Anvisningar finns i [logga in på Azure Stack som operatör](azure-stack-powershell-configure-admin.md).

3. Öppna PowerShell med en upphöjd kommandotolk och kör:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  Den **Lägg till AzsPlatformimage** cmdlet anger värden som används av Azure Resource Manager-mallar för att referera till VM-avbildning. Värdena är:
  - **utgivare**  
    Exempel: `Canonical`  
    Utgivarens namn segment i VM-avbildning som användare använder när de distribuerar avbildningen. Ett exempel är **Microsoft**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **erbjudande**  
    Exempel: `UbuntuServer`  
    Erbjudandet namnet segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **WindowsServer**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **sku**  
    Exempel: `14.04.3-LTS`  
    Namnsegmentet SKU för avbildningen som användare använder när de distribuerar VM-avbildning. Ett exempel är **Datacenter2016**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **version**  
    Exempel: `1.0.0`  
    Versionen av avbildningen som användare använder när de distribuerar VM-avbildning. Den här versionen är i formatet *\#.\#.\#*. Ett exempel är **1.0.0**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **osType**  
    Exempel: `Linux`  
    OsType för avbildningen måste vara antingen **Windows** eller **Linux**.  
  - **OSUri**  
    Exempel: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Du kan ange en URI för blob-lagring för en `osDisk`.  

    Mer information finns i PowerShell-referens för den [Lägg till AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet och [New DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Lägga till en anpassad virtuell datoravbildning i Marketplace med hjälp av PowerShell

1. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).

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

3. Logga in på Azure Stack som en operatör. Anvisningar finns i [logga in på Azure Stack som operatör](azure-stack-powershell-configure-admin.md).

4. Skapa ett lagringskonto i globala Azure eller Azure Stack för att lagra din anpassade Virtuella datoravbildning. Instruktioner finns i [Snabbstart: ladda upp, ladda ned och lista blobar med Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Förbereda en Windows- eller Linux operativsystemavbildning i VHD-format (inte VHDX), överför avbildningen till ditt storage-konto och hämta URI där VM-avbildning kan hämtas med PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Valfritt) Du kan överföra en matris med datadiskar som en del av avbildningen. Skapa din datadiskar med hjälp av cmdleten New-DataDiskObject. Öppna PowerShell från en upphöjd kommandotolk och kör:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Öppna PowerShell med en upphöjd kommandotolk och kör:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Mer information om cmdlet: en Add-AzsPlatformimage och New-DataDiskObject cmdlet finns i Microsoft-PowerShell [dokumentation för Azure Stack-operatör modulen](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Ta bort en VM-avbildning med hjälp av PowerShell

När du behöver inte längre den avbildning av virtuell dator som du laddade upp, kan du ta bort det från Marketplace genom att använda följande cmdlet:

1. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).

2. Logga in på Azure Stack som en operatör.

3. Öppna PowerShell med en upphöjd kommandotolk och kör:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  Den **Remove-AzsPlatformImage** cmdlet anger värden som används av Azure Resource Manager-mallar för att referera till VM-avbildning. Värdena är:
  - **utgivare**  
    Exempel: `Canonical`  
    Utgivarens namn segment i VM-avbildning som användare använder när de distribuerar avbildningen. Ett exempel är **Microsoft**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **erbjudande**  
    Exempel: `UbuntuServer`  
    Erbjudandet namnet segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **WindowsServer**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **sku**  
    Exempel: `14.04.3-LTS`  
    Namnsegmentet SKU för avbildningen som användare använder när de distribuerar VM-avbildning. Ett exempel är **Datacenter2016**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
  - **version**  
    Exempel: `1.0.0`  
    Versionen av avbildningen som användare använder när de distribuerar VM-avbildning. Den här versionen är i formatet *\#.\#.\#*. Ett exempel är **1.0.0**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet.  
    
    Mer information om cmdleten Remove-AzsPlatformImage finns i Microsoft-PowerShell [dokumentation för Azure Stack-operatör modulen](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Nästa steg

[Etablera en virtuell dator](azure-stack-provision-vm.md)
