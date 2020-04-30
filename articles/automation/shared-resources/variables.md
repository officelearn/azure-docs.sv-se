---
title: Hantera variabler i Azure Automation
description: Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i Azure Automation.  I den här artikeln beskrivs information om variabler och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732751"
---
# <a name="manage-variables-in-azure-automation"></a>Hantera variabler i Azure Automation

Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i ditt Automation-konto. Du kan hantera dem från Azure Portal, från PowerShell, i en Runbook eller i en DSC-konfiguration.

Automation-variabler är användbara i följande scenarier:

- Dela ett värde mellan flera Runbooks och DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma Runbook-eller DSC-konfiguration.

- Hantera ett värde som används av Runbooks eller DSC-konfigurationer från portalen eller från PowerShell-kommandoraden. Ett exempel är en uppsättning vanliga konfigurations objekt, till exempel en lista med namn på virtuella datorer, en särskild resurs grupp, ett AD-domännamn och mer.  

Azure Automation behåller variablerna och gör dem tillgängliga även om en Runbook-eller DSC-konfiguration Miss lyckas. Med det här beteendet kan en Runbook-eller DSC-konfiguration ange ett värde som sedan används av en annan Runbook eller av samma Runbook-eller DSC-konfiguration nästa gången den körs.

Azure Automation lagrar varje krypterad variabel på ett säkert sätt. När du skapar en variabel kan du ange dess kryptering och lagring genom att Azure Automation som en säker till gång. Andra säkra till gångar inkluderar autentiseringsuppgifter, certifikat och anslutningar. Azure Automation krypterar dessa till gångar och lagrar dem med hjälp av en unik nyckel som genereras för varje Automation-konto. Nyckeln lagras i systemhanterade Key Vault. Innan du lagrar en säker till gång läser Azure Automation in nyckeln från Key Vault och använder den för att kryptera till gången. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Variabel typer

När du skapar en variabel med Azure Portal måste du ange en datatyp i list rutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Följande är variabel typer som är tillgängliga i Azure Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

Variabeln är inte begränsad till den angivna data typen. Du måste ange variabeln med hjälp av Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger `Not defined`anges värdet för variabeln till null. Du måste ange värdet med cmdleten [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) eller `Set-AutomationVariable` aktiviteten.

Du kan inte använda Azure Portal för att skapa eller ändra värdet för en komplex variabel typ. Du kan dock ange ett värde av vilken typ som helst med hjälp av Windows PowerShell. Komplexa typer hämtas som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden i en enskild variabel genom att skapa en matris eller hash och spara den i variabeln.

>[!NOTE]
>Variabler för virtuella dator namn får bestå av högst 80 tecken. Variabler för resurs grupper får bestå av högst 90 tecken. Se [namngivnings regler och begränsningar för Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>PowerShell-cmdletar som skapar och hanterar variabel till gångar

I AZ-modulen används cmdletarna i följande tabell för att skapa och hantera Automation-variabel till gångar med Windows PowerShell. De levereras som en del av [modulen AZ. Automation](/powershell/azure/overview), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

| Cmdlet | Beskrivning |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Hämtar värdet för en befintlig variabel. Du kan inte använda denna cmdlet för att hämta värdet för en krypterad variabel. Det enda sättet att göra detta är genom att `Get-AutomationVariable` använda aktiviteten i en RUNBOOK eller DSC-konfiguration. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Skapar en ny variabel och anger dess värde.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Tar bort en befintlig variabel.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Ställer in värdet för en befintlig variabel. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Aktiviteter för att få åtkomst till variabler i Runbooks och DSC-konfigurationer

Aktiviteterna i följande tabell används för att få åtkomst till variabler i Runbooks och DSC-konfigurationer. Cmdletarna för dessa aktiviteter levereras med den globala modulen `Orchestrator.AssetManagement.Cmdlets`.

| Aktivitet | Beskrivning |
|:---|:---|
|`Get-AutomationVariable`|Hämtar värdet för en befintlig variabel.|
|`Set-AutomationVariable`|Ställer in värdet för en befintlig variabel.|

> [!NOTE]
> Undvik att använda variabler i `Name` -parametern `Get-AutomationVariable` i en Runbook eller DSC-konfiguration. Användningen av den här parametern kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och automation-variabler i design läge.

Observera att `Get-AutomationVariable` inte fungerar i PowerShell, men endast i en Runbook-eller DSC-konfiguration. Om du till exempel vill visa värdet för en krypterad variabel kan du skapa en Runbook för att hämta variabeln och sedan skriva den till utdataströmmen:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funktioner för att få åtkomst till variabler i python 2-Runbooks

Funktionerna i följande tabell används för att få åtkomst till variabler i en python 2-Runbook.

|Python 2-funktioner|Beskrivning|
|:---|:---|
|`automationassets.get_automation_variable`|Hämtar värdet för en befintlig variabel. |
|`automationassets.set_automation_variable`|Ställer in värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="working-with-automation-variables"></a>Arbeta med Automation-variabler

>[!NOTE]
>Om du vill ta bort krypteringen för en variabel måste du ta bort variabeln och återskapa den som okrypterad.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Skapa en ny variabel med hjälp av Azure Portal

1. Från ditt Automation-konto klickar du på panelen **till gångar** , sedan på bladet **till gångar** och väljer **variabler**.
2. På panelen **variabler** väljer du **Lägg till en variabel**.
3. Slutför alternativen på bladet **ny variabel** och klicka sedan på **skapa** för att spara den nya variabeln.

> [!NOTE]
> När du har sparat en krypterad variabel kan den inte visas i portalen. Den kan bara uppdateras.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Skapa och använda en variabel i Windows PowerShell

Ett PowerShell-skript använder `New-AzAutomationVariable` cmdleten eller dess AzureRM-modul som motsvarighet, för att skapa en ny variabel och ange dess start värde. Om variabeln är krypterad ska anropet använda- `Encrypted` parametern.

Skriptet hämtar värdet för variabeln med hjälp av [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Om värdet är en enkel typ, kommer cmdleten att hämta samma typ. Om det är en komplex typ hämtas en `PSCustomObject` typ.

>[!NOTE]
>Ett PowerShell-skript kan inte hämta ett krypterat värde. Det enda sättet att göra detta är att använda en `Get-AutomationVariable` aktivitet i en Runbook-eller DSC-konfiguration.

I följande exempel visas hur du skapar en variabel av typen sträng och sedan returnerar dess värde.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

I följande exempel visas hur du skapar en variabel med en komplex typ och sedan hämtar dess egenskaper. I det här fallet används ett virtuellt dator objekt från [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Skapa och använda en variabel i en Runbook-eller DSC-konfiguration

Det enda sättet att skapa en ny variabel från en Runbook eller DSC-konfiguration är att använda `New-AzAutomationVariable` cmdleten eller dess AzureRM-modul motsvarande. Skriptet använder denna cmdlet för att ange det ursprungliga värdet för variabeln. Skriptet kan sedan hämta värdet med hjälp av `Get-AzAutomationVariable`. Om värdet är en enkel typ hämtas samma typ. Om det är en komplex typ hämtas en `PSCustomObject` typ.

>[!NOTE]
>Det enda sättet att hämta ett krypterat värde är genom att `Get-AutomationVariable` använda aktiviteten i Runbook-eller DSC-konfigurationen. 

### <a name="textual-runbook-samples"></a>Text Runbook-exempel

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Ange och hämta ett enkelt värde från en variabel

Följande exempel kommandon visar hur du ställer in och hämtar en variabel i en text-Runbook. Det här exemplet förutsätter att du skapar heltals variabler med namnet `NumberOfIterations` och `NumberOfRunnings` och `SampleMessage`en String-variabel med namnet.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Ange och hämta en variabel i en python 2-Runbook

Följande exempel visar hur du använder en variabel, ställer in en variabel och hanterar ett undantag för en icke-existerande variabel i en python 2-Runbook.

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

### <a name="graphical-runbook-samples"></a>Grafiska Runbook-exempel

I en grafisk Runbook kan du lägga till `Get-AutomationVariable` eller `Set-AutomationVariable` -aktiviteten. Högerklicka bara på variabeln i fönstret Bibliotek i den grafiska redigeraren och välj den aktivitet som du vill använda.

![Lägg till variabel på arbets ytan](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Ange värden i en variabel

Följande bild visar exempel aktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk Runbook. I det här exemplet `Get-AzVM` hämtar en enskild virtuell Azure-dator och sparar dator namnet i en befintlig Automation-sträng-variabel. Det spelar ingen roll om [länken är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#links-and-workflow) eftersom koden endast förväntar sig ett enskilt objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du ansluter aktiviteter i grafisk redigering finns i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow).
- För att komma igång med grafiska runbooks, se [min första grafiska Runbook](../automation-first-runbook-graphical.md).
