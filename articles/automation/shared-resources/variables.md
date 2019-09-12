---
title: Variabel till gångar i Azure Automation
description: Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i Azure Automation.  I den här artikeln beskrivs information om variabler och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fe008d20ab43636b59861bcc5a7914ba0fca17e
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910069"
---
# <a name="variable-assets-in-azure-automation"></a>Variabel till gångar i Azure Automation

Variabel till gångar är värden som är tillgängliga för alla Runbooks och DSC-konfigurationer i ditt Automation-konto. De kan hanteras från Azure Portal, PowerShell, i en Runbook eller DSC-konfiguration. Automationsvariabler är användbara för följande scenarier:

- Dela ett värde mellan flera Runbooks och DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma Runbook-eller DSC-konfiguration.

- Hantera ett värde från portalen eller från PowerShell-kommandoraden som används av Runbooks eller DSC-konfigurationer, till exempel en uppsättning vanliga konfigurations objekt som en speciell lista över namn på virtuella datorer, en särskild resurs grupp, ett AD-domännamn och mer.  

Eftersom Automation-variabler är sparade är de tillgängliga även om Runbook-eller DSC-konfigurationen Miss lyckas. Detta gör att ett värde kan anges av en Runbook som sedan används av en annan eller används av samma Runbook-eller DSC-konfiguration nästa gången den körs.

När en variabel skapas kan du ange att den är lagrad som krypterad. Krypterade variabler lagras på ett säkert sätt i Azure Automation och dess värde kan inte hämtas från cmdleten [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) som levereras som en del av modulen Azure PowerShell. Det enda sättet som ett krypterat värde kan hämtas från **Get-AutomationVariable** -aktiviteten i en Runbook-eller DSC-konfiguration. Om du vill ändra en krypterad variabel till icke-krypterad kan du ta bort och återskapa variabeln som okrypterad.

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i ett systemhanterat Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången. Den här processen hanteras av Azure Automation.

## <a name="variable-types"></a>Variabel typer

När du skapar en variabel med Azure Portal måste du ange en datatyp från List rutan så att portalen kan visa lämplig kontroll för att ange variabelvärdet. Variabeln är inte begränsad till den här data typen. Du måste ange variabeln med hjälp av Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger **inte definierar**värdet för variabeln uppsättningarna till **$Null**och du måste ange värdet med cmdleten [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) eller **set-AutomationVariable-** aktiviteten. Du kan inte skapa eller ändra värdet för en komplex variabel typ i portalen, men du kan ange ett värde av vilken typ som helst med hjälp av Windows PowerShell. Komplexa typer returneras som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden i en enskild variabel genom att skapa en matris eller hash och spara den i variabeln.

Följande är en lista över variabel typer som är tillgängliga i Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdletar

I AzureRM används cmdletarna i följande tabell för att skapa och hantera inloggnings resurser för Automation med Windows PowerShell. De levereras som en del av [modulen AzureRM. Automation](/powershell/azure/overview), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

| Cmdlet: ar | Beskrivning |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Hämtar värdet för en befintlig variabel.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Skapar en ny variabel och anger dess värde.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Tar bort en befintlig variabel.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Anger värdet för en befintlig variabel.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att få åtkomst till variabler i en Runbook-och DSC-konfiguration. Skillnaden mellan get-AzureRmAutomationVariable-och get-AutomationVariable-cmdletarna visas i början av det här dokumentet.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-automationvariable|Hämtar värdet för en befintlig variabel.|
|Set-AutomationVariable|Anger värdet för en befintlig variabel.|

> [!NOTE]
> Du bör undvika att använda variabler i parametern – name för **Get-AutomationVariable** i en Runbook-eller DSC-konfiguration eftersom detta kan komplicera att identifiera beroenden mellan RUNBOOKS och DSC-konfiguration, samt automatisera variabler vid design tillfället.

Funktionerna i följande tabell används för att komma åt och hämta variabler i en Python2-Runbook.

|Python2-funktioner|Beskrivning|
|:---|:---|
|automationassets.get_automation_variable|Hämtar värdet för en befintlig variabel. |
|automationassets.set_automation_variable|Anger värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera modulen "automationassets" längst upp i din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-automation-variable"></a>Skapa en ny Automation-variabel

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Så här skapar du en ny variabel med Azure Portal

1. Från ditt Automation-konto klickar du på panelen **till gångar** och väljer sedan **variabler**på bladet **till gångar** .
2. På panelen **variabler** väljer du **Lägg till en variabel**.
3. Slutför alternativen på bladet **ny variabel** och klicka på **skapa** Spara den nya variabeln.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Skapa en ny variabel med Windows PowerShell

Cmdlet [: en New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) skapar en ny variabel och anger dess ursprungliga värde. Du kan hämta värdet med hjälp av [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Om värdet är en enkel typ, returneras samma typ. Om det är en komplex typ returneras en **PSCustomObject** .

Följande exempel kommandon visar hur du skapar en variabel av typen sträng och returnerar sedan värdet.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Följande exempel kommandon visar hur du skapar en variabel med en komplex typ och sedan returnerar dess egenskaper. I det här fallet används ett virtuellt dator objekt från **Get-AzureRmVm** .

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Använda en variabel i en Runbook-eller DSC-konfiguration

Använd aktiviteten **set-AutomationVariable** för att ange värdet för en Automation-variabel i en PowerShell-RUNBOOK eller DSC-konfiguration och **Get-AutomationVariable** för att hämta den. Du bör inte använda cmdletarna **set-AzureRMAutomationVariable** eller **Get-AzureRMAutomationVariable** i en Runbook-eller DSC-konfiguration eftersom de är mindre effektiva än arbets flödes aktiviteterna. Du kan inte heller hämta värdet för säkra variabler med **Get-AzureRMAutomationVariable**. Det enda sättet att skapa en ny variabel från en Runbook eller DSC-konfiguration är att använda cmdleten [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) .

### <a name="textual-runbook-samples"></a>Text Runbook-exempel

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Inställning och hämtning av ett enkelt värde från en variabel

Följande exempel kommandon visar hur du ställer in och hämtar en variabel i en text-Runbook. I det här exemplet förutsätts att variabler av typen heltal med namnet *NumberOfIterations* och *NumberOfRunnings* och en variabel av typen String med namnet *SampleMessage* har skapats.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Ställa in och hämta en variabel i Python2

Följande exempel kod visar hur du använder en variabel, ställer in en variabel och hanterar ett undantag för en obefintlig variabel i en Python2-Runbook.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Grafiska Runbook-exempel

I en grafisk Runbook lägger du till **Get-AutomationVariable** eller **set-AutomationVariable** genom att högerklicka på variabeln i fönstret Bibliotek i den grafiska redigeraren och välja den aktivitet du vill använda.

![Lägg till variabel på arbets ytan](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Ange värden i en variabel

Följande bild visar exempel aktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk Runbook. I det här exemplet hämtar **Get-AzureRmVM** en enda virtuell Azure-dator och dator namnet sparas till en befintlig Automation-variabel med en typ av sträng. Det spelar ingen roll om [länken är en pipeline eller sekvens](../automation-graphical-authoring-intro.md#links-and-workflow) eftersom du bara förväntar dig ett enskilt objekt i utdata.

![Ange enkel variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du ansluter aktiviteter tillsammans i grafisk redigering finns i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow)
- Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](../automation-first-runbook-graphical.md)
