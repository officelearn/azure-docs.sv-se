---
title: Livs cykel hantering av virtuella Azure VMware-lösningar
description: Lär dig att hantera alla aspekter av livs cykeln för dina virtuella Azure VMware-lösningar med Microsoft Azure inbyggda verktyg.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 20948ec088d11468b5750ca89979050965246b58
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664738"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Livs cykel hantering av virtuella Azure VMware-lösningar

Med Microsoft Azure inbyggda verktyg kan du övervaka och hantera dina virtuella datorer (VM) i Azure-miljön. Du kan också övervaka och hantera dina virtuella datorer på Azure VMware-lösningen och dina lokala virtuella datorer. I den här översikten ska vi titta på Azure-erbjudanden för integrerad övervakning och hur du kan använda dess inbyggda verktyg för att hantera virtuella datorer i Azure VMware-lösningen under hela livs cykeln.

## <a name="benefits"></a>Fördelar

- Inbyggda Azure-tjänster kan användas för att hantera dina virtuella datorer i en hybrid miljö (Azure, Azure VMware-lösning och lokalt).
- Integrerad övervakning och visning av din Azure, Azure VMware-lösning och lokala virtuella datorer.
- Med Azure Uppdateringshantering i Azure Automation kan du hantera uppdateringar av operativ system för både dina Windows-och Linux-datorer. 
- Azure Security Center tillhandahåller Avancerat hot skydd, inklusive:
    - Övervakning av filintegritet
    - Filbaserad säkerhets aviseringar
    - Utvärdering av operativ Systems korrigering
    - Utvärdering av felkonfigurationer för säkerhet
    - Endpoint Protection-utvärdering 
- Distribuera enkelt Microsoft Monitoring Agent (MMA) med Azure ARC för nya virtuella datorer. 
- Din Log Analytics arbets yta i Azure Monitor möjliggör insamling av logg samling och prestanda räknare med MMA eller tillägg. Samla in data och loggar till en enda punkt och presentera dessa data till olika Azure Native Services. 
- Ytterligare fördelar med Azure Monitor är: 
    - Sömlös övervakning 
    - Bättre insyn i infrastrukturen 
    - Snabb meddelanden 
    - Automatisk lösning 
    - Kostnads effektivitet 

## <a name="integrated-azure-monitoring-architecture"></a>Integrerad Azure-övervaknings arkitektur

Följande diagram visar den integrerade övervaknings arkitekturen för virtuella Azure VMware-lösningar.

![Integrerad Azure-övervaknings arkitektur](media/lifecycle-mgmt-avs-vms/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrering och distribution av inbyggda Azure-tjänster

**Azure-bågen** utökar Azure-hanteringen till vilken infrastruktur som helst, inklusive Azure VMware-lösning, lokalt eller andra moln plattformar. Azure-bågen kan distribueras genom att installera ett Azure Kubernetes service-kluster (AKS) i Azure VMwares lösnings miljö. Mer information finns i [ansluta ett Azure Arc-aktiverat Kubernetes-kluster](../azure-arc/kubernetes/connect-cluster.md).

Virtuella Azure VMware-lösningar kan övervakas via MMA (kallas även för Log Analytics agent eller OMS Linux-Agent). MMA kan installeras automatiskt när virtuella datorer tillhandahålls genom ARC-arbetsflöden för VM-livscykler. MMA kan också installeras när du distribuerar virtuella datorer från en mall i vCenter; igen med virtuella datorer som tillhandahålls genom ARC-arbetsflöden. Alla etablerade virtuella datorer i Azure VMware-lösningen kan sedan ha MMA installerade och skicka loggar till Azure Log Analytics-arbetsytan. Mer information finns i [Översikt över Log Analytics agent](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics arbets ytan** möjliggör insamling av logg samling och prestanda räknare med hjälp av MMA eller tillägg. Om du vill skapa din Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Om du vill lägga till virtuella Windows-datorer i Log Analytics-arbetsytan, se [installera Log Analytics agent på Windows-datorer](../azure-monitor/platform/agent-windows.md).
- Information om hur du lägger till virtuella Linux-datorer i Log Analytics-arbetsytan finns i [installera Log Analytics agent på Linux-datorer](../azure-monitor/platform/agent-linux.md).

**Azure uppdateringshantering** i Azure Automation hanterar uppdateringar av operativ system för dina Windows-och Linux-datorer i en hybrid miljö. Den övervakar efterlevnaden av korrigeringen och vidarebefordrar aviseringar om avvikelser till Azure Monitor för reparation. Azure Uppdateringshantering måste ansluta till din Log Analytics arbets yta för att kunna använda lagrade data för att bedöma status för uppdateringar på dina virtuella datorer.
- Om du vill lägga till Log Analytics i Azure Uppdateringshantering måste du först [skapa ett Azure Automation-konto](../automation/automation-create-standalone-account.md).
- Om du vill länka din Log Analytics arbets yta till ditt Automation-konto, se [Log Analytics arbets yta och Automation-konto](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Om du vill aktivera Azure Uppdateringshantering för dina virtuella datorer, se [aktivera uppdateringshantering från ett Automation-konto](../automation/update-management/update-mgmt-enable-automation-account.md).
- När du har lagt till virtuella datorer i Azure Uppdateringshantering kan du [distribuera uppdateringar på virtuella datorer och granska resultaten](../automation/update-management/update-mgmt-deploy-updates.md). 

**Azure Security Center** tillhandahåller Avancerat skydd för dina hybrid arbets belastningar i molnet och lokalt. Det kommer att bedöma sårbarheten hos virtuella datorer i Azure VMware-lösningen och utlösa aviseringar efter behov. Dessa säkerhets aviseringar kan vidarebefordras till Azure Monitor för lösning.
- Azure Security Center behöver inte distribueras. Mer information finns i en lista över [funktioner som stöds för virtuella datorer](../security-center/security-center-services.md).
- Om du vill lägga till virtuella datorer i Azure VMware och icke-Azure-datorer i Azure Security Center kan du läsa [Windows-datorer för att Azure Security Center](../security-center/quick-onboard-windows-computer.md) och [publicera Linux-datorer till Azure Security Center](../security-center/quick-onboard-linux-computer.md).
- När du har lagt till virtuella datorer analyserar Azure Security Center säkerhets status för resurserna för att identifiera potentiella sårbarheter. Den innehåller också rekommendationer på fliken Översikt. Mer information finns [i säkerhets rekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).
- Du kan definiera säkerhets principer i Azure Security Center. Information om hur du konfigurerar dina säkerhets principer finns i [arbeta med säkerhets principer](../security-center/tutorial-security-policy.md).

**Azure Monitor** är en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Den kräver ingen distribution.
- Med Azure Monitor kan du samla in data från olika källor för att övervaka och analysera. Mer information finns i [källor för övervaknings data för Azure Monitor](../azure-monitor/platform/data-sources.md).
- Du kan också samla in olika typer av data för analys, visualisering och aviseringar. Mer information finns i [Azure Monitor data plattform](../azure-monitor/platform/data-platform.md).
- Om du vill konfigurera Azure Monitor med din Log Analytics arbets yta, se [konfigurera Log Analytics arbets yta för Azure Monitor for VMS](../azure-monitor/insights/vminsights-configure-workspace.md).
- Du kan skapa aviserings regler för att identifiera problem i miljön, t. ex. hög användning av resurser, saknade korrigeringar, ont om disk utrymme och pulsslag för dina virtuella datorer. Du kan också ställa in ett automatiserat svar på identifierade händelser genom att skicka en avisering till ITSM-verktyg (IT Service Management). Aviserings identifiering kan också skickas via e-post. Information om hur du skapar sådana regler finns i:
    - [Skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-metric.md).
    - [Skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-log.md).
    - [Åtgärds regler](../azure-monitor/platform/alerts-action-rules.md) för att ange automatiserade åtgärder och meddelanden.
    - [Anslut Azure till ITSM-verktyg med anslutningsprogram för hantering av IT-tjänster (ITSM)](../azure-monitor/platform/itsmc-overview.md).

**Azure Platform as a Service (PaaS)** är en utvecklings-och distributions miljö i molnet, med resurser som gör det möjligt att leverera molnbaserade program. Du kan till exempel integrera Azure SQL Database med dina virtuella datorer i Azure VMware-lösningen. SQL-aviseringar kan sedan korreleras med Azure VMware-lösningen VM-aviseringar. Anta till exempel att det SQL Database benet i ditt program är inom Azure-PAAS och att webb program nivån för samma program finns på dina virtuella datorer i Azure VMware-lösningen. Databas aviseringar kan sedan korreleras med aviseringar för webb program. Fel sökning är förenklad med en enkel, integrerad visning av Azure, Azure VMware-lösningen och lokala virtuella datorer.
