---
title: Azure Automation Runbook-typer
description: I den här artikeln beskrivs de typer av Runbooks som du kan använda i Azure Automation och överväganden för att bestämma vilken typ som ska användas.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 184e65c929d43e7a5d4ca3be8bd93770c55cd2a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83836574"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook-typer

Funktionen Azure Automation process automatisering stöder flera typer av Runbooks som definieras i följande tabell. Mer information om process automatiserings miljön finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Grafisk Runbook baserad på Windows PowerShell och skapas och redige ras helt i den grafiska redigeraren i Azure Portal. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Grafisk Runbook baserad på Windows PowerShell-arbetsflöde och skapas och redige ras helt i den grafiska redigeraren i Azure Portal. |
| [PowerShell](#powershell-runbooks) |Text-Runbook baserad på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Text-Runbook baserad på skript för Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Text-Runbook baserat på Python-skript. |

Ta hänsyn till följande överväganden när du bestämmer vilken typ som ska användas för en viss Runbook.

* Du kan inte konvertera Runbooks från grafiskt till text typ, eller tvärtom.
* Det finns begränsningar när du använder Runbooks av olika typer som underordnade Runbooks. Mer information finns i [underordnade Runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafiska runbooks

Du kan skapa och redigera grafiska och grafiska PowerShell Workflow-Runbooks med hjälp av den grafiska redigeraren i Azure Portal. Du kan dock inte skapa eller redigera den här typen av Runbook med ett annat verktyg. Huvud funktioner i grafiska runbooks:

* Kan exporteras till filer i ditt Automation-konto och sedan importeras till ett annat Automation-konto. 
* Generera PowerShell-kod. 
* Kan konverteras till eller från grafiska PowerShell Workflow-Runbooks under import. 

### <a name="advantages"></a>Fördelar

* Använd visuell infogning-länka-konfigurera redigerings modell.
* Fokusera på hur data flödar genom processen.
* Visar hanterings processer visuellt.
* Ta med andra Runbooks som underordnade Runbooks för att skapa arbets flöden på hög nivå.
* Uppmuntra modulär programmering.

### <a name="limitations"></a>Begränsningar

* Det går inte att skapa eller redigera utanför Azure Portal.
* Kan kräva en kod aktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att konvertera till ett [text format](automation-runbook-types.md), eller så kan du inte konvertera en text-Runbook till grafiskt format. 
* Det går inte att visa eller direkt redigera PowerShell-kod som skapas i det grafiska arbets flödet. Du kan visa koden som du skapar i alla kod aktiviteter.
* Det går inte att köra Runbooks på en Linux-Hybrid Runbook Worker. Se [automatisera resurser i ditt data Center eller i molnet med hjälp av hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-Runbooks

PowerShell-Runbooks baseras på Windows PowerShell. Du redigerar koden för runbooken direkt med hjälp av text redigeraren i Azure Portal.  Du kan också använda valfri text redigerare och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-kod utan ytterligare komplicerade arbets flöden i PowerShell.
* Starta snabbare än PowerShell Workflow-Runbooks eftersom de inte behöver kompileras innan de körs.
* Kör i Azure och i hybrid Runbook Worker för både Windows och Linux.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell-skript.
* Runbooks kan inte använda [parallell bearbetning](automation-powershell-workflow.md#use-parallel-processing) för att köra flera åtgärder parallellt.
* Runbooks kan inte använda [kontroll punkter](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) för att återuppta Runbook om det uppstår ett fel.
* Du kan inkludera endast PowerShell Workflow-Runbooks och grafiska runbooks som underordnade Runbooks med hjälp av cmdleten [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är aktuella kända problem med PowerShell-Runbooks:

* PowerShell-Runbooks kan inte hämta en okrypterad [variabel till gång](automation-variables.md) med ett null-värde.
* PowerShell-Runbooks kan inte hämta en variabel till gång med `*~*` i namnet.
* En [Get-process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) -åtgärd i en slinga i en PowerShell-Runbook kan krascha efter cirka 80 iterationer.
* En PowerShell-Runbook kan inte köras om den försöker skriva en stor mängd data till utdataströmmen samtidigt. Du kan vanligt vis undvika det här problemet genom att låta Runbook-jobbet bara mata ut den information som behövs för att arbeta med stora objekt. I stället för att använda `Get-Process` utan begränsningar kan du till exempel låta cmdleten bara mata in de nödvändiga parametrarna som i `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>PowerShell Workflow-Runbooks

PowerShell Workflow-Runbooks är text-Runbooks baserade på [Windows PowerShell-arbetsflöde](automation-powershell-workflow.md). Du redigerar koden för runbooken direkt med hjälp av text redigeraren i Azure Portal. Du kan också använda valfri text redigerare och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell Workflow Code.
* Använd [kontroll punkter](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) för att återuppta åtgärden om det uppstår ett fel.
* Använd [parallell bearbetning](automation-powershell-workflow.md#use-parallel-processing) för att utföra flera åtgärder parallellt.
* Kan inkludera andra grafiska runbooks och PowerShell Workflow-Runbooks som underordnade Runbooks för att skapa arbets flöden på hög nivå.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med PowerShell-arbetsflöde.
* Runbooks måste hantera den ytterligare komplexiteten i PowerShell-arbetsflöde, till exempel [avserialiserade objekt](automation-powershell-workflow.md#deserialized-objects).
* Runbooks tar längre tid att starta än PowerShell-Runbooks eftersom de måste kompileras innan de körs.
* Du kan bara ta med PowerShell-Runbooks som underordnade Runbooks med hjälp av `Start-AzAutomationRunbook` cmdleten.
* Runbooks kan inte köras på en Linux-Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks kompileras under python 2. Du kan redigera koden för runbooken direkt med hjälp av text redigeraren i Azure Portal. Du kan också använda en text redigerare offline och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Använd robusta python-bibliotek.
* Kan köras i Azure eller Linux hybrid Runbook Worker. Windows hybrid Runbook Worker stöds med [python 2.7](https://www.python.org/downloads/release/latest/python2) installerat.

### <a name="limitations"></a>Begränsningar

* Du måste vara bekant med python-skript.
* Endast python 2 stöds för närvarande. Eventuella python 3-/regionsspecifika funktioner fungerar inte.
* Om du vill använda bibliotek från tredje part måste du [Importera paketen](python-packages.md) till Automation-kontot.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Mer information om PowerShell Workflow-Runbooks finns i [Självstudier: skapa en PowerShell-Runbook för arbets flöden](learn/automation-tutorial-runbook-textual.md).
* Mer information om grafiska runbooks finns i [Självstudier: skapa en grafisk Runbook](learn/automation-tutorial-runbook-graphical.md).
* Mer information om python-Runbooks finns i [Självstudier: skapa en python-Runbook](learn/automation-tutorial-runbook-textual-python2.md).