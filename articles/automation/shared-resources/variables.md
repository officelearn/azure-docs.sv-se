---
title: Variabel till gångar i Azure Automation
description: Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i Azure Automation.  I den här artikeln beskrivs information om variabler och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ce56b64502904308f45c74a5471447d93419452
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303060"
---
# <a name="variable-assets-in-azure-automation"></a>Variabel till gångar i Azure Automation

Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i ditt Automation-konto. Du kan hantera dem från Azure Portal, från PowerShell, i en Runbook eller i en DSC-konfiguration.

Automationsvariabler är användbara för följande scenarier:

- Dela ett värde mellan flera Runbooks och DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma Runbook-eller DSC-konfiguration.

- Hantera ett värde som används av Runbooks eller DSC-konfigurationer från portalen eller från PowerShell-kommandoraden. Ett exempel är en uppsättning vanliga konfigurations objekt, till exempel en lista med namn på virtuella datorer, en särskild resurs grupp, ett AD-domännamn och mer.  

Eftersom Automation-variabler är sparade är de tillgängliga även om Runbook-eller DSC-konfigurationen Miss lyckas. Med det här beteendet kan en Runbook-eller DSC-konfiguration ange ett värde som sedan används av en annan Runbook eller av samma Runbook-eller DSC-konfiguration nästa gången den körs.

När du skapar en variabel kan du ange dess kryptering och lagring genom att Azure Automation som en säker till gång. Andra säkra till gångar inkluderar autentiseringsuppgifter, certifikat och anslutningar. Azure Automation krypterar dessa till gångar och lagrar dem med hjälp av en unik nyckel som genereras för varje Automation-konto. Nyckeln lagras i systemhanterade Key Vault. Innan du lagrar en säker till gång läser Azure Automation in nyckeln från Key Vault och använder den för att kryptera till gången. 

Azure Automation lagrar varje krypterad variabel på ett säkert sätt. Värdet kan inte hämtas med cmdleten [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) som levereras som en del av modulen Azure PowerShell. Det enda sättet att hämta ett krypterat värde är genom att använda aktiviteten **Get-AutomationVariable** i en Runbook-eller DSC-konfiguration.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Variabel typer

När du skapar en variabel med Azure Portal måste du ange en datatyp i list rutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Följande är variabel typer som är tillgängliga i Azure Automation:

* String
* Integer
* DateTime
* Boolean
* Null

Variabeln är inte begränsad till den angivna data typen. Du måste ange variabeln med hjälp av Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger **inte definierad**är värdet för variabeln inställt på **Null**och du måste ange värdet med cmdleten [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) eller aktiviteten **set-AutomationVariable** .

Du kan inte använda portalen för att skapa eller ändra värdet för en komplex variabel typ. Du kan dock ange ett värde av vilken typ som helst med hjälp av Windows PowerShell. Komplexa typer hämtas som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden i en enskild variabel genom att skapa en matris eller hash och spara den i en variabel.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>PowerShell-cmdletar som skapar och hanterar variabel till gångar

I AZ-modulen används cmdletarna i följande tabell för att skapa och hantera Automation-variabel till gångar med Windows PowerShell. De levereras som en del av [modulen AZ. Automation](/powershell/azure/overview), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

| Cmdlet | Beskrivning |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Hämtar värdet för en befintlig variabel.|
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Skapar en ny variabel och anger dess värde.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Tar bort en befintlig variabel.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Anger värdet för en befintlig variabel.|

## <a name="activities-to-access-variables"></a>Aktiviteter för åtkomst till variabler

Aktiviteterna i följande tabell används för att få åtkomst till variabler i Runbooks och DSC-konfigurationer. Skillnaden mellan **Get-AzAutomationVariable** och **Get-AutomationVariable** förklaras för krypterade variabler i början av den här artikeln.

| Aktivitet | Beskrivning |
|:---|:---|
|**Get-AutomationVariable**|Hämtar värdet för en befintlig variabel.|
|**Set-AutomationVariable**|Anger värdet för en befintlig variabel.|

> [!NOTE]
> Undvik att använda variabler i *Name* -parametern för **Get-AutomationVariable** i en Runbook-eller DSC-konfiguration. Användningen av den här parametern kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och automation-variabler i design läge.

Funktionerna i följande tabell används för att komma åt och hämta variabler i en Python2-Runbook.

|Python2-funktioner|Beskrivning|
|:---|:---|
|automationassets.get_automation_variable|Hämtar värdet för en befintlig variabel. |
|automationassets.set_automation_variable|Anger värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera **automationassets** -modulen överst i din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-automation-variable"></a>Skapa en ny Automation-variabel

### <a name="create-a-new-variable-using-the-azure-portal"></a>Skapa en ny variabel med hjälp av Azure Portal

1. Från ditt Automation-konto klickar du på panelen **till gångar** och väljer sedan **variabler**på bladet **till gångar** .
2. På panelen **variabler** väljer du **Lägg till en variabel**.
3. Slutför alternativen på bladet **ny variabel** och klicka sedan på **skapa** för att spara den nya variabeln.

>[!NOTE]
>Om du vill ta bort krypteringen för en variabel måste du ta bort variabeln och återskapa den som okrypterad.

### <a name="create-a-new-variable-with-windows-powershell"></a>Skapa en ny variabel med Windows PowerShell

Skriptet använder cmdleten **New-AzAutomationVariable** för att skapa en ny variabel och ange dess start värde. Den kan sedan hämta värdet med hjälp av **Get-AzAutomationVariable**. Om värdet är en enkel typ hämtas samma typ. Om det är en komplex typ hämtas en **PSCustomObject** -typ.

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

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Använda en variabel i en Runbook-eller DSC-konfiguration

Använd aktiviteten **set-AutomationVariable** för att ange värdet för en Automation-variabel i en PowerShell-RUNBOOK eller DSC-konfiguration och **Get-AutomationVariable** för att hämta den. Du bör inte använda cmdletarna **set-AzAutomationVariable** och **Get-AzAutomationVariable** eller motsvarande AzureRM-modul i en Runbook-eller DSC-konfiguration eftersom de är mindre effektiva än arbets flödes aktiviteterna. 

Observera att du inte kan hämta värdet för en säker variabel med **Get-AzAutomationVariable** eller dess AzureRM-modul motsvarande. 

Det enda sättet att skapa en ny variabel från en Runbook eller DSC-konfiguration är att använda cmdleten **New-AzAutomationVariable** .

### <a name="textual-runbook-samples"></a>Text Runbook-exempel

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Ange och hämta ett enkelt värde från en variabel

Följande exempel kommandon visar hur du ställer in och hämtar en variabel i en text-Runbook. Det här exemplet förutsätter att du skapar heltals variabler med namnet *NumberOfIterations* och *NumberOfRunnings* och en String-variabel med namnet *SampleMessage*.

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Ange och hämta en variabel i en Python2-Runbook

Följande exempel visar hur du använder en variabel, ställer in en variabel och hanterar ett undantag för en icke-existerande variabel i en Python2-Runbook.

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

I en grafisk Runbook kan du lägga till aktiviteten **Get-AutomationVariable** eller **set-AutomationVariable** . Högerklicka bara på variabeln i fönstret Bibliotek i den grafiska redigeraren och välj den aktivitet som du vill använda.

![Lägg till variabel på arbets ytan](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Ange värden i en variabel

Följande bild visar exempel aktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk Runbook. I det här exemplet hämtar **Get-AzVM** en enda virtuell Azure-dator och sparar dator namnet i en befintlig Automation-variabel med en typ av sträng. Det spelar ingen roll om [länken är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#links-and-workflow) eftersom koden endast förväntar sig ett enskilt objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du ansluter aktiviteter i grafisk redigering finns i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow).
- För att komma igång med grafiska runbooks, se [min första grafiska Runbook](../automation-first-runbook-graphical.md).
