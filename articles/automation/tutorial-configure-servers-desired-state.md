---
title: Konfigurera servrar till önskade tillstånd och hantera drift med Azure Automation
description: Självstudiekurs - Hantera serverkonfigurationer med Azure Automation State Configuration
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678696"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurera servrar efter önskat tillstånd och hantera drift

Med Azure Automation State Configuration kan du ange konfigurationer för dina servrar och se till att dessa servrar är i angivet tillstånd över tiden.

> [!div class="checklist"]
> - Ombord på en virtuell dator som ska hanteras av Azure Automation DSC
> - Ladda upp en konfiguration till Azure Automation
> - Kompilera en konfiguration till en nodkonfiguration
> - Tilldela en nodkonfiguration till en hanterad nod
> - Kontrollera kompatibilitetsstatus för en hanterad nod

För den här självstudien använder vi en enkel [DSC-konfiguration](/powershell/scripting/dsc/configurations/configurations) som säkerställer att IIS installeras på den virtuella datorn.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En virtuell virtuell azure Resource Manager -dator (inte klassisk) som kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns [i Skapa din första virtuella Windows-dator i Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Förtrogenhet med dsc (Desired State Configuration). Information om DSC finns i [Översikt över windows powershell-önskad tillståndskonfiguration](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Stöd för partiella konfigurationer

Azure Automation State Configuration stöder användning av [partiella konfigurationer](/powershell/scripting/dsc/pull-server/partialconfigs). I det här fallet är DSC konfigurerad för att hantera flera konfigurationer oberoende av varandra och varje konfiguration hämtas från Azure Automation. Endast en konfiguration kan dock tilldelas en nod per automation-konto. Det innebär att om du använder två konfigurationer för en nod behöver du två Automation-konton.

Mer information om hur du registrerar en partiell konfiguration från en pull-tjänst finns i dokumentationen för [partiella konfigurationer](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Mer information om hur team kan samarbeta för att hantera servrar som använder konfiguration som kod finns [i Förstå DSC:s roll i en CI/CD Pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med [cmdleten Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration till Azure Automation


Skriv följande i en textredigerare och spara det lokalt som **TestConfig.ps1**.

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
> I mer avancerade scenarier där du kräver att flera moduler importeras som tillhandahåller `Import-DscResource` DSC-resurser kontrollerar du att varje modul har en unik linje i konfigurationen.

Anropa cmdleten [Import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) för att ladda upp konfigurationen till ditt Automation-konto.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en nodkonfiguration

En DSC-konfiguration måste kompileras till en nodkonfiguration innan den kan tilldelas en nod. Se [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

Anropa [cmdleten Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) för `TestConfig` att kompilera `TestConfig.WebServer` konfigurationen till en nodkonfiguration som namnges i ditt Automation-konto.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrera en virtuell dator som ska hanteras av tillståndskonfiguration

Du kan använda Azure Automation State Configuration för att hantera virtuella Azure-datorer (både Classic och Resource Manager), lokala virtuella datorer, Linux-datorer, VIRTUELLA AWS-datorer och lokala fysiska datorer. I det här avsnittet beskriver vi hur du bara registrerar virtuella Azure Resource Manager-datorer. Information om hur du registrerar andra typer av datorer finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).

Anropa [cmdleten Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) för att registrera din virtuella dator med Azure Automation State Configuration som en hanterad nod. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Ange inställningar för konfigurationsläge

Använd [cmdleten Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) för att registrera en virtuell dator som en hanterad nod och ange konfigurationsegenskaper. Du kan till exempel ange att datorns tillstånd endast ska `ApplyOnly` användas en gång `ConfigurationMode` genom att ange egenskapens värde. Tillståndskonfigurationen försöker inte tillämpa konfigurationen efter den första kontrollen.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Du kan också ange hur ofta DSC `ConfigurationModeFrequencyMins` kontrollerar konfigurationstillståndet med hjälp av egenskapen. Mer information om DSC-konfigurationsinställningar finns i [Konfigurera Den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nodkonfiguration till en hanterad nod

Nu kan vi tilldela den kompilerade nodkonfigurationen till den virtuella datorn som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Detta tilldelar nodkonfigurationen som namnges `TestConfig.WebServer` till `DscVm`den registrerade DSC-noden . Som standard kontrolleras DSC-noden för kompatibilitet med nodkonfigurationen var 30:e minut. Information om hur du ändrar intervallet för efterlevnadskontroll finns i [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrollera kompatibilitetsstatus för en hanterad nod

Du kan hämta rapporter om efterlevnadsstatus för en hanterad nod med cmdleten [Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Ta bort noder från tjänsten

När du lägger till en nod i Azure Automation State Configuration är inställningarna i Local Configuration Manager inställda på att registrera sig med tjänsten och hämta konfigurationer och nödvändiga moduler för att konfigurera datorn.
Om du väljer att ta bort noden från tjänsten kan du göra det med antingen Azure-portalen eller Az-cmdlets.

> [!NOTE]
> Om du avregistrerar en nod från tjänsten anges bara inställningarna för Lokal konfigurationshanteraren så att noden inte längre ansluter till tjänsten.
> Detta påverkar inte den konfiguration som för närvarande tillämpas på noden.
> Om du vill ta bort den aktuella konfigurationen använder du [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) eller tar bort den lokala konfigurationsfilen (det här är det enda alternativet för Linux-noder).

### <a name="azure-portal"></a>Azure Portal

Från Azure Automation klickar du på **Tillståndskonfiguration (DSC)** i innehållsförteckningen.
Klicka på **Noder nästa** gång om du vill visa listan över noder som är registrerade hos tjänsten.
Klicka på namnet på den nod som du vill ta bort.
Klicka på **Avregistrera**i nodvyn som öppnas.

### <a name="powershell"></a>PowerShell

Om du vill avregistrera en nod från Azure Automation State Configuration-tjänsten med PowerShell följer du dokumentationen för cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kontrollerar nas inbyggda noder finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md)
