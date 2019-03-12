---
title: Konfigurera servrar till önskade tillstånd och hantera drift med Azure Automation
description: Självstudie – hantera serverkonfigurationer med Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 582533d23757de748b9cc7d40e45acc00240d384
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570325"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurera servrar till önskade tillstånd och hantera drift

Tillståndskonfiguration för Azure Automation kan du ange konfigurationer för dina servrar och se till att dessa servrar är i det angivna tillståndet över tid.

> [!div class="checklist"]
> - Publicera en virtuell dator som ska hanteras av Azure Automation DSC
> - Ladda upp en konfiguration till Azure Automation
> - Kompilera en konfiguration till en nodkonfiguration
> - Tilldela en nodkonfiguration till en hanterad nod
> - Kontrollera kompatibilitetsstatusen för en hanterad nod

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En Azure Resource Manager-VM (inte klassisk) som kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell-Modulversion 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Liknar processen med Desired State Configuration (DSC). Läs om hur DSC [Windows PowerShell Desired State Configuration-översikt](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration i Azure Automation

Den här självstudien använder vi en enkel DSC-konfiguration som ser till att IIS är installerat på den virtuella datorn.

Information om DSC-konfigurationer finns i [DSC-konfigurationer](/powershell/dsc/configurations).

I en textredigerare skriver du följande och sparar lokalt som `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Anropa den `Import-AzureRmAutomationDscConfiguration` cmdlet för att ladda upp konfigurationen i ditt Automation-konto:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en nodkonfiguration

En DSC-konfiguration måste kompileras till en nodkonfiguration innan den kan tilldelas till en nod.

Läs om hur kompilera konfigurationer [DSC-konfigurationer](/powershell/dsc/configurations).

Anropa den `Start-AzureRmAutomationDscCompilationJob` cmdlet för att kompilera den `TestConfig` konfiguration till en nodkonfiguration:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Detta skapar en nodkonfiguration med namnet `TestConfig.WebServer` i ditt Automation-konto.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrera en virtuell dator som ska hanteras av State Configuration

Du kan använda Azure Automation State Configuration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella datorer i AWS och lokala fysiska datorer. I det här avsnittet beskriver vi hur du registrerar endast Azure Resource Manager-VM. Information om hur du registrerar andra typer av datorer finns i [konfigurera datorer för hantering av Azure Automation-Tillståndskonfiguration](automation-dsc-onboarding.md).

Anropa den `Register-AzureRmAutomationDscNode` cmdlet för att registrera den virtuella datorn med Azure Automation State Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Detta registrerar den angivna virtuella datorn som en hanterad nod i State Configuration.

### <a name="specify-configuration-mode-settings"></a>Ange konfigurationsinställningar för läge

När du registrerar en virtuell dator som en hanterad nod kan du också ange egenskaperna för konfigurationen. Du kan till exempel ange att tillståndet för datorn är tillämpas bara en gång (DSC försöker inte att tillämpa konfigurationen efter den inledande kontrollen) genom att ange `ApplyOnly` som värde för den **ConfigurationMode** egenskap :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Du kan också ange hur ofta DSC kontrollerar konfigurationstillståndet med hjälp av den **ConfigurationModeFrequencyMins** egenskapen:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Läs mer om hur du anger konfigurationsegenskaper för en hanterad nod [registrera AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Läs mer om DSC-konfigurationsinställningar, [konfigurerar den lokala Konfigurationshanteraren](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nodkonfiguration till en hanterad nod

Nu kan vi tilldela kompilerade nodkonfiguration till den virtuella datorn som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Den här koden tilldelar nodkonfiguration med namnet `TestConfig.WebServer` till den registrerade DSC-noden med namnet `DscVm`.
Som standard kontrolleras DSC-nod för kompatibilitet med nodkonfiguration var 30: e minut.
Information om hur du ändrar intervallet för kontroll av efterlevnad finns i [konfigurerar den lokala Konfigurationshanteraren](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrollera kompatibilitetsstatusen för en hanterad nod

Du kan få rapporter om kompatibilitetsstatusen för en hanterad nod genom att anropa den `Get-AzureRmAutomationDscNodeReport` cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs hur för att publicera noder i [konfigurera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)
