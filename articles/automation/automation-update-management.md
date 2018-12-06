---
title: Lösningen för uppdateringshantering i Azure
description: Den här artikeln är avsedd att hjälpa dig att förstå hur du använder lösningen för uppdateringshantering för Azure för att hantera uppdateringar för Windows- och Linux-datorer.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 504bb56a7cb3b9582d5c8d2ab1e770d55b8ca9e5
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961628"
---
# <a name="update-management-solution-in-azure"></a>Lösningen för uppdateringshantering i Azure

Du kan använda lösningen för uppdateringshantering i Azure Automation för att hantera uppdateringar av operativsystemet för dina Windows- och Linux-datorer som distribueras i Azure, lokala miljöer eller andra molnleverantörer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Du kan aktivera uppdateringshantering för virtuella datorer direkt från Azure Automation-kontot. Information om hur du aktiverar uppdateringshantering för virtuella datorer från ditt Automation-konto, se [hantera uppdateringar för flera virtuella datorer](manage-update-multi.md). Du kan också aktivera uppdateringshantering för en virtuell dator från fönstret virtuell dator i Azure-portalen. Det här scenariot är tillgänglig för [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) och [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuella datorer.

## <a name="solution-overview"></a>Lösningsöversikt

Datorer som hanteras av uppdateringshantering Använd följande konfigurationer för att utföra utvärdering och uppdatera distribueringar:

* Microsoft Monitoring Agent (MMA) för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Följande diagram visar en konceptuell vy över beteende och dataflöde över hur lösningen utvärderar och tillämpar säkerhetsuppdateringar till alla anslutna Windows Server och Linux-datorer i en arbetsyta:

![Uppdatera processflöde för hantering](media/automation-update-management/update-mgmt-updateworkflow.png)

Hantering av uppdateringar kan användas för att internt registrera datorer i flera prenumerationer i samma klientorganisation.

När en CVE är viktig, tar 2 – 3 timmar innan uppdateringen att visas för Linux-datorer för utvärdering.  För Windows-datorer tar den 12 – 15 timmar för uppdatering ska visas för utvärderingen efter att det har släppts.

När en dator är klar en sökning efter uppdateringskompatibilitet vidarebefordrar agenten informationen gruppvis till Azure Log Analytics. På en Windows-dator körs kompatibilitetsgenomsökningen var 12: e timme som standard.

Förutom genomsökningsschemat initieras sökningen för uppdateringskompatibilitet inom 15 minuter om du MMA startas innan installationen av uppdateringen och efter installationen av uppdateringen.

För en Linux-dator utförs kompatibilitetsgenomsökningen var tredje timme som standard. Om MMA-agenten startas initieras en kompatibilitetsgenomsökning inom 15 minuter.

Lösningen rapporterar hur uppdaterad datorn är utifrån vilken källa du är konfigurerad för att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS, beroende på när WSUS senast synkroniserad med Microsoft Update kan resultatet skiljer sig från vad Microsoft Updates visar. Det här beteendet är samma för Linux-datorer som är konfigurerade att rapportera till en lokal lagringsplats i stället för till en offentlig repo.

> [!NOTE]
> För att korrekt rapportera till tjänsten, hantering av uppdateringar som kräver vissa webbadresser och portar som ska aktiveras. Mer information om dessa krav finns [Network planera för Hybrid Worker-arbeten](automation-hybrid-runbook-worker.md#network-planning).

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som *valfritt* ingår inte i distributionsomfattningen för Windows-datorer. Endast nödvändiga uppdateringar som ingår i distributionsomfattningen. 

En schemalagd distribution definierar vilka måldatorer som får tillämpliga uppdateringar, antingen genom att uttryckligen ange datorer eller genom att välja en [datorgrupp](../azure-monitor/platform/computer-groups.md) som baseras på loggsökningar för en specifik uppsättning datorer. Du kan även ange ett schema för att godkänna och ange en viss tidsperiod under vilken du kan installera uppdateringar.

Uppdateringar installeras av runbooks i Azure Automation. Du kan inte visa dessa runbooks och runbooks kräver inte någon konfigurering. När en uppdateringsdistribution skapas, skapar ett schema som startar en masteruppdaterings-runbook vid den angivna tidpunkten för datorerna som ingår i uppdateringsdistributionen. Master-runbook startar en underordnad runbook på varje agent så att installera nödvändiga uppdateringar.

Vid det datum och tid som anges i uppdateringsdistributionen kör måldatorerna distributionen parallellt. Innan du installerar görs en sökning för att verifiera att uppdateringarna fortfarande är nödvändiga. Om uppdateringarna som inte godkänts i WSUS, för WSUS-klientdatorer misslyckas distributionen av uppdateringen.

Med en dator som har registrerats för uppdateringshantering i mer än en Log Analytics-arbetsytor (flera värdar) stöds inte.

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Stöds klienttyper

I följande tabell visas en lista över operativsystem som stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Stöder bara uppdatera utvärderingar.         |
|Windows Server 2008 R2 SP1 och senare (inklusive Windows Server 2012 och 2016)    |.NET framework 4.5.1 eller senare krävs. ([Hämta .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 eller senare krävs. ([Hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 rekommenderas för ökad tillförlitlighet.  ([Hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. 'Yum' kräver klassificeringsbaserad uppdatering för att returnera säkerhetsdata som CentOS inte har direkt.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS, 16.04 LTS och 18.04 (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

### <a name="unsupported-client-types"></a>Klientappar typer

I följande tabell visas operativsystem som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klientoperativsystem (till exempel Windows 7 och Windows 10) stöds inte.        |
|Windows Server 2016 Nano Server     | Stöds ej.       |

### <a name="client-requirements"></a>Klientkrav

#### <a name="windows"></a>Windows

Windows-agenter måste konfigureras för att kommunicera med en WSUS-server eller de måste ha åtkomst till Microsoft Update. Du kan använda hantering av uppdateringar med System Center Configuration Manager. Läs mer om integrationsscenarier i [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md#configuration). Den [Windows-agenten](../azure-monitor/platform/agent-windows.md) krävs. Agenten installeras automatiskt om du är onboarding virtuella Azure-datorer.

#### <a name="linux"></a>Linux

För Linux, måste datorn ha åtkomst till en uppdateringslagringsplats. Uppdatera databasen kan vara privat eller offentlig. TLS 1.1 eller TLS 1.2 krävs för att interagera med hantering av uppdateringar. En Log Analytics-agenten för Linux som är konfigurerad att rapportera till fler än en Log Analytics-arbetsytor stöds inte med den här lösningen.

Information om hur du installerar Log Analytics-agenten för Linux och hämta den senaste versionen finns i [Operations Management Suite-agenten för Linux](https://github.com/microsoft/oms-agent-for-linux). Information om hur du installerar Log Analytics-agenten för Windows finns i [Operations Management Suite-agenten för Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Behörigheter

Du behöver specifika behörigheter för att skapa och hantera distributioner av uppdateringar. Läs om dessa behörigheter i [rollbaserad åtkomst - uppdateringshantering](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Lösningskomponenter

Lösningen består av följande resurser. Resurserna som läggs till i ditt Automation-konto. De är antingen direkt anslutna agenter eller i en Operations Manager-ansluten hanteringsgrupp.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper

När du har aktiverat den här lösningen konfigureras automatiskt alla Windows-datorer som är direkt ansluten till Log Analytics-arbetsytan som en Hybrid Runbook Worker för att ge stöd för runbooks som ingår i den här lösningen.

Alla Windows-datorer som hanteras av lösningen visas i den **Hybrid worker-grupper** som en **System hybrid worker-gruppen** för Automation-kontot. Lösningarna använda namngivningskonventionen *Hostname FQDN_GUID*. Du kan inte skicka dessa grupper med runbooks i ditt konto. De misslyckas om du försöker. Dessa grupper är avsedda att stödja endast hanteringslösningen.

Du kan lägga till Windows-datorer till en Hybrid Runbook Worker-grupp i ditt Automation-konto för Automation-runbooks om du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din hanteringsgrupp för System Center Operations Manager är ansluten till en Log Analytics-arbetsyta, installeras följande hanteringspaket i Operations Manager. Dessa hanteringspaket också har installerats på direktanslutna Windows-datorer när du lägger till lösningen. Du behöver inte konfigurera eller hantera dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

Mer information om hur lösningens hanteringspaket uppdateras finns i [ansluta Operations Manager till Log Analytics](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> För system med Operations Manager-agenten för att kunna hanteras helt av uppdateringshantering, måste agenten uppdateras till Microsoft Monitoring Agent. Information om hur du uppdaterar agenten finns [så här uppgraderar du en Operations Manager-agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Bekräfta att icke-Azure-datorer är integrerats

Om du vill bekräfta att direkt anslutna datorer kommunicerar med Log Analytics, efter ett par minuter kan du köra en följande sökningar i loggen.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Du kan granska följande information för att verifiera agentanslutning med Log Analytics på en Windows-dator:

1. På Kontrollpanelen, öppna **Microsoft Monitoring Agent**. På den **Azure Log Analytics** fliken agenten visas följande meddelande: **The Microsoft Monitoring Agent har anslutits till Log Analytics**.
2. Öppna Windows-händelseloggen. Gå till **program- och tjänstloggar\operations Manager** och Sök efter händelse-ID 3000 och 5002 för händelse-ID från källan **tjänstanslutning**. Dessa händelser anger att datorn har registrerats med Log Analytics-arbetsytan och tar emot konfigurationen.

Om agenten inte kan kommunicera med Log Analytics och agenten är konfigurerad för att kommunicera med internet genom en brandvägg eller proxyserver, kontrollerar du att den brandväggen eller proxyservern har konfigurerats korrekt. Läs hur du kontrollerar att den brandväggen eller proxyservern har konfigurerats korrekt i [nätverkskonfiguration för Windows-agenten](../azure-monitor/platform/agent-windows.md) eller [nätverkskonfiguration för Linux-agenten](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Om dina Linux-system har konfigurerats för att kommunicera med en proxy eller Log Analytics-Gateway och är onboarding den här lösningen, uppdatera den *proxy.conf* behörigheter som ska tilldelas den gruppen omiuser läsbehörighet för filen med hjälp av den följande kommandon:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nyligen tillagda Linux-agenter visar statusen **uppdaterad** när en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

För att bekräfta att en Operations Manager-hanteringsgrupp kommunicerar med Log Analytics, se [verifiera Operations Manager-integrering med Log Analytics](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Linux-agenter och initierar installationen av nödvändiga uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br/>En direktanslutning från Operations Manager-agenten till Log Analytics krävs inte. Data vidarebefordras från hanteringsgruppen till Log Analytics-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

Två gånger per dag för varje hanterad Windows-dator utförs en sökning. Var femtonde minut anropas Windows API att fråga efter den senaste uppdateringstiden att fastställa om statusen har ändrats. Om statusen har ändrats, har en fullständig genomsökning initierats.

Utförs en sökning var tredje timme för varje hanterad Linux-dator.

Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data från hanterade datorer.

## <a name="viewing-update-assessments"></a>Visa uppdatering utvärderingar

I ditt Automation-konto väljer **uppdateringshantering** att visa status för dina datorer.

Den här vyn visar information om dina datorer som saknar uppdateringar, uppdateringsdistributioner och schemalagda distributioner. I den **efterlevnad kolumnen**, du kan se den senaste gången datorn uppskattades. I den **uppdatera AGENTBEREDSKAP** kolumn, kan du se om hälsotillståndet för update-agenten. Om det finns ett problem, väljer du länken som leder till Felsökningsdokumentation som kan hjälpa att du lär dig vilka steg för att åtgärda problemet.

Om du vill köra en loggsökning som returnerar information om datorn, uppdatering och distribution, markerar du objektet i listan. Den **Loggsökning** fönstret som öppnas med en fråga för det markerade objektet:

![Standard över uppdateringshantering](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Installera uppdateringar

När uppdateringar utvärderas för alla Linux- och Windows-datorer i din arbetsyta, kan du installera nödvändiga uppdateringar genom att skapa en *uppdateringsdistribution*. En uppdateringsdistribution är en schemalagd installation av nödvändiga uppdateringar för en eller flera datorer. Du kan ange datum och tid för distributionen och en dator eller grupp av datorer som ska ingå i omfattningen för en distribution. Läs mer om datorgrupper i [Computer groups in Log Analytics](../azure-monitor/platform/computer-groups.md) (Datorgrupper i Log Analytics).

 När du inkluderar datorgrupper i din distribution utvärderas gruppmedlemskap bara en gång när schemat skapas. Efterföljande ändringar i en grupp syns inte. Att komma runt denna [dynamiska grupper](#using-dynamic-groups), dessa grupper har lösts vid tidpunkten för distribution och definieras av en fråga.

> [!NOTE]
> Windows-datorer som distribueras från Azure Marketplace som standard är inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det här beteendet ändras inte när du lägger till den här lösningen eller lägga till Windows-datorer i din arbetsyta. Om du inte aktivt hantera uppdateringar med hjälp av den här lösningen, gäller standardbeteendet (för att automatiskt tillämpa uppdateringar).

Konfigurera om Unattended Upgrade-paketet om du vill inaktivera automatiska uppdateringar för att undvika att uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Information om hur du konfigurerar paketet finns i [automatiska uppdateringar-avsnittet i handboken för Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Virtuella datorer som har skapats från Red Hat Enterprise Linux (RHEL) på begäran-avbildningar som är tillgängliga i Azure Marketplace är registrerade åtkomst till den [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) som har distribuerats i Azure. Andra Linux-distribution måste du uppdatera från den distributionsplatsen online filens centrallager genom att följa distributionsmetoder som stöds.

Om du vill skapa en ny uppdateringsdistribution, Välj **distribution av schemauppdatering**. Den **ny Uppdateringsdistribution** öppnas fönstret. Ange värden för de egenskaper som beskrivs i följande tabell och klicka sedan på **skapa**:

| Egenskap  | Beskrivning |
| --- | --- |
| Namn |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Linux eller Windows|
| Grupper för att uppdatera (förhandsversion)|Definiera en fråga som baseras på en kombination av prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer ska ingå i din distribution. Mer information finns i [dynamiska grupper](automation-update-management.md#using-dynamic-groups)|
| Datorer som ska uppdateras |Välj en sparad sökning, importerat gruppen, eller välja dator från listrutan och Välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Mer om de olika metoderna för att skapa datorgrupper i Log Analytics finns i dokumentationen om [datorgrupper i Log Analytics](../azure-monitor/platform/computer-groups.md) |
|Uppdatera klassificeringar|Välj de uppdateringsklassificeringar som du behöver|
|Inkludera/exkludera uppdateringar|Då öppnas det **ta med eller undanta** sidan. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik. Mer information om hur inkludering hanteras finns [inkludering beteende](automation-update-management.md#inclusion-behavior) |
|Schemainställningar|Välj tid att starta och välj antingen en gång eller återkommande för upprepningen|
| Förskript och efterskript|Välj skript ska köras före och efter distributionen|
| Underhållsperiod |Antal minuter som angetts för uppdateringar. Värdet får inte vara mindre än 30 minuter och högst 6 timmar |
| Starta om kontroll| Anger hur omstarter ska hanteras. De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Distributioner av uppdateringar kan även skapas programmässigt. Läs hur du skapar en distribution med REST API i [programvarukonfigurationer för Update - skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempel-runbook som kan användas för att skapa en distribution av varje vecka. Läs mer om denna runbook i [skapa en veckovis uppdateringsdistribution för en eller flera virtuella datorer i en resursgrupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-missing-updates"></a>Visa uppdateringar som saknas

Välj **saknade uppdateringar** att visa en lista med uppdateringar som saknas från dina virtuella datorer. Varje uppdatering visas och kan väljas. Information om hur många datorer som kräver uppdateringen, operativsystemet och en länk för mer information visas. Den **loggsökning** fönstret visar mer information om uppdateringarna.

## <a name="view-update-deployments"></a>Visa uppdateringsdistributioner

Välj den **distributioner av uppdateringar** fliken för att visa listan över befintliga uppdateringsdistributioner. Välj någon av uppdateringsdistributioner i tabellen för att öppna den **uppdatera distribution kör** fönstret för den distributionen.

![Översikt över uppdateringsdistributionens resultat](./media/automation-update-management/update-deployment-run.png)

En uppdateringsdistribution från REST API finns [programvara uppdatera konfigurationen körs](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Uppdatera klassificeringar

I tabellerna nedan listas uppdateringsklassificeringar i uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett specifikt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabbkorrigeringar som är packade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produktfunktioner som distribueras utanför en produktlansering.        |
|Service pack     | En kumulativ uppsättning snabbkorrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper dig att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering för ett program eller en fil som är installerad.        |

### <a name="linux"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett specifikt program eller en produktspecifik, säkerhetsrelaterad fråga.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är viktiga sin natur eller som inte är säkerhetsuppdateringar.        |

För Linux, hantering av uppdateringar kan skilja mellan kritiska uppdateringar och säkerhetsuppdateringar i molnet när visades utvärdering av data på grund av databerikande i molnet. För uppdatering, uppdateringshantering förlitar sig på data för klassificering som är tillgängliga på datorn. Till skillnad från andra distributioner, CentOS har inte denna information tillgänglig direkt. Om du har CentOS-datorer som konfigurerats på ett sätt att returnera säkerhetsdata för följande kommando, kommer hantering av uppdateringar att kunna patch baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande inga metoden stöds-metoden för att aktivera interna klassificering-data tillgängliga på CentOS. För närvarande tillhandahålls endast mån support till kunder som kanske har aktiverat det på egen hand.

## <a name="firstparty-predownload"></a>Första parts korrigeringar och förhandsnedladda

Hantering av uppdateringar är beroende av Windows Update för att hämta och installera Windows-uppdateringar. Därför kan respekterar vi många av inställningarna som används av Windows Update. Om du använder inställningar för att aktivera icke-Windows-uppdateringar, hanterar uppdateringshantering även dessa uppdateringar. Om du vill aktivera hämtar uppdateringar innan en uppdateringsdistribution sker distributioner gå snabbare och är mindre troligt att överskrida underhållsfönstret.

### <a name="pre-download-updates"></a>Pre hämta uppdateringar

Om du vill konfigurera automatiskt hämtar uppdateringar i en Grupprincip, kan du ange den [inställningen Konfigurera automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#BKMK_comp5) till **3**. Detta hämtar uppdateringar som behövs i bakgrunden, men installeras inte. Detta håller uppdateringshantering kontroll över scheman, men kan uppdateringar som hämtas utanför underhållsperioden uppdateringshantering. Det här kan hindra **underhållsperioden har överskridits** fel i hantering av uppdateringar.

Du kan också ange detta med PowerShell, kör följande PowerShell på ett system som du vill hämta uppdateringar automatiskt.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Som standard innehåller Windows Update endast uppdateringar för Windows. Om du aktiverar **hämta uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows**, det finns uppdateringar för andra produkter, inklusive sådana saker säkerhetsuppdateringar för SQL Server eller andra första tillverkare. Det här alternativet kan inte konfigureras av en Grupprincip. Kör följande PowerShell på de system som du vill aktivera andra första part korrigeringsfiler på och uppdateringshantering följer den här inställningen.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="ports"></a>Planera för nätverk

Följande adresser krävs för hantering av uppdateringar. Kommunikation till dessa adresser sker via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.Azure automation.us|

Mer information om portar som kräver att Hybrid Runbook Worker finns [Hybrid Worker-rollen portar](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Det rekommenderas att använda de adresser som anges när du definierar undantag. För IP-adresser som du kan ladda ned den [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och återspeglar aktuella intervall och eventuella kommande ändringar till IP-adressintervall.

## <a name="search-logs"></a>Sök i loggar

Förutom den information som tillhandahålls i Azure-portalen, kan du göra sökningar mot loggarna. På sidorna för lösningen väljer **Log Analytics**. Den **Loggsökning** öppnas fönstret.

Du kan också lära dig hur anpassa frågor eller använda dem från olika klienter och mer genom att besöka: [Log Analytics Sök API-dokumentationen](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Exempelfrågor

Följande avsnitt innehåller Exempelfrågor för loggen för uppdateringsposter som samlas in av den här lösningen:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Enkel utvärdering av Virtuella Azure-frågor (Windows)

Ersätt värdet för VMUUID med VM-GUID för den virtuella datorn som du frågar. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Log Analytics: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Sammanfattning av uppdateringar som saknas

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Saknade uppdateringar lista

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Enskild virtuell dator i Azure-utvärdering frågor (Linux)

För vissa Linux-distributioner finns en [endianness](https://en.wikipedia.org/wiki/Endianness) inte överensstämmer med det VMUUID-värde som kommer från Azure Resource Manager och vad som lagras i Log Analytics. Följande fråga söker efter en matchning på antingen endianness. Ersätt värdena VMUUID med big endian och little endian formatering av GUID för att korrekt returnerar resultat. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Log Analytics: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Sammanfattning av uppdateringar som saknas

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Saknade uppdateringar lista

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Frågor för multi-VM-utvärdering

##### <a name="computers-summary"></a>Sammanfattning av datorer

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Sammanfattning av uppdateringar som saknas

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Datorlista

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Saknade uppdateringar lista

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Med dynamiska grupper (förhandsversion)

Uppdateringshantering ger möjlighet att fokusera på en dynamisk grupp av virtuella Azure-datorer för distributioner av uppdateringar. De här grupperna definieras av en fråga, när en uppdateringsdistribution börjar medlemmar i gruppen utvärderas. När du definierar din fråga måste kan följande objekt användas tillsammans att fylla i den dynamiska gruppen

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/automation-update-management/select-groups.png)

Om du vill förhandsgranska resultaten av en dynamisk grupp klickar du på den **förhandsversion** knappen. Den här förhandsgranskningen visar gruppmedlemskapet vid den tiden, i det här exemplet vi söker efter datorer med taggen **rollen** är lika med **BackendServer**. Om flera virtuella datorer har den här taggen har lagts till, läggs de till alla framtida distributioner mot den gruppen.

![förhandsversion av grupper](./media/automation-update-management/preview-groups.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrera med System Center Configuration Manager

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av ett styrka och mognad av Configuration Manager som hjälper dem att hantera programuppdateringar. Configuration Manager är en del av sin cykel för hantering (SUM) programvara.

Läs hur du integrerar hanteringslösningen med System Center Configuration Manager i [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Inkludering beteende

Uppdatera inkludering kan du ange specifika uppdateringar tillämpas. Korrigeringsfiler eller paket som ingår är installerade. När korrigeringsfiler eller paket ingår och en klassificering har markerats, installeras både inkluderade objekt och objekt som uppfyller klassificeringen.

Det är viktigt att veta att undantag åsidosätta inkluderingar. Till exempel om du definierar en regel för exkludering av `*`, och sedan inga korrigeringar eller paket som är installerade som de är alla undantagna. För Linux-datorer om ett paket som ingår men innehåller ett beroende paket som har undantagits är installeras paketet inte.

## <a name="patch-linux-machines"></a>Patch Linux-datorer

I följande avsnitt beskrivs potentiella problem med Linux-korrigering.

### <a name="unexpected-os-level-upgrades"></a>Oväntat operativsystemsnivån uppgraderas

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan OS-nivå uppgraderingar uppstå via paket. Detta kan leda till uppdateringshantering körningar där OS-versionsnumret ändras. Det här beteendet är avsiktligt eftersom uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på Linux-dator.

Undvik att uppdatera OS-version via hantering av uppdateringar körs, att använda den **undantag** funktionen.

I Red Hat Enterprise Linux är paketnamnet som ska undantas redhat-release-server.x86_64.

![Paket som ska undantas för Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritiska / säkerhetsuppdateringar tillämpas inte

När du distribuerar uppdateringar till en Linux-dator kan välja du klassificeringar för uppdatering. Detta filtrerar de uppdateringar som tillämpas på datorn som uppfyller de angivna kriterierna. Det här filtret tillämpas lokalt på datorn när uppdateringen distribueras.

Eftersom uppdateringshantering update-funktioner i molnet, kan vissa uppdateringar flaggas i uppdateringshantering som har betydelse för säkerhet, trots att den lokala datorn inte har den här informationen. Därmed, om du använder kritiska uppdateringar till en Linux-dator, kan det finnas uppdateringar som inte markeras som med Säkerhetspåverkan om att datorn och uppdateringarna inte tillämpas.

Uppdateringshantering kan dock fortfarande att rapportera den datorn som icke-kompatibel eftersom den innehåller ytterligare information om relevanta uppdateringen.

Distribuera uppdateringar med klassificeringen fungerar inte på CentOS direkt ur lådan. För SUSE, att välja *endast* andra uppdateringar som klassificeringen resultera i att vissa uppdateringar installeras även om säkerhetsuppdateringar rör zypper (package manager) eller dess beroenden krävs först. Det här är en begränsning i zypper. I vissa fall kan du bli ombedd att köra uppdateringsdistribution, för att verifiera Kontrollera update-loggen.

## <a name="troubleshoot"></a>Felsöka

Läs hur du felsöker din uppdateringshantering i [felsökning uppdateringshantering](troubleshoot/update-management.md)

## <a name="next-steps"></a>Nästa steg

Vill du fortsätta till självstudien om hur du hanterar uppdateringar för din Windows-datorer.

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för virtuella datorer i Windows Azure](automation-tutorial-update-management.md)

* Använd loggsökningar i [Log Analytics](../log-analytics/log-analytics-log-searches.md) att visa detaljerad uppdateringsinformation.
* [Skapa aviseringar](automation-tutorial-update-management.md#configure-alerts) för status för uppdateringsdistributionen.

* Läs hur du interagerar med uppdateringshantering via REST API i [Update konfigurationer](/rest/api/automation/softwareupdateconfigurations)
