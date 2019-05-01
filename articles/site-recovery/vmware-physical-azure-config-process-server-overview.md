---
title: Om Azure Site Recovery-konfiguration, process och huvudmålservrar | Microsoft Docs
description: Den här artikeln innehåller en översikt över konfiguration, process och huvudmålservern servrar med hjälp av när du konfigurerar haveriberedskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 78aed4fceab83d942927486cd7ae46a32dd6ca09
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927559"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Om Site Recovery-komponenter (konfiguration, process, huvudmålserver)

Den här artikeln beskrivs den konfiguration, process och huvudmålservrar som används för att replikera virtuella VMware-datorer och fysiska servrar till Azure med den [Site Recovery](site-recovery-overview.md) service.

## <a name="configuration-server"></a>Konfigurationsserver

För haveriberedskap för lokala virtuella VMware-datorer och fysiska servrar, behöver du ett Site Recovery konfigurationsserver distribuerat lokalt.

**Inställning** | **Detaljer** | **Länkar**
--- | --- | ---
**Komponenter**  | Den configuration server-datorn kör alla lokala Site Recovery-komponenter, bland annat konfigurationsservern, processervern och huvudmålservern.<br/><br/> När du ställer in konfigurationsservern installeras alla komponenter automatiskt. | [Läs](vmware-azure-common-questions.md#configuration-server) konfigurationsservern vanliga frågor och svar.
**Roll** | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering. | Mer information om arkitekturen för [VMware](vmware-azure-architecture.md) och [fysisk server](physical-azure-architecture.md) haveriberedskap till Azure.
**VMware-kraven** | För haveriberedskap för lokala virtuella VMware-datorer, måste du installera och kör konfigurationsservern som en lokal, högtillgänglig VMware VM. | [Lär dig mer om](vmware-azure-deploy-configuration-server.md#prerequisites) kraven.
**VMware-distribution** | Vi rekommenderar att du distribuerar konfigurationsservern med hjälp av en nedladdad OVA-mall. Den här metoden ger ett enkelt sätt att konfigurera en konfigurationsserver som uppfyller alla krav och förutsättningar.<br/><br/> Om det inte går att distribuera en VMware-VM med en mall för OVA av någon anledning, kan du ställa in configuration server-datorer manuellt, enligt beskrivningen nedan för katastrofåterställning för fysisk dator. | [Distribuera](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) med en OVA-mall.
**Krav för fysisk server** | För haveriberedskap på lokala fysiska servrar distribuerade konfigurationsservern manuellt. | [Lär dig mer om](/physical-azure-set-up-source.md#prerequisites) kraven.
**Fysisk server-distribution** | Om den inte kan installeras som en VMware-VM, kan du installera den på en fysisk server. | [Distribuera](physical-azure-set-up-source.md#set-up-the-source-environment) konfigurationsservern manuellt.


## <a name="process-server"></a>Processerver

**Inställning** | **Detaljer** | **Länkar**
--- | --- | ---
**Distribution**  | För haveriberedskap och replikeringen av lokala virtuella VMware-datorer och fysiska servrar, behöver du en processerver lokalt. Som standard installeras processervern på konfigurationsservern när du distribuerar den. | [Läs mer](vmware-azure-architecture.md?#architectural-components).
**Rollen (lokalt** | -Tar emot replikeringsdata från datorer som har aktiverats för replikering.<br/> -Optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.<br/> -Utför en push-installation av Site Recovery-Mobilitetstjänsten på den lokala virtuella VMware-datorer och fysiska servrar som du vill replikera.<br/> -Utför automatisk identifiering av lokala datorer. | [Läs mer](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Rollen (återställning efter fel från Azure)** | Efter redundansväxlingen från din lokala plats är som du en processerver i Azure, Azure-dator, hantera återställning efter fel på din lokala plats.<br/><br/> Processerver i Azure är tillfällig. Azure VM kan tas bort när återställningen är klar. | [Läs mer](vmware-azure-set-up-process-server-azure.md).
**Skalning** | För större distributioner lokala du kan ställa in ytterligare, skala ut processervrar. Ytterligare servrar skala ut kapacitet, genom att hantera större antal replikering av datorer och större mängder replikeringstrafik.<br/><br/> Du kan flytta datorer mellan två servrar i processen, för att belastningsutjämna trafik för replikering. | [Läs mer](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Huvudmålservern

Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.

- Den installeras som standard på konfigurationsservern.
- För stora distributioner, kan du lägga till en ytterligare, separat huvudmålserver för återställning efter fel.


## <a name="next-steps"></a>Nästa steg
- Granska den [arkitektur](/vmware-azure-architecture.md) för haveriberedskap för virtuella VMware-datorer och fysiska servrar.
- Granska den [krav och förutsättningar](vmware-physical-azure-support-matrix.md) för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure. 
