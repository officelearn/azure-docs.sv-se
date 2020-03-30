---
title: Underordnade runbooks i Azure Automation
description: Beskriver de olika metoderna för att starta en runbook i Azure Automation från en annan runbook och dela information mellan dem.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367371"
---
# <a name="child-runbooks-in-azure-automation"></a>Underordnade runbooks i Azure Automation

Det är en rekommenderad praxis i Azure Automation för att skriva återanvändbara, modulära runbooks med en diskret funktion som anropas av andra runbooks. En överordnad runbook anropar ofta en eller flera underordnade runbooks för att utföra nödvändiga funktioner. Det finns två sätt att anropa en underordnad runbook, och det finns olika skillnader som du bör förstå för att kunna avgöra vilket som är bäst för dina scenarier.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Anropa en underordnad runbook med hjälp av infogad körning

Om du vill anropa en runbook-infogad från en annan runbook använder du namnet på runbooken och anger värden för dess parametrar, precis som du skulle använda en aktivitet eller en cmdlet. Alla runbooks i samma Automation-konto är tillgängliga för alla andra som ska användas på detta sätt. Den överordnade runbooken väntar på att den underordnade runbooken ska slutföras innan den flyttas till nästa rad, och alla utdata återgår direkt till den överordnade.

När du aktiverar en infogad runbook körs den i samma jobb som den överordnade runbooken. Det finns ingen indikation i jobbhistoriken för barnkörboken. Eventuella undantag och eventuella utdata från den underordnade runbooken är associerade med den överordnade. Detta resulterar i färre jobb och gör dem enklare att spåra och felsöka.

När en runbook publiceras måste alla underordnade runbooks som den anropar redan publiceras. Anledningen är att Azure Automation skapar en association med alla underordnade runbooks när den kompilerar en runbook. Om de underordnade runbooksna inte redan har publicerats, verkar den överordnade runbooken publicera korrekt men genererar ett undantag när den startas. Om detta inträffar kan du publicera om den överordnade runbooken för att referera till de underordnade runbooksna på rätt sätt. Du behöver inte publicera om den överordnade runbooken om någon underordnad runbook ändras eftersom associationen redan har skapats.

Parametrarna för en underordnad runbook som kallas infogad kan vara av vilken datatyp som helst, inklusive komplexa objekt. Det finns ingen [JSON-serialisering](start-runbooks.md#runbook-parameters), som när du startar runbooken med Azure-portalen eller med [Cmdlet start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Runbook-typer

Vilka runbooktyper kan anropa varandra?

* En [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) och en [grafisk runbook](automation-runbook-types.md#graphical-runbooks) kan anropa varandra infogad, eftersom båda är PowerShell-baserade.
* En [PowerShell-arbetsflödeskörningsbok](automation-runbook-types.md#powershell-workflow-runbooks) och en grafisk PowerShell-arbetsflödeskörningsbok kan anropa varandra infogad, eftersom båda är PowerShell-arbetsflödesbaserade.
* PowerShell-typerna och PowerShell-arbetsflödestyperna kan inte anropa varandra `Start-AzAutomationRunbook`infogade och måste använda .

När spelar publiceringsordningen roll?

Publiceringsordningen för runbooks spelar bara roll för PowerShell Workflow och grafiska PowerShell-arbetsflödeskörningsböcker.

När runbooken anropar en grafisk eller PowerShell Workflow-underordnad runbook med hjälp av infogad körning används namnet på runbooken. Namnet måste börja `.\\` med för att ange att skriptet finns i den lokala katalogen.

### <a name="example"></a>Exempel

I följande exempel startar en underordnad testkörning som accepterar ett komplext objekt, ett heltalsvärde och ett booleskt värde. Resultatet av den underordnade runbooken tilldelas till en variabel. I det här fallet är den underordnade runbooken en PowerShell Workflow runbook.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Här är samma exempel med hjälp av en PowerShell-runbook som underordnad.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Starta en underordnad runbook med en cmdlet

> [!IMPORTANT]
> Om runbooken anropar en underordnad `Start-AzAutomationRunbook` runbook med `Wait` cmdlet med parametern och den underordnade runbooken ger ett objektresultat kan åtgärden uppstå ett fel. Mer information om felet finns i [Underordnade runbooks med objektutdata](troubleshoot/runbooks.md#child-runbook-object) för att lära dig hur du implementerar logiken för att avsöka efter resultaten med cmdleten [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Du kan `Start-AzAutomationRunbook` använda för att starta en runbook som beskrivs i [Så här startar du en runbook med Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Det finns två användningssätt för denna cmdlet. I ett läge returnerar cmdlet jobb-ID:n när jobbet skapas för den underordnade runbooken. I det andra läget, som skriptet aktiverar genom att ange parametern *Vänta,* väntar cmdleten tills det underordnade jobbet är klart och returnerar utdata från den underordnade runbooken.

Jobbet från en underordnad runbook som startades med en cmdlet körs separat från det överordnade runbook-jobbet. Detta resulterar i fler jobb än att starta runbook infogad, och gör jobben svårare att spåra. Överordnad kan starta mer än en underordnad runbook asynkront utan att vänta på att var och en ska slutföras. För den här parallella körningen som anropar de underordnade runbooks infogade måste den överordnade runbooken använda [det parallella nyckelordet](automation-powershell-workflow.md#parallel-processing).

Underordnad runbook-utdata återgår inte till den överordnade runbooken på ett tillförlitligt sätt på grund av tidpunkten. Dessutom kanske variabler `$VerbosePreference`som `$WarningPreference`, och andra inte sprids till de underordnade runbooks. För att undvika dessa problem kan du starta de `Start-AzAutomationRunbook` underordnade `Wait` runbooks som separata Automation-jobb med parametern. Den här tekniken blockerar den överordnade runbooken tills den underordnade runbooken är klar.

Om du inte vill att den överordnade runbooken ska blockeras när `Start-AzAutomationRunbook` du `Wait` väntar kan du starta den underordnade runbooken utan parametern. I det här fallet måste din runbook använda [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) för att vänta på att jobbet har slutförts. Den måste också använda [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) och [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) för att hämta resultaten.

Parametrar för en underordnad runbook som startas med en cmdlet tillhandahålls som en hashtable, enligt beskrivningen i [Runbook-parametrar](start-runbooks.md#runbook-parameters). Endast enkla datatyper kan användas. Om runbooken har en parameter med en komplex datatyp, måste den anropas infogad.

Prenumerationskontexten kan gå förlorad när underordnade runbooks startas som separata jobb. För att den underordnade runbooken ska kunna köra Az-modul cmdlets mot en specifik Azure-prenumeration måste barnet autentisera till den här prenumerationen oberoende av den överordnade runbooken.

Om jobb inom samma Automation-konto fungerar med mer än en prenumeration kan det ändra den valda prenumerationskontexten för andra jobb om du väljer en prenumeration i ett jobb. Undvik den här `Disable-AzContextAutosave –Scope Process` situationen i början av varje runbook. Den här åtgärden sparar bara kontexten till körningen av runbook.

### <a name="example"></a>Exempel

I följande exempel startar en underordnad runbook med parametrar och `Start-AzAutomationRunbook` väntar sedan `Wait` på att den ska slutföras med hjälp av cmdlet med parametern. När du är klar samlar exemplet cmdlet-utdata från den underordnade runbooken. Om `Start-AzAutomationRunbook`du vill använda måste skriptet autentisera till din Azure-prenumeration.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Jämförelse av metoder för att anropa en underordnad runbook

I följande tabell sammanfattas skillnaderna mellan de två sätten att anropa en runbook från en annan runbook.

|  | Infogad | Cmdlet |
|:--- |:--- |:--- |
| Jobb |Underordnade runbooks körs i samma jobb som överordnade. |Ett separat jobb skapas för den underordnade runbooken. |
| Körnings- |Överordnad runbook väntar på att underordnad runbook ska slutföras innan du fortsätter. |Överordnad runbook fortsätter omedelbart efter att den underordnade runbooken har startats *eller* den överordnade runbooken väntar på att det underordnade jobbet ska slutföras. |
| Resultat |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad runbook måste hämta utdata från det underordnade runbook-jobbet *eller* att den överordnade runbooken direkt kan hämta utdata från underordnad runbook. |
| Parametrar |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för de underordnade runbookparametrarna måste kombineras till en enda hashtable. Den här hashtablen kan bara innehålla enkla datatyper, matris- och objektdatatyper som använder JSON-serialisering. |
| Automation-konto |Överordnad runbook kan bara använda underordnad runbook i samma Automation-konto. |Överordnade runbooks kan använda en underordnad runbook från alla Automation-konton, från samma Azure-prenumeration och även från en annan prenumeration som du har en anslutning till. |
| Publicera |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad runbook publiceras när som helst innan den överordnade runbooken startas. |

## <a name="next-steps"></a>Nästa steg

* [Starta en runbook i Azure Automation](start-runbooks.md)
* [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)