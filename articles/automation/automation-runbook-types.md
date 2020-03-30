---
title: Azure Automation Runbook-typer
description: 'Beskriver de olika typer av runbooks som du kan använda i Azure Automation och överväganden som du bör ta hänsyn till när du bestämmer vilken typ som ska användas. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278615"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-runbooktyper

Azure Automation stöder flera typer av runbooks som beskrivs kortfattat i följande tabell.  Avsnitten nedan ger ytterligare information om varje typ, inklusive överväganden om när du ska använda varje.

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks)|Baserat på Windows PowerShell och skapas och redigeras helt i grafisk redigerare i Azure-portalen. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks)|Baserat på Windows PowerShell-arbetsflöde och skapas och redigeras helt i den grafiska redigeraren i Azure-portalen. |
| [Powershell](#powershell-runbooks) |Lärokörbok baserad på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks)|Lärokörbok baserad på Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Läroprogram baserat på Python. |

## <a name="graphical-runbooks"></a>Grafiska runbooks

[Grafiska](automation-runbook-types.md#graphical-runbooks) och grafiska PowerShell-arbetsflödeskörningar skapas och redigeras med den grafiska redigeraren i Azure-portalen.  Du kan exportera dem till en fil och sedan importera dem till ett annat automatiseringskonto. Men du kan inte skapa eller redigera dem med ett annat verktyg. Grafiska runbooks genererar PowerShell-kod, men du kan inte direkt visa eller ändra koden. Grafiska runbooks kan inte konverteras till något av [textformaten,](automation-runbook-types.md)och inte heller kan en lärorunda konverteras till grafiskt format. Grafiska runbooks kan konverteras till grafiska PowerShell-arbetsflödesrunbooks under import och tvärtom.

### <a name="advantages"></a>Fördelar

* Visuell programförfattningsmodell för att infoga-länk
* Fokus på hur data flödar genom processen
* Visuellt representera hanteringsprocesser
* Inkludera andra runbooks som underordnade runbooks för att skapa arbetsflöden på hög nivå
* Uppmuntrar modulär programmering

### <a name="limitations"></a>Begränsningar

* Det går inte att redigera runbook utanför Azure-portalen.
* Kan kräva en kodaktivitet som innehåller PowerShell-kod för att köra komplex logik.
* Det går inte att visa eller direkt redigera PowerShell-koden som skapas av det grafiska arbetsflödet. Du kan visa koden du skapar i valfri kodaktiviteter.
* Det går inte att köras på en Linux Hybrid Runbook Worker

## <a name="powershell-runbooks"></a>PowerShell-runböcker

PowerShell-runbooks baseras på Windows PowerShell.  Du redigerar direkt koden för runbooken med textredigeraren i Azure-portalen.  Du kan också använda valfri offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

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
* Det går inte att köras på en Linux Hybrid Runbook Worker

## <a name="python-runbooks"></a>Python runbooks

Python runbooks kompilerar under Python 2.  Du kan redigera koden för runbooken direkt med textredigeraren i Azure-portalen, eller med en offlinetextredigerare och [importera runbooken](manage-runbooks.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Använd de robusta Python-biblioteken.
* Kan köras i Azure eller på båda Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers stöds med [python2.7](https://www.python.org/downloads/release/latest/python2) installerat.

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med Python-skript.
* Endast Python 2 stöds för tillfället, vilket innebär att Python 3-specifika funktioner misslyckas.
* Om du vill använda bibliotek från tredje part måste du [importera paketet](python-packages.md) till Automation-kontot för att det ska kunna användas.

## <a name="considerations"></a>Överväganden

Ta hänsyn till följande ytterligare överväganden vid fastställandet av vilken typ som ska användas för en viss runbook.

* Du kan inte konvertera runbooks från grafisk till texttyp eller tvärtom.
* Det finns begränsningar med runbooks av olika typer som en underordnad runbook. Mer information finns i [Underordnade runbooks i Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk runbook-redigering finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Information om skillnaderna mellan PowerShell- och PowerShell-arbetsflöden för runbooks finns i [Lära sig Windows PowerShell-arbetsflöden](automation-powershell-workflow.md)
* Mer information om hur du skapar eller [importerar](manage-runbooks.md) en Runbook finns i Skapa eller importera en runbook
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
