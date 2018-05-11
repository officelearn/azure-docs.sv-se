---
title: Konfigurera servrar till önskade tillstånd och hantera drift med Azure Automation
description: Självstudiekurs – hantera serverkonfigurationer med Azure Automation DSC
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
manager: carmonm
ms.topic: article
ms.date: 09/25/2017
ms.openlocfilehash: a8533e6353e12ee6046eb4d22a3035ce9cd739d6
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurera servrar till önskade tillstånd och hantera drift

Azure Automation önskat tillståndskonfigurationen (DSC) kan du ange konfigurationer för servrarna och se till att dessa servrar finns i det angivna tillståndet över tid.



> [!div class="checklist"]
> * Publicera en virtuell dator som ska hanteras av Azure Automation DSC
> * Ladda upp en konfiguration till Azure Automation
> * Kompilera en konfiguration till en konfiguration av noden
> * Tilldela en nodkonfiguration till en hanterad nod
> * Kontrollera efterlevnadsstatus för för en hanterad nod

## <a name="prerequisites"></a>Förutsättningar

Den här kursen behöver du:

* Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
* En Azure Resource Manager VM (inte klassiskt) kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Azure PowerShell module 3,6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).
* Om du är bekant med DSC. Information om DSC finns [Windows PowerShell Desired Configuration översikt över](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration till Azure Automation

Den här självstudiekursen kommer använder vi en enkel DSC-konfiguration som garanterar att IIS installeras på den virtuella datorn.

Information om DSC-konfigurationer finns i [DSC-konfigurationer](https://docs.microsoft.com/powershell/dsc/configurations).

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

Anropa den `Import-AzureRmAutomationDscConfiguration` för att ladda upp konfigurationen i ditt Automation-konto:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en konfiguration av noden

DSC-konfigurationen måste kompileras till ett nodkonfiguration innan den kan tilldelas till en nod.

Information om kompilering konfigurationer finns [DSC-konfigurationer](https://docs.microsoft.com/powershell/dsc/configurations).

Anropa den `Start-AzureRmAutomationDscCompilationJob` för att kompilera den `TestConfig` konfiguration till en konfiguration av noden:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Detta skapar en konfiguration av noden med namnet `TestConfig.WebServer` i Automation-kontot.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Registrera en virtuell dator som ska hanteras av DSC

Du kan använda Azure Automation DSC för att hantera virtuella Azure-datorer (både klassiska och hanteraren för filserverresurser), lokala virtuella datorer, Linux-datorer, AWS virtuella datorer och lokala fysiska datorer. I det här avsnittet upp vi hur du registrerar endast Azure Resource Manager virtuella datorer.
Information om hur du registrerar andra typer av datorer finns i [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).

Anropa den `Register-AzureRmAutomationDscNode` cmdleten för att registrera den virtuella datorn med Azure Automation DSC.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

Detta registrerar den angivna virtuella datorn som en DSC-nod i Azure Automation-konto.

### <a name="specify-configuration-mode-settings"></a>Ange konfigurationsinställningar för läge

Du kan också ange egenskaper för konfiguration när du registrerar en virtuell dator som en hanterad nod.
Du kan till exempel ange att tillståndet för datorn är tillämpas bara en gång (DSC försöker inte att tillämpa konfigurationen efter den inledande kontrollen) genom att ange `ApplyOnly` som värde för den **ConfigurationMode** egenskapen :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Du kan även ange hur ofta DSC kontrollerar konfigurationen med hjälp av den **ConfigurationModeFrequencyMins** egenskapen:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Läs mer om hur du anger konfigurationsegenskaper för en hanterad nod [registrera AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Läs mer om DSC-konfigurationsinställningar, [konfigurera den lokala Configuration Manager](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nodkonfiguration till en hanterad nod

Nu kan vi tilldela kompilerade nodkonfiguration till den virtuella datorn som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Den här tilldelas nodkonfiguration med namnet `TestConfig.WebServer` till den registrerade DSC-noden med namnet `DscVm`.
Som standard kontrolleras DSC-noden för att uppfylla nodkonfiguration var 30: e minut.
Information om hur du ändrar intervallet för att kontrollera finns [konfigurera den lokala Configuration Manager](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrollera efterlevnadsstatus för för en hanterad nod

Du kan hämta rapporter om kompatibilitetsstatusen för DSC-nod genom att anropa den `Get-AzureRmAutomationDscNodeReport` cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Nästa steg

* Att lära dig hur du vill publicera noder som ska hanteras med Azure Automation DSC finns [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)
* Information om hur du använder Azure-portalen för att använda Automation DSC finns [komma igång med Azure Automation DSC](automation-dsc-getting-started.md)
* Läs om kompilering av DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilering konfigurationer i Azure Automation DSC](automation-dsc-compile.md)
* PowerShell-cmdlet-referens för Azure Automation DSC, se [Azure Automation DSC-cmdlets](/powershell/module/azurerm.automation/#automation)
* Information om priser finns [priser för Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
* Ett exempel på hur Azure Automation DSC i en pipeline för kontinuerlig distribution finns [kontinuerlig distribution IaaS virtuella datorer med hjälp av Azure Automation DSC och Chocolatey](automation-dsc-cd-chocolatey.md)
