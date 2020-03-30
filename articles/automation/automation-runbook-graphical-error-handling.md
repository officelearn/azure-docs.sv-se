---
title: Felhantering i Azure Automation grafiska runbooks
description: Den här artikeln beskriver hur du implementerar felhanteringslogik i Azure Automation grafiska runbooks.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367082"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Felhantering i Azure Automation grafiska runbooks

En viktig designprincip att tänka på för din grafiska Azure Automation-runbook är identifieringen av problem som runbooken kan uppstå under körningen. De här problemen kan omfatta lyckade åtgärder, förväntade feltillstånd och oväntade feltillstånd.

Ofta, om det finns ett icke-avslutande fel som uppstår med en runbook-aktivitet, hanterar Windows PowerShell aktiviteten genom att bearbeta alla aktiviteter som följer, oavsett felet. Det är troligt att felet genererar ett undantag, men nästa aktivitet kan fortfarande få köras.

Din grafiska runbook bör innehålla felhanteringskod för att hantera körningsproblem. Om du vill validera utdata för en aktivitet eller hantera ett fel kan du använda en PowerShell-kodaktivitet, definiera villkorsstyrd logik på utdatalänken för aktiviteten eller använda en annan metod.

Grafiska Azure Automation-runbooks har förbättrats och fått en funktion för felhantering. Du kan nu omvandla undantag till icke-avslutande fel och skapa fellänkar mellan aktiviteter. Den förbättrade processen gör att din runbook kan fånga fel och hantera realiserade eller oväntade förhållanden. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>PowerShell-feltyper

De typer av PowerShell-fel som kan uppstå under körning av runbook avslutar fel och icke-avslutande fel.
 
### <a name="terminating-error"></a>Avsluta fel

Ett avslutande fel är ett allvarligt fel under körningen som stoppar ett kommando eller skriptkörning helt. Exempel på detta är obefintliga cmdlets, syntaxfel som förhindrar att en cmdlet körs och andra allvarliga fel.

### <a name="non-terminating-error"></a>Fel som inte är avslutande

Ett fel som inte är avslutande är ett icke-allvarligt fel som gör att körningen kan fortsätta trots feltillståndet. Exempel på detta är driftfel, till exempel fil som inte hittades fel och behörighetsproblem.

## <a name="when-to-use-error-handling"></a>När du ska använda felhantering

Använd felhantering i runbooken när en kritisk aktivitet genererar ett fel eller ett undantag. Det är viktigt att förhindra att nästa aktivitet i runbooken bearbetas och att felet hanteras på rätt sätt. Hanteringen av felet är särskilt viktigt när dina runbooks stöder en affärs- eller tjänståtgärdsprocess.

För varje aktivitet som kan skapa ett fel kan du lägga till en fellänk som pekar på någon annan aktivitet. Målaktiviteten kan vara av vilken typ som helst, inklusive kodaktivitet, anrop av en cmdlet, åkallan av en annan runbook och så vidare. Målaktiviteten kan också ha utgående länkar, antingen vanliga länkar eller fellänkar. Länkarna gör det möjligt för runbook att implementera komplex felhanteringslogik utan att tillgripa en kodaktivitet.

Den rekommenderade metoden är att skapa en dedikerad felhanteringskörningskörning med gemensamma funktioner, men den här metoden är inte obligatorisk. Tänk dig till exempel en runbook som försöker starta en virtuell dator och installera ett program på den. Om den virtuella datorn inte startar korrekt:

1. Skickar ett meddelande om det här problemet.
2. Startar en annan runbook som automatiskt etablerar en ny virtuell dator i stället.

En lösning är att ha en fellänk i runbooken som pekar på en aktivitet som hanterar steg ett. Runbook kan till exempel `Write-Warning` ansluta cmdlet till en aktivitet för steg två, till exempel [Cmdlet start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

Du kan också generalisera detta beteende för användning i många runbooks genom att placera dessa två aktiviteter i en separat felhantering runbook. Innan den ursprungliga runbooken anropar den här körboken för felhantering kan den skapa ett anpassat meddelande från dess data och sedan skicka den som en parameter till felhanteringskörningskörningsboken.

## <a name="how-to-use-error-handling"></a>Hur du använder felhantering

Varje aktivitet i runbooken har en konfigurationsinställning som förvandlar undantag till icke-avslutande fel. Som standard är denna inställning inaktiverad. Vi rekommenderar att du aktiverar den här inställningen för alla aktiviteter där runbooken hanterar fel. Den här inställningen säkerställer att runbooken hanterar både avslutande och icke-avslutande fel i aktiviteten som icke-avslutande fel med hjälp av en fellänk.  

När du har aktiverat konfigurationsinställningen ska runbooken skapa en aktivitet som hanterar felet. Om aktiviteten ger upphov till något fel följs de utgående fellänkarna. De regelbundna länkarna följs inte, även om aktiviteten ger regelbunden produktion också.<br><br> ![Fellänksexempel för Automation Runbook](media/automation-runbook-graphical-error-handling/error-link-example.png)

I följande exempel hämtar en runbook en variabel som innehåller datornamnet på en virtuell dator. Den försöker sedan starta den virtuella datorn med nästa aktivitet.<br><br> ![Exempel på hantering av automatiseringskörningsfel](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Aktiviteten `Get-AutomationVariable` och [Start-AzVM-cmdleten](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) är konfigurerade för att konvertera undantag till fel. Om det finns problem med att hämta variabeln eller starta den virtuella datorn genererar koden fel.<br><br> ![Aktivitetsinställningar för](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)automatiseringskörningsfelhantering .

Fellänkar flödar från `error management` dessa aktiviteter till en enda kodaktivitet. Den här aktiviteten är konfigurerad med ett `throw` enkelt PowerShell-uttryck `$Error.Exception.Message` som använder nyckelordet för att stoppa bearbetningen, tillsammans med för att få meddelandet som beskriver det aktuella undantaget.<br><br> ![Exempel på hantering av felhanteringskod för automatiseringskörning](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om länkar och länktyper i grafiska runbooks kan du läsa [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow) (Grafisk redigering i Azure Automation).

* Mer information om körning av runbook, övervakning av runbook-jobb och annan teknisk information finns [i Körning av Runbook i Azure Automation](automation-runbook-execution.md).