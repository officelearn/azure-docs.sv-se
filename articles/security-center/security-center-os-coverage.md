---
title: Plattformar som stöds av Azure Security Center | Microsoft Docs
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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 47e66bfc4550372b571b7263ef5b7f2f149712dc
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307307"
---
# <a name="supported-platforms"></a>Plattformar som stöds 

## Virtuella datorer/servrar<a name="vm-server"></a>

Security Center stöder virtuella datorer/servrar på olika typer av hybrid miljöer:

* Endast Azure
* Azure och lokalt
* Azure och andra moln
* Azure, andra moln och lokalt

För en Azure-miljö aktive rad på en Azure-prenumeration identifierar Azure Security Center automatiskt IaaS-resurser som distribueras i prenumerationen.

> [!NOTE]
> För att få en fullständig uppsättning säkerhetsfunktioner måste du ha Log Analytics- [agenten](../azure-monitor/platform/agents-overview.md#log-analytics-agent), som används av Azure Security Center, installerad och [korrekt konfigurerad för att skicka data till Azure Security Center](security-center-enable-data-collection.md#manual-agent).


I följande avsnitt listas de serveroperativ system som stöds och som [Log Analytics-agenten](../azure-monitor/platform/agents-overview.md#log-analytics-agent), som används av Azure Security Center, kan köras.

### Windows Server-operativsystem<a name="os-windows"></a>

|Operativsystem|Stöds av Azure Security Center|Stöd för integrering med Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Mer information om vilka funktioner som stöds för Windows-operativsystem som anges ovan finns i [funktioner för virtuell dator/server som stöds](security-center-services.md##vm-server-features).

### Linux-operativsystem<a name="os-linux"></a>

64-bitars

* CentOS 6 och 7
* Amazon Linux 2017,09
* Oracle Linux 6 och 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS och 18,04 LTS
* SUSE Linux Enterprise Server 12

32-bitars
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14,04 LTS och 16,04 LTS

> [!NOTE]
> Eftersom listan över Linux-operativsystem som stöds ständigt ändras kan du klicka [här](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) om du vill visa den senaste listan över versioner som stöds, om det har gjorts ändringar sedan det här avsnittet publicerades senast.

Mer information om vilka funktioner som stöds för Linux-operativsystem som anges ovan finns i [funktioner för virtuella datorer/server som stöds](security-center-services.md##vm-server-features).

### Hanterade virtuella maskin tjänster<a name="virtual-machine"></a>

Virtuella datorer skapas också i en kund prenumeration som en del av vissa Azure-hanterade tjänster, till exempel Azure Kubernetes (AKS), Azure Databricks med mera. De här virtuella datorerna identifieras också av Azure Security Center och Log Analytics-agenten kan installeras och konfigureras enligt de [Windows/Linux-operativsystem](#os-windows)som stöds, som anges ovan.

### Cloud Services<a name="cloud-services"></a>

Virtuella datorer som körs i en moln tjänst stöds också. Endast webb-och arbets roller för moln tjänster som körs i produktions platser övervakas. Mer information om moln tjänster finns i [Översikt över Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

## PaaS-tjänster<a name="paas-services"></a>

Följande Azure PaaS-resurser stöds av Azure Security Center:

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Lagringskonto
* Apptjänst
* Funktion
* Molntjänst
* VNet
* Undernät
* NIC
* NSG
* Batch-konto
* Service Fabric-konto
* Automation-konto
* Lastbalanserare
* Search
* service bus namnrymd
* Stream analytics
* Namnområde för händelsehubb
* Logikappar
* Redis
* Data Lake Analytics
* Data Lake Store
* Nyckelvalv

Mer information om vilka funktioner som stöds för ovanstående lista över PaaS-resurser finns i [PaaS Services-funktioner som stöds](security-center-services.md#paas-services).

Skydd för Virtual Machines som finns i Azure Stack stöds också. Mer information om Security Centers integrering med Azure Stack finns i [publicera dina Azure Stack virtuella datorer i Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data och Log Analytics agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig att [planera och förstå design överväganden för att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Lär dig mer om [funktioner som är tillgängliga för de olika moln miljöerna](security-center-services.md).
- Lär dig mer om [hot identifiering för virtuella datorer & servrar i Azure Security Center](security-center-alerts-iaas.md).
- Hitta [vanliga frågor om hur du använder Azure Security Center](security-center-faq.md).
- Hitta [blogg inlägg om säkerhet och efterlevnad i Azure](https://blogs.msdn.com/b/azuresecurity/).
