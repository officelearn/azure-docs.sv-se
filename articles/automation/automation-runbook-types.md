---
title: Azure Automation Runbook-typer
description: 'Beskriver de olika typerna av runbooks som du kan använda i Azure Automation- och säkerhetsaspekter som du bör beakta när du fastställer vilken typ som ska användas. '
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 800380c68e1bb1314021ab60f92ccd3b392dbe32
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="azure-automation-runbook-types"></a>Typer av Azure Automation-runbook
Azure Automation har stöd för flera typer av runbooks som beskrivs kortfattat i följande tabell.  I avsnitten nedan innehåller ytterligare information om varje typ av bland annat tänka på när du använder.

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks) |Baserat på Windows PowerShell och skapas och redigerade helt i grafiska redigerare i Azure-portalen. |
| [Grafisk PowerShell-arbetsflöde](#graphical-runbooks) |Baserat på Windows PowerShell-arbetsflöde och skapas och redigeras helt i grafiska redigerare i Azure-portalen. |
| [PowerShell](#powershell-runbooks) |Text-runbook som baseras på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks) |Text-runbook som baseras på Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Text-runbook baserat på Python. |

## <a name="graphical-runbooks"></a>Grafiska runbook-flöden
[Grafisk](automation-runbook-types.md#graphical-runbooks) och grafisk PowerShell-arbetsflöde runbooks skapas och redigeras med grafiska redigerare i Azure-portalen.  Du kan exportera dem till en fil och sedan importera dem till en annan automation-konto, men du kan inte skapa eller redigera dem med ett annat verktyg.  Grafiska runbook-flöden generera PowerShell-kod, men du kan inte visa eller ändra koden. Grafiska runbook-flöden kan inte konverteras till en av de [textformat](automation-runbook-types.md), eller kan en text-runbook konverteras till grafiskt format. Grafiska runbook-flöden kan konverteras till grafisk PowerShell-arbetsflöde runbooks under import och vice versa.

### <a name="advantages"></a>Fördelar
* Visual insert-länk-konfigurera redigering modellen  
* Fokusera på hur data flödar genom processen  
* Visuellt representera hanteringsprocesser  
* Med andra runbooks som underordnade runbooks för att skapa avancerade arbetsflöden  
* Uppmuntrar modulära programmering  


### <a name="limitations"></a>Begränsningar
* Det går inte att redigera runbook utanför Azure-portalen.
* Kan kräva en kod aktivitet som innehåller PowerShell-kod för att utföra komplex logik.
* Det går inte att visa eller redigera direkt PowerShell-koden som skapas av grafiskt arbetsflöde. Observera att du kan visa den kod som du skapar i koden aktiviteter.

## <a name="powershell-runbooks"></a>PowerShell-runbooks
PowerShell-runbooks är baserade på Windows PowerShell.  Du redigera direkt koden för runbook med textredigeraren i Azure-portalen.  Du kan också använda en textredigerare som offline och [importera runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) i Azure Automation.

### <a name="advantages"></a>Fördelar
* Implementera alla komplex logik med PowerShell-koden utan ytterligare svårigheter med PowerShell-arbetsflöde. 
* Runbook startar snabbare än PowerShell-arbetsflöde runbooks eftersom det inte behöver kompileras innan du kör.

### <a name="limitations"></a>Begränsningar
* Måste vara bekant med PowerShell-skript.
* Det går inte att använda [parallellbearbetning](automation-powershell-workflow.md#parallel-processing) att utföra flera åtgärder parallellt.
* Det går inte att använda [kontrollpunkter](automation-powershell-workflow.md#checkpoints) att återuppta runbook vid fel.
* PowerShell-arbetsflöde runbooks och grafiska runbook-flöden kan bara ingå som underordnade runbooks med hjälp av cmdleten Start-AzureAutomationRunbook som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem
Följande är aktuella kända problem med PowerShell-runbooks.

* PowerShell-runbooks kan inte hämta en okrypterad [variabeltillgång](automation-variables.md) med ett null-värde.
* PowerShell-runbooks kan inte hämta en [variabeltillgång](automation-variables.md) med *~* i namnet.
* Get-Process i en loop i ett PowerShell-runbook kan krascha efter ca 80 iterationer. 
* En PowerShell-runbook kan misslyckas om det görs ett försök att skriva en stor mängd data till utdataströmmen samtidigt.   Du kan ofta undvika det här problemet genom att skicka ut bara den information som du behöver när du arbetar med stora objekt.  Till exempel i stället för att mata ut ungefär så *Get-Process*, du kan spara de obligatoriska fälten med *Get-Process | Välj processnamn CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell-arbetsflöde runbooks
PowerShell-arbetsflöde runbooks är text runbooks enligt [Windows PowerShell-arbetsflöde](automation-powershell-workflow.md).  Du redigera direkt koden för runbook med textredigeraren i Azure-portalen.  Du kan också använda en textredigerare som offline och [importera runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) i Azure Automation.

### <a name="advantages"></a>Fördelar
* Implementera alla komplex logik med PowerShell-arbetsflöde kod.
* Använd [kontrollpunkter](automation-powershell-workflow.md#checkpoints) att återuppta runbook vid fel.
* Använd [parallellbearbetning](automation-powershell-workflow.md#parallel-processing) att utföra flera åtgärder parallellt.
* Kan innehålla andra grafiska runbook-flöden och PowerShell-arbetsflöde runbooks som underordnade runbooks för att skapa avancerade arbetsflöden.

### <a name="limitations"></a>Begränsningar
* Författare måste vara bekant med PowerShell-arbetsflöde.
* Runbook måste hantera den extra komplexiteten med PowerShell-arbetsflöde som [avserialiseras objekt](automation-powershell-workflow.md#code-changes).
* Runbook tar längre tid att starta än PowerShell runbooks eftersom den måste kompileras innan du kör.
* PowerShell-runbooks kan bara ingå som underordnade runbooks med hjälp av cmdleten Start-AzureAutomationRunbook som skapar ett nytt jobb.

## <a name="python-runbooks"></a>Python runbooks
Python runbooks kompilera under Python 2.  Du kan redigera koden för runbook med textredigeraren i Azure portal direkt eller du kan använda valfri textredigerare som offline och [importera runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) i Azure Automation.

### <a name="advantages"></a>Fördelar
* Använda robust standardbiblioteket av Python.

### <a name="limitations"></a>Begränsningar
* Måste vara bekant med Python-skript.
* Endast Python 2 stöds för tillfället, vilket innebär att Python 3 funktioner kommer att misslyckas.

### <a name="known-issues"></a>Kända problem
Följande är aktuella kända problem med Python runbooks.

* För att kunna använda tredjeparts-bibliotek runbook måste köras på en [Windows Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) eller [Linux Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) med bibliotek som är installerad på datorn innan den runbook har startats.

## <a name="considerations"></a>Överväganden
Du bör beakta följande ytterligare överväganden när du fastställer vilken typ som ska användas för en viss runbook.

* Du kan inte konvertera runbooks från grafiska till text- eller vice versa.
* Det finns begränsningar med hjälp av runbooks med olika typer som en underordnad runbook.  Se [underordnade runbooks i Azure Automation](automation-child-runbooks.md) för mer information.

## <a name="next-steps"></a>Nästa steg
* Mer information om redigering av grafisk runbook finns [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Att förstå skillnaderna mellan PowerShell och PowerShell arbetsflöden för runbooks, se [Learning Windows PowerShell-arbetsflöde](automation-powershell-workflow.md)
* Mer information om hur du skapar eller importerar en Runbook finns [skapa eller importera en Runbook](automation-creating-importing-runbook.md)

