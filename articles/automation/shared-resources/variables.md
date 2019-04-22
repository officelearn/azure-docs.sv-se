---
title: Variabler för tillgångar i Azure Automation
description: Variabler för tillgångar är värden som är tillgängliga för alla runbooks och DSC-konfigurationer i Azure Automation.  Den här artikeln beskrivs detaljer om variabler och hur du arbetar med dem i både textbaserade och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc26c0357dcb071c4c75e8684fe47144a04177e4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58807040"
---
# <a name="variable-assets-in-azure-automation"></a>Variabler för tillgångar i Azure Automation

Variabler för tillgångar är värden som är tillgängliga för alla runbooks och DSC-konfigurationer i ditt automation-konto. De kan hanteras från Azure portal, PowerShell, i en runbook eller DSC-konfiguration. Automationsvariabler är användbara för följande scenarier:

- Dela ett värde mellan flera runbooks eller DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma runbook eller DSC-konfiguration.

- Hantera ett värde från portalen eller PowerShell-kommandoraden som används av runbooks eller DSC-konfigurationer, till exempel en uppsättning vanliga konfigurationsobjekt, till exempel specifika lista över namn på virtuella datorer, en specifik resursgrupp, ett AD-domännamn och mer.  

Eftersom Automation-variabler är beständiga, är de tillgängliga även om runbook eller DSC-konfiguration misslyckas. Det här innebär att ett värde ställs in av en runbook som sedan används av en annan eller används av samma runbook eller DSC-konfigurationen nästa gång den körs.

När en variabel har skapats kan du ange att lagras krypterade. Krypterade variabler lagras på ett säkert sätt i Azure Automation och dess värde kan inte hämtas från den [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) cmdlet som levereras som en del av Azure PowerShell-modulen. Det enda sättet att ett krypterat värde kan hämtas är från den **Get-AutomationVariable** aktivitet i en runbook eller DSC-konfiguration.

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Key Vault hanteras av en datorn. Innan du lagrar en säker resurs som lästs in från Key Vault nyckeln och sedan används för att kryptera tillgången. Den här processen hanteras av Azure Automation.

## <a name="variable-types"></a>Variabeltyper

När du skapar en variabel med Azure-portalen kan ange du en datatyp från den nedrullningsbara listan så portalen kan visa lämplig kontroll för att ange variabelvärdet. Variabeln är inte begränsad till den här datatypen. Du måste ange variabeln med hjälp av Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger **inte har definierats**, och sedan värdet för variabeln anger till **$null**, och du måste ange värdet med den [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet eller **Set-AutomationVariable** aktivitet. Du kan inte skapa eller ändra värdet för en variabel för komplex typ i portalen, men du kan ange ett värde av valfri typ med Windows PowerShell. Komplexa typer returneras som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Du kan lagra flera värden till en enda variabel genom att skapa en matris eller en hash-tabell och spara den till variabeln.

Här följer en lista över variabeltyper som är tillgängliga i Automation:

* String
* Integer
* DateTime
* Boolesk
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdletar

För AzureRM används cmdletar i följande tabell för att skapa och hantera inloggningstillgångar i automation med Windows PowerShell. De levereras som en del av den [AzureRM.Automation modulen](/powershell/azure/overview), som är tillgängligt för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdlet: ar | Beskrivning |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Hämtar värdet för en befintlig variabel.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Skapar en ny variabel och anger dess värde.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Tar bort en befintlig variabel.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Anger värdet för en befintlig variabel.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i en runbook och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-automationvariable|Hämtar värdet för en befintlig variabel.|
|Set-AutomationVariable|Anger värdet för en befintlig variabel.|

> [!NOTE]
> Du bör undvika att använda variabler i – Name-parametern i **Get-AutomationVariable** i en runbook eller DSC-konfiguration eftersom detta kan göra det svårare att hitta beroenden mellan runbooks eller DSC-konfiguration och automatisering variabler vid designtillfället.

Funktioner i följande tabell används för att få åtkomst till och hämta variabler i en Python2-runbook.

|Python2-funktioner|Beskrivning|
|:---|:---|
|automationassets.get_automation_variable|Hämtar värdet för en befintlig variabel. |
|automationassets.set_automation_variable|Anger värdet för en befintlig variabel. |

> [!NOTE]
> Du måste importera modulen ”automationassets” överst i Python-runbook för att komma åt funktionerna för tillgången.

## <a name="creating-a-new-automation-variable"></a>Skapa en ny Automation-variabel

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Skapa en ny variabel med Azure portal

1. Från ditt Automation-konto klickar du på den **tillgångar** panelen och klicka sedan på den **tillgångar** bladet väljer **variabler**.
2. På den **variabler** panelen, väljer **Lägg till en variabel**.
3. Klar med alternativen på den **ny variabel** bladet och klickar på **skapa** spara variabeln.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Skapa en ny variabel med Windows PowerShell

Den [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet skapar en ny variabel och anger sitt ursprungliga värde. Du kan hämta värdet med hjälp av [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Om värdet är en enkel typ, returneras samma typ. Om det är en komplex typ, en **PSCustomObject** returneras.

Följande exempelkommandon visar hur du skapar en variabel av typen sträng och returnerar sedan värdet.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Följande exempelkommandon visar hur du skapar en variabel med en komplex typ och återgå sedan dess egenskaper. I det här fallet en virtuell dator objekt från **Get-AzureRmVm** används.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Med hjälp av en variabel i en runbook eller DSC-konfiguration

Använd den **Set-AutomationVariable** aktivitet för att ange värdet för ett Automation-variabel i en PowerShell-runbook eller DSC-konfiguration, och **Get-AutomationVariable** att hämta den. Du bör inte använda den **Set-AzureRMAutomationVariable** eller **Get-AzureRMAutomationVariable** cmdletar i en runbook eller DSC-konfiguration eftersom de är mindre effektivt än arbetsflödesaktiviteterna. Du kan också det går inte att hämta värdet för säker variabler med **Get-AzureRMAutomationVariable**. Det enda sättet att skapa en ny variabel från inom en runbook eller DSC-konfiguration är att använda den [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet.

### <a name="textual-runbook-samples"></a>Textbaserade runbook-exempel

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Ange och läsa ett enkelt värde från en variabel

Följande exempelkommandon visar hur du anger och hämtar en variabel i en text runbook. I det här exemplet förutsätts att variabler av typen heltal med namnet *NumberOfIterations* och *NumberOfRunnings* och en variabel av typen sträng med namnet *SampleMessage* har skapats.

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

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Ange och läsa ett komplext objekt i en variabel

Följande exempelkod visar hur du uppdaterar en variabel med ett komplext värde i en text runbook. I det här exemplet hämtas en Azure virtuell dator med **Get-AzureVM** och sparas på en befintlig Automation-variabel.  Enligt beskrivningen i [variabeltyper](#variable-types), detta lagras som en PSCustomObject.

```powershell
$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
Set-AutomationVariable -Name "MyComplexVariable" -Value $vm
```

I följande kod hämtas från variabeln värdet och används för att starta den virtuella datorn.

```powershell
$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
if ($vmObject.PowerState -eq 'Stopped') {
    Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
}
```

#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Ange och läsa en samling i en variabel

Följande exempelkod visar hur du använder en variabel med en samling med komplexa värden i en text runbook. I det här exemplet hämtas flera virtuella Azure-datorer med **Get-AzureVM** och sparas på en befintlig Automation-variabel. Enligt beskrivningen i [variabeltyper](#variable-types), detta lagras som en uppsättning PSCustomObjects.

```powershell
$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}
Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms
```

I följande kod hämtas från variabeln samlingen och används för att starta varje virtuell dator.

```powershell
$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
ForEach ($vmValue in $vmValues)
{
    if ($vmValue.PowerState -eq 'Stopped') {
        Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
    }
}
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Ange och läsa en variabel i Python2

Följande exempelkod visar hur du använder en variabel, ange en variabel och hantera ett undantag för en icke-existerande variabel i en Python2-runbook.

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

### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

I en grafisk runbook, lägger du till den **Get-AutomationVariable** eller **Set-AutomationVariable** genom att högerklicka på variabeln i rutan bibliotek för den grafiska redigeraren och välja aktiviteten du vill.

![Lägg till variabel på arbetsytan](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Ange värden i en variabel

I följande bild visas exempel aktiviteter för att uppdatera en variabel med ett enkelt värde i en grafisk runbook. I det här exemplet **Get-AzureRmVM** hämtar en Azure virtuell dator och datornamnet sparar till en befintlig Automation-variabel med en typ av strängen. Det spelar ingen roll om den [länken är en pipeline eller sequence](../automation-graphical-authoring-intro.md#links-and-workflow) eftersom du bara förväntar dig ett enda objekt i utdata.

![Ange enkla variabel](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om att ansluta aktiviteter tillsammans i grafisk redigering i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow)
- Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](../automation-first-runbook-graphical.md)
