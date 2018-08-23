---
title: Underordnade runbooks i Azure Automation
description: Beskriver de olika metoderna för att starta en runbook i Azure Automation från en annan runbook och dela information med varandra.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2060239b27ef05c34ea6f5b388b4c4086a44a826
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42058184"
---
# <a name="child-runbooks-in-azure-automation"></a>Underordnade runbooks i Azure Automation

Det är bästa praxis i Azure Automation för att skriva återanvändningsbara, modulbaserade runbooks med en diskret funktion som kan användas av andra runbooks. En överordnad runbook anropar ofta en eller flera underordnade runbooks för att utföra nödvändiga funktioner. Det finns två sätt att anropa en underordnad runbook och var och en har tydliga skillnader som du bör känna till så att du kan fastställa vilket som är bäst för dina olika scenarier.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Anropa en underordnad runbook med infogad körning

Om du vill aktivera en infogad runbook från en annan runbook, Använd namnet på runbooken och ange värden för parametrarna exakt samma sätt som du använder en aktivitet eller cmdlet.  Alla runbooks i samma Automation-kontot är tillgängliga för alla andra som ska användas i det här sättet. Den överordnade runbooken väntar på att den underordnade runbooken ska slutföras innan du går till nästa rad och eventuella utdata returneras direkt till överordnat.

När du anropar en infogad runbook körs i samma jobb som den överordnade runbooken. Det kommer inte att visa jobbets historik för underordnad runbook som kördes. Eventuella undantag och strömmad utdata från den underordnade runbooken kommer att associeras med överordnat. Detta innebär färre jobb och gör det enklare att spåra och felsöka eftersom alla undantag från den underordnade runbooken och någon av dess stream-utdata är associerade med det överordnade jobbet.

När en runbook publiceras måste alla underordnade runbooks som anropas redan publiceras. Det beror på att Azure Automation bygger en association med någon av underordnade runbooks när en runbook kompileras. Om de inte är den överordnade runbooken kommer att visas publicerar korrekt, men genererar ett undantag när den startas. Om det händer kan du publicera om den överordnade runbooken för att korrekt referens till underordnade runbooks. Du behöver inte att publicera den överordnade runbooken om någon av underordnade runbooks ändras eftersom associationen kommer redan har skapats.

Parametrarna för en underordnad runbook som anropas internt kan vara en datatyp som inkluderar komplexa objekt och det finns inga [JSON-serialisering](automation-starting-a-runbook.md#runbook-parameters) eftersom det inte finns när du startar runbooken med Azure portal eller med den Start-AzureRmAutomationRunbook cmdlet.

### <a name="runbook-types"></a>Runbook-typer

Vilka typer kan anropa varandra:

* En [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) och [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) kan anropa varje andra infogad (båda är PowerShell-baserat).
* En [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) och grafiska PowerShell Workflow-runbooks kan anropa varje andra infogad (båda är PowerShell-arbetsflöde baserat)
* PowerShell-typer och PowerShell Workflow-typer kan inte anropa varandra infogade och måste använda Start-AzureRmAutomationRunbook.

När du publicera ordning bara:

* Publicera ordningen för runbooks är bara viktig för PowerShell-arbetsflödet och grafiska PowerShell Workflow-runbooks.

När du anropar en grafisk eller PowerShell-arbetsflöde underordnad runbook med infogad körning, använder du bara namnet på runbooken.  När du anropar en underordnad runbook som PowerShell, måste du starta dess namn med *.\\*  att ange att skriptet finns i den lokala katalogen.

### <a name="example"></a>Exempel

I följande exempel aktiverar ett test en underordnad runbook som accepterar tre parametrar, ett komplext objekt, ett heltal och ett booleskt värde. Utdata från den underordnade runbooken tilldelas till en variabel.  I det här fallet är den underordnade runbooken PowerShell Workflow-runbook.

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

Du kan använda den [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) cmdlet för att starta en runbook enligt beskrivningen i [att starta en runbook med Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Det finns två lägen för denna cmdlet.  I ett läge returnerar cmdlet: en jobb-id som underordnat jobb har skapats för den underordnade runbooken.  I det andra läget som du gör det genom att ange den **-vänta** parametern cmdleten ska vänta tills underordnat jobbet har slutförts och returnerar utdata från den underordnade runbooken.

Jobbet från en underordnad runbook som startas med en cmdlet körs i ett separat jobb från den överordnade runbooken. Detta innebär fler jobb än anropar en infogad runbook och gör dem svårare att spåra. Överordnat kan starta flera underordnade runbooks asynkront utan att behöva vänta på att alla ska slutföras. För att få samma typ av parallell körning vid anrop av infogade underordnade runbooks, skulle den överordnade runbooken måste du använda den [parallellt nyckelord](automation-powershell-workflow.md#parallel-processing).

Utdata från den underordnade runbooks kommer inte tillbaka till den överordnade runbooken på ett tillförlitligt sätt på grund av tiden. Även vissa variabler som $VerbosePreference, $WarningPreference, och andra inte att överföra till underordnade runbooks. För att undvika dessa problem kan du anropa underordnade runbooks som separata Automation-jobb med hjälp av den `Start-AzureRmAutomationRunbook` cmdlet med den `-Wait` växla. Detta hindrar den överordnade runbooken tills den underordnade runbooken har slutförts.

Om du inte vill att den överordnade runbooken blockeras på att vänta, du kan anropa en underordnad runbook med hjälp av `Start-AzureRmAutomationRunbook` cmdlet utan att den `-Wait` växla. Sedan måste du använda `Get-AzureRmAutomationJob` vänta tills jobbet är slutfört, och `Get-AzureRmAutomationJobOutput` och `Get-AzureRmAutomationJobOutputRecord` att hämta resultaten.

Parametrar för en underordnad runbook som startas med en cmdlet tillhandahålls som en hash-tabell enligt beskrivningen i [Runbookparametrar](automation-starting-a-runbook.md#runbook-parameters). Endast enkla datatyper kan användas. Om runbooken har en parameter med en komplex datatyp, det måste den anropas infogad.

Om du arbetar med flera prenumerationer prenumerationskontexten kan gå förlorade vid underordnade runbooks. För att säkerställa att prenumerationskontexten överförs till underordnade runbooks, lägger du till den `DefaultProfile` parameter till cmdleten och pass kontexten till den.

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

|  | Infogad | Cmdlet |
|:--- |:--- |:--- |
| Jobb |Underordnade runbooks körs i samma jobb som det överordnade objektet. |Ett separat jobb skapas för den underordnade runbooken. |
| Körnings- |Överordnad runbook väntar tills den underordnade runbooken ska slutföras innan du fortsätter. |Överordnad runbook fortsätter omedelbart efter att underordnad runbook har startats *eller* överordnad runbook väntar underordnade jobbet är slutfört. |
| Resultat |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad runbook måste hämta utdata från underordnat runbook-jobb *eller* överordnad runbook kan hämta utdata direkt från underordnad runbook. |
| Parametrar |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för underordnade runbook-parametrar måste kombineras i en enda hash-tabell och får endast innehålla enkla, matris- och objektdatatyper som använder JSON-serialisering. |
| Automation-konto |Överordnad runbook kan bara använda underordnad runbook på samma automation-konto. |Överordnad runbook kan använda underordnad runbook från alla automation-konto från samma Azure-prenumeration och även en annan prenumeration om du har en anslutning till den. |
| Publicering |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad runbook måste publiceras innan överordnad runbook startas. |

## <a name="next-steps"></a>Nästa steg

* [Starta en runbook i Azure Automation](automation-starting-a-runbook.md)
* [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)
