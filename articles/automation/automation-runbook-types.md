---
title: Azure Automation-runbooktyper
description: Beskriver de olika typer av runbooks som du kan använda i Azure Automation och överväganden för att avgöra vilken typ som ska användas.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 4e8a5d2d168b8f60b7a32a8af358c6097003de60
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261303"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbooktyper

Azure Automation-tjänsten för processautomatisering stöder flera typer av runbooks, enligt definitionen i följande tabell. Mer information om processautomatiseringsmiljön finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Grafisk runbook baserad på Windows PowerShell och skapas och redigeras helt i den grafiska redigeraren i Azure-portalen. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Grafisk runbook baserad på Windows PowerShell Workflow och skapas och redigeras helt i den grafiska redigeraren i Azure-portalen. |
| [PowerShell](#powershell-runbooks) |Lärokörbok baserad på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Lärokörbok baserad på Skript för Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Läroprogram baserat på Python-skript. |

Ta hänsyn till följande överväganden vid fastställandet av vilken typ som ska användas för en viss runbook.

* Du kan inte konvertera runbooks från grafisk till texttyp eller tvärtom.
* Det finns begränsningar när du använder runbooks av olika typer som underordnade runbooks. Mer information finns i [Underordnade runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafiska runbooks

Du kan skapa och redigera grafiska och grafiska PowerShell-arbetsflödeskörningsböcker med hjälp av den grafiska redigeraren i Azure-portalen. Du kan dock inte skapa eller redigera den här typen av runbook med ett annat verktyg. Huvuddragen i grafiska runbooks:

* Kan exporteras till filer i ditt Automation-konto och sedan importeras till ett annat Automation-konto. 
* Generera PowerShell-kod. 
* Kan konverteras till eller från grafiska PowerShell-arbetsflödeskörningar under importen. 

### <a name="advantages"></a>Fördelar

* Använd visuella infogningslänk-konfigurera redigeringsmodell.
* Fokusera på hur data flödar genom processen.
* Visuellt representera hanteringsprocesser.
* Inkludera andra runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå.
* Uppmuntra modulär programmering.

### <a name="limitations"></a>Begränsningar

* Det går inte att skapa eller redigera utanför Azure-portalen.
* Kan kräva en kodaktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att konvertera till något av [textformaten](automation-runbook-types.md)och du kan inte heller konvertera en lärobok till grafiskt format. 
* Det går inte att visa eller direkt redigera PowerShell-kod som det grafiska arbetsflödet skapar. Du kan visa koden du skapar i alla kodaktiviteter.
* Det går inte att köra runbooks på en Linux Hybrid Runbook Worker. Se [Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runböcker

PowerShell-runbooks baseras på Windows PowerShell. Du redigerar direkt koden för runbooken med textredigeraren i Azure-portalen.  Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-kod utan ytterligare komplexitet i PowerShell Workflow.
* Starta snabbare än PowerShell Workflow runbooks, eftersom de inte behöver kompileras innan de körs.
* Kör i Azure och på Hybrid Runbook Workers för både Windows och Linux.

### <a name="limitations"></a>Begränsningar

* Du måste känna till PowerShell-skript.
* Runbooks kan inte använda [parallell bearbetning](automation-powershell-workflow.md#parallel-processing) för att utföra flera åtgärder parallellt.
* Runbooks kan inte använda [kontrollpunkter](automation-powershell-workflow.md#checkpoints) för att återuppta runbook om det finns ett fel.
* Du kan bara inkludera PowerShell Workflow runbooks och grafiska runbooks som underordnade runbooks med hjälp av [Cmdlet Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är aktuella kända problem med PowerShell-runbooks:

* PowerShell-runbooks kan inte hämta en okrypterad [variabel tillgång](automation-variables.md) med ett null-värde.
* PowerShell-runbooks kan inte hämta `*~*` en variabel tillgång med i namnet.
* En [Get-Process-åtgärd](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) i en loop i en PowerShell-runbook kan krascha efter cirka 80 iterationer.
* En PowerShell-runbook kan misslyckas om den försöker skriva en stor mängd data till utdataströmmen samtidigt. Du kan vanligtvis komma runt det här problemet genom att låta runbook-utdata bara den information som behövs för att arbeta med stora objekt. I stället för `Get-Process` att använda utan begränsningar kan du till exempel ha cmdlet-utdata bara de parametrar som krävs som i `Get-Process | Select ProcessName, CPU`.

## <a name="powershell-workflow-runbooks"></a>PowerShell-arbetsflödeskörningsböcker

PowerShell-arbetsflödeskörningsböcker är läroböcker som baseras på [Windows PowerShell Workflow](automation-powershell-workflow.md). Du redigerar direkt koden för runbooken med textredigeraren i Azure-portalen. Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-arbetsflödeskod.
* Använd [kontrollpunkter](automation-powershell-workflow.md#checkpoints) för att återuppta åtgärden om det finns ett fel.
* Använd [parallell bearbetning](automation-powershell-workflow.md#parallel-processing) för att utföra flera åtgärder parallellt.
* Kan inkludera andra grafiska runbooks och PowerShell Workflow runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell Workflow.
* Runbooks måste hantera den ytterligare komplexiteten i PowerShell Workflow, till exempel [deserialiserade objekt](automation-powershell-workflow.md#code-changes).
* Runbooks tar längre tid att starta än PowerShell runbooks eftersom de måste kompileras innan de körs.
* Du kan bara inkludera PowerShell-runbooks som `Start-AzAutomationRunbook` underordnade runbooks med hjälp av cmdlet.
* Runbooks kan inte köras på en Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python runbooks

Python runbooks kompilerar under Python 2. Du kan redigera koden för runbooken direkt med textredigeraren i Azure-portalen. Du kan också använda en offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Använd de robusta Python-biblioteken.
* Kan köras i Azure eller Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers stöds med [python2.7](https://www.python.org/downloads/release/latest/python2) installerat.

### <a name="limitations"></a>Begränsningar

* Du måste känna till Python-skript.
* Endast Python 2 stöds för närvarande. Alla Python 3-specifika funktioner misslyckas.
* Om du vill använda bibliotek från tredje part måste du [importera paketen](python-packages.md) till Automation-kontot.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk runbook-redigering finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
* Information om skillnaderna mellan PowerShell- och PowerShell-arbetsflöden för runbooks finns i Utbildningsflöde av [Windows PowerShell](automation-powershell-workflow.md).
* Mer information om hur du skapar eller importerar en runbook finns [i Hantera runbooks i Azure Automation](manage-runbooks.md).
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
