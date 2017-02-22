---
title: Felhantering i grafiska Azure Automation-runbooks | Microsoft Docs
description: "Den här artikeln beskriver hur du implementerar felhanteringslogik i Azure Automation grafiska runbooks."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 08cba012cca61eeb03187d2b4165e2a79b15bc3d
ms.openlocfilehash: 12313f7f245d32c33882f1036f7d4b48bfb3ddc5

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Felhantering i Azure Automation grafiska runbooks

En princip för runbook-design som är viktig att ta hänsyn är att identifiera de olika problem som kan uppstå i en runbook. De här problemen kan omfatta lyckade åtgärder, förväntade feltillstånd och oväntade feltillstånd.

Runbooks bör inkludera felhantering. Om du vill validera utdata för en aktivitet eller hantera ett fel på lämpligt sätt med grafiska runbooks kan du använda en Windows PowerShell-kodaktivitet, definiera villkorslogik på utdatalänken för aktiviteten eller tillämpa en annan metod.          

Om det finns ett icke-avslutande fel som uppstår i en runbook-aktivitet, bearbetas en aktivitet som följer oavsett felet. Det är troligt att felet genererar ett undantag, men nästa aktivitet kan fortfarande få köras. Så här har PowerShell utformats för att hantera fel.    

De typer av PowerShell-fel som kan uppstå under körning är avslutande eller icke-avslutande. Skillnaderna mellan avslutande och icke avslutande fel är följande:

* **Avslutande fel**: Ett allvarligt fel vid körning som stoppar kommandot (eller skriptkörningen) helt. Exempel på detta är obefintliga cmdlet:ar, syntaxfel som förhindrar att en cmdlet körs eller andra allvarliga fel.

* **Icke-avslutande fel**: Ett icke-allvarligt fel som gör att körningen kan fortsätta trots felet. Exempel på detta är operativa fel som att en fil inte gick att hitta och behörighetsproblem.

Grafiska Azure Automation-runbooks har förbättrats och fått en funktion för felhantering. Du kan nu omvandla undantag till icke-avslutande fel och skapa fellänkar mellan aktiviteter. På så sätt kan en runbookredigerare fånga upp fel och hantera det förväntade eller oväntade tillståndet.  

## <a name="when-to-use-error-handling"></a>När du ska använda felhantering

När det finns en kritisk aktivitet som genererar ett fel eller ett undantag är det viktigt att förhindra att nästa aktivitet i din runbook behandlas samt att hantera felet på rätt sätt. Detta är särskilt viktigt när runbooks stöder en företags- eller tjänståtgärdsprocess.

För varje aktivitet som kan producera ett fel kan runbook-redigeraren lägga till en fellänk som pekar till andra aktiviteter.  Målaktiviteten kan vara av valfri typ: kodaktivitet, anrop av en cmdlet, anrop av en annan runbook och så vidare.

Målaktiviteten kan dessutom ha utgående länkar. Dessa länkar kan vara vanliga länkar eller fellänkar. Det innebär att runbook-författaren kan implementera komplex felhanteringslogik utan att behöva använda en kodaktivitet. Den rekommenderade metoden är att skapa en särskild runbook för felhantering med gemensamma funktioner, men detta är inte obligatoriskt. Felhanteringslogiken i en PowerShell-kodaktivitet är inte det enda alternativet.  

Anta exempelvis att en runbook försöker starta en virtuell dator och installera ett program på den. Om den virtuella datorn inte startar på rätt sätt utför den två åtgärder:

1. Den skickar ett meddelande om det här problemet.
2. Den startar en annan runbook som automatiskt etablerar en ny virtuell dator i stället.

En lösning är att ha en fellänk som hänvisar till en aktivitet som hanterar steg ett. Du kan till exempel ansluta cmdleten **Write-Warning** till en aktivitet för steg två, exempelvis cmdleten **Start-AzureRmAutomationRunbook**.

Du kan också generalisera detta beteende för användning i många runbooks genom att placera dessa två aktiviteter i en separat felhanterings-runbook och följa de riktlinjer som har föreslagits tidigare. Innan du anropar denna felhanterings-runbook kan du skapa ett anpassat meddelande från data i den ursprungliga runbooken och därefter skicka detta som en parameter till felhanterings-runbooken.

## <a name="how-to-use-error-handling"></a>Hur du använder felhantering

Varje aktivitet har en konfigurationsinställning som omvandlar undantag till icke-avslutande fel. Som standard är denna inställning inaktiverad. Du bör du aktivera den här inställningen för varje aktivitet där du vill hantera fel.  

Genom att aktivera denna konfiguration kan du säkerställa att både avslutande och icke-avslutande fel i aktiviteten hanteras som icke-avslutande fel, och de kan sedan hanteras med en fellänk.  

När du har konfigurerat den här inställningen skapar du en aktivitet som hanterar felet. Om en aktivitet producerar ett fel kommer de utgående fellänkarna att följas och de vanliga länkarna följs inte, även om aktiviteten även producerar reguljära utdata.<br><br> ![Fellänksexempel för Automation Runbook](media/automation-runbook-graphical-error-handling/error-link-example.png)

I följande exempel hämtar en runbook en variabel som innehåller datornamnet på en virtuell dator. Den försöker sedan starta den virtuella datorn med nästa aktivitet.<br><br> ![Felhanteringsexempel för Automation Runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Aktiviteten **Get-AutomationVariable** och **Start AzureRmVm** har konfigurerats för att konvertera undantag till fel.  Om det är problem med att hämta variabeln eller att starta den virtuella datorn genereras fel.<br><br> ![Felhantering i Automation Runbook, aktivitetsinställningar](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Fellänkar flödar från dessa aktiviteter till en enda **felhanteringsaktivitet** (en kodaktivitet). Den här aktiviteten konfigureras med ett enkelt PowerShell-uttryck med hjälp av nyckelordet *Throw* för att stoppa bearbetningen tillsammans med *$Error.Exception.Message* för att hämta det meddelande som beskriver det aktuella undantaget.<br><br> ![Automation Runbook-felhantering, kodexempel](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om länkar och länktyper i grafiska runbooks kan du läsa [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow) (Grafisk redigering i Azure Automation).

* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).



<!--HONumber=Feb17_HO1-->


