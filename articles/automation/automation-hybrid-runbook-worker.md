---
title: Översikt över Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller en översikt över Hybrid Runbook Worker som du kan använda för att köra Runbooks på datorer i ditt lokala data Center eller en moln leverantör.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: f555d603756159dd108345260d71233c23c01ce5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715723"
---
# <a name="hybrid-runbook-worker-overview"></a>Översikt över Hybrid Runbook Worker

Runbooks i Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azures moln plattform. Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer.

Följande bild visar den här funktionen:

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

En Hybrid Runbook Worker kan köra antingen Windows eller Linux-operativsystemet. För övervakning kräver det att Azure Monitor och en Log Analytics-Agent används för det operativ system som stöds. Mer information finns i [Azure Monitor](automation-runbook-execution.md#azure-monitor).

Varje Hybrid Runbook Worker är medlem i en Hybrid Runbook Worker grupp som du anger när du installerar agenten. En grupp kan innehålla en enda agent, men du kan installera flera agenter i en grupp för hög tillgänglighet. Varje dator kan vara värd för en hybrid Worker-rapportering till ett Automation-konto. 

När du startar en Runbook på en Hybrid Runbook Worker anger du den grupp som den körs på. Varje anställd i gruppen avsöker Azure Automation för att se om några jobb är tillgängliga. Om ett jobb är tillgängligt tar den första arbets tagaren att hämta jobbet. Bearbetnings tiden för jobb kön beror på maskin varu profilen för Hybrid Worker och belastningen. Du kan inte ange en viss arbetare. 

Använd en Hybrid Runbook Worker i stället för ett [Azure-sandbox](automation-runbook-execution.md#runbook-execution-environment) eftersom det inte har många av de begränsade [begränsningarna](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) för disk utrymme, minne eller nätverks platser. Gränserna för en hybrid arbetare är bara relaterade till arbetarnas egna resurser. 

> [!NOTE]
> Hybrid Runbook Worker begränsas inte av den [verkliga delnings](automation-runbook-execution.md#fair-share) tiden som Azure-sandbox har. 

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker installation

Processen för att installera en Hybrid Runbook Worker är beroende av operativ systemet. I tabellen nedan definieras distributions typerna.

|Operativsystem  |Distributions typer  |
|---------|---------|
|Windows     | [Automatiserad](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Den rekommenderade installations metoden är att använda en Azure Automation Runbook för att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden är att följa en steg-för-steg-procedur för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn.

## <a name="network-planning"></a><a name="network-planning"></a>Planera för nätverk

För att Hybrid Runbook Worker ska kunna ansluta till och registrera med Azure Automation måste den ha åtkomst till port numret och URL-adresserna som beskrivs i det här avsnittet. Arbets tagaren måste också ha åtkomst till de [portar och URL: er som krävs för att Log Analytics agenten](../azure-monitor/platform/agent-windows.md) ska kunna ansluta till Azure Monitor Log Analytics-arbetsytan.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Följande port och URL-adresser krävs för Hybrid Runbook Worker:

* Port: endast TCP 443 krävs för utgående Internet åtkomst
* Global URL: *. azure-automation.net
* Global URL för US Gov, Virginia: –*.azure-automation.us
* Agent tjänst: https:// \< workspaceId \> . agentsvc.Azure-Automation.net

Vi rekommenderar att du använder de adresser som anges när du definierar [undantag](automation-runbook-execution.md#exceptions). För IP-adresser kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar i IP-intervallen.

### <a name="dns-records-per-region"></a>DNS-poster per region

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa Hybrid Runbook Worker kommunikation till det regionala data centret. Följande tabell innehåller DNS-posten för varje region.

| **Region** | **DNS-post** |
| --- | --- |
| Australien, centrala |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australien, östra |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Kanada, centrala |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japan, östra |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa, norra |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| USA, södra centrala |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA, västra centrala | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa, västra |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA, västra 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Om du vill ha en lista över regions-IP-adresser i stället för region namn laddar du ned XML-filen för [Azure datacenter-IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) från Microsoft Download Center En uppdaterad IP-serveradress har publicerats varje vecka. 

IP-adress filen visar de IP-adressintervall som används i Microsoft Azure Data Center. Den innehåller beräknings-, SQL-och lagrings intervall och återspeglar för närvarande distribuerade intervall och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka.

Det är en bra idé att ladda ned den nya IP-adressen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. 

> [!NOTE]
> Om du använder Azure ExpressRoute måste du komma ihåg att IP-adressutrymmet används för att uppdatera Border Gateway Protocol-annonsering (BGP) för Azure-utrymmet under den första veckan i varje månad.

### <a name="proxy-server-use"></a>Använd proxyserver

Om du använder en proxyserver för kommunikation mellan Azure Automation och Log Analytics-agenten måste du se till att lämpliga resurser är tillgängliga. Tids gränsen för begär Anden från Hybrid Runbook Worker-och automation-tjänsterna är 30 sekunder. Efter tre försök Miss lyckas en begäran. 

### <a name="firewall-use"></a>Brand Väggs användning

Om du använder en brand vägg för att begränsa åtkomsten till Internet, måste du konfigurera brand väggen för att tillåta åtkomst. Om du använder Log Analytics gateway som proxy kontrollerar du att den har kon figurer ATS för Hybrid Runbook Worker. Se [konfigurera Log Analytics Gateway för Automation hybrid Worker](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Uppdateringshantering på Hybrid Runbook Worker

När Azure Automation [uppdateringshantering](automation-update-management.md) är aktive rad konfigureras alla datorer som är anslutna till din Log Analytics-arbetsyta automatiskt som en hybrid Runbook Worker. Varje arbets tagare kan stödja Runbooks riktade mot hantering av uppdateringar. 

En dator som kon figurer ATS på det här sättet är inte registrerad med några Hybrid Runbook Worker grupper som redan har definierats i ditt Automation-konto. Du kan lägga till datorn i en Hybrid Runbook Worker grupp, men du måste använda samma konto för både Uppdateringshantering och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Uppdateringshantering adresser för Hybrid Runbook Worker

Utöver de standard adresser och portar som krävs i Hybrid Runbook Worker behöver Uppdateringshantering adresserna i nästa tabell. Kommunikationen med de här adresserna använder port 443.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation tillstånds konfiguration på en Hybrid Runbook Worker

Du kan köra [Azure Automation tillstånds konfiguration](automation-dsc-overview.md) på en hybrid Runbook Worker. Om du vill hantera konfigurationen av servrar som stöder Hybrid Runbook Worker måste du lägga till servrarna som DSC-noder. Se [Aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks på en Hybrid Runbook Worker

Du kan ha Runbooks som hanterar resurser på den lokala datorn eller köra mot resurser i den lokala miljön där en Hybrid Runbook Worker distribueras. I så fall kan du välja att köra dina runbooks på Hybrid Worker i stället för i ett Automation-konto. Runbooks som körs på en Hybrid Runbook Worker är identiska i strukturen för de som du kör i Automation-kontot. Se [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker jobb

Hybrid Runbook Worker jobb körs under det lokala **system** kontot på Windows eller [nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) i Linux. Azure Automation hanterar jobb i hybrid Runbook Worker något annorlunda än jobb som körs i Azure-sandbox. Se [körnings miljö för Runbook](automation-runbook-execution.md#runbook-execution-environment).

Om den Hybrid Runbook Worker värddatorn startar om, körs pågående Runbook-jobb från början eller från den senaste kontroll punkten för PowerShell Workflow-Runbooks. När ett Runbook-jobb har startats om fler än tre gånger pausas det.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-behörigheter för en Hybrid Runbook Worker

Eftersom de använder icke-Azure-resurser kan Runbooks som körs på en Hybrid Runbook Worker inte använda den autentiseringsmekanism som vanligt vis används av Runbooks som autentiserar sig för Azure-resurser. En Runbook ger antingen sin egen autentisering till lokala resurser eller konfigurerar autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Du kan också ange ett Kör som-konto för att tillhandahålla en användar kontext för alla Runbooks.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Information om hur du felsöker dina hybrid Runbook Worker finns i [Felsöka hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).