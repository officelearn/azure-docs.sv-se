---
title: Hantera variabler i Azure Automation
description: Den här artikeln beskriver hur du arbetar med variabler i Runbooks och DSC-konfigurationer.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9658175b0d42db9acfc94d39e4ab226bfe2cfc4b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187327"
---
# <a name="manage-variables-in-azure-automation"></a>Hantera variabler i Azure Automation

Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i ditt Automation-konto. Du kan hantera dem från Azure Portal, från PowerShell, i en Runbook eller i en DSC-konfiguration.

Automation-variabler är användbara i följande scenarier:

- Dela ett värde mellan flera Runbooks och DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma Runbook-eller DSC-konfiguration.

- Hantera ett värde som används av Runbooks eller DSC-konfigurationer från portalen eller från PowerShell-kommandoraden. Ett exempel är en uppsättning vanliga konfigurations objekt, till exempel en lista med namn på virtuella datorer, en särskild resurs grupp, ett AD-domännamn och mer.  

Azure Automation behåller variablerna och gör dem tillgängliga även om en Runbook-eller DSC-konfiguration Miss lyckas. Med det här beteendet kan en Runbook-eller DSC-konfiguration ange ett värde som sedan används av en annan Runbook eller av samma Runbook-eller DSC-konfiguration nästa gången den körs.

Azure Automation lagrar varje krypterad variabel på ett säkert sätt. När du skapar en variabel kan du ange dess kryptering och lagring genom att Azure Automation som en säker till gång. 

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Azure Automation lagrar nyckeln i systemhanterade Key Vault. Innan du lagrar en säker till gång läser Automation in nyckeln från Key Vault och använder den för att kryptera till gången. 

## <a name="variable-types"></a>Variabel typer

När du skapar en variabel med Azure Portal måste du ange en datatyp i list rutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Följande är variabel typer som är tillgängliga i Azure Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

Variabeln är inte begränsad till den angivna data typen. Du måste ange variabeln med hjälp av Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger `Not defined` anges värdet för variabeln till null. Du måste ange värdet med cmdleten [set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) eller den interna `Set-AutomationVariable` cmdleten.

Du kan inte använda Azure Portal för att skapa eller ändra värdet för en komplex variabel typ. Du kan dock ange ett värde av vilken typ som helst med hjälp av Windows PowerShell. Komplexa typer hämtas som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden i en enskild variabel genom att skapa en matris eller hash och spara den i variabeln.

>[!NOTE]
>Variabler för virtuella dator namn får bestå av högst 80 tecken. Variabler för resurs grupper får bestå av högst 90 tecken. Se [namngivnings regler och begränsningar för Azure-resurser](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell-cmdletar för att få åtkomst till variabler

Cmdletarna i följande tabell skapar och hanterar Automation-variabler med PowerShell. De levereras som en del av [AZ-modulerna](modules.md#az-modules).

| Cmdlet | Beskrivning |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Hämtar värdet för en befintlig variabel. Om värdet är en enkel typ hämtas samma typ. Om det är en komplex typ hämtas en `PSCustomObject` typ. <br>**Obs:**  Du kan inte använda denna cmdlet för att hämta värdet för en krypterad variabel. Det enda sättet att göra detta är genom att använda den interna `Get-AutomationVariable` cmdleten i en Runbook-eller DSC-konfiguration. Se [interna cmdlets för att få åtkomst till variabler](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Skapar en ny variabel och anger dess värde.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Tar bort en befintlig variabel.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Ställer in värdet för en befintlig variabel. |

## <a name="internal-cmdlets-to-access-variables"></a>Interna cmdletar för att få åtkomst till variabler

De interna cmdletarna i följande tabell används för att få åtkomst till variabler i dina runbooks och DSC-konfigurationer. Dessa cmdletar levereras med den globala modulen `Orchestrator.AssetManagement.Cmdlets` . Mer information finns i [interna cmdletar](modules.md#internal-cmdlets).

| Intern cmdlet | Beskrivning |
|:---|:---|
|`Get-AutomationVariable`|Hämtar värdet för en befintlig variabel.|
|`Set-AutomationVariable`|Ställer in värdet för en befintlig variabel.|

> [!NOTE]
> Undvik att använda variabler i- `Name` parametern i `Get-AutomationVariable` en RUNBOOK eller DSC-konfiguration. Användningen av variabler kan komplicera identifieringen av beroenden mellan Runbooks och automation-variabler i design läge.

`Get-AutomationVariable`fungerar inte i PowerShell, men endast i en Runbook-eller DSC-konfiguration. Om du till exempel vill visa värdet för en krypterad variabel kan du skapa en Runbook för att hämta variabeln och sedan skriva den till utdataströmmen:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Python 2-funktioner för att få åtkomst till variabler

Funktionerna i följande tabell används för att få åtkomst till variabler i en python 2-Runbook.

|Python 2-funktioner|Beskrivning|
|:---|:---|
|`automationassets.get_automation_variable`|Hämtar värdet för en befintlig variabel. |
|`automationassets.set_automation_variable`|Ställer in värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="create-and-get-a-variable"></a>Skapa och hämta en variabel

>[!NOTE]
>Om du vill ta bort krypteringen för en variabel måste du ta bort variabeln och återskapa den som okrypterad.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Skapa och hämta en variabel med hjälp av Azure Portal

1. Från ditt Automation-konto klickar du på panelen **till gångar** , sedan på bladet **till gångar** och väljer **variabler**.
2. På panelen **variabler** väljer du **Lägg till en variabel**.
3. Slutför alternativen på bladet **ny variabel** och klicka sedan på **skapa** för att spara den nya variabeln.

> [!NOTE]
> När du har sparat en krypterad variabel kan den inte visas i portalen. Den kan bara uppdateras.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Skapa och hämta en variabel i Windows PowerShell

Din Runbook-eller DSC-konfiguration använder `New-AzAutomationVariable` cmdleten för att skapa en ny variabel och anger dess start värde. Om variabeln är krypterad ska anropet använda- `Encrypted` parametern. Skriptet kan hämta värdet för variabeln med hjälp av `Get-AzAutomationVariable` . 

>[!NOTE]
>Ett PowerShell-skript kan inte hämta ett krypterat värde. Det enda sättet att göra detta är att använda den interna `Get-AutomationVariable` cmdleten.

I följande exempel visas hur du skapar en sträng variabel och sedan returnerar dess värde.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

I följande exempel visas hur du skapar en variabel med en komplex typ och sedan hämtar dess egenskaper. I det här fallet används ett virtuellt dator objekt från [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>Text Runbook-exempel

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Hämta och ange ett enkelt värde från en variabel

I följande exempel visas hur du ställer in och hämtar en variabel i en text-Runbook. Det här exemplet förutsätter att du skapar heltals variabler med namnet `NumberOfIterations` och `NumberOfRunnings` och en String-variabel med namnet `SampleMessage` .

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Hämta och ange en variabel i en python 2-Runbook

Följande exempel visar hur du hämtar en variabel, ställer in en variabel och hanterar ett undantag för en icke-existerande variabel i en python 2-Runbook.

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

## <a name="graphical-runbook-examples"></a>Grafiska Runbook-exempel

I en grafisk Runbook kan du lägga till aktiviteter för de interna cmdletarna `Get-AutomationVariable` eller `Set-AutomationVariable` . Högerklicka bara på varje variabel i fönstret Bibliotek i den grafiska redigeraren och välj den aktivitet som du vill använda.

![Lägg till variabel på arbets ytan](../media/variables/runbook-variable-add-canvas.png)

Följande bild visar exempel aktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk Runbook. I det här exemplet har aktiviteten för att `Get-AzVM` Hämta en enda virtuell Azure-dator och sparar dator namnet i en befintlig Automation-sträng-variabel. Det spelar ingen roll om [länken är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#use-links-for-workflow) eftersom koden endast förväntar sig ett enskilt objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdletar som används för att få åtkomst till variabler finns [i hantera moduler i Azure Automation](modules.md).
* Allmän information om Runbooks finns [i Runbook-körning i Azure Automation](../automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns i [Översikt över Azure Automation tillstånds konfiguration](../automation-dsc-overview.md).
