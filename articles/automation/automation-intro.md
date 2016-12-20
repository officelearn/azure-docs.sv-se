---
title: "Vad är Azure Automation? | Microsoft Docs"
description: "Lär dig vilket värde Azure Automation tillför och få svar på vanliga frågor så att du snabbt kan komma igång med runbooks och Azure Automation DSC."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "vad är automation, azure automation, azure automation-exempel"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 16f6285577294ff49f11a27f48daea090655a2b1


---
# <a name="azure-automation-overview"></a>Översikt över Azure Automation
Microsoft Azure Automation gör det möjligt för användare att automatisera manuella, tidskrävande, felbenägna och ofta återkommande uppgifter som är vanliga i molnet och företagsmiljöer. Det sparar tid och ökar tillförlitligheten i vanliga administrativa uppgifter och schemalägger dem även så att de kan utföras automatiskt med jämna mellanrum. Du kan automatisera processer med hjälp av runbooks eller automatisera konfigurationshantering med Desired State Configuration. Den här artikeln innehåller en kort översikt över Azure Automation och ger svar på några vanliga frågor. Du kan referera till andra artiklar i det här biblioteket om du vill ha mer detaljerad information om de olika avsnitten.

## <a name="automating-processes-with-runbooks"></a>Automatisera processer med runbooks
En runbook är en uppsättning aktiviteter som utför en automatisk process i Azure Automation. Det kan vara en enkel process som startar en virtuell dator och skapar en loggpost eller så kanske du har en komplex runbook som kombinerar andra mindre runbooks för att utföra en komplex process över flera resurser eller till och med flera moln och i lokala miljöer.  

Du kanske till exempel har en befintlig manuell process för att trunkera en SQL-databas om den närmar sig den högsta tillåtna storleken som innehåller flera steg, till exempel: anslut till servern, anslut till databasen, hämta databasens aktuella storlek, kontrollera om tröskelvärdet har överskridits, trunkera den och meddela användaren. I stället för att manuellt utföra vart och ett av dessa steg kan du skapa en runbook som utför alla dessa uppgifter som en enda process. Du startar runbooken, anger informationen som krävs, till exempel SQL-servernamnet, databasnamnet och mottagarens e-postadress och lutar dig sedan tillbaka medan processen körs. 

## <a name="what-can-runbooks-automate"></a>Vad kan runbooks automatisera?
Eftersom runbooks i Azure Automation baseras på Windows PowerShell eller Windows PowerShell Workflow så kan de göra allt som PowerShell kan. Om ett program eller en tjänst har ett API kan en runbook arbeta med dem. Om du har en PowerShell-modul för programmet kan du läsa in modulen i Azure Automation och ta med dessa cmdlets i din runbook. Azure Automation-runbooks körs i Azure-molnet och kan komma åt alla molnresurser eller externa resurser som kan nås från molnet. Med hjälp av [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) kan runbooks köras i ditt lokala datacenter för att hantera lokala resurser. 

## <a name="getting-runbooks-from-the-community"></a>Hämtar runbooks från communityn
[Runbook-galleriet](automation-runbook-gallery.md#runbooks-in-runbook-gallery) innehåller runbooks från Microsoft och communityn som du kan använda som de är i din miljö eller anpassa efter behov. De är också användbara som referenser när du lär dig hur du skapar egna runbooks. Du kan även bidra med egna runbooks i galleriet som du tror att andra användare kan ha nytta av. 

## <a name="creating-runbooks-with-azure-automation"></a>Skapa runbooks med Azure Automation
Du kan [skapa dina egna runbooks](automation-creating-importing-runbook.md) från scratch eller ändra runbooks från [runbook-galleriet](http://msdn.microsoft.com/library/azure/dn781422.aspx) efter dina behov. Det finns tre olika [runbook-typer](automation-runbook-types.md) som du kan välja mellan beroende på dina krav och PowerShell-miljö. Om du föredrar att arbeta direkt med PowerShell-koden måste du använda en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) eller [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) som du redigerar offline eller med [textredigeraren](http://msdn.microsoft.com/library/azure/dn879137.aspx) på Azure-portalen. Om du vill redigera en runbook utan att arbeta direkt med den underliggande koden kan du skapa en [grafisk runbook](automation-runbook-types.md#graphical-runbooks) med hjälp av den [grafiska redigeraren](automation-graphical-authoring-intro.md) på Azure-portalen. 

Föredrar du att titta eller läsa? Titta på videoklippet nedan från Microsoft Ignite-sessionen i maj 2015. Obs! Begreppen och funktionerna som beskrivs i den här videon är korrekta, men Azure Automation har utvecklats mycket sedan videon spelades in och har nu ett mer omfattande användargränssnitt på Azure-portalen och stöd för fler funktioner.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatisera konfigurationshanteringen med Desired State Configuration
[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) är en plattform som hjälper dig att hantera, distribuera och tillämpa konfigurationen för fysiska värdar och virtuella datorer med en deklarativ PowerShell-syntax. Du kan definiera konfigurationer på en central DSC-hämtningsserver som måldatorer kan hämta och använda automatiskt. DSC tillhandahåller en uppsättning PowerShell-cmdlets som du kan använda för att hantera konfigurationer och resurser.  

[Azure Automation DSC](automation-dsc-overview.md) är en molnbaserad lösning för PowerShell DSC som tillhandahåller tjänster som krävs för företagsmiljöer.  Du kan hantera dina DSC-resurser i Azure Automation och tillämpa konfigurationer på virtuella eller fysiska datorer som hämtar dem från en DSC-hämtningsserver i Azure-molnet.  Lösningen innehåller också rapporteringstjänster som informerar dig om viktiga händelser, t.ex. när noder har avvikit från sin tilldelade konfiguration och när en ny konfiguration har tillämpats. 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Skapa dina egna DSC-konfigurationer med Azure Automation
[DSC-konfigurationer](automation-dsc-overview.md#azure-automation-dsc-terms) definierar det önskade tillståndet för en nod.  Flera noder kan använda samma konfiguration så att alla har samma tillstånd.  Du kan skapa en konfiguration med valfri textredigerare på den lokala datorn och sedan importera den till Azure Automation där du kan sammanställa och tillämpa den på noder.

## <a name="getting-modules-and-configurations"></a>Hämta moduler och konfigurationer
Du kan hämta [PowerShell-moduler](automation-runbook-gallery.md#modules-in-powershell-gallery) som innehåller cmdlets som du kan använda i dina runbooks och DSC-konfigurationer från [PowerShell-galleriet](http://www.powershellgallery.com/). Du kan starta det här galleriet från Azure-portalen och importera moduler direkt till Azure Automation, eller hämta och importera dem manuellt. Du kan inte installera moduler direkt från Azure-portalen, men du kan hämta dem och installera dem på samma sätt som andra moduler. 

## <a name="example-practical-applications-of-azure-automation"></a>Exempel på praktiska tillämpningar med Azure Automation
Nedan följer några exempel på olika automatiseringsscenarier med Azure Automation. 

* Skapa och kopiera virtuella datorer i olika Azure-prenumerationer. 
* Schemalägg filkopieringar från en lokal dator till en Azure Blob Storage-behållare. 
* Automatisera säkerhetsfunktioner, t.ex. funktioner som nekar förfrågningar från en klient när en DoS-attack (Denial of Service) upptäckts. 
* Se till att datorerna alltid uppfyller kraven i konfigurerade säkerhetsprinciper.
* Hantera kontinuerlig distribution av programkod i molnet och i den lokala infrastrukturen. 
* Skapa en Active Directory-skog i Azure för din labbmiljö. 
* Trunkera en tabell i en SQL-databas om databasen närmar sig den största tillåtna storleken. 
* Fjärruppdatera miljöinställningar för en Azure-webbplats. 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Hur relaterar Azure Automation till andra automatiseringsverktyg?
[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) är avsett att automatisera hanteringsuppgifter i det privata molnet. Det installeras lokalt i ditt datacenter som en komponent i [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). SMA och Azure Automation använder samma runbook-format baserat på Windows PowerShell och Windows PowerShell Workflow, men SMA har inte stöd för [grafiska runbooks](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) är avsett för automatisering av lokala resurser. Den använder ett annat runbook-format än Azure Automation och Service Management Automation och har ett grafiskt gränssnitt som gör att du kan skapa runbooks utan att skriva skript. Dess runbooks består av aktiviteter från integrationspaket som är skrivna specifikt för Orchestrator. 

## <a name="where-can-i-get-more-information"></a>Var hittar jag mer information?
Det finns många resurser där du kan lära dig mer om Azure Automation och hur du skapar egna runbooks. 

* **Azure Automation-biblioteket** är där du är just nu. Artiklarna i det här biblioteket innehåller komplett dokumentation om hur du konfigurerar och administrerar Azure Automation och hur du skapar egna runbooks. 
* [Azure PowerShell-cmdlets](http://msdn.microsoft.com/library/jj156055.aspx) innehåller information om hur du automatiserar Azure-åtgärder med hjälp av Windows PowerShell. Runbooks använder dessa cmdlets för att arbeta med Azure-resurser. 
* [Management-bloggen](https://azure.microsoft.com/blog/tag/azure-automation/) innehåller den senaste informationen om Azure Automation och andra hanteringstekniker från Microsoft. Prenumerera på den här bloggen för att hålla dig uppdaterad om det senaste från Azure Automation-teamet. 
* Använd [Automation-forumet](http://go.microsoft.com/fwlink/p/?LinkId=390561) för att ställa frågor om Azure Automation som besvaras av Microsoft- och Automation-communityn. 
* [Azure Automation-cmdlets](https://msdn.microsoft.com/library/mt244122.aspx) innehåller information om hur du automatiserar administrationsåtgärder. Här hittar du cmdlets för att hantera konton, tillgångar, runbooks och DSC för Automation.

## <a name="can-i-provide-feedback"></a>Kan jag lämna feedback?
**Vi tar gärna emot din feedback!** Om du letar efter en lösning för Azure Automation-runbooks eller en integreringsmodul kan du publicera en skriptbegäran på Script Center. Om du har feedback eller funktionsönskemål om Azure Automation kan du publicera dem i [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Tack! 




<!--HONumber=Dec16_HO1-->


