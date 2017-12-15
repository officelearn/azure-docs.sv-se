---
title: Azure Automation Windows Hybrid Runbook Worker | Microsoft Docs
description: "Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som gör det möjligt att köra runbooks på Windows-datorer i ditt lokala datacenter eller molnmiljö."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 71c98a7e17472ae0aa7646b9e7fc745363546211
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Så här distribuerar du en Windows-Hybrid Runbook Worker

Runbooks i Azure Automation kan inte komma åt resurser i andra moln eller lokala miljö eftersom de körs i Azure-molnet.  Funktionen Hybrid Runbook Worker i Azure Automation kan du köra runbooks direkt på den dator där rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer.  

Den här funktionen visas i följande bild:<br>

![Hybrid Runbook Worker-översikt](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

En teknisk översikt över Hybrid Runbook Worker-rollen och distribution överväganden finns [Automation arkitektur, översikt](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker-grupper

Varje Runbook Worker-Hybrid är medlem i en Hybrid Runbook Worker-grupp som du anger när du installerar agenten.  En grupp kan innehålla en enskild agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en runbook på en Hybrid Runbook Worker, anger du den grupp som den körs på.  Medlemmar i gruppen avgör vilken worker services begäran.  Du kan inte ange en viss worker.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installera Windows Hybrid Runbook Worker 

Om du vill installera och konfigurera en Windows-Hybrid Runbook Worker, finns det två metoder.  Den rekommenderade metoden använder en Automation-runbook för att helt automatisera processen som krävs för att konfigurera en Windows-dator.  Den andra metoden är följer stegvisa instruktioner för att manuellt installera och konfigurera rollen.  

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som stöder Hybrid Runbook Worker-rollen med önskad tillstånd Configuration DSC () som du behöver lägga till dem som DSC-noder.  Mer information om onboarding dem för hantering med DSC, se [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).        
Om du aktiverar den [uppdatering hanteringslösning](../operations-management-suite/oms-solution-update-management.md), en Windows-dator som är ansluten till din OMS-arbetsyta konfigureras automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen.  Det har inte registrerats med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto.  Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto till stöd för Automation-runbooks så länge som du använder samma konto för både lösning och Hybrid Runbook Worker-gruppmedlemskap.  Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.  

Granska följande information om den [maskin- och programvarukrav](automation-offering-get-started.md#hybrid-runbook-worker) och [information för att förbereda nätverket](automation-offering-get-started.md#network-planning) innan du börjar distribuera en Hybrid Runbook Worker.  När en runbook worker har distribuerats, granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.  
 
### <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen.  

1. Hämta den *ny OnPremiseHybridWorker.ps1* skript från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) direkt från den dator som kör Hybrid Runbook Worker-rollen eller från en annan dator i din miljö och kopierar den till arbetaren.  

    Den *ny OnPremiseHybridWorker.ps1* skriptet kräver följande parametrar under körning:

  * *AutomationAccountName* (obligatoriskt) - namnet på ditt Automation-konto.  
  * *ResourceGroupName* (obligatoriskt) - namnet på resursgruppen som är associerade med ditt Automation-konto.  
  * *HybridGroupName* (obligatoriskt) - namnet på en Hybrid Runbook Worker-grupp som du anger som mål för runbooks som stöder det här scenariot. 
  *  *SubscriptionID* (obligatoriskt) - Azure prenumerations-Id som ditt Automation-konto.
  *  *WorkspaceName* (valfritt) – namnet på OMS-arbetsytan.  Om du inte har en OMS-arbetsyta skriptet skapar och konfigurerar en.  

     > [!NOTE]
     > Det finns för närvarande endast Automation regioner som stöds för integrering med OMS - **Australien sydost**, **östra USA 2**, **Sydostasien**, och **Västeuropa**.  Om ditt Automation-konto inte är i någon av de regionerna, skriptet skapar en OMS-arbetsyta men den varnar dig om att det går inte att länka dem tillsammans.
     >
2. På datorn, startar **Windows PowerShell** från den **starta** skärm i administratörsläge.  
3. Från PowerShell-kommandoradsgränssnitt, navigera till mappen som innehåller skriptet som du hämtade och köra den ändra värdena för parametrarna *- AutomationAccountName*, *- ResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, och *- WorkspaceName*.

     > [!NOTE] 
     > Du uppmanas att autentisera med Azure när du kör skriptet.  Du **måste** logga in med ett konto som är medlem i rollen administratörer för prenumeration och medadministratör för prenumerationen.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Du uppmanas att komma överens om att installera **NuGet** och du uppmanas att autentisera med dina autentiseringsuppgifter för Azure.<br><br> ![Ny OnPremiseHybridWorker skript körs](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. När skriptet har slutförts bladet Hybrid Worker-grupper visas den nya gruppen och antal medlemmar eller om en befintlig grupp, ökar antalet medlemmar.  Du kan välja grupp från listan på den **Hybrid Worker grupper** och välj den **hybrider** panelen.  På den **hybrider** bladet visas varje medlem i gruppen i listan.  

### <a name="manual-deployment"></a>Manuell distribution 

Utför de två första stegen en gång för Automation-miljö och upprepa återstående steg för varje worker-dator.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Skapa Operations Management Suite-arbetsyta

Om du inte redan har en Operations Management Suite-arbetsyta, skapar du en med hjälp av anvisningarna i [hantera din arbetsyta](../log-analytics/log-analytics-manage-access.md). Du kan använda en befintlig arbetsyta om du redan har en.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Lägg till Automation-lösningen till Operations Management Suite-arbetsyta

Lösningar för att lägga till funktioner till Operations Management Suite.  Automation-lösningen lägger till funktionalitet för Azure Automation, inklusive stöd för Runbook Worker-Hybrid.  När du lägger till lösningen till din arbetsyta skickar den automatiskt ned worker komponenter till agentdatorn som du installerar i nästa steg.

Följ anvisningarna på [att lägga till en lösning med lösningar galleriet](../log-analytics/log-analytics-add-solutions.md) att lägga till den **Automation** lösningen till Operations Management Suite-arbetsyta.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installera Microsoft Monitoring Agent

Microsoft Monitoring Agent ansluter datorer till Operations Management Suite.  När du installerar agenten på den lokala datorn och ansluta till arbetsytan, hämtas automatiskt de komponenter som krävs för Runbook Worker-Hybrid.

Följ anvisningarna på [ansluta Windows-datorer till logganalys](../log-analytics/log-analytics-windows-agent.md) att installera agenten på den lokala datorn.  Du kan upprepa processen för flera datorer att lägga till flera personer i din miljö.

När agenten har lyckats ansluta till Operations Management Suite, visas det på den **anslutna källor** fliken Operations Management Suite **inställningar** fönstret.  Du kan kontrollera att agenten har hämtat Automation-lösningen på rätt sätt när den har en mapp med namnet **AzureAutomationFiles** i C:\Program Files\Microsoft övervakning Agent\Agent.  För att bekräfta versionen av Hybrid Runbook Worker, kan du gå till C:\Program Files\Microsoft övervakning Agent\Agent\AzureAutomation\ och anteckna den \\ *version* undermappen.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera runbook-miljön och ansluta till Azure Automation

När du lägger till en agent till Operations Management Suite Automation-lösningen push-meddelanden den **HybridRegistration** PowerShell-modulen som innehåller den **Add-HybridRunbookWorker** cmdlet.  Du kan använda denna cmdlet för att installera runbook-miljön på datorn och registrera den med Azure Automation.

Öppna ett PowerShell-session i administratörsläge och kör följande kommandon för att importera modulen.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Kör sedan den **Add-HybridRunbookWorker** cmdlet med följande syntax:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Du kan hämta den information som krävs för denna cmdlet från den **hantera nycklar** bladet i Azure-portalen.  Öppna bladet genom att välja den **nycklar** alternativet från den **inställningar** bladet i ditt Automation-konto.

![Hybrid Runbook Worker-översikt](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Gruppnamn** är namnet på Hybrid Runbook Worker-gruppen. Om den här gruppen finns redan i automation-konto, läggs den aktuella datorn till den.  Om den inte redan finns, sedan läggs den.
* **Slutpunkten** är den **URL** i den **hantera nycklar** bladet.
* **Token** är den **primära åtkomstnyckeln** i den **hantera nycklar** bladet.  

Använd den **-Verbose** växel med **Add-HybridRunbookWorker** få detaljerad information om installationen.

#### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda någon av de aktiviteter och cmdlets som definierats i moduler som installerats i din Azure Automation-miljö.  Dessa moduler distribueras automatiskt inte till lokala datorer, så du måste installera dem manuellt.  Undantaget är Azure-modulen som installeras som standard ger tillgång till cmdlets för alla Azure-tjänster och aktiviteter för Azure Automation.

Eftersom Huvudsyftet med Hybrid Runbook Worker-funktionen är att hantera lokala resurser, behöver du troligen installera moduler som stöder dessa resurser.  Du kan referera till [installerar moduler](http://msdn.microsoft.com/library/dd878350.aspx) information om hur du installerar Windows PowerShell-moduler.  Moduler som är installerade måste vara på en plats som refereras av PSModulePath miljövariabeln så att de importeras automatiskt av worker-hybriden.  Mer information finns i [ändra installationssökvägen PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="troubleshooting"></a>Felsökning 

Hybrid Runbook Worker är beroende av Microsoft Monitoring Agent kan kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av worker misslyckas, är här några möjliga orsaker till felet:  

1. Worker-hybriden finns bakom en proxyserver eller brandvägg.  
    Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.  

2. Worker-hybriden körs på datorn har mindre än minimikraven på maskinvara [krav](automation-offering-get-started.md#hybrid-runbook-worker).  
    Datorer som kör Runbook Worker-hybriden ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars kommer att bli överbelastad datorn beroende på resursutnyttjande andra bakgrundsprocesser och konkurrens på grund av runbooks under körning och orsaka fördröjningar för runbook-jobb eller timeout.
   Bekräfta att den dator som är tilldelad att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara.  Om den finns, kan du övervaka användningen av CPU och minne för att fastställa alla korrelation mellan prestanda för Hybrid Runbook Worker-processer och Windows.  Om det finns minne eller CPU-belastning, kan detta tyda på att behöva uppgradera eller lägga till fler processorer eller öka minne för att adressera resurs flaskhalsar och åtgärda felet. Du kan också markera en annan beräkningsresurser som stöder de minimikrav och skala när arbetsbelastning indikera att öka krävs.
    
3. Tjänsten Microsoft Monitoring Agent körs inte.  
    Om Microsoft Monitoring Agent Windows-tjänsten inte körs, förhindrar detta Hybrid Runbook Worker från att kommunicera med Azure Automation.  Kontrollera att agenten körs genom att ange följande kommando i PowerShell: `get-service healthservice`.  Om tjänsten stoppas kan du ange följande kommando i PowerShell för att starta tjänsten: `start-service healthservice`.  

4. I den **program och tjänster för Logs\Operations** händelseloggen händelse 4502 och EventMessage som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** med följande beskrivning: *certifikatet som presenterades av tjänsten <wsid>. oms.opinsights.azure.com har inte utfärdats av en certifikatutfärdare som används för Microsoft-tjänster. Kontakta nätverksadministratören för att se om de kör en proxy som hindrar TLS/SSL-kommunikation. Artikeln KB3126513 innehåller ytterligare felsökningsinformation för problem med nätverksanslutningen.*
    Detta kan orsakas av din brandväggen proxy eller network blockking-kommunikation till Microsoft Azure.  Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

Loggfilerna lagras lokalt på varje worker-hybrid på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Du kan kontrollera om det finns några varning eller felhändelser som sparas i den **program och tjänster Logs\Microsoft-SMA\Operations** och **program och tjänster för Logs\Operations** händelseloggen som visar att det finns en anslutning eller andra problem som påverkar onboarding av rollen för Azure Automation eller problem under normal drift.  

## <a name="next-steps"></a>Nästa steg

* Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-remove-hrw.md)