---
title: Underordnade Runbooks i Azure Automation
description: Beskriver de olika metoderna för att starta en Runbook i Azure Automation från en annan Runbook och dela information mellan dem.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a35ee69e6a167f4907294c88710d0484353d4cb2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367017"
---
# <a name="child-runbooks-in-azure-automation"></a>Underordnade Runbooks i Azure Automation

Det är en rekommenderad metod i Azure Automation att skriva återanvändbara, modulära Runbooks med en diskret funktion som är av andra Runbooks. En överordnad Runbook anropar ofta en eller flera underordnade Runbooks för att utföra de funktioner som krävs. Det finns två sätt att anropa en underordnad Runbook och var och en har distinkta skillnader som du bör känna till, så att du kan avgöra vilket som passar bäst för dina olika scenarier.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Anropa en underordnad Runbook med intern körning

Om du vill aktivera en infogad runbook från en annan runbook, Använd namnet på runbooken och ange värden för parametrarna exakt samma sätt som du använder en aktivitet eller cmdlet.  Alla Runbooks i samma Automation-konto är tillgängliga för alla andra som ska användas på det här sättet. Den överordnade runbooken väntar på att den underordnade runbooken ska slutföras innan du går till nästa rad och eventuella utdata returneras direkt till överordnat.

När du anropar en infogad runbook körs i samma jobb som den överordnade runbooken. Det finns ingen indikation i jobb historiken för den underordnade Runbook som den kördes. Eventuella undantag och eventuella strömmande utdata från den underordnade runbooken är kopplade till den överordnade. Detta resulterar i färre jobb och gör det enklare att spåra och felsöka eftersom eventuella undantag som har utlösts av den underordnade runbooken och alla dess strömmade utdata är associerade med det överordnade jobbet.

När en Runbook publiceras måste alla underordnade Runbooks som den anropar redan publiceras. Detta beror på att Azure Automation skapar en Association med underordnade runbooks när en Runbook kompileras. Om de inte är det verkar den överordnade runbooken publiceras korrekt, men genererar ett undantag när den startas. Om detta inträffar kan du publicera om den överordnade runbooken så att den refererar till underordnade Runbooks korrekt. Du behöver inte publicera om den överordnade runbooken om någon av de underordnade Runbooks ändras eftersom associationen redan har skapats.

Parametrarna för en underordnad Runbook som kallas infogade kan vara vilken datatyp som helst, inklusive komplexa objekt. Det finns ingen [JSON-serialisering](start-runbooks.md#runbook-parameters) eftersom du startar runbooken med hjälp av Azure Portal eller med cmdleten Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Runbook-typer

Vilka typer kan anropa varandra:

* En [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) och [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) kan anropa varandra på samma infogade (båda är PowerShell-baserade).
* Runbook-flöden för [PowerShell-arbetsflöden](automation-runbook-types.md#powershell-workflow-runbooks) och grafiska PowerShell-arbetsflöden kan anropa varandra (båda är PowerShell-arbetsflöde baserade)
* PowerShell-typerna och PowerShell-arbetsflödet kan inte anropa varandra på samma rad och måste använda Start-AzureRmAutomationRunbook.

När publicerar order:

* Publicerings ordningen för Runbooks är bara av betydelse för PowerShell-arbetsflöde och grafiska PowerShell-arbetsflöden.

När du anropar en underordnad, grafisk eller PowerShell-underordnad Runbook med hjälp av infogad körning, använder du namnet på runbooken.  När du anropar en underordnad PowerShell-Runbook måste du starta dess namn med *.\\* för att ange att skriptet finns i den lokala katalogen.

### <a name="example"></a>Exempel

I följande exempel startas en underordnad test-Runbook som accepterar tre parametrar, ett komplext objekt, ett heltal och ett booleskt värde. Utdata från den underordnade runbooken tilldelas till en variabel.  I det här fallet är underordnad Runbook en PowerShell-Runbook för arbets flöden.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Följande är samma exempel som med en PowerShell-Runbook som underordnad.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Starta en underordnad Runbook med cmdlet

> [!IMPORTANT]
> Om du anropar en underordnad Runbook med `Start-AzureRmAutomationRunbook`-cmdlet med `-Wait`-växeln och resultatet av den underordnade Runbook-flödet är ett objekt kan du stöta på fel. Undvik felet genom att se [underordnade Runbooks med objekt utdata](troubleshoot/runbooks.md#child-runbook-object) för att lära dig hur du implementerar logiken för att söka efter resultat och använder kommandot [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Du kan använda cmdleten [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) för att starta en Runbook enligt beskrivningen i [för att starta en Runbook med Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Det finns två användnings lägen för denna cmdlet.  I ett läge returnerar cmdleten jobb-ID: t när det underordnade jobbet skapas för den underordnade runbooken.  I det andra läget, som du aktiverar genom att ange parametern **-wait** , väntar cmdleten tills det underordnade jobbet är klart och returnerar utdata från den underordnade runbooken.

Jobbet från en underordnad Runbook som startades med en cmdlet körs i ett separat jobb från den överordnade runbooken. Detta leder till fler jobb än att starta en Runbook i rad och gör dem svårare att spåra. Överordnad kan starta fler än en underordnad Runbook asynkront utan att vänta på att de ska slutföras. För att få samma typ av parallell körning vid anrop av infogade underordnade runbooks, skulle den överordnade runbooken måste du använda den [parallellt nyckelord](automation-powershell-workflow.md#parallel-processing).

Utdata från underordnade Runbooks returneras inte till den överordnade runbooken tillförlitligt på grund av tids inställning. Även vissa variabler som $VerbosePreference, $WarningPreference och andra kanske inte sprids till underordnade Runbooks. För att undvika dessa problem kan du starta de underordnade Runbooks som separata Automation-jobb med hjälp av `Start-AzureRmAutomationRunbook`-cmdlet med växeln `-Wait`. Detta blockerar den överordnade runbooken tills den underordnade runbooken har slutförts.

Om du inte vill att den överordnade runbooken ska blockeras vid väntan kan du starta underordnad Runbook med hjälp av `Start-AzureRmAutomationRunbook` cmdlet utan `-Wait`-växeln. Du måste sedan använda `Get-AzureRmAutomationJob` för att vänta på att jobbet ska slutföras, och `Get-AzureRmAutomationJobOutput` och `Get-AzureRmAutomationJobOutputRecord` för att hämta resultatet.

Parametrar för en underordnad runbook som startas med en cmdlet tillhandahålls som en hash-tabell enligt beskrivningen i [Runbookparametrar](start-runbooks.md#runbook-parameters). Endast enkla data typer kan användas. Om runbooken har en parameter med en komplex datatyp, det måste den anropas infogad.

Prenumerations kontexten kan gå förlorad när du startar underordnade Runbooks som separata jobb. För att underordnad Runbook ska kunna köra Azure RM-cmdletar mot en speciell Azure-prenumeration måste den underordnade runbooken autentisera till den här prenumerationen oberoende av den överordnade runbooken.

Om jobb inom samma Automation-konto fungerar med fler än en prenumeration kan du ändra det aktuella prenumerations sammanhanget för andra jobb genom att välja en prenumeration i ett jobb. Undvik det här problemet genom att använda `Disable-AzureRmContextAutosave –Scope Processsave` i början av varje Runbook. Den här åtgärden sparar bara kontexten till den Runbook-körningen.

### <a name="example"></a>Exempel

I följande exempel startar en underordnad Runbook med parametrar och väntar sedan på att den ska slutföras med hjälp av parametern start-AzureRmAutomationRunbook-wait. När den har slutförts samlas utdata in från den underordnade runbooken. Om du vill använda `Start-AzureRmAutomationRunbook`måste du autentisera till din Azure-prenumeration.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Jämförelse av metoder för att anropa en underordnad Runbook

I följande tabell sammanfattas skillnaderna mellan de två metoderna för att anropa en runbook från en annan runbook.

|  | Infogad | Cmdlet |
|:--- |:--- |:--- |
| Jobb |Underordnade runbooks körs i samma jobb som det överordnade objektet. |Ett separat jobb skapas för den underordnade runbooken. |
| Körning |Överordnad runbook väntar tills den underordnade runbooken ska slutföras innan du fortsätter. |Överordnad Runbook fortsätter omedelbart efter att underordnad Runbook har startats *eller* överordnad Runbook väntar tills det underordnade jobbet har slutförts. |
| Resultat |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad Runbook måste hämta utdata från underordnat Runbook-jobb *eller* överordnad Runbook kan hämta utdata direkt från underordnad Runbook. |
| Parametrar |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för underordnade Runbook-parametrar måste kombineras till en enskild hash. Den här hash-koden kan bara innehålla enkla, matris-och objekt data typer som använder JSON-serialisering. |
| Automation-konto |Överordnad Runbook kan bara använda underordnad Runbook i samma Automation-konto. |Överordnad Runbooks kan använda en underordnad Runbook från alla Automation-konton från samma Azure-prenumeration och till och med en annan prenumeration som du har en anslutning till. |
| Publicering |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad Runbook måste publiceras när en överordnad Runbook startas. |

## <a name="next-steps"></a>Nästa steg

* [Starta en Runbook i Azure Automation](start-runbooks.md)
* [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)

