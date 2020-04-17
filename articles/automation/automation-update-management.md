---
title: Lösning för uppdateringshantering i Azure
description: I den här artikeln beskrivs hur du använder Azure Update Management-lösningen för att hantera uppdateringar för dina Windows- och Linux-datorer.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: eaba4bf7760e150f2477ee743c797f94784b8506
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535510"
---
# <a name="update-management-solution-in-azure"></a>Lösning för uppdateringshantering i Azure

Du kan använda update management-lösningen i Azure Automation för att hantera operativsystemuppdateringar för dina Windows- och Linux-datorer i Azure, i lokala miljöer och i andra molnmiljöer. Du kan snabbt bedöma statusen för tillgängliga uppdateringar på alla agentdatorer och hantera processen för att installera nödvändiga uppdateringar för servrar.

Du kan aktivera uppdateringshantering för virtuella datorer med hjälp av följande metoder:

- Från ditt [Azure Automation-konto](automation-onboard-solutions-from-automation-account.md) för en eller flera Azure-datorer och manuellt för datorer som inte är Azure.

- För en enda Azure-virtuell dator från sidan för den virtuella datorn i Azure-portalen. Det här scenariot är tillgängligt för [virtuella Linux-](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) och [Windows-datorer.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- För [flera virtuella Azure-datorer](manage-update-multi.md) genom att välja dem från sidan **Virtuella datorer** i Azure-portalen. 

> [!NOTE]
> Lösningen för uppdateringshantering kräver att du länkar en Log Analytics-arbetsyta till ditt Automation-konto. En slutgiltig lista över regioner som stöds finns i [Azure Workspace-mappningar](./how-to/region-mappings.md). Regionmappningarna påverkar inte möjligheten att hantera virtuella datorer i en separat region från ditt Automation-konto.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

En Azure [Resource Manager-mall](automation-update-management-deploy-template.md) är tillgänglig som gör att du kan distribuera lösning för uppdateringshantering till ett nytt eller befintligt Automation-konto och Log Analytics-arbetsyta i din prenumeration.

## <a name="solution-overview"></a>Lösningsöversikt

Datorer som hanteras av Update Management använder följande konfigurationer för att utföra utvärdering och uppdatera distributioner:

* Log Analytics-agent för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Följande diagram illustrerar hur lösningen bedömer och tillämpar säkerhetsuppdateringar på alla anslutna Windows Server- och Linux-datorer på en arbetsyta:

![Processflöde för uppdateringshantering](./media/automation-update-management/update-mgmt-updateworkflow.png)

Du kan använda Uppdateringshantering till att internt registrera datorer i flera prenumerationer hos samma klientorganisation.

När ett paket har släppts tar det 2 till 3 timmar för plåstret att dyka upp för Linux-maskiner för bedömning. För Windows-maskiner tar det 12 till 15 timmar innan plåstret visas för bedömning när den har släppts.

När en dator har slutfört en sökning efter uppdateringsefterlevnad vidarebefordrar agenten informationen i bulk till Azure Monitor-loggar. På en Windows-dator körs regelefterlevnaden var 12:e timme som standard.

Förutom skanningsschemat initieras genomsökningen efter uppdateringsefterlevnad inom 15 minuter efter att Log Analytics-agenten startats om, före installationen av uppdateringen och efter installationen av uppdateringen.

För en Linux-dator utförs regelefterlevnaden varje timme som standard. Om Log Analytics-agenten startas om initieras en efterlevnadssökning inom 15 minuter.

Lösningen rapporterar hur uppdaterad datorn är baserat på vilken källa du är konfigurerad att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS, beroende på när WSUS senast synkroniserades med Microsoft Update, kan resultaten skilja sig från vad Microsoft Update visar. Det här beteendet är detsamma för Linux-datorer som är konfigurerade för att rapportera till en lokal repo i stället för till en offentlig repo.

> [!NOTE]
> För att kunna rapportera till tjänsten på rätt sätt kräver uppdateringshantering att vissa webbadresser och portar aktiveras. Mer information om dessa krav finns i [Nätverksplanering för hybridarbetare](automation-hybrid-runbook-worker.md#network-planning).

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringar genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som *valfria* ingår inte i distributionsomfånget för Windows-datorer. Endast nödvändiga uppdateringar ingår i distributionsomfattningen.

Den schemalagda distributionen definierar vilka måldatorer som får tillämpliga uppdateringar. Det gör det antingen genom att uttryckligen ange vissa datorer eller genom att välja en [datorgrupp](../azure-monitor/platform/computer-groups.md) som baseras på loggsökningar av en viss uppsättning datorer (eller på en [Azure-fråga](automation-update-management-query-logs.md) som dynamiskt väljer virtuella Azure-datorer baserat på angivna villkor). Dessa grupper skiljer sig från [scopekonfiguration](../azure-monitor/insights/solution-targeting.md), som endast används för att avgöra vilka datorer som får hanteringspaket som aktiverar lösningen.

Du anger också ett schema för att godkänna och ange en tidsperiod under vilken uppdateringar kan installeras. Den här perioden kallas underhållsfönstret. En 20-minuters span av underhållsfönstret är reserverad för omstarter, förutsatt att en behövs och du valde lämpligt omstartsalternativ. Om korrigeringen tar längre tid än förväntat och det finns mindre än 20 minuter i underhållsfönstret sker ingen omstart.

Uppdateringar installeras av runbooks i Azure Automation. Du kan inte visa dessa runbooks och de kräver ingen konfiguration. När en uppdateringsdistribution skapas skapas ett schema som startar en huvuduppdateringskörningsbok vid den angivna tiden för de inkluderade datorerna. Huvudkörningsboken startar en underordnad runbook på varje agent för att installera de nödvändiga uppdateringarna.

Vid det datum och den tid som anges i uppdateringsdistributionen kör måldatorerna distributionen parallellt. Före installationen körs en genomsökning för att kontrollera att uppdateringarna fortfarande krävs. Om uppdateringarna inte godkänns i WSUS för WSUS-klientdatorer misslyckas uppdateringsdistributionen.

Det går inte att ha en dator registrerad för uppdateringshantering på mer än en Log Analytics-arbetsyta (även kallad multihoming).

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Klienttyper som stöds

I följande tabell visas de operativsystem som stöds för uppdateringsutvärderingar. Korrigering kräver en Hybrid Runbook Worker. Information om hybridkörningsarbetsbladskrav finns i installationsguiderna för installation av en [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) och en Linux Hybrid [Runbook Worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM och SP1 Standard)| Uppdateringshantering stöder endast utvärderingar för det här operativsystemet, korrigering stöds inte eftersom [Hybrid Runbook Worker](automation-windows-hrw-install.md) inte stöds för Windows Server 2008 R2. |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter kräver åtkomst till en uppdateringsdatabas. Klassificeringsbaserad korrigering `yum` kräver att du returnerar säkerhetsdata som CentOS inte har i sina RTM-versioner. Mer information om klassificeringsbaserad korrigering på CentOS finns i [Uppdatera klassificeringar på Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter kräver åtkomst till en uppdateringsdatabas.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter kräver åtkomst till en uppdateringsdatabas.        |
|Ubuntu 14.04 LTS, 16.04 LTS och 18.04 (x86/x64)      |Linux-agenter kräver åtkomst till en uppdateringsdatabas.         |

> [!NOTE]
> Azure-skalningsuppsättningar för virtuella datorer kan hanteras via uppdateringshantering. Uppdateringshantering fungerar på själva instanserna och inte på basavbildningen. Du måste schemalägga uppdateringarna på ett inkrementellt sätt, så att inte alla VM-instanser uppdateras på en gång.
> Du kan lägga till noder för skalningsuppsättningar för virtuella datorer genom att följa stegen under [Inbyggt en icke-Azure-dator](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Klienttyper som inte stöds

I följande tabell visas operativsystem som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klientoperativsystem (till exempel Windows 7 och Windows 10) stöds inte.        |
|Windows Server 2016 Nano Server     | Stöds inte.       |
|Azure Kubernetes-tjänstnoder | Stöds inte. Använda korrigeringsprocessen som beskrivs i [Tillämpa säkerhets- och kernel-uppdateringar på Linux-noder i Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Klientkrav

Följande information beskriver OS-specifika klientkrav. Mer information finns i [Nätverksplanering](#ports).

#### <a name="windows"></a>Windows

Windows-agenter måste vara konfigurerade för att kommunicera med en WSUS-server, eller så kräver de åtkomst till Microsoft Update.

Du kan använda Uppdateringshantering med Configuration Manager. Mer information om integrationsscenarier finns i [Integrera Configuration Manager med Uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md#configuration). [Log Analytics Windows-agenten](../azure-monitor/platform/agent-windows.md) krävs. Agenten installeras automatiskt om du är ombord på en Azure VM.

Som standard är virtuella Windows-datorer som distribueras från Azure Marketplace inställda på att ta emot automatiska uppdateringar från Windows Update Service. Det här beteendet ändras inte när du lägger till den här lösningen eller lägger till virtuella Windows-datorer på arbetsytan. Om du inte aktivt hanterar uppdateringar med den här lösningen gäller standardbeteendet (för att automatiskt installera uppdateringar).

> [!NOTE]
> En användare kan ändra grupprincipen så att datorns omstarter endast kan utföras av användaren, inte av systemet. Hanterade datorer kan fastna om Uppdateringshantering inte har behörighet att starta om datorn utan manuell interaktion från användaren.
>
> Mer information finns i [Konfigurera grupprincipinställningar för automatiska uppdateringar](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

För Linux kräver datorn åtkomst till en uppdateringsdatabas. Uppdateringsdatabasen kan vara privat eller offentlig. TLS 1.1 eller TLS 1.2 krävs för att interagera med uppdateringshantering. En Log Analytics-agent för Linux som är konfigurerad för att rapportera till mer än en Log Analytics-arbetsyta stöds inte med den här lösningen. Maskinen måste också ha Python 2.x installerat.

Information om hur du installerar Log Analytics-agenten för Linux och hämta den senaste versionen finns i [Log Analytics-agenten för Linux](../azure-monitor/platform/agent-linux.md). Information om hur du installerar Log Analytics-agenten för Windows finns i [Ansluta Windows-datorer till Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Virtuella datorer som har skapats från de RHEL-avbildningar (Red Hat Enterprise Linux) som är tillgängliga på Azure Marketplace registreras för att komma åt [RHUI (Red Hat Update Infrastructure)](../virtual-machines/workloads/redhat/redhat-rhui.md) som distribueras i Azure. All annan Linux-distribution måste uppdateras från distributionens onlinefilarkiv med hjälp av distributionens metoder som stöds.

## <a name="permissions"></a>Behörigheter

Om du vill skapa och hantera uppdateringsdistributioner behöver du specifika behörigheter. Mer information om dessa behörigheter finns i [Rollbaserad åtkomst – Uppdateringshantering](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Lösningskomponenter

Lösningen består av följande resurser. Dessa resurser läggs automatiskt till i ditt Automation-konto när du aktiverar lösningen. 

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper

När du har aktiverat den här lösningen konfigureras alla Windows-datorer som är direkt anslutna till logganalysarbetsytan automatiskt som en Hybrid Runbook Worker för att stödja runbooks som ingår i den här lösningen.

Varje Windows-dator som hanteras av lösningen visas i fönstret **Hybrid-arbetargrupper** som en **systemhybridarbetsgrupp** för Automation-kontot. Lösningarna använder *namnkonventionen för värdnamn FQDN_GUID.* Du kan inte rikta in dig på dessa grupper med runbooks i ditt konto. Om du försöker misslyckas försöket. Dessa grupper är avsedda att stödja endast den här hanteringslösningen.

Du kan lägga till Windows-datorn i en Hybrid Runbook Worker-grupp i ditt Automation-konto för att stödja Automation-runbooks om du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om hanteringsgruppen systemcenter operations manager är [ansluten till en Log Analytics-arbetsyta](../azure-monitor/platform/om-agents.md)installeras följande hanteringspaket i Operations Manager. Dessa hanteringspaket installeras också på direkt anslutna Windows-datorer när du har lagt till lösningen. Du behöver inte konfigurera eller hantera dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

> [!NOTE]
> Om du har en Hanteringsgrupp för Operations Manager 1807 eller 2019 ansluten till en Log Analytics-arbetsyta med agenter som konfigurerats i hanteringsgruppen för att samla in loggdata måste du åsidosätta följande regel för att hantera dem med uppdateringshantering: Åsidosätt parametern **IsAutoRegistrationEnabled** och ställ in **True** i regeln **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Mer information om hur lösningshanteringspaket uppdateras finns i [Anslut Operations Manager till Azure Monitor-loggar](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> För datorer med Operations Manger-agenten, som ska hanteras fullständigt av Update Management, måste agenten uppdateras till Log Analytics-agenten för Windows eller Linux. Mer information om hur du uppdaterar agenten finns i [Så här uppgraderar du en Operations Manager-agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). I miljöer som använder Operations Manager måste du köra System Center Operations Manager 2012 R2 UR 14 eller senare.

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Windows-agenter och initierar sedan installation av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Linux-agenter och initierar sedan installation av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br/><br/>En direkt anslutning från Operations Manager-agenten till Azure Monitor-loggar krävs inte. Data vidarebefordras från hanteringsgruppen till log analytics-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

En genomsökning utförs två gånger per dag för varje hanterad Windows-dator. Var 15:e minut anropas Windows-API:et för att fråga efter den senaste uppdateringstiden för att avgöra om statusen har ändrats. Om statusen har ändrats initieras en efterlevnadssökning.

En genomsökning utförs varje timme för varje hanterad Linux-maskin.

Det kan ta mellan 30 minuter och 6 timmar för instrumentpanelen att visa uppdaterade data från hanterade datorer.

Den genomsnittliga dataanvändningen av Azure Monitor-loggar för en dator som använder Uppdateringshantering är cirka 25 MB per månad. Det här värdet är bara en approximation och kan komma att ändras, beroende på din miljö. Vi rekommenderar att du övervakar din miljö för att hålla reda på din exakta användning. Mer information om hur du analyserar dataanvändning finns i [Hantera användning och kostnad](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planera för nätverk

Följande adresser krävs specifikt för uppdateringshantering. Kommunikation till dessa adresser sker över port 443.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

För Windows-datorer måste du också tillåta trafik till alla slutpunkter som krävs av Windows Update. Du hittar en uppdaterad lista över nödvändiga slutpunkter i [problem relaterade till HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Om du har en lokal [Windows Update-server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)måste du också tillåta trafik till den server som anges i [WSUS-nyckeln](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

För Red Hat Linux-datorer, se [IPs för RHUI-innehållsleveransservrar](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) för nödvändiga slutpunkter. För andra Linux-distributioner, se din leverantörsdokumentation.

Mer information om portar som krävs för Hybrid Runbook Worker finns i [Hybrid Worker-rollportar](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Vi rekommenderar att du använder adresserna som anges när du definierar undantag. För IP-adresser kan du hämta [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och återspeglar de för närvarande distribuerade intervallen och eventuella kommande ändringar av IP-intervallen.

Följ instruktionerna i [Connect-datorer utan internetuppkoppling](../azure-monitor/platform/gateway.md) för att konfigurera datorer som inte har tillgång till internet.

## <a name="update-classifications"></a>Klassificering av uppdateringar

I följande tabeller visas uppdateringsklassificeringarna i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett specifikt problem som åtgärdar ett kritiskt, icke-säkerhetsrelaterat fel.        |
|Säkerhetsuppdateringar     | En uppdatering för ett produktspecifikt, säkerhetsrelaterat problem.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabbkorrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produktfunktioner som distribueras utanför en produktversion.        |
|Service pack     | En kumulativ uppsättning snabbkorrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virusfiler eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att utföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som för närvarande är installerad.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett specifikt problem eller ett produktspecifikt, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är av avgörande betydelse eller som inte är säkerhetsuppdateringar.        |

För Linux kan uppdateringshantering skilja mellan viktiga uppdateringar och säkerhetsuppdateringar i molnet samtidigt som bedömningsdata visas på grund av dataanrikning i molnet. För korrigering förlitar sig Uppdateringshantering på klassificeringsdata som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte denna information tillgänglig i RTM-versionen. Om du har CentOS-datorer konfigurerade för att returnera säkerhetsdata för följande kommando kan Uppdateringshantering korrigera baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera inbyggt klassificeringsdatatillgänglighet på CentOS. För närvarande ges endast support för bästa möjliga insats till kunder som kan ha aktiverat detta på egen hand. 

Om du vill klassificera uppdateringar på Red Hat Enterprise version 6 måste du installera yum-security-insticksprogrammet. På Red Hat Enterprise Linux 7 är plugin redan en del av yum själv, det finns ingen anledning att installera något. Mer information finns i följande [red hat-kunskapsartikel](https://access.redhat.com/solutions/10021).

## <a name="integrate-with-configuration-manager"></a>Integrera med Configuration Manager

Kunder som har investerat i Microsoft Endpoint Configuration Manager för hantering av datorer, servrar och mobila enheter förlitar sig också på Configuration Manager styrka och mognad för att hjälpa dem att hantera programuppdateringar. Configuration Manager är en del av deras programuppdateringshanteringscykel (SUM).

Mer information om hur du integrerar hanteringslösningen med Configuration Manager finns i [Integrera Configuration Manager med Uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-updates-on-windows"></a>Uppdateringar från tredje part i Windows

Uppdateringshantering är beroende av den lokalt konfigurerade uppdateringsdatabasen för att uppdatera Windows-system som stöds. Detta är antingen WSUS eller Windows Update. Med verktyg som [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) kan du importera och publicera anpassade uppdateringar med WSUS. Det här scenariot gör det möjligt för Uppdateringshantering att uppdatera datorer som använder Configuration Manager som uppdateringsdatabas med programvara från tredje part. Mer information om hur du konfigurerar Uppdateringar Publisher finns i [Installera uppdateringar Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Aktivera uppdateringshantering

För att börja uppdatera system måste du aktivera lösningen för uppdateringshantering. Följande är de rekommenderade och stöds metoder för att ombord på lösningen:

- [Från en virtuell dator](automation-onboard-solutions-from-vm.md)

- [Från att bläddra i flera maskiner](automation-onboard-solutions-from-browse.md)

- [Från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)

- [Med en Azure Automation-runbook](automation-onboard-solutions.md)

- [Med en Azure Resource Manager-mall](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Nästa steg

Granska vanliga [frågor och svar om](automation-faq.md) Azure Automation för att granska vanliga frågor om den här lösningen.
