---
title: Konfigurera servrar till önskade tillstånd och hantera drift med Azure Automation
description: Självstudie – hantera serverkonfigurationer med Azure Automation tillstånds konfiguration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 0d877dafc4ab4f8ec4edb0a94450fa9c5dfcd0bb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850235"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurera servrar till önskat tillstånd och hantera drift

Med Azure Automation tillstånds konfiguration kan du ange konfigurationer för dina servrar och se till att servrarna är i det angivna läget över tid.

> [!div class="checklist"]
> - Publicera en virtuell dator som ska hanteras av Azure Automation DSC
> - Ladda upp en konfiguration till Azure Automation
> - Kompilera en konfiguration till en Node-konfiguration
> - Tilldela en nods konfiguration till en hanterad nod
> - Kontrol lera status för efterlevnad för en hanterad nod

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En Azure Resource Manager virtuell dator (inte klassisk) som kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell modul version 3,6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Förtrogen med önskad tillstånds konfiguration (DSC). Information om DSC finns i [Windows PowerShell Desired State Configuration Overview](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration till Azure Automation

I den här självstudien använder vi en enkel DSC-konfiguration som garanterar att IIS är installerat på den virtuella datorn.

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

> [!NOTE]
> I mer avancerade scenarier där du kräver att flera moduler importeras som tillhandahåller DSC-resurser, se till att varje modul har en `Import-DscResource` unik rad i konfigurationen.

`Import-AzureRmAutomationDscConfiguration` Anropa cmdleten för att ladda upp konfigurationen till ditt Automation-konto:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en Node-konfiguration

En DSC-konfiguration måste kompileras till en noduppsättning innan den kan tilldelas till en nod.

Information om hur du kompilerar konfigurationer finns i [DSC-konfigurationer](/powershell/dsc/configurations).

Anropa cmdleten för att `TestConfig` kompilera konfigurationen till en Node-konfiguration: `Start-AzureRmAutomationDscCompilationJob`

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Detta skapar en Node-konfiguration `TestConfig.WebServer` med namnet i ditt Automation-konto.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrera en virtuell dator som ska hanteras av tillstånds konfiguration

Du kan använda Azure Automation tillstånds konfiguration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella AWS-datorer och lokala fysiska datorer. I det här avsnittet beskriver vi hur du registrerar endast Azure Resource Manager virtuella datorer. Information om hur du registrerar andra typer av datorer finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

`Register-AzureRmAutomationDscNode` Anropa cmdleten för att registrera din virtuella dator med Azure Automation tillstånds konfiguration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Detta registrerar den angivna virtuella datorn som en hanterad nod i tillstånds konfiguration.

### <a name="specify-configuration-mode-settings"></a>Ange inställningar för konfigurations läge

När du registrerar en virtuell dator som en hanterad nod kan du också ange egenskaper för konfigurationen. Du kan till exempel ange att datorns tillstånd bara ska tillämpas en gång (DSC försöker inte tillämpa konfigurationen efter den första kontrollen) genom att ange `ApplyOnly` som värde för egenskapen **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Du kan också ange hur ofta DSC ska kontrol lera konfigurations statusen med hjälp av egenskapen **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Mer information om hur du anger konfigurations egenskaper för en hanterad nod finns i [register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Mer information om konfigurations inställningar för DSC finns i [Konfigurera den lokala Configuration Manager](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nods konfiguration till en hanterad nod

Nu kan vi tilldela den kompilerade nodens konfiguration till den virtuella dator som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Det tilldelar noden konfigurationen `TestConfig.WebServer` till den registrerade DSC-noden med namnet. `DscVm`
DSC-noden kontrol leras som standard med nodens konfiguration var 30: e minut.
Information om hur du ändrar intervallet för kompatibilitetskontroll finns i [Konfigurera den lokala Configuration Manager](/PowerShell/DSC/metaConfig).

## <a name="working-with-partial-configurations"></a>Arbeta med ofullständiga konfigurationer

Azure Automation tillstånds konfiguration har stöd för användning av [partiella konfigurationer](/powershell/dsc/pull-server/partialconfigs).
I det här scenariot konfigureras DSC för att hantera flera konfigurationer oberoende och varje konfiguration hämtas från Azure Automation.
Men bara en konfiguration kan tilldelas till en nod per Automation-konto.
Det innebär att om du använder två konfigurationer för en nod behöver du två Automation-konton.

Mer information om hur du registrerar en del konfiguration från pull-tjänsten finns i dokumentationen för [del konfigurationer](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Mer information om hur team kan arbeta tillsammans med kollektivt hantera servrar med hjälp av konfiguration som kod finns i [förstå DSC-rollen i en CI/CD-pipeline](/powershell/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrol lera status för efterlevnad för en hanterad nod

Du kan få rapporter om kompatibilitetsstatus för en hanterad nod genom att `Get-AzureRmAutomationDscNodeReport` anropa cmdleten:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Tar bort noder från tjänsten

När du lägger till en nod i Azure Automation tillstånds konfiguration anges inställningarna i lokala Configuration Manager att registreras med tjänsten och hämta konfigurationer och obligatoriska moduler för att konfigurera datorn.
Om du väljer att ta bort noden från tjänsten kan du göra det med hjälp av antingen Azure Portal eller AZ-cmdletar.

> [!NOTE]
> När du avregistrerar en nod från tjänsten anges bara de lokala Configuration Manager inställningarna så att noden inte längre ansluter till tjänsten.
> Detta påverkar inte den konfiguration som för närvarande används på noden.
> Om du vill ta bort den aktuella konfigurationen använder du [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) eller tar bort den lokala konfigurations filen (detta är det enda alternativet för Linux-noder).

### <a name="azure-portal"></a>Azure Portal

Klicka på **tillstånds konfiguration (DSC)** i innehålls förteckningen från Azure Automation.
Klicka sedan på **noder** för att visa en lista över noder som är registrerade i tjänsten.
Klicka på namnet på den nod som du vill ta bort.
Klicka på avregistrera i vyn Node somöppnas.

### <a name="powershell"></a>PowerShell

Om du vill avregistrera en nod från Azure Automation tillstånds konfigurations tjänst med PowerShell följer du dokumentationen för cmdleten [unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Nästa steg

- För att komma igång, se [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md)
- Information om hur du kan publicera noder finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md)
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/azurerm.automation/#automation)
- För pris information, se [priser för Azure Automation tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
