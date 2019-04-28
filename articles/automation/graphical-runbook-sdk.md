---
title: Översikt över Azure Automation Grpahical runbook SDK
description: Den här artikeln beskriver hur du använder Azure Automation grafiska Runbook-SDK
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc9f1feff6d8d45e52c8621d1ec4b36e0a4a4a76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303224"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Använd Azure Automation grafiska runbook SDK

[Grafiska runbooks](automation-graphical-authoring-intro.md) är runbookflöden som hjälper dig att hantera komplexitet i den underliggande koden för Windows PowerShell eller PowerShell-arbetsflöde. Microsoft Azure Automation grafiska redigering SDK kan utvecklare skapa och redigera grafiska Runbooks för användning med Azure Automation-tjänsten. I följande kodavsnitt visar det grundläggande flödet för att skapa en grafisk runbook från din kod.

## <a name="pre-requisites"></a>Förutsättningar

Börja genom att importera den `Microsoft.Azure.Automation.GraphicalRunbook.Model` paketet i projektet.

## <a name="create-a-runbook-object-instance"></a>Skapa en instans för runbook-objekt

Referens för den `Orchestrator.GraphRunbook.Model` sammansättning och skapa en instans av den `Orchestrator.GraphRunbook.Model.GraphRunbook` klass:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Lägg till runbook-parametrar

Skapa en instans av `Orchestrator.GraphRunbook.Model.Parameter` objekt och lägga till dem i runbooken:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Lägga till aktiviteter och länkar

Skapa en instans av aktiviteter för lämpliga typer och lägga till dem i runbooken:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Aktiviteter har implementerats av följande klasser i den `Orchestrator.GraphRunbook.Model` namnområde:

|Klass  |Aktivitet  |
|---------|---------|
|CommandActivity     | Anropar ett PowerShell-kommando (cmdlet, funktionen osv.).        |
|InvokeRunbookActivity     | Anropar infogad för en annan runbook.        |
|JunctionActivity     | Väntar på att alla inkommande grenar ska slutföras.        |
|WorkflowScriptActivity     | Kör ett kodblock PowerShell eller PowerShell-arbetsflöde (beroende på typ av runbook) i samband med runbook. Detta är ett kraftfullt verktyg, men inte många: Användargränssnittet visas den här skriptblock som text. motorn för körning behandlar det angivna blocket som en svart ruta och gör inga försök att analysera innehållet, förutom en grundläggande syntax-kontroll. Om du behöver bara anropa ett enda PowerShell-kommando, föredra CommandActivity.        |

> [!NOTE]
> Härled inte egna aktiviteter från de angivna klasserna: Azure Automation kommer inte att kunna använda runbooks med anpassade aktivitetstyper.

CommandActivity och InvokeRunbookActivity parametrar måste anges som värde beskrivningar, inte direkt värden. Värdet beskrivningar ange hur du ska produceras faktiska parametrarnas värden. Följande värde beskrivningar tillhandahålls för närvarande:


|Säkerhetsbeskrivning  |Definition  |
|---------|---------|
|ConstantValueDescriptor     | Refererar till ett hårdkodat konstant värde.        |
|RunbookParameterValueDescriptor     | Refererar till en runbook-parameter med namnet.        |
|ActivityOutputValueDescriptor     | Refererar till en överordnad aktivitet utdata, vilket gör att en aktivitet prenumererar du ”” på data som produceras av en annan aktivitet.        |
|AutomationVariableValueDescriptor     | Refererar till ett Automation-variabeltillgång efter namn.         |
|AutomationCredentialValueDescriptor     | Refererar till ett Automation-certifikattillgång med namnet.        |
|AutomationConnectionValueDescriptor     | Refererar till ett Automation-anslutningstillgång med namnet.        |
|PowerShellExpressionValueDescriptor     | Anger en egen PowerShell-uttryck som utvärderas innan du anropar aktiviteten.  <br/>Detta är ett kraftfullt verktyg, men inte många: Gränssnittet för att visa det här uttrycket som text. motorn för körning behandlar det angivna blocket som en svart ruta och gör inga försök att analysera innehållet, förutom en grundläggande syntax-kontroll. När det är möjligt, föredrar du mer specifika beskrivningar av värdet.      |

> [!NOTE]
> Härled inte ditt eget värde beskrivningar av de angivna klasserna: Azure Automation kommer inte att kunna använda runbooks med anpassade descriptor värdetyper.

Skapa en instans av länkar som ansluter aktiviteter och lägga till dem i runbooken:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Spara runbooken till en fil

Använd `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` att serialisera en runbook till en sträng:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Den här strängen kan sparas till en fil med det **.graphrunbook** tillägget och den här filen kan importeras till Azure Automation.
Det serialiserade formatet kan ändras i framtida versioner av `Orchestrator.GraphRunbook.Model.dll`. Vi lovar att bakåtkompatibilitet: alla runbook serialiseras med en äldre version av `Orchestrator.GraphRunbook.Model.dll` kan deserialiseras av en nyare version. Framåtkompatibilitet är inte säkert: en runbook som serialiseras med en nyare version är kanske inte deserialiseras av äldre klientversioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om grafiska Runbooks i Azure Automation i [grafisk redigering introduktion](automation-graphical-authoring-intro.md)

