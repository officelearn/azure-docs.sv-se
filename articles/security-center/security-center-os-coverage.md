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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208832"
---
# <a name="supported-platforms"></a>Plattformar som stöds 

## <a name="virtual-machines--servers"></a>Virtuella maskiner / servrar<a name="vm-server"></a>

Security Center stöder virtuella datorer /servrar i olika typer av hybridmiljöer:

* Endast Azure
* Azure och lokalt
* Azure och andra moln
* Azure, andra moln och lokalt

För en Azure-miljö som aktiveras på en Azure-prenumeration identifierar Azure Security Center automatiskt IaaS-resurser som distribueras inom prenumerationen.

> [!NOTE]
> Om du vill ta emot hela uppsättningen säkerhetsfunktioner måste du ha [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), som används av Azure Security Center, installerat och korrekt [konfigurerat för att skicka data till Azure Security Center](security-center-enable-data-collection.md#manual-agent).

I följande avsnitt listas de serveroperativsystem som stöds där [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), som används av Azure Security Center, kan köras.

### <a name="windows-server-operating-systems"></a>Operativsystem för Windows-servrar<a name="os-windows"></a>

|Operativsystem|Stöds av Azure Security Center|Stöd för integrering med Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Mer information om de funktioner som stöds för Windows-operativsystemen, som anges ovan, finns i [Funktioner för virtuell dator/server som stöds](security-center-services.md#vm-server-features).

### <a name="windows-operating-systems"></a>Windows-operativsystem<a name="os-windows (non-server)"></a>

Azure Security Center integreras med Azure-tjänster för att övervaka och skydda dina Windows-baserade virtuella datorer.

### <a name="linux-operating-systems"></a>Linux-operativsystem<a name="os-linux"></a>

64-bitars

* CentOS 6 och 7
* Amazon Linux 2017.09
* Oracle Linux 6 och Oracle Linux 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS och 18.04 LTS
* SUSE Linux Enterprise Server 12

32-bitars
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS och 16.04 LTS

> [!NOTE]
> Eftersom listan över linux-operativsystem som stöds ständigt förändras, om du föredrar, klicka [här](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) för att visa den senaste listan över versioner som stöds, om det har skett ändringar sedan det här avsnittet senast publicerades.

Mer information om de funktioner som stöds för Linux-operativsystemen, som anges ovan, finns i [funktioner för virtuell maskin/server som stöds](security-center-services.md#vm-server-features).

### <a name="managed-virtual-machine-services"></a>Hanterade tjänster för virtuella datorer<a name="virtual-machine"></a>

Virtuella datorer skapas också i en kundprenumeration som en del av vissa Azure-hanterade tjänster, till exempel Azure Kubernetes (AKS), Azure Databricks med mera. Dessa virtuella datorer identifieras också av Azure Security Center, och logganalysagenten kan installeras och konfigureras enligt de [Windows/Linux-operativsystem](#os-windows)som stöds , som anges ovan.

### <a name="cloud-services"></a>Molntjänster<a name="cloud-services"></a>

Virtuella datorer som körs i en molntjänst stöds också. Endast molntjänsters webb- och arbetsroller som körs i produktionsplatser övervakas. Mer information om molntjänster finns i [Översikt över Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Skydd för virtuella datorer som finns i Azure Stack stöds också. Mer information om Security Centers integrering med Azure Stack finns [i Onboard dina virtuella Azure Stack-datorer till Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Nästa steg

- Läs om hur [Security Center samlar in data och Log Analytics Agent](security-center-enable-data-collection.md).
- Läs om hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig hur du [planerar och förstår designövervägandena om att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Läs mer om [tillgängliga funktioner för de olika molnmiljöerna](security-center-services.md).
- Läs mer om [hotskydd för Windows- och Linux-datorer i Azure Security Center](threat-protection.md#windows-machines).
