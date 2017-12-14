---
title: "Lägga till en VM-avbildning i Azure stapel | Microsoft Docs"
description: "Lägg till din organisations anpassade Windows eller Linux VM bild för klienter att använda."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 6c18debd022f0f233b52d81899e8edd7cf1e0456
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Göra en virtuell datoravbildning tillgängligt i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

I Azure-stacken kan operatörer göra anpassade virtuella datoravbildningar tillgängliga för användarna. Dessa avbildningar kan refereras av Azure Resource Manager-mallar eller lägga till dem i Azure Marketplace-Gränssnittet som ett Marketplace-objekt. 

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Lägg till en VM-avbildning till Marketplace med hjälp av PowerShell

Kör följande krav, antingen från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).  

2. Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  

3. Förbereda en Windows- eller Linux virtuell hårddisk operativsystemavbildning i VHD-format (Använd inte VHDX-format).
   
   * För Windows-avbildningar för instruktioner om hur du förbereder avbildningen, se [ladda upp en Windows VM-avbildning till Azure för Resource Manager distributioner](../virtual-machines/windows/upload-generalized-managed.md).
   * Linux-avbildningar, se [distribuera Linux virtuella datorer på Azure-stacken](azure-stack-linux.md). Slutför stegen för att förbereda avbildningen eller Använd en befintlig Azure-stacken Linux-avbildning som beskrivs i artikeln.  

Om du vill lägga till bilden på Azure Marketplace för stacken, gör du följande:

1. Importera Connect och ComputeAdmin modulerna:
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. Logga in på Azure Stack-miljö. Kör något av följande skript, beroende på om du har distribuerat Azure Stack-miljö med hjälp av Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS). (Ersätt Azure AD `tenantName`, `GraphAudience` slutpunkt, och `ArmEndpoint` värden för att avspegla din miljö konfiguration.)

    * **Azure Active Directory**. Använd följande cmdlet:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"
      
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

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

   * **Active Directory Federation Services**. Använd följande cmdlet:
    
        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin 

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID 
        ```
    
3. Lägga till VM-avbildning genom att anropa den `Add-AzsVMImage` cmdlet. I den `Add-AzsVMImage` cmdlet, ange `osType` som Windows- eller Linux. Inkludera utgivare, erbjudande, SKU och version för VM-avbildning. Information om tillåtna parametrar finns [parametrar](#parameters). Parametrarna som används av Azure Resource Manager-mallar för att referera till den Virtuella datoravbildningen. I följande exempel anropar skriptet:
     
  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

Kommandot gör följande:

* Autentiserar till Azure Stack-miljö.
* Överför den lokala virtuella Hårddisken till en tillfällig nyligen skapade lagringskontot.
* Lägger till VM-avbildning till Virtuella datoravbildningens centrallager.
* Skapar en Marketplace-objektet.

Kontrollera att kommandot har körts utan problem, i portalen, gå till Marketplace. Verifiera att VM-avbildning är tillgängliga i den **virtuella datorer** kategori.

![VM-avbildning som har lagts till](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-by-using-powershell"></a>Ta bort en VM-avbildning med hjälp av PowerShell

När du behöver inte längre avbildningen av den virtuella dator som du överfört, kan du ta bort den från Marketplace med hjälp av följande cmdlet:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Parametrar

| Parameter | Beskrivning |
| --- | --- |
| **Publisher** |Utgivarens namn segment i VM-avbildning som användare använder när de distribuerar avbildningen. Ett exempel är **Microsoft**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet. |
| **erbjudande** |Erbjudandet namnet segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **WindowsServer**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet. |
| **SKU** |SKU namn segment i VM-avbildning som användare använder när de distribuerar VM-avbildning. Ett exempel är **Datacenter2016**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet. |
| **version** |Versionen av VM-avbildning som användare använder när de distribuerar VM-avbildning. Den här versionen är i formatet *\#.\#.\#*. Ett exempel är **1.0.0**. Ta inte med ett blanksteg eller andra specialtecken i det här fältet. |
| **osType** |OsType bildens måste vara antingen **Windows** eller **Linux**. |
| **osDiskLocalPath** |Den lokala sökvägen till VHD som överförs som en VM-avbildning till Azure-stacken för OS-disken. |
| **dataDiskLocalPaths** |En valfri matris med lokala sökvägar för datadiskar som kan överföras som en del av VM-avbildning. |
| **CreateGalleryItem** |En boolesk flagga som avgör om du vill skapa ett objekt i Marketplace. Som standard anges till **SANT**. |
| **Rubrik** |Visningsnamnet för Marketplace-objektet. Som standard anges till den `Publisher-Offer-Sku` värdet för VM-avbildning. |
| **Beskrivning** |Beskrivning av Marketplace-objektet. |
| **Plats** |Den plats där VM-avbildning måste publiceras. Det här värdet är som standard **lokala**.|
| **osDiskBlobURI** |(Valfritt) Det här skriptet kan också användas med en URI för Blob-lagring för `osDisk`. |
| **dataDiskBlobURIs** |(Valfritt) Det här skriptet accepterar också en matris med Blob storage URI: er för att lägga till diskar i avbildningen. |

## <a name="add-a-vm-image-through-the-portal"></a>Lägg till en VM-avbildning via portalen

> [!NOTE]
> Med den här metoden måste du skapa Marketplace-objektet separat.

Bilder måste kunna refereras av en URI för Blob-lagring. Förbereda en Windows- eller Linux operativsystemavbildning i VHD-format (inte VHDX) och överför avbildningen till ett lagringskonto i Azure eller Azure-stacken. Om avbildningen har redan överförts till Blob-lagring i Azure eller Azure-grupp, kan du hoppa över steg 1.

1. [Ladda upp en Windows VM-avbildning till Azure för Resource Manager distributioner](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) eller för en Linux-avbildning, följ instruktionerna i [distribuera Linux virtuella datorer på Azure-stacken](azure-stack-linux.md). Innan du laddar upp avbildningen är det viktigt att beakta följande faktorer:

   * Det är mer effektivt att ladda upp en bild till Azure-stacken Blob-lagring än till Azure Blob storage eftersom det tar mindre tid att push-avbildningen till Azure-stacken avbildningslagringsplatsen. 
   
   * När du överför den [Windows VM-avbildning](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), se till att ersätta den **inloggning till Azure** med den [konfigurera operatorn Stack Azure PowerShell-miljö](azure-stack-powershell-configure-admin.md) steg.  

   * Anteckna Blob storage URI: N där du laddar upp avbildningen. URI för Blob-lagring har följande format:  *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * Om du vill göra blob anonymt tillgänglig, går du till kontot lagringsblobbehållare där VM-avbildning VHD överfördes. Välj **Blob**, och välj sedan **åtkomstprincip**. Alternativt kan kan du i stället Generera en signatur för delad åtkomst för behållaren och inkludera den som en del av blob-URI.

   ![Gå till storage-konto-blobbar](./media/azure-stack-add-vm-image/image1.png)

   ![Ange blobåtkomst till offentliga](./media/azure-stack-add-vm-image/image2.png)

2. Logga in på Azure-stacken som operator. Välj på menyn **fler tjänster** > **Resursproviders**. Markera **Compute** > **VM-avbildningar** > **Lägg till**.

3. Under **lägga till en VM-avbildning**, ange utgivare, erbjudande, SKU och version för avbildningen av virtuella datorn. Dessa namn segment finns i VM-avbildning i Resource Manager-mallar. Se till att välja den **osType** värdet på rätt sätt. För **OS-diskens Blob-URI**, anger du Blob-URI där bilden överfördes. Markera **skapa** att börja skapa VM-avbildning.
   
   ![Börja skapa avbildningen](./media/azure-stack-add-vm-image/image4.png)

   När avbildningen har skapats, VM avbildningen status ändras till **lyckades**.

4. Om du vill att den virtuella datoravbildningen mer tillgänglig för användarna i Användargränssnittet, är det en bra idé att [skapa Marketplace-objektet](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Nästa steg

[Etablera en virtuell dator](azure-stack-provision-vm.md)
