---
title: Översikt över Azure Automation Grpahical runbook SDK
description: I den här artikeln beskrivs hur du använder Azure Automation Graphical Runbook SDK
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: d4dcf6681ade977847c204dd1237f7cd7a67775e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75418248"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Använda Azure Automation Graphical runbook SDK

[Grafiska runbooks](automation-graphical-authoring-intro.md) är runbooks som hjälper till att hantera komplexiteten i den underliggande Windows PowerShell- eller PowerShell-arbetsflödeskoden. Microsoft Azure Automation Graphical Authoring SDK gör det möjligt för utvecklare att skapa och redigera grafiska runbooks som kan användas med Azure Automation-tjänsten. Följande kodavsnitt visar det grundläggande flödet för att skapa en grafisk runbook från koden.

## <a name="pre-requisites"></a>Förutsättningar

Du kan börja `Microsoft.Azure.Automation.GraphicalRunbook.Model` med att importera paketet till projektet.

## <a name="create-a-runbook-object-instance"></a>Skapa en runbook-objektförekomst

Referera `Orchestrator.GraphRunbook.Model` till sammansättningen och `Orchestrator.GraphRunbook.Model.GraphRunbook` skapa en instans av klassen:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Lägga till runbook-parametrar

Instansiera `Orchestrator.GraphRunbook.Model.Parameter` objekt och lägga till dem i runbook:

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

Instansiera aktiviteter av lämpliga typer och lägg till dem i runbook:

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

Aktiviteterna genomförs av följande klasser `Orchestrator.GraphRunbook.Model` i namnområdet:

|Klass  |Aktivitet  |
|---------|---------|
|Kommandoaktivitet     | Anropar ett PowerShell-kommando (cmdlet, funktion osv.).        |
|AnropaRunbookActivity     | Anropar en annan runbook infogad.        |
|KorsningAktivitet     | Väntar på att alla inkommande grenar ska slutföras.        |
|Arbetsflödesscriptaktivitet     | Kör ett block med PowerShell- eller PowerShell-arbetsflödeskod (beroende på runbook-typ) i ramen för runbooken. Detta är ett kraftfullt verktyg, men inte överanvända det: användargränssnittet kommer att visa detta skriptblock som text; Körningsmotorn behandlar det medföljande blocket som en svart ruta och gör inga försök att analysera dess innehåll, förutom en grundläggande syntaxkontroll. Om du bara behöver anropa ett enda PowerShell-kommando föredrar du CommandActivity.        |

> [!NOTE]
> Härled inte dina egna aktiviteter från de angivna klasserna: Azure Automation kan inte använda runbooks med anpassade aktivitetstyper.

Parametrarna CommandActivity och InvokeRunbookActivity måste anges som värdebeskrivare, inte direkta värden. Värdebeskrivningar anger hur de faktiska parametervärdena ska produceras. Följande värdebeskrivare tillhandahålls för närvarande:


|Deskriptor  |Definition  |
|---------|---------|
|ConstantValueDescriptor     | Refererar till ett hårdkodat konstantvärde.        |
|RunbookParameterValueDescriptor     | Refererar till en runbook-parameter efter namn.        |
|ActivityOutputValueDescriptor     | Refererar till en aktivitetsutflöde i tidigare led, vilket gör att en aktivitet kan "prenumerera" på data som produceras av en annan aktivitet.        |
|AutomationVariableValueDescriptor     | Refererar till en resurs för automatiseringsvariabel efter namn.         |
|AutomationCredentialValueDescriptor     | Refererar till en automationscertifikattillgång efter namn.        |
|AutomationConnectionValueDescriptor     | Refererar till en automationsanslutningstillgång efter namn.        |
|PowerShellExpressionValueDescriptor     | Anger ett PowerShell-uttryck i fri form som ska utvärderas precis innan aktiviteten anropas.  <br/>Detta är ett kraftfullt verktyg, men inte överanvända det: användargränssnittet kommer att visa detta uttryck som text; Körningsmotorn behandlar det medföljande blocket som en svart ruta och gör inga försök att analysera dess innehåll, förutom en grundläggande syntaxkontroll. Om möjligt föredrar du mer specifika värdebeskrivare.      |

> [!NOTE]
> Härled inte dina egna värdebeskrivare från de angivna klasserna: Azure Automation kan inte använda runbooks med anpassade värdebeskrivningstyper.

Instansiera länkar som kopplar samman aktiviteter och lägger till dem i runbooken:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Spara runbooken i en fil

Används `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` för att serialisera en runbook till en sträng:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Den här strängen kan sparas i en fil med **.graphrunbook-tillägget** och den här filen kan importeras till Azure Automation.
Serialiserat format kan ändras i `Orchestrator.GraphRunbook.Model.dll`framtida versioner av . Vi lovar bakåtkompatibilitet: alla runbook serialiserade `Orchestrator.GraphRunbook.Model.dll` med en äldre version av kan deserialiseras av någon nyare version. Kompatibilitet för vidarebefordran är inte garanterad: en runbook serialiserad med en nyare version kanske inte kan anpassas av äldre versioner.

## <a name="next-steps"></a>Nästa steg

Mer information om grafiska runbooks i Azure Automation finns i [Introduktion till grafisk redigering](automation-graphical-authoring-intro.md)

