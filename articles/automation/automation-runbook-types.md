---
title: Azure Automation-runbooktyper
description: Beskriver de olika typer av runbooks som du kan använda i Azure Automation och överväganden som du bör ta hänsyn till när du bestämmer vilken typ som ska användas.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010179"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbooktyper

Azure Automation-tjänsten för processautomatisering stöder flera typer av runbooks. Typerna definieras kortfattat i följande tabell och beskrivs mer i detalj i avsnitten nedan. Mer information om processautomatiseringsmiljön finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Grafisk runbook baserad på Windows PowerShell och skapas och redigeras helt i den grafiska redigeraren i Azure-portalen. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Grafisk runbook baserad på Windows PowerShell Workflow och skapas och redigeras helt i den grafiska redigeraren i Azure-portalen. |
| [PowerShell](#powershell-runbooks) |Lärokörbok baserad på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Lärokörbok baserad på Skript för Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Läroprogram baserat på Python-skript. |

## <a name="graphical-runbooks"></a>Grafiska runbooks

Du kan skapa och redigera grafiska och grafiska PowerShell-arbetsflödeskörningsböcker med hjälp av den grafiska redigeraren i Azure-portalen. Du kan dock inte skapa eller redigera den här typen av runbook med ett annat verktyg.

En grafisk runbook har följande huvudfunktioner:

* Kan exporteras till en fil i ditt Automation-konto och sedan importeras till ett annat Automation-konto. 
* Genererar PowerShell-kod. 
* Kan konverteras till eller från en grafisk PowerShell-arbetsflödeskörningsbok under importen. 

### <a name="advantages"></a>Fördelar

* Använder visuella infoga-länk-konfigurera redigeringsmodell.
* Fokuserar på hur data flödar genom processen.
* Visuellt representerar hanteringsprocesser.
* Inkluderar andra runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå.
* Uppmuntrar modulär programmering.

### <a name="limitations"></a>Begränsningar

* Det går inte att skapa eller redigera utanför Azure-portalen.
* Kan kräva en kodaktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att konvertera till något av [textformaten](automation-runbook-types.md)och inte heller kan en lärobok konverteras till grafiskt format. 
* Du kan inte visa eller direkt redigera Den PowerShell-kod som det grafiska arbetsflödet skapar. Du kan visa koden du skapar i alla kodaktiviteter.
* Körs inte på en Linux Hybrid Runbook Worker. Se [Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell-runböcker

PowerShell-runbooks baseras på Windows PowerShell. Du redigerar direkt koden för runbooken med textredigeraren i Azure-portalen.  Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-kod utan ytterligare komplexitet i PowerShell Workflow.
* Runbook startar snabbare än PowerShell Workflow runbooks eftersom det inte behöver kompileras innan du kör.
* Kan köras i Azure eller på både Linux- och Windows Hybrid Runbook Workers

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med PowerShell-skript.
* Det går inte att använda [parallell bearbetning](automation-powershell-workflow.md#parallel-processing) för att utföra flera åtgärder parallellt.
* Det går inte att använda [kontrollpunkter](automation-powershell-workflow.md#checkpoints) för att återuppta runbook om det finns ett fel.
* PowerShell Workflow runbooks och graphical runbooks kan bara inkluderas som underordnade runbooks med hjälp av Cmdlet Start-AzureAutomationRunbook, som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är aktuella kända problem med PowerShell runbooks.

* PowerShell-runbooks kan inte hämta en okrypterad [variabel tillgång](automation-variables.md) med ett null-värde.
* PowerShell-runbooks kan inte hämta *~* en variabel [tillgång](automation-variables.md) med i namnet.
* Get-Process i en loop i en PowerShell-runbook kan krascha efter cirka 80 iterationer.
* En PowerShell-runbook kan misslyckas om den försöker skriva en stor mängd data till utdataströmmen samtidigt.   Du kan vanligtvis komma runt det här problemet genom att mata ut bara den information du behöver när du arbetar med stora objekt.  I stället för att mata ut något som *get-process*kan du till exempel mata ut bara de obligatoriska fälten med *Get-Process | Välj ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell-arbetsflödeskörningsböcker

PowerShell-arbetsflödeskörningsböcker är läroböcker som baseras på [Windows PowerShell Workflow](automation-powershell-workflow.md).  Du redigerar direkt koden för runbooken med textredigeraren i Azure-portalen.  Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera all komplex logik med PowerShell-arbetsflödeskod.
* Använd [kontrollpunkter](automation-powershell-workflow.md#checkpoints) för att återuppta runbook om det finns ett fel.
* Använd [parallell bearbetning](automation-powershell-workflow.md#parallel-processing) för att utföra flera åtgärder parallellt.
* Kan inkludera andra grafiska runbooks och PowerShell Workflow runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå.

### <a name="limitations"></a>Begränsningar

* Författaren måste vara bekant med PowerShell Workflow.
* Runbook måste hantera den ytterligare komplexiteten i PowerShell-arbetsflödet, till exempel [deserialiserade objekt](automation-powershell-workflow.md#code-changes).
* Runbook tar längre tid att starta än PowerShell runbooks eftersom det måste kompileras innan du kör.
* PowerShell-runbooks kan bara inkluderas som underordnade runbooks med hjälp av Cmdlet Start-AzureAutomationRunbook, som skapar ett nytt jobb.
* Det går inte att köra på en Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Python runbooks

Python runbooks kompilerar under Python 2. Du kan redigera koden för runbooken direkt med textredigeraren i Azure-portalen, eller med en offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Använd de robusta Python-biblioteken.
* Kan köras i Azure eller på båda Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers stöds med [python2.7](https://www.python.org/downloads/release/latest/python2) installerat.

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med Python-skript.
* Endast Python 2 stöds för tillfället, vilket innebär att Python 3-specifika funktioner misslyckas.
* Om du vill använda bibliotek från tredje part måste du [importera paketet](python-packages.md) till Automation-kontot för att det ska användas.

## <a name="considerations"></a>Överväganden

Ta hänsyn till följande ytterligare överväganden vid fastställandet av vilken typ som ska användas för en viss runbook.

* Du kan inte konvertera runbooks från grafisk till texttyp eller tvärtom.
* Det finns begränsningar med runbooks av olika typer som en underordnad runbook. Mer information finns i [Underordnade runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk runbook-redigering finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Information om skillnaderna mellan PowerShell- och PowerShell-arbetsflöden för runbooks finns i [Lära sig Windows PowerShell-arbetsflöden](automation-powershell-workflow.md)
* Mer information om hur du skapar eller [importerar](manage-runbooks.md) en Runbook finns i Skapa eller importera en runbook
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
