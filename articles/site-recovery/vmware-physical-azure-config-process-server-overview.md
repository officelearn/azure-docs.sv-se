---
title: Om azure site recovery-konfiguration/process-/huvudmålservrar
description: Den här artikeln innehåller en översikt över konfigurations-, process- och huvudmålservrar som använder när du konfigurerar haveriberedskap för lokala virtuella virtuella datorer med VMware till Azure med Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062096"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Om komponenter för återställning av webbplats (konfiguration, process, huvudmål)

I den här artikeln beskrivs konfigurations-, process- och huvudmålservrar som används av [tjänsten Site Recovery](site-recovery-overview.md) för att replikera virtuella datorer och fysiska servrar till Azure.

## <a name="configuration-server"></a>Konfigurationsserver

För haveriberedskap av lokala virtuella datorer med VMware och fysiska servrar distribuerar du en lokal konfigurationsserver för platsåterställning.

**Inställning** | **Detaljer** | **Länkar**
--- | --- | ---
**Komponenter**  | Konfigurationsserverdatorn kör alla lokala site recovery-komponenter, som inkluderar konfigurationsservern, processservern och huvudmålservern.<br/><br/> När du konfigurerar konfigurationsservern installeras alla komponenter automatiskt. | [Läs](vmware-azure-common-questions.md#configuration-server) vanliga frågor och svar om konfigurationsservern.
**Roll** | Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering. | Läs mer om arkitekturen för [VMware](vmware-azure-architecture.md) och fysisk serverkatastrofåterställning till Azure. [physical server](physical-azure-architecture.md)
**Krav på VMware** | För haveriberedskap av lokala virtuella datorer med VMware måste du installera och köra konfigurationsservern som en lokal, högtillgänglig virtuell dator med hög tillgänglig VMware. | [Läs mer om](vmware-azure-deploy-configuration-server.md#prerequisites) förutsättningarna.
**VMware-distribution** | Vi rekommenderar att du distribuerar konfigurationsservern med hjälp av en nedladdad OVA-mall. Den här metoden är ett enkelt sätt att konfigurera en konfigurationsserver som uppfyller alla krav och förutsättningar.<br/><br/> Om du av någon anledning inte kan distribuera en virtuell VMware-virtuell dator med hjälp av en OVA-mall kan du konfigurera konfigurationsserverdatorerna manuellt, enligt beskrivningen nedan för fysisk datorkatastrofåterställning. | [Distribuera](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) med en OVA-mall.
**Krav för fysisk server** | För haveriberedskap på lokala fysiska servrar distribuerar du konfigurationsservern manuellt. | [Läs mer om](physical-azure-set-up-source.md#prerequisites) förutsättningarna.
**Distribuera fysisk server** | Om den inte kan installeras som en virtuell virtuell VMware-dator kan du installera den på en fysisk server. | [Distribuera](physical-azure-set-up-source.md#set-up-the-source-environment) konfigurationsservern manuellt.

## <a name="process-server"></a>Bearbeta server

En processserver hanterar replikeringsdata under redundans- och redundans och installerar mobilitetstjänsten för lokala virtuella datorer och fysiska servrar.

**Inställning** | **Detaljer** | **Länkar**
--- | --- | ---
**Distribution**  | Som standard installeras processservern när konfigurationsservern distribueras. <br/><br/> En lokal processserver behövs för haveriberedskap och replikering av lokala virtuella datorer och fysiska servrar. | [Läs mer](vmware-azure-architecture.md#architectural-components).
**Roll (lokalt)** | Tar emot replikeringsdata från datorer som är aktiverade för replikering. <br/><br/> Optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. <br/><br/> Utför en push-installation av tjänsten Site Recovery Mobility på lokala virtuella datorer och fysiska servrar som du vill replikera. <br/><br/> Utför automatisk identifiering av lokala datorer. | [Läs mer](vmware-azure-enable-replication.md).
**Roll (återställning efter fel från Azure)** | Efter redundans från din lokala webbplats konfigurerar du en processserver i Azure, som en virtuell Azure-dator, för att hantera återställning av återställning till din lokala plats.<br/><br/> Processservern i Azure är tillfällig. Den virtuella Azure-datorn kan tas bort när återställning efter återställning av återställningen har gjorts. | [Läs mer](vmware-azure-set-up-process-server-azure.md).
**Skalning** | För större distributioner kan du lokalt konfigurera ytterligare utskalningsprocessservrar. Ytterligare servrar skala ut kapacitet, genom att hantera ett större antal replikerande datorer och större volymer av replikeringstrafik.<br/><br/> Du kan flytta datorer mellan två processservrar för att ladda replikeringstrafiken. | [Läs mer](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Huvudmålservern

Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.

- Som standard installeras huvudmålservern på konfigurationsservern.
- För stora distributioner kan du lägga till ytterligare en separat huvudmålserver för återställning efter fel.

## <a name="next-steps"></a>Nästa steg

- Granska [arkitekturen](vmware-azure-architecture.md) för haveriberedskap av virtuella datorer och fysiska servrar.
- Granska [kraven och förutsättningarna](vmware-physical-azure-support-matrix.md) för haveriberedskap för virtuella datorer och fysiska servrar till Azure.
