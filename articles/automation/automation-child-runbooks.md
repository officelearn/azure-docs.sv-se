---
title: Underordnade runbooks i Azure Automation
description: Beskriver de olika metoderna för att starta en runbook i Azure Automation från en annan runbook och dela information med varandra.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 37cf44e2c9d28b1aac8f2ab80ba29d126fb8651f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422976"
---
# <a name="child-runbooks-in-azure-automation"></a>Underordnade runbooks i Azure Automation

Det är en rekommendation i Azure Automation för att skriva återanvändningsbara, modulbaserade runbooks med en diskret funktion som är av andra runbooks. En överordnad runbook anropar ofta en eller flera underordnade runbooks för att utföra nödvändiga funktioner. Det finns två sätt att anropa en underordnad runbook och var och en har tydliga skillnader som du bör känna till så att du kan fastställa som passar bäst för dina olika scenarier.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Anropa en underordnad runbook med infogad körning

Om du vill aktivera en infogad runbook från en annan runbook, Använd namnet på runbooken och ange värden för parametrarna exakt samma sätt som du använder en aktivitet eller cmdlet.  Alla runbooks i samma Automation-kontot är tillgängliga för alla andra som ska användas i det här sättet. Den överordnade runbooken väntar på att den underordnade runbooken ska slutföras innan du går till nästa rad och eventuella utdata returneras direkt till överordnat.

När du anropar en infogad runbook körs i samma jobb som den överordnade runbooken. Det finns ingen indikation i jobbhistoriken på underordnad runbook som kördes. Eventuella undantag och strömmad utdata från den underordnade runbooken är associerad med överordnat. Detta innebär färre jobb och gör det enklare att spåra och felsöka eftersom alla undantag från den underordnade runbooken och någon av dess stream-utdata är associerade med det överordnade jobbet.

När en runbook publiceras måste alla underordnade runbooks som anropas redan publiceras. Det beror på att Azure Automation bygger en association med någon av underordnade runbooks när en runbook kompileras. Om de inte är den överordnade runbooken publicerar korrekt visas, men genererar ett undantag när den startas. Om det händer kan du publicera om den överordnade runbooken för att korrekt referens till underordnade runbooks. Du behöver inte att publicera den överordnade runbooken om någon av underordnade runbooks ändras eftersom associationen redan har skapats.

Parametrarna för en underordnad runbook som anropas internt kan vara en datatyp som inkluderar komplexa objekt. Det finns inga [JSON-serialisering](automation-starting-a-runbook.md#runbook-parameters) eftersom det inte finns när du startar en runbook med Azure portal eller med cmdleten Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Runbook-typer

Vilka typer kan anropa varandra:

* En [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) och [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) kan anropa varje andra infogad (båda är PowerShell-baserat).
* En [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) och grafiska PowerShell Workflow-runbooks kan anropa varje andra infogad (båda är PowerShell-arbetsflöde baserat)
* PowerShell-typer och PowerShell Workflow-typer kan inte anropa varandra infogade och måste använda Start-AzureRmAutomationRunbook.

När du publicera ordning bara:

* Publicera ordningen för runbooks är bara viktig för PowerShell-arbetsflödet och grafiska PowerShell Workflow-runbooks.

När du anropar en grafisk eller PowerShell-arbetsflöde underordnad runbook med infogad körning, kan du använda namnet på runbooken.  När du anropar en underordnad runbook som PowerShell, måste du starta dess namn med *.\\*  att ange att skriptet finns i den lokala katalogen.

### <a name="example"></a>Exempel

I följande exempel startar ett test en underordnad runbook som accepterar tre parametrar, ett komplext objekt, ett heltal och ett booleskt värde. Utdata från den underordnade runbooken tilldelas till en variabel.  I det här fallet är den underordnade runbooken PowerShell Workflow-runbook.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Nedan följer samma exempel med hjälp av en PowerShell-runbook som barnet.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Starta en underordnad runbook med hjälp av cmdlet:

> [!IMPORTANT]
> Om du anropar en underordnad runbook med den `Start-AzureRmAutomationRunbook` cmdlet med den `-Wait` växeln och resultatet av den underordnade runbooken är ett objekt, kan det uppstå fel. Om du vill kringgå felet, se [underordnade runbooks med objektutdata](troubleshoot/runbooks.md#child-runbook-object) information om hur du implementerar logik för att söka efter resultaten och använda den [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Du kan använda den [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) cmdlet för att starta en runbook enligt beskrivningen i [att starta en runbook med Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Det finns två lägen för denna cmdlet.  I ett läge returnerar cmdlet: en jobb-id när underordnat jobb skapas för den underordnade runbooken.  I det andra läget som du gör det genom att ange den **-vänta** parametern cmdleten väntar tills det underordnade jobbet har slutförts och returnerar resultatet från den underordnade runbooken.

Jobbet från en underordnad runbook som startas med en cmdlet körs i ett separat jobb från den överordnade runbooken. Detta innebär fler jobb än att starta infogad runbook och gör dem svårare att spåra. Överordnat kan starta mer än en underordnad runbook asynkront utan att behöva vänta på att alla ska slutföras. För att få samma typ av parallell körning vid anrop av infogade underordnade runbooks, skulle den överordnade runbooken måste du använda den [parallellt nyckelord](automation-powershell-workflow.md#parallel-processing).

Resultatet av underordnade runbooks inte tillbaka till den överordnade runbooken på ett tillförlitligt sätt på grund av tiden. Även vissa variabler som $VerbosePreference, $WarningPreference, och andra inte att överföra till underordnade runbooks. För att undvika dessa problem kan du starta underordnade runbooks som separata Automation-jobb med hjälp av den `Start-AzureRmAutomationRunbook` cmdlet med den `-Wait` växla. Detta hindrar den överordnade runbooken tills den underordnade runbooken har slutförts.

Om du inte vill den överordnade runbooken blockeras på väntar du startar en underordnad runbook med hjälp av `Start-AzureRmAutomationRunbook` cmdlet utan att den `-Wait` växla. Sedan måste du använda `Get-AzureRmAutomationJob` vänta tills jobbet är slutfört, och `Get-AzureRmAutomationJobOutput` och `Get-AzureRmAutomationJobOutputRecord` att hämta resultaten.

Parametrar för en underordnad runbook som startas med en cmdlet tillhandahålls som en hash-tabell enligt beskrivningen i [Runbookparametrar](automation-starting-a-runbook.md#runbook-parameters). Endast enkla datatyper kan användas. Om runbooken har en parameter med en komplex datatyp, det måste den anropas infogad.

Prenumerationskontexten kan gå förlorade när du startar underordnade runbooks som separat jobb. För den underordnade runbooken ska köra Azure RM-cmdlet: ar mot en specifik Azure-prenumeration, måste den underordnade runbooken autentisera till den här prenumerationen oberoende av den överordnade runbooken.

Om jobb i samma Automation-kontot fungerar med mer än en prenumeration, kan om du väljer en prenumeration för ett jobb ändras den markerade prenumerationskontexten för andra jobb. Du kan undvika det här problemet genom att använda `Disable-AzureRmContextAutosave –Scope Processsave` i början av varje runbook. Den här åtgärden sparar endast kontexten till den runbook-körningen.

### <a name="example"></a>Exempel

I följande exempel startar en underordnad runbook med parametrar och väntar sedan tills den är klar med Start-AzureRmAutomationRunbook-vänta parametern. När slutförd samlas utdata från den underordnade runbooken. Att använda `Start-AzureRmAutomationRunbook`, du måste autentisera till Azure-prenumerationen.

```azurepowershell-interactive
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
    -DefaultProfile $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Jämförelse av metoder för att anropa en underordnad runbook

I följande tabell sammanfattas skillnaderna mellan de två metoderna för att anropa en runbook från en annan runbook.

|  | Infogad | Cmdlet: |
|:--- |:--- |:--- |
| Jobbet |Underordnade runbooks körs i samma jobb som det överordnade objektet. |Ett separat jobb skapas för den underordnade runbooken. |
| Körning |Överordnad runbook väntar tills den underordnade runbooken ska slutföras innan du fortsätter. |Överordnad runbook fortsätter omedelbart efter att underordnad runbook har startats *eller* överordnad runbook väntar underordnade jobbet är slutfört. |
| Resultat |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad runbook måste hämta utdata från underordnat runbook-jobb *eller* överordnad runbook kan hämta utdata direkt från underordnad runbook. |
| Parametrar |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för underordnade runbook-parametrar måste kombineras i en enda hash-tabell. Den här hash-tabell kan endast innehålla enkla, matris och objekt-datatyper som använder JSON-serialisering. |
| Automation-konto |Överordnad runbook kan bara använda underordnad runbook på samma automation-konto. |Överordnade runbooks kan använda en underordnad runbook från alla automation-konto från samma Azure-prenumeration och även en annan prenumeration som du har en anslutning till. |
| Publicering |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad runbook måste publiceras när som helst innan överordnad runbook startas. |

## <a name="next-steps"></a>Nästa steg

* [Starta en runbook i Azure Automation](automation-starting-a-runbook.md)
* [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)

