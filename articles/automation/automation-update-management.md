---
title: Uppdateringshantering lösning i Azure
description: Den här artikeln är avsedd att hjälpa dig att förstå hur du använder Azure Uppdateringshantering-lösningen för att hantera uppdateringar för dina Windows-och Linux-datorer.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20d8106f06b708527fc60e025a19c6b07656acb3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102655"
---
# <a name="update-management-solution-in-azure"></a>Uppdateringshantering lösning i Azure

Du kan använda Uppdateringshantering lösning i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-datorer i Azure, i lokala miljöer eller i andra moln leverantörer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Du kan aktivera Uppdateringshantering för virtuella datorer direkt från ditt Azure Automation-konto. Information om hur du aktiverar Uppdateringshantering för virtuella datorer från ditt Automation-konto finns i [Hantera uppdateringar för flera virtuella datorer](manage-update-multi.md). Du kan också aktivera Uppdateringshantering för en virtuell dator från sidan virtuell dator i Azure Portal. Det här scenariot är tillgängligt för virtuella [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) -och [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) -datorer.

> [!NOTE]
> Uppdateringshantering-lösningen kräver att du länkar en Log Analytics arbets yta till ditt Automation-konto. En slutgiltig lista över regioner som stöds finns i [mappningar för Azure-arbetsytor](./how-to/region-mappings.md). Region mappningarna påverkar inte möjligheten att hantera virtuella datorer i en separat region än ditt Automation-konto.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Lösningsöversikt

Datorer som hanteras med Uppdateringshantering använda följande konfigurationer för att utföra utvärderings-och uppdaterings distributioner:

* Microsoft Monitoring Agent (MMA) för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Följande diagram visar en konceptuell vy över beteendet och data flödet med hur lösningen utvärderar och tillämpar säkerhets uppdateringar på alla anslutna Windows Server-och Linux-datorer i en arbets yta:

![Uppdateringshantering process flöde](./media/automation-update-management/update-mgmt-updateworkflow.png)

Uppdateringshantering kan användas för att internt publicera datorer i flera prenumerationer i samma klient organisation.

När ett paket har frigjorts tar det 2-3 timmar innan korrigeringen visas för Linux-datorer för utvärdering. För Windows-datorer tar det 12-15 timmar innan korrigeringen visas för utvärdering när den har släppts.

När en dator har slutfört en sökning efter uppdateringens efterlevnad vidarebefordrar agenten informationen i bulk till Azure Monitor loggar. På en Windows-dator körs kompatibilitetskontroll var 12: e timme som standard.

Förutom genomsöknings schemat initieras genomsökningen av kompatibiliteten inom 15 minuter från den MMA som startas om, innan installationen av uppdateringen och efter installationen av uppdateringen.

För en Linux-dator utförs genomsökningen varje timme som standard. Om MMA-agenten startas om initieras en kompatibilitetskontroll inom 15 minuter.

Lösningen rapporterar hur uppdaterad datorn baseras på vilken källa du är konfigurerad att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS, beroende på när WSUS senast synkroniserades med Microsoft Update, kan resultatet skilja sig från vad Microsoft Updates visar. Detta är detsamma för Linux-datorer som kon figurer ATS för att rapportera till en lokal lagrings platsen i stället för till en offentlig lagrings platsen.

> [!NOTE]
> För att rapporten ska kunna rapporteras till tjänsten måste vissa URL: er och portar vara aktiverade för Uppdateringshantering. Mer information om dessa krav finns i [nätverks planering för Hybrid arbetare](automation-hybrid-runbook-worker.md#network-planning).

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som *valfria* ingår inte i distributions omfånget för Windows-datorer. Endast nödvändiga uppdateringar ingår i distributions omfånget.

Den schemalagda distributionen definierar vilka mål datorer som får tillämpliga uppdateringar, antingen genom att uttryckligen ange datorer eller genom att välja en [dator grupp](../azure-monitor/platform/computer-groups.md) som baseras på loggs ökningar av en specifik uppsättning datorer eller en [Azure-fråga](#azure-machines) som dynamiskt väljer virtuella Azure-datorer baserat på angivna villkor. De här grupperna skiljer sig från [omfattnings konfigurationen](../azure-monitor/insights/solution-targeting.md), som endast används för att avgöra vilka datorer som får hanterings paketen som aktiverar lösningen.

Du kan också ange ett schema för godkännande och ange en tids period då uppdateringar kan installeras. Den här tids perioden kallas underhålls perioden. Tio minuter i underhålls perioden är reserverat för omstarter om en omstart krävs och du valt lämpligt alternativ för omstart. Om korrigeringen tar längre tid än förväntat och det finns mindre än tio minuter i underhålls perioden görs ingen omstart.

Uppdateringar installeras av runbooks i Azure Automation. Du kan inte visa dessa Runbooks och Runbooks kräver ingen konfiguration. När en uppdaterings distribution skapas skapar uppdaterings distributionen ett schema som startar en huvud uppdaterings-Runbook vid den angivna tidpunkten för de datorer som ingår. Huvud-Runbook startar en underordnad Runbook på varje agent för att installera nödvändiga uppdateringar.

Vid det datum och den tid som anges i uppdaterings distributionen kör mål datorerna distributionen parallellt. Före installationen körs en genomsökning för att verifiera att uppdateringarna fortfarande krävs. För WSUS-klientdatorer, om uppdateringarna inte är godkända i WSUS, Miss lyckas uppdaterings distributionen.

En dator som är registrerad för Uppdateringshantering i fler än en Log Analytics arbets ytor (Multi-värdar) stöds inte.

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Stöds klienttyper

I följande tabell visas en lista över operativ system som stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center Core/standard)<br><br>Windows Server 2016 (Data Center/Data Center Core/standard)<br><br>Windows Server 2012 R2 (Data Center/standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM och SP1 standard)|**Uppdaterings utvärderingar**: Stöds<br><br>**Uppdatering**: Kräver Hybrid Runbook Worker. Se [hybrid Runbook Worker krav](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)|
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. Klassificerings baserad uppdatering kräver ' yum ' för att returnera säkerhets data som CentOS inte har gjort i rutan. Mer information om klassificerings-baserad uppdatering på CentOS finns i [uppdaterings klassificeringar på Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14,04 LTS, 16,04 LTS och 18,04 (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

> [!NOTE]
> Skalnings uppsättningar för virtuella Azure-datorer kan hanteras med Uppdateringshantering. Uppdateringshantering fungerar på själva instanserna och inte på bas avbildningen. Du måste schemalägga uppdateringarna på ett stegvist sätt, eftersom du inte vill uppdatera alla VM-instanser samtidigt.
> VMSS-noder kan läggas till genom att följa stegen under [onbaord en icke-Azure-dator](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Klientappar typer

I följande tabell visas operativsystem som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klientoperativsystem (till exempel Windows 7 och Windows 10) stöds inte.        |
|Windows Server 2016 Nano Server     | Stöds ej.       |
|Azure Kubernetes service-noder | Stöds ej. Använd korrigerings processen som beskrivs i [tillämpa säkerhets-och kernel-uppdateringar på Linux-noder i Azure Kubernetes service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Klient krav

#### <a name="windows"></a>Windows

Windows-agenter måste konfigureras för att kommunicera med en WSUS-server, eller så måste de ha åtkomst till Microsoft Update. Du kan använda Uppdateringshantering med System Center Configuration Manager. Mer information om integrations scenarier finns i [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md#configuration). [Windows](../azure-monitor/platform/agent-windows.md) -agenten krävs. Agenten installeras automatiskt om du registrerar en virtuell Azure-dator.

> [!NOTE]
> Det är möjligt för en användare att ändra grupprincip så att omstarter av datorn bara kan utföras av användaren, inte av systemet. Hanterade datorer kan fastna, om Uppdateringshantering inte har behörighet att starta om datorn utan manuell interaktion från användaren.
>
> Mer information finns i [konfigurera grupprincip inställningar för automatiska uppdateringar](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

För Linux måste datorn ha åtkomst till en uppdaterings lagrings plats. Uppdaterings lagrings platsen kan vara privat eller offentlig. TLS 1,1 eller TLS 1,2 krävs för att interagera med Uppdateringshantering. En Log Analytics-agent för Linux som är konfigurerad för att rapportera till fler än en Log Analytics arbets ytor stöds inte med den här lösningen.  Datorn måste också ha python 2. x installerat.

Information om hur du installerar Log Analytics-agenten för Linux och laddar ned den senaste versionen finns i [Log Analytics agent för Linux](https://github.com/microsoft/oms-agent-for-linux). Information om hur du installerar Log Analytics agent för Windows finns i [Microsoft Monitoring Agent för Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Behörigheter

Om du vill skapa och hantera uppdaterings distributioner måste du ha vissa behörigheter. Mer information om dessa behörigheter finns i [rollbaserad åtkomst uppdateringshantering](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Lösningskomponenter

Lösningen består av följande resurser. Resurserna läggs till i ditt Automation-konto. De är antingen direktanslutna agenter eller i en Operations Manager ansluten hanterings grupp.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper

När du har aktiverat den här lösningen konfigureras en Windows-dator som är direkt ansluten till din Log Analytics-arbetsyta automatiskt som en Hybrid Runbook Worker för att stödja Runbooks som ingår i den här lösningen.

Varje Windows-dator som hanteras av lösningen visas i rutan **hybrid Worker-grupper** som en **system hybrid Worker-grupp** för Automation-kontot. Lösningarna använder namngivnings konventionens *värdnamn FQDN_GUID*. Du kan inte rikta de här grupperna med Runbooks i ditt konto. De fungerar inte om du försöker. De här grupperna är avsedda att endast stödja hanterings lösningen.

Du kan lägga till Windows-datorer i en Hybrid Runbook Worker grupp i ditt Automation-konto för att stödja Automation-runbooks om du använder samma konto för både lösningen och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din System Center Operations Manager hanterings grupp är ansluten till en Log Analytics arbets yta installeras följande hanterings paket i Operations Manager. Dessa hanterings paket installeras också på direktanslutna Windows-datorer när du har lagt till lösningen. Du behöver inte konfigurera eller hantera dessa hanterings paket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

> [!NOTE]
> Om du har en Operations Manager 1807-eller 2019-hanterings grupp med agenter som kon figurer ATS på hanterings grupps nivå för att kopplas till en arbets yta, är den aktuella lösningen för att hämta dem att åsidosätta **IsAutoRegistrationEnabled** till **Sant** i regeln **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Mer information om hur hanterings paket för lösningar uppdateras finns i [anslut Operations Manager till Azure Monitor loggar](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> För att system med Operations Manager-agenten ska kunna hanteras fullständigt av Uppdateringshantering måste agenten uppdateras till Microsoft Monitoring Agent. Information om hur du uppdaterar agenten finns i [så här uppgraderar du en Operations Manager-agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). För miljöer som använder Operations Manager krävs det att du kör System Center Operations Manager 2012 R2 UR 14 eller senare.

## <a name="onboard"></a>Aktivera Uppdateringshantering

Om du vill börja korrigera system måste du aktivera Uppdateringshantering-lösningen. Det finns många sätt att publicera datorer på Uppdateringshantering. Följande är de rekommenderade och de sätt som stöds för att publicera lösningen:

* [Från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Från att bläddra flera datorer](automation-onboard-solutions-from-browse.md)
* [Från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Med en Azure Automation Runbook](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Bekräfta att icke-Azure-datorer har publicerats

För att bekräfta att direktanslutna datorer kommunicerar med Azure Monitor loggar efter några minuter kan du köra en följande loggs ökning.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

På en Windows-dator kan du granska följande information för att verifiera agent anslutningen med Azure Monitor loggar:

1. Öppna **Microsoft Monitoring Agent**i kontroll panelen. På fliken **Azure-Log Analytics** visar agenten följande meddelande: **Microsoft Monitoring Agent har anslutit till Log Analytics**.
2. Öppna händelse loggen i Windows. Gå till **program-och tjänst loggar \ Operations Manager** och Sök efter händelse-ID 3000 och händelse-ID 5002 från käll **tjänst anslutningen**. Dessa händelser anger att datorn har registrerats med Log Analytics-arbetsytan och tar emot konfiguration.

Om agenten inte kan kommunicera med Azure Monitor loggar och agenten är konfigurerad för att kommunicera med Internet via en brand vägg eller proxyserver, kontrollerar du att brand väggen eller proxyservern har kon figurer ATS korrekt. Information om hur du verifierar att brand väggen eller proxyservern har kon figurer ATS korrekt finns i [nätverks konfiguration för Windows-agent](../azure-monitor/platform/agent-windows.md) eller [nätverks konfiguration för Linux](../log-analytics/log-analytics-agent-linux.md)-agenten.

> [!NOTE]
> Om Linux-systemen har kon figurer ATS för att kommunicera med en proxy eller Log Analytics gateway och du registrerar den här lösningen uppdaterar du *proxyn. conf* -behörighet för att ge behörigheten omiuser Group Läs behörighet för filen genom att använda följande kommandon:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nyligen tillagda Linux-agenter visar statusen **uppdaterad** när en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

För att bekräfta att en Operations Manager hanterings grupp kommunicerar med Azure Monitor loggar, se [verifiera Operations Manager integrering med Azure Monitor loggar](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om system uppdateringar från Windows-agenter och initierar sedan installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om system uppdateringar från Linux-agenter och initierar sedan installationen av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br/>En direkt anslutning från Operations Manager agent till Azure Monitor loggar krävs inte. Data vidarebefordras från hanteringsgruppen till Log Analytics-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

En sökning utförs två gånger per dag för varje hanterad Windows-dator. Var 15: e minut anropas Windows API för att fråga efter den senaste uppdaterings tiden för att fastställa om statusen har ändrats. Om statusen har ändrats initieras en kompatibilitetskontroll.

En sökning utförs varje timme för varje hanterad Linux-dator.

Det kan ta mellan 30 minuter och 6 timmar för instrument panelen att Visa uppdaterade data från hanterade datorer.

Genomsnitts Azure Monitor loggar data användningen för en dator som använder Uppdateringshantering är cirka 25MB per månad. Det här värdet är bara en uppskattning och kan komma att ändras baserat på din miljö. Vi rekommenderar att du övervakar din miljö för att se den exakta användningen som du har.

## <a name="viewing-update-assessments"></a>Visa uppdaterings utvärderingar

I ditt Automation-konto väljer du **uppdateringshantering** för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer, uppdateringar som saknas, uppdaterings distributioner och schemalagda uppdaterings distributioner. I **kolumnen efterlevnad**kan du se den senaste gången datorn utvärderades. I kolumnen **Uppdatera agent beredskap** kan du se om hälso tillståndet för uppdaterings agenten. Om det uppstår ett problem väljer du länken för att gå till fel söknings dokumentation som kan hjälpa dig att lära dig vilka steg du bör vidta för att åtgärda problemet.

Om du vill köra en loggs ökning som returnerar information om datorn, uppdateringen eller distributionen väljer du objektet i listan. Fönstret **loggs ökning** öppnas med en fråga för det valda objektet:

![Uppdateringshantering standardvy](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Installera uppdateringar

När uppdateringar har utvärderats för alla Linux-och Windows-datorer i din arbets yta kan du installera nödvändiga uppdateringar genom att skapa en *uppdaterings distribution*. Om du vill skapa en uppdaterings distribution måste du ha Skriv behörighet till Automation-kontot och skriv åtkomst till de virtuella Azure-datorer som är mål i distributionen. En uppdaterings distribution är en schemalagd installation av nödvändiga uppdateringar för en eller flera datorer. Du anger datum och tid för distributionen och en dator eller grupp av datorer som ska inkluderas i omfånget för en distribution. Mer information om dator grupper finns [i dator grupper i Azure Monitor loggar](../azure-monitor/platform/computer-groups.md).

När du inkluderar dator grupper i din uppdaterings distribution utvärderas grupp medlemskap bara en gång, vid tidpunkten för skapandet av schemat. Efterföljande ändringar i en grupp visas inte. För att komma runt detta använda [dynamiska grupper](#using-dynamic-groups)löses dessa grupper vid distributions tiden och definieras av en fråga för virtuella Azure-datorer eller en sparad sökning för virtuella datorer som inte är Azure-datorer.

> [!NOTE]
> Virtuella Windows-datorer som distribueras från Azure Marketplace som standard är inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det här beteendet ändras inte när du lägger till den här lösningen eller lägger till virtuella Windows-datorer i din arbets yta. Om du inte aktivt hanterar uppdateringar med hjälp av den här lösningen gäller standard beteendet (för att tillämpa uppdateringar automatiskt).

Om du vill undvika att uppdateringar tillämpas utanför en underhålls period på Ubuntu konfigurerar du om det obevakade uppgraderings paketet så att automatiska uppdateringar inaktive ras. Information om hur du konfigurerar paketet finns i [avsnittet automatiska uppdateringar i Ubuntu Server guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Virtuella datorer som har skapats från RHEL-avbildningar (på begäran Red Hat Enterprise Linux) som är tillgängliga på Azure Marketplace är registrerade för att få åtkomst till Red Hat-( [RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) som har distribuerats i Azure. Alla andra Linux-distributioner måste uppdateras från distributionens online-fillagringsplats genom att följa distributionens metoder som stöds.

Om du vill skapa en ny uppdaterings distribution väljer du **Schemalägg uppdaterings distribution**. Sidan **ny uppdaterings distribution** öppnas. Ange värden för egenskaperna som beskrivs i följande tabell och klicka sedan på **skapa**:

| Egenskap | Description |
| --- | --- |
| Name |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Linux eller Windows|
| Grupper att uppdatera |För Azure-datorer definierar du en fråga baserat på en kombination av prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. </br></br>För datorer som inte är Azure-datorer väljer du en befintlig sparad sökning för att välja en grupp datorer som inte är Azure-datorer att inkludera i distributionen. </br></br>Mer information finns i [Dynamiska grupper](automation-update-management.md#using-dynamic-groups)|
| Datorer som ska uppdateras |Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md) |
|Uppdatera klassificeringar|Välj alla uppdaterings klassificeringar som du behöver|
|Inkludera/exkludera uppdateringar|Då öppnas sidan **Inkludera/exkludera** . Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik. Mer information om hur inkludering hanteras och finns i [inkluderingsbeteende](automation-update-management.md#inclusion-behavior) |
|Schemainställningar|Välj tid för start och välj antingen en gång eller återkommande för upprepningen|
| Före skript + efter skript|Välj de skript som ska köras före och efter distributionen|
| Underhållsperiod |Antal minuter som har angetts för uppdateringar. Värdet kan inte vara mindre än 30 minuter och högst 6 timmar |
| Starta om kontroll| Fastställer hur omstarter ska hanteras. De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Uppdaterings distributioner kan också skapas program mässigt. Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempel-Runbook som kan användas för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="maintenance-windows"></a>Underhålls fönster

Windows-underhåll styr den tids period som tillåts för att installera uppdateringar. Tänk på följande när du anger en underhålls period.

* Underhålls fönster styr hur många uppdateringar som försöker installeras.
* Uppdateringshantering slutar inte att installera nya uppdateringar om slutet av en underhålls period närmar sig.
* Uppdateringshantering avslutar inte pågående uppdateringar om underhålls perioden har överskridits.
* Om underhålls perioden överskrids i Windows beror det ofta på att en service pack uppdatering tar lång tid att installera.

### <a name="multi-tenant"></a>Uppdaterings distributioner mellan klienter

Om du har datorer i en annan Azure-klient rapporterar att Uppdateringshantering att du behöver korrigera, måste du använda följande lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med växeln `-ForUpdate` för att skapa ett schema och använda [cmdleten New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna i den andra klient till `-NonAzureComputer` parametern. I följande exempel visas ett exempel på hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Visa saknade uppdateringar

Välj **saknade uppdateringar** om du vill visa en lista med uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdateringen, operativ systemet och en länk för mer information visas. I fönstret **loggs ökning** visas mer information om uppdateringarna.

## <a name="view-update-deployments"></a>Visa uppdaterings distributioner

Välj fliken **uppdaterings distributioner** om du vill visa en lista över befintliga uppdaterings distributioner. Välj någon av uppdaterings distributionerna i tabellen för att öppna körnings fönstret för **uppdaterings distribution** för den uppdaterings distributionen. Jobb loggar lagras i högst 30 dagar.

![Översikt över resultat av uppdaterings distribution](./media/automation-update-management/update-deployment-run.png)

Om du vill visa en uppdaterings distribution från REST API, se [konfiguration av program uppdaterings konfiguration](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Uppdatera klassificeringar

I följande tabeller visas uppdaterings klassificeringarna i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett särskilt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service Pack     | En kumulativ uppsättning snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus-eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som är installerad för närvarande.        |

### <a name="linux-2"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska i natur eller inte är säkerhets uppdateringar.        |

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet och Visa utvärderings data på grund av data berikning i molnet. Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i rutan. Om du har CentOS-datorer som har kon figurer ATS på ett sätt för att returnera säkerhets data för följande kommando, kommer Uppdateringshantering kunna korrigeras baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För närvarande tillhandahålls kunder som kan ha aktiverat detta på egen hand.

## <a name="firstparty-predownload"></a>Avancerade inställningar

Uppdateringshantering förlitar sig på Windows Update för att ladda ned och installera Windows-uppdateringar. Därför respekterar vi många av de inställningar som används av Windows Update. Om du använder inställningar för att aktivera uppdateringar som inte kommer från Windows hanterar Uppdateringshantering även dessa uppdateringar. Om du vill aktivera nedladdning av uppdateringar innan en uppdaterings distribution sker, kan uppdaterings distributioner gå snabbare och mindre troligt vis överskrida underhålls perioden.

### <a name="pre-download-updates"></a>För hämtnings uppdateringar

Om du vill konfigurera automatisk nedladdning av uppdateringar i grupprincip kan du ange [inställningen konfigurera automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) till **3**. Detta laddar ned de uppdateringar som krävs i bakgrunden, men installerar dem inte. Detta behåller Uppdateringshantering kontroll över scheman, men tillåter att uppdateringar laddas ned utanför Uppdateringshantering underhålls fönstret. Detta kan förhindra att **underhålls fönstret har överskridit** fel i uppdateringshantering.

Du kan också ange detta med PowerShell och köra följande PowerShell på ett system som du vill hämta uppdateringar automatiskt.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Inaktivera automatisk installation

Virtuella Azure-datorer har automatisk installation av uppdateringar som är aktiverade som standard. Detta kan medföra att uppdateringar installeras innan du schemalägger dem att installeras av Uppdateringshantering. Du kan inaktivera det här beteendet genom `NoAutoUpdate` att ange register `1`nyckeln till. Följande PowerShell-kodfragment visar ett sätt att göra detta.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Som standard tillhandahåller Windows Update endast uppdateringar för Windows. Om du aktiverar **ge mig uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows**, får du uppdateringar för andra produkter, inklusive säkerhets korrigeringar för SQL Server eller andra program från den första parten. Det här alternativet kan inte konfigureras av grupprincip. Kör följande PowerShell på de system som du vill aktivera för andra uppdateringar från första part och Uppdateringshantering kommer att använda den här inställningen.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Uppdateringar från tredje part på Windows

Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Detta är antingen WSUS eller Windows Update. Med verktyg [som](/sccm/sum/tools/updates-publisher
) System Center Updates Publisher (Updates Publisher) kan du publicera anpassade uppdateringar i WSUS. Med det här scenariot kan Uppdateringshantering korrigera datorer som använder System Center Configuration Manager som sitt uppdaterings lager med program vara från tredje part. Information om hur du konfigurerar Updates Publisher finns i [Installera Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Nätverks planering

Följande adresser krävs specifikt för Uppdateringshantering. Kommunikationen med de här adresserna sker via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

För Windows-datorer måste du också tillåta trafik till alla slut punkter som krävs av Windows Update.  Du hittar en uppdaterad lista med nödvändiga slut punkter i [problem som rör http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Om du har en lokal [Windows Update-Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)måste du också tillåta trafik till servern som anges i [WSUS-nyckeln](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

För Red Hat Linux-datorer kan du se [IP-adresserna för RHUI Content Delivery servers](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) för obligatoriska slut punkter. Andra Linux-distributioner finns i leverantörens dokumentation.

Mer information om portar som Hybrid Runbook Worker kräver finns i [hybrid Worker roll portar](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Vi rekommenderar att du använder de adresser som anges när du definierar undantag. För IP-adresser kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och återspeglar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen.

Följ anvisningarna i [ansluta datorer utan Internet åtkomst](../azure-monitor/platform/gateway.md) för att konfigurera datorer som inte har till gång till Internet.

## <a name="search-logs"></a>Sök i loggar

Utöver den information som finns i Azure Portal kan du söka efter loggarna. På lösnings sidorna väljer du **Log Analytics**. Fönstret **loggs ökning** öppnas.

Du kan också lära dig hur du anpassar frågorna eller använder dem från olika klienter och mer genom att besöka:  [Log Analytics Search API-](
https://dev.loganalytics.io/)dokumentation.

### <a name="sample-queries"></a>Exempelfrågor

Följande avsnitt innehåller exempel på logg frågor för uppdaterings poster som samlas in av den här lösningen:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Enskilda utvärderings frågor för Azure VM (Windows)

Ersätt VMUUID-värdet med VM-GUID för den virtuella dator som du frågar. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Azure Monitor loggar:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

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

#### <a name="single-azure-vm-assessment-queries-linux"></a>Enkla Azure VM Assessment-frågor (Linux)

För vissa Linux-distributioner, finns det ett värde för [endian](https://en.wikipedia.org/wiki/Endianness) -fel med VMUUID-värdet som kommer från Azure Resource Manager och vad som lagras i Azure Monitor loggar. Följande fråga söker efter en matchning på antingen endian. Ersätt VMUUID-värdena med big-endian-och lite-endian-formatet för GUID för att returnera resultaten korrekt. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Azure Monitor loggar:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

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

#### <a name="multi-vm-assessment-queries"></a>Frågor om utvärdering av flera virtuella datorer

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
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

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
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>Lista med datorer

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

##### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

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

## <a name="using-dynamic-groups"></a>Använda dynamiska grupper

Uppdateringshantering ger möjlighet att rikta en dynamisk grupp med virtuella Azure-datorer eller icke-Azure-datorer för uppdaterings distributioner. Dessa grupper utvärderas vid distributions tiden så att du inte behöver redigera distributionen för att lägga till datorer.

> [!NOTE]
> Du måste ha rätt behörighet när du skapar en uppdaterings distribution. Mer information finns i [Installera uppdateringar](#install-updates).

### <a name="azure-machines"></a>Azure-datorer

Dessa grupper definieras av en fråga, när en uppdaterings distribution börjar, utvärderas medlemmarna i gruppen. Dynamiska grupper fungerar inte med klassiska virtuella datorer. När du definierar din fråga kan följande objekt användas tillsammans för att fylla i den dynamiska gruppen

* Subscription
* Resursgrupper
* Platser
* Tags

![Välj grupper](./media/automation-update-management/select-groups.png)

Om du vill förhandsgranska resultatet av en dynamisk grupp klickar du på knappen **Förhandsgranska** . Den här för hands versionen visar grupp medlemskapet vid denna tidpunkt, i det här exemplet söker vi efter datorer med tag- **rollen** är lika med **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner för den gruppen.

![för hands versions grupper](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Datorer som inte är Azure-datorer

För datorer som inte är Azure-datorer används sparade sökningar även som dator grupper för att skapa den dynamiska gruppen. Information om hur du skapar en sparad sökning finns i [skapa en dator grupp](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När gruppen har skapats kan du välja den i listan över sparade sökningar. Klicka på **Förhandsgranska** för att förhandsgranska datorerna i den sparade sökningen vid detta tillfälle.

![Välj grupper](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrera med System Center Configuration Manager

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av styrka och mognad för Configuration Manager för att hjälpa dem att hantera program uppdateringar. Configuration Manager ingår i sin program uppdaterings hanterings cykel (SUM).

Information om hur du integrerar hanterings lösningen med System Center Configuration Manager finns i [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Inkluderings beteende

Med uppdaterings inkludering kan du ange vilka uppdateringar som ska tillämpas. Korrigeringar eller paket som ingår är installerade. När korrigeringar eller paket ingår och en klassificering har valts, installeras även de objekt och objekt som ingår i klassificeringen.

Det är viktigt att veta att undantagen åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*`, installeras inga korrigeringar eller paket eftersom de undantas. Undantagna uppdateringar visas fortfarande som saknas på datorn. För Linux-datorer om ett paket ingår men har ett beroende paket som uteslutits, installeras inte paketet.

## <a name="patch-linux-machines"></a>Korrigera Linux-datorer

I följande avsnitt beskrivs eventuella problem med Linux-korrigeringar.

### <a name="unexpected-os-level-upgrades"></a>Oväntade uppgraderingar på operativ Systems nivå

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar av operativ Systems nivå ske via paket. Detta kan leda till att Uppdateringshantering körs där versions numret för operativ systemet ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på Linux-datorn, är detta avsiktligt avsiktligt.

Använd undantags funktionen om du vill undvika att uppdatera operativ system versionen via uppdateringshantering körs.

I Red Hat Enterprise Linux är paket namnet som ska undantas RedHat-release-Server. x86_64.

![Paket som ska undantas för Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritiska/säkerhets uppdateringar tillämpas inte

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdaterings klassificeringar. Detta filtrerar de uppdateringar som tillämpas på den dator som uppfyller de angivna kriterierna. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdaterings berikning i molnet, kan vissa uppdateringar flaggas i Uppdateringshantering som en säkerhets påverkan, även om den lokala datorn inte har den informationen. Det innebär att om du använder kritiska uppdateringar på en Linux-dator kan det finnas uppdateringar som inte har marker ATS som säkerhets påverkan på den datorn och uppdateringarna tillämpas inte.

Uppdateringshantering kan dock fortfarande rapportera datorn som icke-kompatibel eftersom den innehåller ytterligare information om den relevanta uppdateringen.

Distribution av uppdateringar efter uppdaterings klassificering fungerar inte på CentOS från lådan. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att säkerställa att uppdateringarna tillämpas. För SUSE, väljer du *endast* "andra uppdateringar" eftersom klassificeringen kan leda till att vissa säkerhets uppdateringar också installeras om säkerhets uppdateringar som rör zypper (paket hanteraren) eller dess beroenden krävs först. Det här beteendet är en begränsning i zypper. I vissa fall kan du behöva köra uppdaterings distributionen igen. Verifiera uppdaterings loggen genom att kontrol lera uppdaterings loggen.

## <a name="remove-a-vm-from-update-management"></a>Ta bort en virtuell dator från Uppdateringshantering

Så här tar du bort en virtuell dator från Uppdateringshantering:

* I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen för omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates`. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
* Ta bort [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [Log Analytics agent för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudien för att lära dig hur du hanterar uppdateringar för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)

* Använd loggs ökningar i [Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) om du vill visa detaljerade uppdaterings data.
* [Skapa aviseringar](automation-tutorial-update-management.md#configure-alerts) för status för uppdaterings distribution.

* Information om hur du interagerar med Uppdateringshantering via REST API finns i [konfigurationer för program uppdatering](/rest/api/automation/softwareupdateconfigurations)
* Information om hur du felsöker Uppdateringshantering finns i [fel sökning uppdateringshantering](troubleshoot/update-management.md)
