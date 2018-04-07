---
title: Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om installation och användning av Hybrid Runbook Worker som är en funktion i Azure Automation där du kan köra runbooks på datorer i ditt lokala datacenter eller molnet providern.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 8f212797decdd967154584927984bc0a4e58f4ba
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatisera resurser i ditt datacenter eller molnet med Hybrid Runbook Worker

Runbooks i Azure Automation kan inte komma åt resurser i andra moln eller lokala miljö eftersom de körs i Azure-molnet. Funktionen Hybrid Runbook Worker i Azure Automation kan du köra runbooks direkt på den dator där rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer.

Den här funktionen visas i följande bild:

![Hybrid Runbook Worker-översikt](media/automation-hybrid-runbook-worker/automation.png)

En teknisk översikt över Hybrid Runbook Worker-rollen och distribution överväganden finns [Automation arkitektur, översikt](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker-grupper

Varje Runbook Worker-Hybrid är medlem i en Hybrid Runbook Worker-grupp som du anger när du installerar agenten. En grupp kan innehålla en enskild agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en runbook på en Hybrid Runbook Worker, anger du den grupp som den körs på. Medlemmar i gruppen avgör vilken worker services begäran. Du kan inte ange en viss worker.

## <a name="relationship-to-service-management-automation"></a>Relation till Service Management Automation

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) kan du köra samma runbooks som stöds av Azure Automation i ditt lokala datacenter. SMA distribueras tillsammans med Windows Azure-paket som innehåller ett grafiskt gränssnitt för hantering av SMA för Windows Azure-paket. Till skillnad från Azure Automation kräver SMA en lokal installation som innehåller servrar som värd för API: et, en databas som innehåller runbooks och SMA konfiguration och Runbook Workers för att köra runbook-jobb. Azure Automation ger dessa tjänster i molnet och endast kräver att upprätthålla Hybrid Runbook Worker i din lokala miljö.

Om du är en befintlig SMA-användare kan du flytta dina runbooks till Azure Automation för att användas med Hybrid Runbook Worker utan ändringar, förutsatt att de utföra sina egna autentisering av resurser som beskrivs i [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md). Runbooks i SMA körs i kontexten för kontot på worker-servern, vilket kan ge att autentiseringen för runbooks.

Du kan använda följande kriterier för att avgöra om Azure Automation Hybrid Runbook Worker eller Service Management Automation är mer lämpliga för dina behov.

* SMA kräver en lokal installation av dess underliggande komponenter som är anslutna till Windows Azure Pack om grafiska hanteringsgränssnittet krävs. Flera lokala resurser behövs med högre underhållskostnader än Azure Automation som bara behöver en agent installeras på lokala runbook workers. Agenterna hanteras av Azure, ytterligare minska underhållskostnaderna.
* Azure Automation lagrar dess runbooks i molnet och skickar dem till lokala Hybrid Runbook Worker. Om säkerhetsprinciperna inte tillåter detta problem bör du använda SMA.
* SMA ingår i System Center. och därför kräver en licens för System Center 2012 R2. Azure Automation baseras på en skiktad prenumeration modell.
* Azure Automation har avancerade funktioner, till exempel grafiska runbook-flöden som inte är tillgängliga i SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installera Windows Hybrid Runbook Worker

Om du vill installera och konfigurera en Windows-Hybrid Runbook Worker, finns det två metoder. Den rekommenderade metoden använder en Automation-runbook för att helt automatisera processen som krävs för att konfigurera en Windows-dator. Den andra metoden är följer stegvisa instruktioner för att manuellt installera och konfigurera rollen.

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som stöder Hybrid Runbook Worker-rollen med önskad tillstånd Configuration DSC () som du behöver lägga till dem som DSC-noder. Mer information om onboarding dem för hantering med DSC, se [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
>
>Om du aktiverar den [uppdatering hanteringslösning](../operations-management-suite/oms-solution-update-management.md), en Windows-dator som är ansluten till logganalys-arbetsytan konfigureras automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Det har inte registrerats med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto till stöd för Automation-runbooks så länge som du använder samma konto för både lösning och Hybrid Runbook Worker-gruppmedlemskap. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

Granska följande information om den [maskin- och programvarukrav](automation-offering-get-started.md#hybrid-runbook-worker) och [information för att förbereda nätverket](automation-offering-get-started.md#network-planning) innan du börjar distribuera en Hybrid Runbook Worker. När en runbook worker har distribuerats, granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.

### <a name="automated-deployment"></a>Automatisk distribution

Utför följande steg för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen.

1. Hämta den *ny OnPremiseHybridWorker.ps1* skript från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) direkt från den dator som kör Hybrid Runbook Worker-rollen eller från en annan dator i din miljö och kopierar den till arbetaren.

   Den *ny OnPremiseHybridWorker.ps1* skriptet kräver följande parametrar under körning:

   * *AutomationAccountName* (obligatoriskt) - namnet på ditt Automation-konto.
   * *AAResourceGroupName* (obligatoriskt) - namnet på resursgruppen som är associerade med ditt Automation-konto
   * *OMSResourceGroupName* (valfritt) – namnet på resursgruppen för OMS-arbetsyta. Om inget anges används AAResourceGroupName.
   * *HybridGroupName* (obligatoriskt) - namnet på en Hybrid Runbook Worker-grupp som du anger som mål för runbooks som stöder det här scenariot.
   * *SubscriptionID* (obligatoriskt) - det Azure-prenumerationens ID som ditt Automation-konto.
   * *WorkspaceName* (valfritt) - namnet för det logganalys-arbetsytan. Om du inte har en logganalys-arbetsytan skriptet skapar och konfigurerar en.

     > [!NOTE]
     > Det finns för närvarande endast Automation regioner som stöds för integrering med logganalys - **Australien sydost**, **östra USA 2**, **Sydostasien**, och  **Västra Europa**. Om ditt Automation-konto inte är i någon av de regionerna, skriptet skapar en logganalys-arbetsyta men den varnar dig om att det går inte att länka dem tillsammans.

2. På datorn, startar **Windows PowerShell** från den **starta** skärm i administratörsläge.
3. Från PowerShell-kommandoradsgränssnitt, navigera till mappen som innehåller skriptet som du hämtade och köra den ändra värdena för parametrarna *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, och *- WorkspaceName*.

     > [!NOTE]
     > Du uppmanas att autentisera med Azure när du kör skriptet. Du **måste** logga in med ett konto som är medlem i rollen administratörer för prenumeration och medadministratör för prenumerationen.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Du uppmanas att komma överens om att installera **NuGet** och du uppmanas att autentisera med dina autentiseringsuppgifter för Azure.

5. När skriptet har slutförts bladet Hybrid Worker-grupper visas den nya gruppen och antal medlemmar eller om en befintlig grupp, ökar antalet medlemmar. Du kan välja grupp från listan på den **Hybrid Worker grupper** och välj den **hybrider** panelen. På den **hybrider** bladet visas varje medlem i gruppen i listan.

### <a name="manual-deployment"></a>Manuell distribution

Utför de två första stegen en gång för Automation-miljö och upprepa återstående steg för varje worker-dator.

#### <a name="1-create-log-analytics-workspace"></a>1. Skapa Log Analytics-arbetsyta

Om du inte redan har en logganalys-arbetsytan, skapar du en med hjälp av anvisningarna i [hantera din arbetsyta](../log-analytics/log-analytics-manage-access.md). Du kan använda en befintlig arbetsyta om du redan har en.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Lägg till Automation-lösningen i logganalys-arbetsytan

Lösningar lägger till funktioner i Log Analytics. Automation-lösningen lägger till funktionalitet för Azure Automation, inklusive stöd för Runbook Worker-Hybrid. När du lägger till lösningen till din arbetsyta skickar den automatiskt ned worker komponenter till agentdatorn som du installerar i nästa steg.

Följ anvisningarna på [att lägga till en lösning med lösningar galleriet](../log-analytics/log-analytics-add-solutions.md) att lägga till den **Automation** lösning till logganalys-arbetsytan.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installera Microsoft Monitoring Agent

Microsoft Monitoring Agent ansluter datorer till logganalys. När du installerar agenten på den lokala datorn och ansluta till arbetsytan, hämtas automatiskt de komponenter som krävs för Runbook Worker-Hybrid.

Följ anvisningarna på [ansluta Windows-datorer till logganalys](../log-analytics/log-analytics-windows-agent.md) att installera agenten på den lokala datorn. Du kan upprepa processen för flera datorer att lägga till flera personer i din miljö.

Du kan kontrollera att agenten har hämtat Automation-lösningen på rätt sätt när den har en mapp med namnet **AzureAutomationFiles** i C:\Program Files\Microsoft övervakning Agent\Agent. För att bekräfta versionen av Hybrid Runbook Worker, kan du gå till C:\Program Files\Microsoft övervakning Agent\Agent\AzureAutomation\ och anteckna den \\ *version* undermappen.  

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installera runbook-miljön och ansluta till Azure Automation

När du lägger till en agent till logganalys Automation-lösningen push-meddelanden den **HybridRegistration** PowerShell-modulen som innehåller den **Add-HybridRunbookWorker** cmdlet. Du kan använda denna cmdlet för att installera runbook-miljön på datorn och registrera den med Azure Automation.

Om du vill importera modulen, öppna ett PowerShell-session i administratörsläge och kör följande kommandon:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Kör sedan den **Add-HybridRunbookWorker** cmdlet med följande syntax:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Du kan hämta den information som krävs för denna cmdlet genom att klicka på **nycklar** alternativ **kontoinställningar** i Automation-kontot.

* **Gruppnamn** är namnet på Hybrid Runbook Worker-gruppen. Om den här gruppen finns redan i automation-konto, läggs den aktuella datorn till den. Om den inte redan finns, sedan läggs den.
* **Slutpunkten** är den **URL** i den **nycklar** sidan.
* **Token** är den **primära åtkomstnyckeln** i den **nycklar** sidan.

Använd den **-Verbose** växel med **Add-HybridRunbookWorker** få detaljerad information om installationen.

#### <a name="5-install-powershell-modules"></a>5. Installera PowerShell-moduler

Runbooks kan använda någon av de aktiviteter och cmdlets som definierats i moduler som installerats i din Azure Automation-miljö. Dessa moduler distribueras automatiskt inte till lokala datorer, så du måste installera dem manuellt. Undantaget är Azure-modulen som installeras som standard ger tillgång till cmdlets för alla Azure-tjänster och aktiviteter för Azure Automation.

Eftersom Huvudsyftet med Hybrid Runbook Worker-funktionen är att hantera lokala resurser, behöver du troligen installera moduler som stöder dessa resurser. Du kan referera till [installerar moduler](http://msdn.microsoft.com/library/dd878350.aspx) information om hur du installerar Windows PowerShell-moduler. Moduler som är installerade måste vara på en plats som refereras av PSModulePath miljövariabeln så att de importeras automatiskt av worker-hybriden. Mer information finns i [ändra installationssökvägen PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="removing-hybrid-runbook-worker"></a>Tar bort Hybrid Runbook Worker

Du kan ta bort en eller flera Runbook Worker-hybrider från en grupp och du tar bort gruppen, beroende på dina krav. Utför följande steg för att ta bort en Hybrid Runbook Worker från en lokal dator:

1. Navigera till ditt Automation-konto i Azure-portalen.
2. Från den **inställningar** bladet väljer **nycklar** och anteckna värdena för fältet **URL** och **primära åtkomstnyckeln**. Du behöver den här informationen för nästa steg.
3. Öppna ett PowerShell-session i administratörsläge och kör följande kommando – `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`. Använd den **-Verbose** växla för en detaljerad logg över borttagningen.

> [!NOTE]
> Microsoft Monitoring Agent tas inte bort från datorn, funktioner och konfiguration av Hybrid Runbook Worker-rollen.

## <a name="remove-hybrid-worker-groups"></a>Ta bort Hybrid Worker-grupper

Om du vill ta bort en grupp, måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen med hjälp av proceduren som visades tidigare och utför sedan följande steg för att ta bort gruppen.

1. Öppna Automation-kontot i Azure-portalen.
1. Under **Processautomatisering**väljer **Hybrid worker grupper**. Välj den grupp som du vill ta bort. När du har valt en viss grupp av **Hybrid worker-gruppen** egenskapsbladet visas.

   ![Bladet hybrid Runbook Worker-grupp](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Klicka på egenskapsbladet för den valda gruppen **ta bort**. Ett meddelande visas där du ombeds bekräfta åtgärden, Välj **Ja** om du är säker på att du vill fortsätta.

   ![Ta bort grupp bekräftelsedialogruta](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="troubleshooting"></a>Felsökning

Hybrid Runbook Worker är beroende av Microsoft Monitoring Agent kan kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av worker misslyckas, är här några möjliga orsaker till felet:

1. Worker-hybriden finns bakom en proxyserver eller brandvägg.

   Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

2. Worker-hybriden körs på datorn har mindre än minimikraven på maskinvara [krav](automation-offering-get-started.md#hybrid-runbook-worker).

   Datorer som kör Runbook Worker-hybriden ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars blir överbelastad datorn beroende på resursutnyttjande andra bakgrundsprocesser och konkurrens på grund av runbooks under körning och orsaka fördröjningar för runbook-jobb eller timeout.

   Bekräfta att den dator som är tilldelad att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara. Om den finns, kan du övervaka användningen av CPU och minne för att fastställa alla korrelation mellan prestanda för Hybrid Runbook Worker-processer och Windows. Om det finns minne eller CPU-belastning, kan detta tyda på att behöva uppgradera eller lägga till fler processorer eller öka minne för att adressera resurs flaskhalsar och åtgärda felet. Du kan också markera en annan beräkningsresurser som stöder de minimikrav och skala när arbetsbelastning indikera att öka krävs.

3. Tjänsten Microsoft Monitoring Agent körs inte.

   Om Microsoft Monitoring Agent Windows-tjänsten inte körs, förhindrar detta Hybrid Runbook Worker från att kommunicera med Azure Automation. Kontrollera att agenten körs genom att ange följande kommando i PowerShell: `get-service healthservice`. Om tjänsten stoppas kan du ange följande kommando i PowerShell för att starta tjänsten: `start-service healthservice`.

4. I den **program och tjänster för Logs\Operations** händelseloggen händelse 4502 och EventMessage som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**med följande beskrivning: *certifikatet som presenterades av tjänsten \<wsid\>. oms.opinsights.azure.com har inte utfärdats av en certifikatutfärdare som används för Microsoft-tjänster. Kontakta nätverksadministratören för att se om de kör en proxy som hindrar TLS/SSL-kommunikation. Artikeln KB3126513 innehåller ytterligare felsökningsinformation för problem med nätverksanslutningen.*
    Detta kan bero på att din proxy- eller brandvägg som blockerar kommunikation till Microsoft Azure. Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

Loggfilerna lagras lokalt på varje worker-hybrid på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrollera om det finns några varning eller felhändelser som sparas i den **program och tjänster Logs\Microsoft-SMA\Operations** och **program och tjänster för Logs\Operations** händelseloggen som visar att det finns en anslutning eller andra problem som påverkar onboarding av rollen för Azure Automation eller problem under normal drift.

## <a name="next-steps"></a>Nästa steg

Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
