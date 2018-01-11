---
title: "Variabeln tillgångar i Azure Automation | Microsoft Docs"
description: "Variabeln tillgångar är värden som är tillgängliga för alla runbooks och i Azure Automation DSC-konfigurationer.  Den här artikeln beskriver hur du variabler och hur du arbetar med dem i både text och grafiska redigering."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/09/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e38d2b751090cfdc078de4e8c683c6bb9b48fac3
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="variable-assets-in-azure-automation"></a>Variabeln tillgångar i Azure Automation

Variabeln tillgångar är värden som är tillgängliga för alla runbooks och DSC-konfigurationer i automation-kontot. De kan skapas, ändras och hämtas från Azure-portalen, Windows PowerShell och inifrån en runbook eller DSC-konfigurationen. Automationsvariabler är användbara för följande scenarier:

- Dela ett värde mellan flera runbooks eller DSC-konfigurationer.

- Dela ett värde mellan flera jobb från samma runbook eller DSC-konfigurationen.

- Hantera ett värde från portalen eller Windows PowerShell-kommandoraden som används av runbooks eller DSC-konfigurationer, till exempel en uppsättning gemensamma konfigurationsobjekt som specifik lista av VM-namn, en specifik resursgrupp, en AD-domännamn och så vidare.  

Automationsvariabler är beständiga så att de fortsätter att vara tillgängliga även om den eller de DSC-konfigurationen misslyckas.  Detta kan även ett värde som anges av en runbook som sedan används av en annan eller används av samma runbook eller DSC-konfigurationen nästa gång den körs.

När en variabel har skapats kan du ange att den lagras krypterade.  När en variabel krypteras, lagras den säkert i Azure Automation och dess värde kan inte hämtas från den [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx) cmdlet som levereras som en del av Azure PowerShell-modulen.  Det enda sättet att ett krypterat värde kan hämtas är från den **Get-automationvariable,** aktivitet i en runbook eller DSC-konfigurationen.

> [!NOTE]
> Säkra tillgångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure-Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln är krypterad med ett certifikat för master och lagras i Azure Automation. Innan du sparar en säker resurs, nyckeln för automation-kontot dekrypteras med master certifikatet och sedan används för att kryptera tillgången.

## <a name="variable-types"></a>Datatyper

När du skapar en variabel med Azure-portalen, måste du ange en datatyp från den nedrullningsbara listan så att portalen kan visa lämplig kontroll för att ange värdet för variabeln. Variabeln är inte begränsat till denna datatyp, men du måste ange variabeln med Windows PowerShell om du vill ange ett värde av en annan typ. Om du anger **inte har definierats**, och värdet för variabeln anges **$null**, och du måste ange värdet med den [Set AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) cmdlet eller **Set-automationvariable,** aktivitet.  Du kan inte skapa eller ändra värdet för en variabel för komplex typ i portalen, men du kan ange ett värde av valfri typ med Windows PowerShell. Komplexa typer returneras som en [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Du kan lagra flera värden till en enda variabel genom att skapa en matris eller hash-tabell och spara den på variabeln.

Här följer en lista över variabla typer som är tillgängliga i Automation:

* Sträng
* Integer
* DateTime
* Boolesk
* Null

## <a name="scripting-the-creation-and-management-of-variables"></a>Skapa och hantera variabler-skript

Cmdlets i följande tabell används för att skapa och hantera automatisering variabler med Windows PowerShell. De levereras som en del av den [Azure PowerShell-modulen](../powershell-install-configure.md) som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationen.

|Cmdlet: ar|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx)|Hämtar värdet för en befintlig variabel.|
|[Ny AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx)|Skapar en ny variabel och anger dess värde.|
|[Ta bort AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt619354.aspx)|Tar bort en befintlig variabel.|
|[Ange AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603601.aspx)|Anger värdet för en befintlig variabel.|

Arbetsflödesaktiviteter i följande tabell används för åtkomst till automationsvariabler i en runbook. De är bara tillgängliga för användning i en runbook eller DSC-konfigurationen och levereras inte som en del av Azure PowerShell-modulen.

|Arbetsflödesaktiviteter|Beskrivning|
|:---|:---|
|Get-automationvariable|Hämtar värdet för en befintlig variabel.|
|Set-automationvariable|Anger värdet för en befintlig variabel.|

> [!NOTE] 
> Du bör undvika att använda variabler i – Name-parametern i **Get-automationvariable,** i en runbook eller DSC-konfigurationen eftersom detta kan göra det svårare att hitta beroenden mellan runbooks eller DSC-konfiguration och automatisering variabler i designläge.

Funktioner i följande tabell används för att komma åt och hämta variabler i en Python2 runbook. 

|Python2 funktioner|Beskrivning|
|:---|:---|
|automationassets.get_automation_variable|Hämtar värdet för en befintlig variabel. |
|automationassets.set_automation_variable|Anger värdet för en befintlig variabel. |

> [!NOTE] 
> Du måste importera modulen ”automationassets” längst upp i Python-runbook för att komma åt funktionerna tillgång.

## <a name="creating-a-new-automation-variable"></a>Skapa en ny Automation-variabel

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Skapa en ny variabel med Azure-portalen

1. Från ditt Automation-konto klickar du på den **tillgångar** panelen och klicka sedan på den **tillgångar** bladet väljer **variabler**.
2. På den **variabler** panelen, väljer **lägga till en variabel**.
3. Fyll i alternativen på den **ny variabel** bladet och klicka på **skapa** spara variabeln.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Skapa en ny variabel med Windows PowerShell

Den [ny AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx) cmdlet skapar en ny variabel och anger sitt ursprungliga värde. Du kan hämta värde med hjälp av [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx). Om värdet är en enkel typ, returneras samma typ. Om det är en komplex typ och sedan en **PSCustomObject** returneras.

Följande exempelkommandon visar hur du skapar en variabel av typen string och returnerar sedan värdet.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Följande exempelkommandon visar hur du skapar en variabel med en komplex typ och återgå sedan dess egenskaper. I det här fallet en virtuell dator objekt från **Get-AzureRmVm** används.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Använda en variabel i en runbook eller DSC-konfiguration

Använd den **Set-automationvariable,** aktivitet för att ange värdet för ett Automation-variabel i ett PowerShell-runbook eller DSC-konfigurationen och **Get-automationvariable,** att hämta den.  Du bör inte använda den **Set AzureAutomationVariable** eller **Get-AzureAutomationVariable** cmdletarna i en runbook eller DSC-konfigurationen eftersom de är mindre effektivt än arbetsflödesaktiviteterna.  Du kan också hämta värdet för säker variabler med **Get-AzureAutomationVariable**.  Det enda sättet att skapa en ny variabel från i en runbook eller DSC-konfigurationen är att använda den [ny AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) cmdlet.


### <a name="textual-runbook-samples"></a>Textrepresentation runbook-exempel

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Ställa in och hämta ett enkelt värde från en variabel

Följande exempelkommandon visar hur du ställer in och hämtar en variabel i text-runbook. I det här exemplet förutsätts att variabler av typen heltal med namnet *NumberOfIterations* och *NumberOfRunnings* och en variabel av typen sträng med namnet *SampleMessage* redan har skapats.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Ställa in och hämta ett komplext objekt i en variabel

Följande exempelkod visar hur du uppdaterar en variabel med ett komplext värde i text-runbook. I det här exemplet hämtas en virtuell Azure-dator med **Get-AzureVM** och sparas i en befintlig Automation-variabel.  Enligt beskrivningen i [datatyper](#variable-types), detta lagras som en PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

I följande kod värdet hämtas från variabeln och används för att starta den virtuella datorn.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Ställa in och hämta en samling i en variabel

Följande exempelkod visar hur du använder en variabel med en samling med komplexa värden i text-runbook. I det här exemplet hämtas flera virtuella Azure-datorer med **Get-AzureVM** och sparas i en befintlig Automation-variabel.  Enligt beskrivningen i [datatyper](#variable-types), lagras detta som en samling PSCustomObjects.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

I följande kod som hämtats från variabeln samlingen och används för att starta varje virtuell dator.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Ställa in och hämta en variabel i Python2
Följande exempelkod visar hur du använder en variabel, ange en variabel och hanterar ett undantag för en obefintlig variabel i en Python2 runbook.

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


### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

I en grafisk runbook kan du lägga till den **Get-automationvariable,** eller **Set-automationvariable,** genom att högerklicka på variabeln i rutan bibliotek av grafiska redigerare och välja aktiviteten som du vill.

![Lägg till variabel på arbetsytan](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Ange värden i en variabel
Följande bild visar exempel aktiviteter att uppdatera en variabel med ett enkelt värde i en grafisk runbook. I det här exemplet hämtas en enda virtuell Azure-dator med **Get-AzureRmVM** och datornamnet sparas på en befintlig Automation-variabel av typen sträng.  Det spelar ingen roll om den [länken är en pipeline eller sekvens](automation-graphical-authoring-intro.md#links-and-workflow) eftersom vi räknar endast ett objekt i utdata.

![Ange enkla variabel](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du ansluter aktiviteter tillsammans i grafiska redigering finns [länkar i grafiska redigering](automation-graphical-authoring-intro.md#links-and-workflow)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md) 

