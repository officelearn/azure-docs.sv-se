---
title: Konfigurera servrar till önskade tillstånd och hantera drift med Azure Automation
description: Självstudiekurs - Hantera serverkonfigurationer med Azure Automation State Configuration
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416597"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurera servrar efter önskat tillstånd och hantera drift

Med Azure Automation State Configuration kan du ange konfigurationer för dina servrar och se till att dessa servrar är i angivet tillstånd över tiden.

> [!div class="checklist"]
> - Ombord på en virtuell dator som ska hanteras av Azure Automation DSC
> - Ladda upp en konfiguration till Azure Automation
> - Kompilera en konfiguration till en nodkonfiguration
> - Tilldela en nodkonfiguration till en hanterad nod
> - Kontrollera kompatibilitetsstatus för en hanterad nod

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

- Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
- En virtuell virtuell azure Resource Manager -dator (inte klassisk) som kör Windows Server 2008 R2 eller senare. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Förtrogenhet med dsc (Desired State Configuration). Information om DSC finns i [Konfigurationsöversikt över önskat tillstånd för Windows PowerShell](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration till Azure Automation

För den här självstudien använder vi en enkel DSC-konfiguration som säkerställer att IIS installeras på den virtuella datorn.

Information om DSC-konfigurationer finns i [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

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
> I mer avancerade scenarier där du kräver att flera moduler importeras som tillhandahåller `Import-DscResource` DSC-resurser kontrollerar du att varje modul har en unik linje i konfigurationen.

Anropa `Import-AzureRmAutomationDscConfiguration` cmdleten för att ladda upp konfigurationen till ditt Automation-konto:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en nodkonfiguration

En DSC-konfiguration måste kompileras till en nodkonfiguration innan den kan tilldelas en nod.

Information om hur du kompilerar konfigurationer finns i [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

Anropa `Start-AzureRmAutomationDscCompilationJob` cmdlet för att `TestConfig` kompilera konfigurationen till en nodkonfiguration:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Detta skapar en nodkonfiguration som namnges `TestConfig.WebServer` i ditt Automation-konto.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrera en virtuell dator som ska hanteras av tillståndskonfiguration

Du kan använda Azure Automation State Configuration för att hantera virtuella Azure-datorer (både Classic och Resource Manager), lokala virtuella datorer, Linux-datorer, VIRTUELLA AWS-datorer och lokala fysiska datorer. I det här avsnittet beskriver vi hur du bara registrerar virtuella Azure Resource Manager-datorer. Information om hur du registrerar andra typer av datorer finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).

Anropa `Register-AzureRmAutomationDscNode` cmdlet för att registrera din virtuella dator med Azure Automation State Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Detta registrerar den angivna virtuella datorn som en hanterad nod i tillståndskonfiguration.

### <a name="specify-configuration-mode-settings"></a>Ange inställningar för konfigurationsläge

När du registrerar en virtuell dator som en hanterad nod kan du också ange egenskaper för konfigurationen. Du kan till exempel ange att datorns tillstånd ska användas endast en gång (DSC försöker inte tillämpa `ApplyOnly` konfigurationen efter den första kontrollen) genom att ange som värdet för **ConfigurationMode-egenskapen:**

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Du kan också ange hur ofta DSC kontrollerar konfigurationstillståndet med hjälp av egenskapen **ConfigurationModeFrequencyMins:**

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Mer information om hur du anger konfigurationsegenskaper för en hanterad nod finns i [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Mer information om DSC-konfigurationsinställningar finns i [Konfigurera Den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nodkonfiguration till en hanterad nod

Nu kan vi tilldela den kompilerade nodkonfigurationen till den virtuella datorn som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Detta tilldelar nodkonfigurationen som namnges `TestConfig.WebServer` till `DscVm`den registrerade DSC-noden med namnet .
Som standard kontrolleras DSC-noden för kompatibilitet med nodkonfigurationen var 30:e minut.
Information om hur du ändrar intervallet för efterlevnadskontroll finns i [Konfigurera den lokala konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Arbeta med partiella konfigurationer

Azure Automation State Configuration stöder användning av [partiella konfigurationer](/powershell/scripting/dsc/pull-server/partialconfigs).
I det här fallet är DSC konfigurerad för att hantera flera konfigurationer oberoende av varandra och varje konfiguration hämtas från Azure Automation.
Endast en konfiguration kan dock tilldelas en nod per automation-konto.
Det innebär att om du använder två konfigurationer för en nod behöver du två automatiseringskonton.

Mer information om hur du registrerar en partiell konfiguration från pull-tjänsten finns i dokumentationen för [partiella konfigurationer](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Mer information om hur team kan samarbeta för att hantera servrar som använder konfiguration som kod finns [i Förstå DSC:s roll i en CI/CD Pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrollera kompatibilitetsstatus för en hanterad nod

Du kan hämta rapporter om efterlevnadsstatus för `Get-AzureRmAutomationDscNodeReport` en hanterad nod genom att anropa cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Ta bort noder från tjänsten

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

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Mer information om hur du 2019:er kontrollerar [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md)
