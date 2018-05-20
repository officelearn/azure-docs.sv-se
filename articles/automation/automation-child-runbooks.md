---
title: Underordnade runbooks i Azure Automation
description: Beskriver de olika metoderna för att starta en runbook i Azure Automation från en annan runbook och dela information med varandra.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0511c2bf7eed15f997f8444c945afb18179bbc63
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="child-runbooks-in-azure-automation"></a>Underordnade runbooks i Azure Automation

Det är bästa praxis i Azure Automation för att skriva återanvändningsbara, modulbaserade runbooks med en diskret funktion som kan användas av andra runbooks. En överordnad runbook anropar ofta en eller flera underordnade runbooks för att utföra en funktion som krävs. Det finns två sätt att anropa en underordnad runbook och var och en har tydliga skillnader som du bör känna till så att du kan bestämma vilket som är bäst för dina olika scenarier.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Anropa en underordnad runbook med infogad körning

Om du vill aktivera en infogad runbook från en annan runbook, ange namnet på en runbook och ange värden för parametrarna exakt samma sätt som du använder en aktivitet eller cmdlet.  Alla runbooks i samma Automation-kontot är tillgängliga för alla andra som ska användas i det här sättet. Den överordnade runbooken väntar på att den underordnade runbooken ska slutföras innan du fortsätter till nästa rad och eventuella utdata returneras direkt till överordnat.

När du aktiverar en infogad runbook körs i samma jobb som den överordnade runbooken. Det kommer inte att visa jobbets historik på underordnad runbook som kördes. Eventuella undantag och strömmad utdata från den underordnade runbooken kommer att associeras med överordnat. Detta innebär färre jobb och gör det enklare att spåra och felsöka, eftersom alla undantag från den underordnade runbooken och all strömmad utdata är associerade med det överordnade jobbet.

När en runbook publiceras måste alla underordnade runbooks som anropas redan publiceras. Det beror på att Azure Automation bygger en association med någon av underordnade runbooks när en runbook kompileras. Om de inte är den överordnade runbooken kommer att visas publicerar korrekt, men genererar ett undantag när den startas. Om det händer kan du publicera om den överordnade runbooken för att korrekt referens till underordnade runbooks. Du behöver inte publicera om den överordnade runbooken om någon av underordnade runbooks ändras eftersom associationen kommer redan har skapats.

Parametrarna för en underordnad runbook som anropas internt kan vara en datatyp som inkluderar komplexa objekt och det finns inga [JSON-serialisering](automation-starting-a-runbook.md#runbook-parameters) eftersom det inte finns när du startar runbooken med Azure-portalen eller med den Start-AzureRmAutomationRunbook cmdlet.

### <a name="runbook-types"></a>Runbook-typer

Vilka typer kan anropa varandra:

* En [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) och [grafiska runbook-flöden](automation-runbook-types.md#graphical-runbooks) kan anropa varje infogad som (båda är PowerShell-baserat).
* En [PowerShell-arbetsflödesrunbook](automation-runbook-types.md#powershell-workflow-runbooks) och grafisk PowerShell-arbetsflöde runbooks kan anropa varje infogad som (båda är PowerShell-arbetsflöde baserat)
* PowerShell-typer och PowerShell-arbetsflöde typer kan inte anropa varandra infogade och måste använda Start AzureRmAutomationRunbook.

När du publicera ordning frågan:

* Publicera ordningen för runbooks gäller endast för PowerShell-arbetsflödet och grafisk PowerShell-arbetsflöde runbooks.

När du anropar en grafisk eller PowerShell-arbetsflöde underordnad runbook med infogad körning, använder du bara namnet på runbook.  När du anropar en underordnad runbook som PowerShell, du måste föregås namnet med *.\\*  att ange att skriptet finns i den lokala katalogen. 

### <a name="example"></a>Exempel

I följande exempel anropar ett test en underordnad runbook som accepterar tre parametrar, ett komplext objekt, ett heltal och ett booleskt värde. Utdata från den underordnade runbooken tilldelas till en variabel.  I det här fallet är den underordnade runbooken PowerShell-arbetsflödesrunbook

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Följande är det här exemplet använder en PowerShell-runbook som underordnad.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Starta en underordnad runbook med hjälp av cmdlet

Du kan använda den [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) att starta en runbook enligt beskrivningen i [att starta en runbook med Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Det finns två lägen för användning av denna cmdlet.  I ett läge returnerar cmdleten jobb-id som underordnade jobb skapas för den underordnade runbooken.  I andra läge, som du aktiverar genom att ange den **-vänta** parameter för cmdlet ska vänta tills underordnat jobbet har slutförts och returnerar resultatet från den underordnade runbooken.

Jobbet från en underordnad runbook som startas med en cmdlet kommer att köras i ett separat jobb från den överordnade runbooken. Detta innebär fler jobb än anropas infogad runbook och gör dem svårare att spåra. Överordnat kan starta flera underordnade runbooks asynkront utan att vänta på att alla ska slutföras. För att få samma typ av parallell körning vid anrop av infogade underordnade runbooks, skulle den överordnade runbooken måste du använda den [parallellt nyckelord](automation-powershell-workflow.md#parallel-processing).

Resultatet av underordnade runbooks inte returneras till den överordnade runbooken på ett tillförlitligt sätt på grund av tidsinställning. Även vissa variabler som $VerbosePreference $WarningPreference, och andra kan inte spridas till underordnade runbooks. För att undvika de här problemen, du kan anropa underordnade runbooks som separata Automation-jobb med hjälp av den `Start-AzureRmAutomationRunbook` med den `-Wait` växla. Detta hindrar den överordnade runbooken tills den underordnade runbooken har slutförts.

Om du inte vill att den överordnade runbooken ska blockeras på väntar du kan anropa en underordnad runbook med hjälp av `Start-AzureRmAutomationRunbook` cmdlet utan den `-Wait` växla. Sedan måste du använda `Get-AzureRmAutomationJob` att vänta tills jobbet har slutförts, och `Get-AzureRmAutomationJobOutput` och `Get-AzureRmAutomationJobOutputRecord` att hämta resultaten.

Parametrar för en underordnad runbook som startas med en cmdlet tillhandahålls som en hash-tabell enligt beskrivningen i [Runbookparametrar](automation-starting-a-runbook.md#runbook-parameters). Endast enkla datatyper kan användas. Om runbooken har en parameter med en komplex datatyp, det måste den anropas infogad.

### <a name="example"></a>Exempel

I följande exempel startas en underordnad runbook med parametrar och väntar sedan tills den är klar med Start-AzureRmAutomationRunbook-vänta parametern. När slutförd samlas utdata från den underordnade runbooken.

```azurepowershell-interactive
$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}
$joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Jämförelse av metoder för att anropa en underordnad runbook
I följande tabell sammanfattas skillnaderna mellan de två metoderna för att anropa en runbook från en annan runbook.

|  | Infogad | Cmdlet |
|:--- |:--- |:--- |
| Jobb |Underordnade runbooks körs i samma jobb som överordnad. |Ett separat jobb skapas för den underordnade runbooken. |
| Körnings- |Överordnad runbook väntar på att den underordnade runbooken ska slutföras innan du fortsätter. |Överordnad runbook fortsätter omedelbart efter att underordnad runbook startas *eller* överordnad runbook väntar underordnade jobbet är slutfört. |
| Resultat |Överordnad runbook kan hämta utdata direkt från underordnad runbook. |Överordnad runbook måste hämta utdata från underordnat runbook-jobb *eller* överordnad runbook kan hämta utdata direkt från underordnad runbook. |
| Parametrar |Värden för parametrar i underordnad runbook anges separat och kan använda alla datatyper. |Värden för parametrar i underordnad runbook måste kombineras i en enda hash-tabell och kan endast innehålla enkla, matris- och objektdatatyper som använder JSON-serialisering. |
| Automation-konto |Överordnad runbook kan bara använda underordnad runbook på samma automation-konto. |Överordnad runbook kan använda underordnad runbook från en automation-konto från samma Azure-prenumerationen och även en annan prenumeration om du har en anslutning till den. |
| Publicering |Underordnad runbook måste publiceras innan överordnad runbook publiceras. |Underordnad runbook måste publiceras innan överordnad runbook startas. |

## <a name="next-steps"></a>Nästa steg

* [Starta en runbook i Azure Automation](automation-starting-a-runbook.md)
* [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md)
