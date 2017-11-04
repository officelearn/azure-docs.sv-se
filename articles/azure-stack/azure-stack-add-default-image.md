---
title: "Lägg till standard VM-avbildning i Azure-stacken Marketplace | Microsoft Docs"
description: "Lägg till Windows Server 2016 VM standardavbildningen Stack Azure Marketplace."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e5e7ab55800eccea98cbd71ae32bdc611ee3e961
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Lägg till Windows Server 2016 VM-avbildning i Azure-stacken Marketplace

Som standard finns inga avbildningar av virtuella datorer i Azure-stacken Marketplace. En Azure-stacken operator måste lägga till en bild Marketplace för användare att komma åt. Du kan lägga till Windows Server 2016-avbildningen på Azure Marketplace för stacken med någon av följande metoder:

* [Ladda ned avbildningen från Azure Marketplace](#add-the-image-by-downloading-it-from-the-azure-marketplace). Använd det här alternativet om du arbetar i ett scenario med anslutna och du har registrerat din Azure Stack-instans med Azure.

* [Lägga till avbildningen med hjälp av PowerShell](#add-the-image-by-using-powershell). Använd det här alternativet om du har distribuerat Azure Stack i ett scenario med frånkopplade eller i scenarier med begränsad anslutning.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Lägga till avbildningen genom att hämta från Azure Marketplace

1. Distribuera Azure-stacken och logga sedan in din Azure-stacken Development Kit.

2. Välj **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**. 

3. Hitta eller Sök efter den **Windows Server 2016 Datacenter – Eval** bild och välj sedan **hämta**.

   ![Ladda ned avbildningen från Azure](media/azure-stack-add-default-image/download-image.png)

När hämtningen är slutförd bilden är tillgänglig under **Marketplace Management**. Bilden är också tillgängligt under **virtuella datorer**.

## <a name="add-the-image-by-using-powershell"></a>Lägga till avbildningen med hjälp av PowerShell

### <a name="prerequisites"></a>Krav 

Kör följande krav, antingen från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  

2. Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  

3. På sidan Windows Server-utvärderingar [Hämta utvärderingsversionen för Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). När du uppmanas, Välj ISO-versionen av filen. Registrera sökvägen till nedladdningsplatsen, som används senare i stegen som beskrivs i den här artikeln. Det här steget kräver en Internetanslutning.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Lägga till avbildningen till stacken Azure Marketplace
   
1. Importera Azure Stack Connect och ComputeAdmin moduler med hjälp av följande kommandon:

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
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Lägg till Windows Server 2016-bild på Azure Marketplace för stacken. (Ersätt *fully_qualified_path_to_ISO* med sökvägen till Windows Server 2016 ISO som du hämtade.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Kontrollera att Windows Server 2016 VM-avbildning har den senaste kumulativa uppdateringen, inkluderar den `IncludeLatestCU` parameter när du kör den `New-AzsServer2016VMImage` cmdlet. Information om tillåtna parametrar för den `New-AzsServer2016VMImage` cmdlet, se [parametrar](#parameters). Det tar ungefär en timme att publicera bilden till stacken Azure Marketplace. 

## <a name="parameters"></a>Parametrar

|Nya AzsServer2016VMImage parametrar|Krävs?|Beskrivning|
|-----|-----|------|
|ISOPath|Ja|Den fullständiga sökvägen till den hämtade 2016 ISO-Avbildningen för Windows Server.|
|Net35|Nej|Installerar .NET 3.5-körningsmiljön på Windows Server 2016-avbildningen. Det här värdet är som standard **SANT**.|
|Version|Nej|Anger **Core**, **fullständig**, eller **både** avbildningar för Windows Server 2016. Det här värdet är som standard **fullständig**.|
|VHDSizeInMB|Nej|Anger storleken (i MB) för VHD-avbildningen som ska läggas till din Azure Stack-miljö. Det här värdet anges till 40 960 MB som standard.|
|CreateGalleryItem|Nej|Anger om en Marketplace-objektet ska skapas för Windows Server 2016-avbildningen. Det här värdet är som standard **SANT**.|
|location |Nej |Anger platsen där Windows Server 2016 avbildningen ska publiceras.|
|IncludeLatestCU|Nej|Gäller den senaste kumulativa uppdateringen för Windows Server 2016 för den nya virtuella Hårddisken.|
|CUUri |Nej |Anger Windows Server 2016 ackumulerade uppdateringen för att köras från en specifik URI. |
|CUPath |Nej |Anger Windows Server 2016 ackumulerade uppdateringen för att köras från en lokal sökväg. Det här alternativet är användbart om du har distribuerat Azure Stack-instans i en frånkopplad miljö.|

## <a name="next-steps"></a>Nästa steg

[Etablera en virtuell dator](azure-stack-provision-vm.md)
