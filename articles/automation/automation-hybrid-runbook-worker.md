---
title: Översikt över Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller en översikt över Hybrid Runbook Worker som du kan använda för att köra Runbooks på datorer i ditt lokala data Center eller en moln leverantör.
services: automation
ms.subservice: process-automation
ms.date: 09/14/2020
ms.topic: conceptual
ms.openlocfilehash: 3c88d21c6ad17c613c5d708bf697ae8717c9ec91
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075271"
---
# <a name="hybrid-runbook-worker-overview"></a>Översikt över Hybrid Runbook Worker

Runbooks i Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azures moln plattform. Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer.

Följande bild visar den här funktionen:

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

En Hybrid Runbook Worker kan köras antingen på Windows eller Linux-operativsystemet. Det beror på [Log Analytics agent](../azure-monitor/platform/log-analytics-agent.md) som rapporteras till en Azure Monitor [Log Analytics-arbetsyta](../azure-monitor/platform/design-logs-deployment.md). Arbets ytan kan inte bara övervaka datorn för det operativ system som stöds, utan även för att ladda ned de komponenter som krävs för Hybrid Runbook Worker.

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

Den rekommenderade installations metoden är att använda en Azure Automation Runbook för att helt automatisera processen med att konfigurera en Windows-dator. Om det inte är möjligt kan du följa en steg-för-steg-procedur för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn.

## <a name="network-planning"></a><a name="network-planning"></a>Planera för nätverk

För att Hybrid Runbook Worker ska kunna ansluta till och registrera med Azure Automation måste den ha åtkomst till port numret och URL-adresserna som beskrivs i det här avsnittet. Arbets tagaren måste också ha åtkomst till de [portar och URL: er som krävs för att Log Analytics agenten](../azure-monitor/platform/agent-windows.md) ska kunna ansluta till Azure Monitor Log Analytics-arbetsytan.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Följande port och URL-adresser krävs för Hybrid Runbook Worker:

* Port: endast TCP 443 krävs för utgående Internet åtkomst
* Global URL: `*.azure-automation.net`
* Global URL för US Gov, Virginia: `*.azure-automation.us`
* Agent tjänst: `https://<workspaceId>.agentsvc.azure-automation.net`

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa Hybrid Runbook Worker kommunikation till det regionala data centret. Granska [DNS-posterna som används av Azure Automation](how-to/automation-region-dns-records.md) för de DNS-poster som krävs.

### <a name="proxy-server-use"></a>Använd proxyserver

Om du använder en proxyserver för kommunikation mellan Azure Automation och datorer som kör Log Analytics agent, måste du se till att lämpliga resurser är tillgängliga. Tids gränsen för begär Anden från Hybrid Runbook Worker-och automation-tjänsterna är 30 sekunder. Efter tre försök Miss lyckas en begäran.

### <a name="firewall-use"></a>Brand Väggs användning

Om du använder en brand vägg för att begränsa åtkomsten till Internet, måste du konfigurera brand väggen för att tillåta åtkomst. Om du använder Log Analytics gateway som proxy kontrollerar du att den har kon figurer ATS för Hybrid Runbook Worker. Se [konfigurera Log Analytics Gateway för Automation hybrid Runbook Worker](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Tjänsttaggar

Azure Automation stöder service tag-Taggar i Azure Virtual Network, från och med service tag- [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet **GuestAndHybridManagement**  i rätt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för Automation-tjänsten. Den här Service tag-koden har inte stöd för att tillåta mer detaljerad kontroll genom att begränsa IP-intervallen till en speciell region.

Service Tag-numret för Azure Automation-tjänsten tillhandahåller endast IP-adresser som används för följande scenarier:

* Utlös webhookar inifrån ditt virtuella nätverk
* Tillåt hybrid Runbook-arbetare eller tillstånds konfigurations agenter på ditt VNet för att kommunicera med Automation-tjänsten

>[!NOTE]
>Service tag- **GuestAndHybridManagement** har för närvarande inte stöd för körning av Runbook-jobb i en Azure-sandbox, bara direkt på en hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Stöd för påverkan på nivå 5 (IL5)

Azure Automation Hybrid Runbook Worker kan användas i Azure Government för att stödja påverkan på nivå 5 i någon av följande två konfigurationer:

* [Isolerad virtuell dator](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). När de distribueras använder de hela den fysiska värden för den virtuella datorn som tillhandahåller den nödvändiga isolerings nivån som krävs för att stödja IL5-arbetsbelastningar.

* [Dedikerade Azure-värdar](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), som tillhandahåller fysiska servrar som kan vara värdar för en eller flera virtuella datorer, dedikerade till en Azure-prenumeration.

>[!NOTE]
>Beräknings isolering med Hybrid Runbook Worker-rollen är tillgänglig för moln för kommersiella och amerikanska myndigheter i Azure. 

## <a name="update-management-on-hybrid-runbook-worker"></a>Uppdateringshantering på Hybrid Runbook Worker

När Azure Automation [uppdateringshantering](update-management/update-mgmt-overview.md) är aktive rad konfigureras alla datorer som är anslutna till din Log Analytics-arbetsyta automatiskt som en hybrid Runbook Worker. Varje arbets tagare kan stödja Runbooks riktade mot hantering av uppdateringar.

En dator som har kon figurer ATS på det här sättet är inte registrerad med Hybrid Runbook Worker grupper som redan har definierats i ditt Automation-konto. Du kan lägga till datorn i en Hybrid Runbook Worker grupp, men du måste använda samma konto för både Uppdateringshantering och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Uppdateringshantering adresser för Hybrid Runbook Worker

Utöver de standard adresser och portar som krävs för Hybrid Runbook Worker har Uppdateringshantering ytterligare krav på nätverks konfiguration som beskrivs i avsnittet [nätverks planering](update-management/update-mgmt-overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation tillstånds konfiguration på en Hybrid Runbook Worker

Du kan köra [Azure Automation tillstånds konfiguration](automation-dsc-overview.md) på en hybrid Runbook Worker. Om du vill hantera konfigurationen av servrar som stöder Hybrid Runbook Worker måste du lägga till servrarna som DSC-noder. Se [Aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks på en Hybrid Runbook Worker

Du kan ha Runbooks som hanterar resurser på den lokala datorn eller köra mot resurser i den lokala miljön där en Hybrid Runbook Worker distribueras. I så fall kan du välja att köra dina runbooks på Hybrid Worker i stället för i ett Automation-konto. Runbooks som körs på en Hybrid Runbook Worker är identiska i strukturen för de som du kör i Automation-kontot. Se [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker jobb

Hybrid Runbook Worker jobb körs under det lokala **system** kontot på Windows eller [nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) i Linux. Azure Automation hanterar jobb i hybrid Runbook Worker något annorlunda än jobb som körs i Azure-sandbox. Se [körnings miljö för Runbook](automation-runbook-execution.md#runbook-execution-environment).

Om den Hybrid Runbook Worker värddatorn startar om, körs pågående Runbook-jobb från början eller från den senaste kontroll punkten för PowerShell Workflow-Runbooks. När ett Runbook-jobb har startats om fler än tre gånger pausas det.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-behörigheter för en Hybrid Runbook Worker

Eftersom de använder icke-Azure-resurser kan Runbooks som körs på en Hybrid Runbook Worker inte använda den autentiseringsmekanism som vanligt vis används av Runbooks som autentiserar sig för Azure-resurser. En Runbook ger antingen sin egen autentisering till lokala resurser eller konfigurerar autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Du kan också ange ett Kör som-konto för att tillhandahålla en användar kontext för alla Runbooks.

## <a name="view-hybrid-runbook-workers"></a>Visa hybrid Runbook Worker

När Uppdateringshantering funktionen är aktive rad på Windows-servrar eller virtuella datorer kan du inventera listan över system hybrid Runbook Worker-grupper i Azure Portal. Du kan visa upp till 2 000 arbetare i portalen genom att välja fliken **hybrid** arbetare i alternativet hybrid Worker i **gruppen alternativ hybrid arbetare** från den vänstra rutan för det valda Automation-kontot.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Sidan för Hybrid arbets grupper i Automation-konto" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Om du har mer än 2 000 hybrid arbetare kan du köra följande PowerShell-skript för att hämta en lista över alla:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem](troubleshoot/hybrid-runbook-worker.md#general).