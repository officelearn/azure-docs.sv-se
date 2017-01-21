---
title: Felhantering i Azure Automation grafiska runbooks | Microsoft Docs
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
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Felhantering i Azure Automation grafiska runbooks

En viktig runbook-designprincip som du behöver överväga är att identifiera olika problem som runbook kan uppleva som framgång, förväntade och oväntade feltillstånd.  Runbooks bör inkludera felhantering för att identifiera detta.  Om du vill validera utdata för en aktivitet eller hantera ett fel på lämpligt sätt med grafiska runbooks skulle du förmodligen följa med en PowerShell kodaktivitet, definiera villkorslogik på utdatalänken för aktiviteten eller tillämpa en annan metod.          

Ofta när runbooks kör, om det finns ett icke avslutande fel som inträffar med en aktivitet, bearbetas alla efterföljande aktiviteter oavsett detta.  Naturligtvis kommer det sannolikt att genereras ett undantag, men det viktiga är att nästa aktivitet tillåts att köra. Anledningen till detta beteende beror på hur PowerShell-språket är utformat för att hantera fel.    

De typer av PowerShell-fel som kan uppstå under körning är avslutande eller icke-avslutande.  Skillnaden är följande:

* Avslutande fel: ett allvarligt fel vid körning som stoppar kommandot (eller skriptkörningen) helt. Exempel på detta kan vara obefintliga cmdlet:ar, syntaxfel som förhindrar att en cmdlet körs eller andra allvarliga fel.

* Icke-avslutande fel: ett icke allvarligt fel som gör att körningen kan fortsätta trots felet. Exempel innefattar operativa fel som att en fil inte gick att hitta, behörighetsproblem osv.

Azure Automation grafiska runbooks har förbättrats med möjligheten att lägga till felhantering så att du nu kan göra undantag till icke-avslutande fel och skapa fellänkar mellan aktiviteter. På så sätt kan en runbookredigerare fånga upp fel och ha ett sätt att hantera det förväntade eller oväntade tillståndet.  

## <a name="when-to-use"></a>När du ska använda detta

Kontroll av arbetsflödets körning är viktigt för att säkerställa att du alltid när det finns en kritisk aktivitet som genererar ett fel eller ett undantag kan förhindra fortsättning till nästa aktivitet i din runbook och hantera felet på rätt sätt.  Detta är särskilt nödvändigt när runbooks stöder en företags- eller tjänståtgärdsprocess.

För varje aktivitet som kan producera ett fel kan runbook-redigeraren lägga till en fellänk som pekar till andra aktiviteter.  Målaktiviteten kan vara av valfri typ: kodaktivitet, anropning av en cmdlet, anropning av en annan runbook eller något annat. 

Dessutom kan målaktiviteten även ha utgående länkar, och de kan vara vanliga länkar eller fellänkar, så att runbook-redigeraren kan implementera komplex felhanteringslogik utan att tillgripa en kodaktivitet.  Medan den rekommenderade metoden är att skapa en dedikerad felhanterings-runbook med gemensamma funktioner, är det inte obligatoriskt och felhanteringslogik i en PowerShell-kodaktivitet inte är det enda alternativet.  

Överväg till exempel en runbook som försöker starta en virtuell dator och installera ett program på den, men om den virtuella datorn inte startar korrekt utför den två åtgärder: 

1. Skickar ett meddelande om det här problemet.
2. Startar en annan runbook som automatiskt etablerar en ny virtuell dator i stället. 

En lösning kan vara att ha en fellänk som pekar till en aktivitet för att hantera steg 1, som **Write-Warning**-cmdleten, ansluten till en aktivitet för steg 2, som **Start AzureRmAutomationRunbook**-cmdleten. 

Du kan också generalisera detta beteende för användning i många runbooks och placera dessa två aktiviteter i en separat felhanterings-runbook, följande de riktlinjer som föreslogs tidigare.  Innan du anropar denna felhanterings-runbook kan du skapa ett anpassat meddelande från data i den ursprungliga runbooken och därefter skicka detta som en parameter till felhanterings-runbooken. 

## <a name="how-to-use"></a>Hur du ska använda detta

Varje aktivitet har en konfiguration för att göra undantag till icke-avslutande fel. Som standard är detta inaktiverat.  Du bör du aktivera det för varje aktivitet där du vill hantera fel.  Genom att aktivera denna konfiguration kan du säkerställa att både avslutande och icke-avslutande fel i aktiviteten kommer att hanteras som icke-avslutande fel, och de kan sedan hanteras med en fellänk.  När du har konfigurerat den här inställningen skapar du en aktivitet som hanterar felet.  Om en aktivitet producerar något fel kommer de utgående fellänkarna att följas och de vanliga länkarna kommer inte att göra det, även om aktiviteten även producerar reguljära utdata.<br><br> ![Automation Runbook fellänksexempel](media/automation-runbook-graphical-error-handling/error-link-example.png)

I följande enkla exempel har vi en runbook som hämtar en variabel som innehåller namnet på en virtuell dator och sedan försöker starta den virtuella datorn med nästa aktivitet.<br><br> ![Automation Runbook felhanteringsexempel](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Aktiviteten **Get-AutomationVariable** och **Start AzureRmVm** har konfigurerats för att konvertera undantag till fel.  Om det är problem med att hämta variabeln eller att starta den virtuella datorn genereras fel.<br><br> ![Automation Runbook felhantering, aktivitetsexempel](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Fellänkar flödar från dessa aktiviteter till en enskild **felhanterings**-aktivitet (en kodaktivitet), som är konfigurerad med ett enkelt PowerShell-uttryck med hjälp av nyckelordet *Trow* för att stoppa bearbetningen tillsammans med nyckelordet *$Error.Exception.Message* för att hämta det meddelande som beskriver det aktuella undantaget.<br><br> ![Automation Runbook felhantering, kodexempel](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om länkar och förstå länktyper i grafiska runbooks kan du se [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


