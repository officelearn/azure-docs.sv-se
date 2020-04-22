---
title: Hantera variabler i Azure Automation
description: Variabla tillgångar är värden som är tillgängliga för alla runbooks- och DSC-konfigurationer i Azure Automation.  I den här artikeln beskrivs information om variabler och hur du arbetar med dem i både text- och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732751"
---
# <a name="manage-variables-in-azure-automation"></a>Hantera variabler i Azure Automation

Variabla tillgångar är värden som är tillgängliga för alla runbooks- och DSC-konfigurationer i ditt Automation-konto. Du kan hantera dem från Azure-portalen, från PowerShell, i en runbook eller i en DSC-konfiguration.

Automatiseringsvariabler är användbara för följande scenarier:

- Dela ett värde mellan flera runbooks- eller DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma runbook eller DSC-konfiguration.

- Hantera ett värde som används av runbooks- eller DSC-konfigurationer från portalen eller från PowerShell-kommandoraden. Ett exempel är en uppsättning vanliga konfigurationsobjekt, till exempel en specifik lista med VM-namn, en viss resursgrupp, ett AD-domännamn med mera.  

Azure Automation beständiga variabler och gör dem tillgängliga även om en runbook- eller DSC-konfiguration misslyckas. Med det här beteendet kan en runbook- eller DSC-konfiguration ange ett värde som sedan används av en annan runbook, eller av samma runbook- eller DSC-konfiguration nästa gång den körs.

Azure Automation lagrar varje krypterad variabel på ett säkert sätt. När du skapar en variabel kan du ange dess kryptering och lagring av Azure Automation som en säker tillgång. Andra säkra resurser inkluderar autentiseringsuppgifter, certifikat och anslutningar. Azure Automation krypterar dessa tillgångar och lagrar dem med en unik nyckel som genereras för varje Automation-konto. Nyckeln lagras i ett systemhanterade Key Vault. Innan du lagrar en säker tillgång läser Azure Automation in nyckeln från Key Vault och använder den sedan för att kryptera tillgången. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Variabla typer

När du skapar en variabel med Azure-portalen måste du ange en datatyp från listrutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Följande är variabla typer som är tillgängliga i Azure Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

Variabeln är inte begränsad till den angivna datatypen. Du måste ange variabeln med Windows PowerShell om du vill ange ett värde av en annan typ. Om du `Not defined`anger är variabelns värde inställt på Null. Du måste ange värdet med cmdleten [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) eller `Set-AutomationVariable` aktiviteten.

Du kan inte använda Azure-portalen för att skapa eller ändra värdet för en komplex variabeltyp. Du kan dock ange ett värde av alla typer med Windows PowerShell. Komplexa typer hämtas som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden till en enda variabel genom att skapa en matris eller hashtable och spara den i variabeln.

>[!NOTE]
>VM-namnvariabler kan vara högst 80 tecken. Resursgruppsvariabler kan vara högst 90 tecken. Se [Namngivningsregler och begränsningar för Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>PowerShell-cmdlets som skapar och hanterar variabla tillgångar

För Az-modulen används cmdlets i följande tabell för att skapa och hantera variabela resurser för Automation med Windows PowerShell. De levereras som en del av [Az.Automation-modulen](/powershell/azure/overview), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdlet | Beskrivning |
|:---|:---|
|[Få-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Hämtar värdet för en befintlig variabel. Du kan inte använda den här cmdleten för att hämta värdet för en krypterad variabel. Det enda sättet att göra `Get-AutomationVariable` detta är att använda aktiviteten i en runbook eller DSC-konfiguration. |
|[Ny-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Skapar en ny variabel och anger dess värde.|
|[Ta bort-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Tar bort en befintlig variabel.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Ställer in värdet för en befintlig variabel. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Aktiviteter för att komma åt variabler i runbooks- och DSC-konfigurationer

Aktiviteterna i följande tabell används för att komma åt variabler i runbooks- och DSC-konfigurationer. Cmdlets för dessa aktiviteter kommer `Orchestrator.AssetManagement.Cmdlets`med den globala modulen .

| Aktivitet | Beskrivning |
|:---|:---|
|`Get-AutomationVariable`|Hämtar värdet för en befintlig variabel.|
|`Set-AutomationVariable`|Ställer in värdet för en befintlig variabel.|

> [!NOTE]
> Undvik att använda `Name` variabler `Get-AutomationVariable` i parametern i en runbook- eller DSC-konfiguration. Användning av den här parametern kan försvåra identifieringen av beroenden mellan runbooks- eller DSC-konfigurationer och automatiseringsvariabler vid designtillfället.

Observera `Get-AutomationVariable` att det inte fungerar i PowerShell, men bara i en runbook- eller DSC-konfiguration. Om du till exempel vill visa värdet för en krypterad variabel kan du skapa en runbook för att hämta variabeln och sedan skriva den till utdataströmmen:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funktioner för att komma åt variabler i Python 2-runbooks

Funktionerna i följande tabell används för att komma åt variabler i en Python 2-runbook.

|Python 2-funktioner|Beskrivning|
|:---|:---|
|`automationassets.get_automation_variable`|Hämtar värdet för en befintlig variabel. |
|`automationassets.set_automation_variable`|Ställer in värdet för en befintlig variabel. |

> [!NOTE]
> Du måste `automationassets` importera modulen högst upp i Python-runbooken för att komma åt tillgångsfunktionerna.

## <a name="working-with-automation-variables"></a>Arbeta med automationsvariabler

>[!NOTE]
>Om du vill ta bort krypteringen för en variabel måste du ta bort variabeln och återskapa den som okrypterad.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Skapa en ny variabel med Azure-portalen

1. I ditt Automation-konto klickar du på panelen **Tillgångar,** sedan **bladet Tillgångar** och väljer **Variabler**.
2. På panelen **Variabler** väljer du **Lägg till en variabel**.
3. Slutför alternativen på bladet **Ny variabel** och klicka sedan på **Skapa** för att spara den nya variabeln.

> [!NOTE]
> När du har sparat en krypterad variabel kan den inte visas i portalen. Den kan bara uppdateras.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Skapa och använda en variabel i Windows PowerShell

Ett PowerShell-skript `New-AzAutomationVariable` använder cmdlet, eller dess AzureRM-modul motsvarande, för att skapa en ny variabel och ange dess ursprungliga värde. Om variabeln är krypterad ska `Encrypted` anropet använda parametern.

Skriptet hämtar värdet för variabeln med [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Om värdet är en enkel typ hämtas cmdleten samma typ. Om det är en komplex `PSCustomObject` typ hämtas en typ.

>[!NOTE]
>Ett PowerShell-skript kan inte hämta ett krypterat värde. Det enda sättet att göra `Get-AutomationVariable` detta är att använda en aktivitet i en runbook eller DSC-konfiguration.

I följande exempel visas hur du skapar en variabel av typen Sträng och sedan returnerar dess värde.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

I följande exempel visas hur du skapar en variabel med en komplex typ och sedan hämtar dess egenskaper. I det här fallet används ett virtuellt datorobjekt från [Get-AzVM.](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Skapa och använda en variabel i en runbook- eller DSC-konfiguration

Det enda sättet att skapa en ny variabel inifrån en runbook eller DSC-konfiguration är att använda `New-AzAutomationVariable` cmdlet eller dess AzureRM-modul motsvarande. Skriptet använder den här cmdleten för att ange variabelns ursprungliga värde. Skriptet kan sedan hämta `Get-AzAutomationVariable`värdet med . Om värdet är en enkel typ hämtas samma typ. Om det är en komplex `PSCustomObject` typ hämtas en typ.

>[!NOTE]
>Det enda sättet att hämta ett krypterat värde är att använda `Get-AutomationVariable` aktiviteten i runbook- eller DSC-konfigurationen. 

### <a name="textual-runbook-samples"></a>Textbaserade runbook-exempel

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Ange och hämta ett enkelt värde från en variabel

Följande exempelkommandon visar hur du ställer in och hämtar en variabel i en textföljdskörning. Det här exemplet förutsätter att heltalsvariabler skapas `NumberOfIterations` och `NumberOfRunnings` en strängvariabel med namnet `SampleMessage`.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Ange och hämta en variabel i en Python 2-runbook

Följande exempel visar hur du använder en variabel, anger en variabel och hanterar ett undantag för en obefintlig variabel i en Python 2-runbook.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

I en grafisk runbook kan `Get-AutomationVariable` du `Set-AutomationVariable` lägga till aktiviteten eller aktiviteten. Högerklicka bara på variabeln i biblioteksfönstret i den grafiska redigeraren och välj den aktivitet du vill använda.

![Lägga till variabel på arbetsyta](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Ange värden i en variabel

Följande bild visar exempelaktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk runbook. I det `Get-AzVM` här exemplet hämtar du en enda virtuell Azure-dator och sparar datornamnet på en befintlig automationssträngvariabel. Det spelar ingen roll om [länken är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#links-and-workflow) eftersom koden bara förväntar sig ett enda objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du kopplar aktiviteter i grafisk redigering finns [i Länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow).
- Kom igång med grafiska runbooks finns i [Min första grafiska runbook](../automation-first-runbook-graphical.md).
