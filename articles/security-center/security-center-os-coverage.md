---
title: Plattformar som stöds av Azure Security Center | Microsoft-dokument
description: Det här dokumentet innehåller en lista över plattformar som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521920"
---
# <a name="supported-platforms"></a>Plattformar som stöds 

På den här sidan visas de plattformar och miljöer som stöds av Azure Security Center.

## <a name="combinations-of-environments"></a>Kombinationer av miljöer<a name="vm-server"></a>

Azure Security Center stöder virtuella datorer och servrar i olika typer av hybridmiljöer:

* Endast Azure
* Azure och lokalt
* Azure och andra moln
* Azure, andra moln och lokalt

För en Azure-miljö som aktiveras på en Azure-prenumeration identifierar Azure Security Center automatiskt IaaS-resurser som distribueras inom prenumerationen.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Säkerhetscenter är beroende av [Logganalysagenten](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Se till att dina datorer kör ett av de operativsystem som stöds för den här agenten enligt beskrivningen på följande sidor:

* [Log Analytics-agent för operativsystem som stöds av Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Log Analytics-agent för operativsystem som stöds av Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Kontrollera också att din Log Analytics-agent är [korrekt konfigurerad för att skicka data till Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Mer information om de specifika Security Center-funktioner som är tillgängliga i Windows och Linux finns i [Funktionstäckning för datorer](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Hanterade tjänster för virtuella datorer<a name="virtual-machine"></a>

Virtuella datorer skapas också i en kundprenumeration som en del av vissa Azure-hanterade tjänster, till exempel Azure Kubernetes (AKS), Azure Databricks med mera. Security Center identifierar även dessa virtuella datorer och Log Analytics-agenten kan installeras och konfigureras om ett operativsystem som stöds är tillgängligt.

## <a name="cloud-services"></a>Molntjänster<a name="cloud-services"></a>

Virtuella datorer som körs i en molntjänst stöds också. Endast molntjänsters webb- och arbetsroller som körs i produktionsplatser övervakas. Mer information om molntjänster finns i [Översikt över Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Skydd för virtuella datorer som finns i Azure Stack stöds också. Mer information om Security Centers integrering med Azure Stack finns [i Onboard dina virtuella Azure Stack-datorer till Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Nästa steg

- Läs om hur [Security Center samlar in data med log analytics-agenten](security-center-enable-data-collection.md).
- Läs om hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig hur du [planerar och förstår designövervägandena om att anta Azure Security Center](security-center-planning-and-operations-guide.md).