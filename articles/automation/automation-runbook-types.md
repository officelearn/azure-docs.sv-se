---
title: Azure Automation Runbook-typer
description: 'Beskriver de olika typerna av Runbooks som du kan använda i Azure Automation och överväganden som du bör ta hänsyn till när du bestämmer vilken typ som ska användas. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373142"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook-typer

Azure Automation stöder flera typer av Runbooks som beskrivs kortfattat i följande tabell.  I avsnitten nedan finns mer information om varje typ, inklusive överväganden när du ska använda dem.

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Baserat på Windows PowerShell och skapas och redige ras helt i grafiskt redigerings program i Azure Portal. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Baserat på Windows PowerShell-arbetsflöde och skapas och redige ras helt i den grafiska redigeraren i Azure Portal. |
| [PowerShell](#powershell-runbooks) |Text-runbook som baseras på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Text-runbook som baseras på Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Text-Runbook baserat på python. |

## <a name="graphical-runbooks"></a>Grafiska runbooks

De [grafiska](automation-runbook-types.md#graphical-runbooks) och grafiska PowerShell Workflow-Runbooks skapas och redige ras med den grafiska redigeraren i Azure Portal.  Du kan exportera dem till en fil och sedan importera dem till ett annat Automation-konto. Men du kan inte skapa eller redigera dem med ett annat verktyg. Grafiska runbooks genererar PowerShell-kod, men du kan inte direkt Visa eller ändra koden. Grafiska runbooks kan inte konverteras till ett [text format](automation-runbook-types.md)och kan inte heller en text-Runbook konverteras till grafiskt format. Grafiska runbooks kan konverteras till grafiska PowerShell Workflow-Runbooks under import och på annat sätt.

### <a name="advantages"></a>Fördelar

* Visuell infogning-länka-konfigurera redigerings modell
* Fokusera på hur data flödar genom processen
* Visa hanterings processer visuellt
* Ta med andra Runbooks som underordnade Runbooks för att skapa arbets flöden på hög nivå
* Uppmuntrar modulär programmering

### <a name="limitations"></a>Begränsningar

* Det går inte att redigera Runbook utanför Azure Portal.
* Kan kräva en kod aktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att visa eller direkt redigera PowerShell-koden som skapas av det grafiska arbets flödet. Du kan visa koden som du skapar i alla kod aktiviteter.
* Kan inte köras på en Linux-Hybrid Runbook Worker

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks är baserade på Windows PowerShell.  Du redigerar koden för runbooken direkt med hjälp av text redigeraren i Azure Portal.  Du kan också använda valfri text redigerare och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera alla komplex logik med PowerShell-kod utan ytterligare komplexiteten i PowerShell-arbetsflöde.
* Runbook startar snabbare än PowerShell Workflow-runbooks eftersom det inte behöver kompileras innan du kör.
* Kan köras i Azure eller i både Linux-och Windows hybrid Runbook Worker

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med PowerShell-skript.
* Det går inte att använda [parallell bearbetning](automation-powershell-workflow.md#parallel-processing) för att köra flera åtgärder parallellt.
* Det går inte att använda [kontroll punkter](automation-powershell-workflow.md#checkpoints) för att återuppta Runbook om det uppstår ett fel.
* PowerShell Workflow-Runbooks och grafiska runbooks kan bara inkluderas som underordnade Runbooks med hjälp av cmdleten Start-AzureAutomationRunbook, som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är aktuella kända problem med PowerShell-Runbooks.

* PowerShell-Runbooks kan inte hämta en okrypterad [variabel till gång](automation-variables.md) med ett null-värde.
* PowerShell-Runbooks kan inte hämta en [variabel till gång](automation-variables.md) med *~* i namnet.
* Get-process i en slinga i en PowerShell-Runbook kan krascha efter cirka 80 iterationer.
* En PowerShell-Runbook kan Miss lyckas om den försöker skriva en stor mängd data till utdataströmmen samtidigt.   Du kan vanligt vis kringgå det här problemet genom att placera bara den information du behöver när du arbetar med stora objekt.  I stället för att lägga till något som *Get-process*kan du till exempel bara skriva ut de obligatoriska fälten med *Get-process | Välj ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell Workflow-Runbooks

PowerShell Workflow-Runbooks är text-Runbooks baserade på [Windows PowerShell-arbetsflöde](automation-powershell-workflow.md).  Du redigerar koden för runbooken direkt med hjälp av text redigeraren i Azure Portal.  Du kan också använda valfri text redigerare och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera alla komplex logik med PowerShell Workflow-kod.
* Använd [kontroll punkter](automation-powershell-workflow.md#checkpoints) för att återuppta Runbook om det uppstår ett fel.
* Använd [parallell bearbetning](automation-powershell-workflow.md#parallel-processing) för att utföra flera åtgärder parallellt.
* Kan inkludera andra grafiska runbooks och PowerShell Workflow-Runbooks som underordnade Runbooks för att skapa arbets flöden på hög nivå.

### <a name="limitations"></a>Begränsningar

* Författare måste vara bekant med PowerShell-arbetsflöde.
* Runbooken måste hantera den ytterligare komplexiteten hos PowerShell-arbetsflöde, till exempel [avserialiserade objekt](automation-powershell-workflow.md#code-changes).
* Runbook tar längre tid att starta än PowerShell-runbooks eftersom den måste kompileras innan du kör.
* PowerShell-Runbooks kan bara inkluderas som underordnade Runbooks med hjälp av cmdleten Start-AzureAutomationRunbook, som skapar ett nytt jobb.
* Kan inte köras på en Linux-Hybrid Runbook Worker

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks kompileras under python 2.  Du kan redigera koden för runbooken direkt med hjälp av text redigeraren i Azure Portal eller med en text redigerare offline och [Importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Använd robusta python-bibliotek.
* Kan köras i Azure eller i både Linux hybrid Runbook Worker. Windows hybrid Runbook Worker stöds med [python 2.7](https://www.python.org/downloads/release/latest/python2) installerat.

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med python-skript.
* Endast python 2 stöds för tillfället, vilket innebär att python 3-funktioner inte fungerar.
* Om du vill använda bibliotek från tredje part måste du [importera paketet](python-packages.md) till Automation-kontot för att det ska kunna användas.

## <a name="considerations"></a>Överväganden

Ta hänsyn till följande ytterligare överväganden när du bestämmer vilken typ som ska användas för en viss Runbook.

* Du kan inte konvertera Runbooks från grafisk till text typ eller tvärtom.
* Det finns begränsningar med hjälp av Runbooks av olika typer som underordnad Runbook. Mer information finns i [underordnade Runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering av Runbook finns [i grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Information om skillnaderna mellan PowerShell-och PowerShell-arbetsflöden för Runbooks finns i [Learning Windows PowerShell-arbetsflöde](automation-powershell-workflow.md)
* Mer information om hur du skapar eller importerar en Runbook finns i [skapa eller importera en Runbook](manage-runbooks.md)
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).
