---
title: Azure Automation Runbook-typer
description: 'Beskriver de olika typerna av runbooks som du kan använda i Azure Automation och överväganden som du bör tänka på när du bestämmer vilken typ som ska använda. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 572d887a14aa25e45f0ad74660c5d3fbb1d32851
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425636"
---
# <a name="azure-automation-runbook-types"></a>Typer av Azure Automation-runbooks

Azure Automation har stöd för flera typer av runbooks som beskrivs kortfattat i följande tabell.  I avsnitten nedan innehåller ytterligare information om varje typ av bland annat tänka på när du använder.

| Typ | Beskrivning |
|:--- |:--- |
| [Grafisk](#graphical-runbooks) |Baserat på Windows PowerShell och skapas och redigerade helt i grafiska redigeraren i Azure-portalen. |
| [Grafiskt PowerShell-arbetsflöde](#graphical-runbooks) |Baserade på Windows PowerShell-arbetsflöden och skapas och redigeras helt i den grafiska redigeraren i Azure-portalen. |
| [PowerShell](#powershell-runbooks) |Text-runbook som baseras på Windows PowerShell-skript. |
| [PowerShell-arbetsflöde](#powershell-workflow-runbooks) |Text-runbook som baseras på Windows PowerShell-arbetsflöde. |
| [Python](#python-runbooks) |Text-runbook är baserad på Python. |

## <a name="graphical-runbooks"></a>Grafiska runbooks

[Grafisk](automation-runbook-types.md#graphical-runbooks) och grafiska PowerShell Workflow-runbooks skapas och redigeras med den grafiska redigeraren i Azure-portalen.  Du kan exportera dem till en fil och importera dem till ett annat automation-konto, men du kan inte skapa eller redigera dem med ett annat verktyg.  Grafiska runbooks generera PowerShell-kod, men du kan inte visa eller ändra koden direkt. Grafiska runbooks kan inte konverteras till en av de [textformat](automation-runbook-types.md), och inte heller kan en text-runbook konverteras till grafiskt format. Grafiska runbooks kan konverteras till grafiska PowerShell Workflow-runbooks under importen och vice versa.

### <a name="advantages"></a>Fördelar

* Visual insert-länk-konfigurera redigering modell  
* Fokusera på hur data flödar genom processen  
* Visuellt representera processer  
* Inkludera andra runbooks som underordnade runbooks för att skapa avancerade arbetsflöden  
* Uppmuntrar modulära programmering  

### <a name="limitations"></a>Begränsningar

* Det går inte att redigera runbook utanför Azure-portalen.
* Kan kräva en kodaktiviteten som innehåller PowerShell-kod för att utföra komplex logik.
* Det går inte att visa eller redigera PowerShell-koden som skapas av grafiskt arbetsflöde direkt. Observera att du kan visa koden som du skapar i alla aktiviteter i kod.

## <a name="powershell-runbooks"></a>PowerShell-runbooks

PowerShell-runbooks är baserade på Windows PowerShell.  Du kan direkt redigera koden för en runbook med textredigeraren i Azure-portalen.  Du kan också använda textredigerare offline och [importera runbook](automation-creating-importing-runbook.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera alla komplex logik med PowerShell-kod utan ytterligare komplexiteten i PowerShell-arbetsflöde.
* Runbook startar snabbare än PowerShell Workflow-runbooks eftersom det inte behöver kompileras innan du kör.

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med PowerShell-skript.
* Det går inte att använda [parallellbearbetning](automation-powershell-workflow.md#parallel-processing) att utföra flera åtgärder parallellt.
* Det går inte att använda [kontrollpunkter](automation-powershell-workflow.md#checkpoints) att återuppta runbook vid fel.
* PowerShell Workflow-runbooks och grafiska runbooks kan bara ingå som underordnade runbooks med hjälp av cmdleten Start-AzureAutomationRunbook som skapar ett nytt jobb.

### <a name="known-issues"></a>Kända problem

Följande är aktuella kända problem med PowerShell-runbooks.

* PowerShell-runbooks kan inte hämta en icke krypterade [variabeltillgång](automation-variables.md) med ett null-värde.
* PowerShell-runbooks kan inte hämta en [variabeltillgång](automation-variables.md) med *~* i namnet.
* Get-Process i en loop i ett PowerShell-runbook kan krascha efter cirka 80 upprepningar.
* En PowerShell-runbook kan misslyckas om det görs ett försök att skriva en mycket stor mängd data till utdataströmmen på samma gång.   Du kan vanligtvis kan lösa problemet genom att skicka ut bara den information som du behöver när du arbetar med stora objekt.  Till exempel i stället för att mata ut ungefär så *Get-Process*, du kan mata ut de obligatoriska fälten med *Get-Process | Välj ProcessName CPU*.

## <a name="powershell-workflow-runbooks"></a>PowerShell Workflow-runbooks

PowerShell Workflow-runbooks är text-runbookflöden baserade på [Windows PowerShell-arbetsflöde](automation-powershell-workflow.md).  Du kan direkt redigera koden för en runbook med textredigeraren i Azure-portalen.  Du kan också använda textredigerare offline och [importera runbook](automation-creating-importing-runbook.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Implementera alla komplex logik med PowerShell Workflow-kod.
* Använd [kontrollpunkter](automation-powershell-workflow.md#checkpoints) att återuppta runbook vid fel.
* Använd [parallellbearbetning](automation-powershell-workflow.md#parallel-processing) att utföra flera åtgärder parallellt.
* Kan innehålla andra grafiska runbooks och PowerShell Workflow-runbooks som underordnade runbooks för att skapa avancerade arbetsflöden.

### <a name="limitations"></a>Begränsningar

* Författare måste vara bekant med PowerShell-arbetsflöde.
* Runbook måste hantera den ytterligare komplexiteten med PowerShell-arbetsflöde som [deserialisera objekt](automation-powershell-workflow.md#code-changes).
* Runbook tar längre tid att starta än PowerShell-runbooks eftersom den måste kompileras innan du kör.
* PowerShell-runbooks kan bara ingå som underordnade runbooks med hjälp av cmdleten Start-AzureAutomationRunbook som skapar ett nytt jobb.

## <a name="python-runbooks"></a>Python-runbooks

Python runbooks kompilera under Python 2.  Du kan redigera koden för en runbook med textredigeraren i Azure portal direkt och du kan använda valfri offline textredigerare och [importera runbook](automation-creating-importing-runbook.md) till Azure Automation.

### <a name="advantages"></a>Fördelar

* Utnyttja de robusta Python-biblioteken.

### <a name="limitations"></a>Begränsningar

* Måste vara bekant med Python-skript.
* Endast Python 2 stöds för tillfället, vilket innebär att Python 3 funktioner kommer att misslyckas.
* För att kunna använda bibliotek från tredje part, måste du [importera paketet](python-packages.md) till Automation-kontot för den som ska användas.

## <a name="considerations"></a>Överväganden

Du bör beakta följande ytterligare överväganden när du bestämmer vilken typ som ska användas för en specifik runbook.

* Du kan inte konvertera runbooks från grafiska till typ av text eller vice versa.
* Det finns begränsningar med hjälp av runbooks med olika typer som en underordnad runbook.  Se [underordnade runbooks i Azure Automation](automation-child-runbooks.md) för mer information.

## <a name="next-steps"></a>Nästa steg

* Läs mer om grafisk runbook-redigering i [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Att förstå skillnaderna mellan PowerShell och PowerShell arbetsflöden för runbooks, se [Learning Windows PowerShell-arbetsflöde](automation-powershell-workflow.md)
* Mer information om hur du skapar eller importerar en Runbook finns i [skapa eller importera en Runbook](automation-creating-importing-runbook.md)

