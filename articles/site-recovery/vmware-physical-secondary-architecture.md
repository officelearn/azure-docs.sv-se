---
title: Arkitektur för VMware/fysisk server-replikering i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att replikera lokala virtuella VMware-datorer eller fysiska Windows/Linux-servrar till en sekundär VMware-plats med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 04591c57f90e3c540963d77c2213498bf0ad2577
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921404"
---
# <a name="vmware-vmphysical-server-to-vmware-replication-architecture"></a>VMware VM/fysiska servern till arkitektur för VMware-replikering

Den här artikeln beskrivs arkitekturen och processer som används när du replikera, redundansväxla och återställa den lokala virtuella VMware-datorer (VM) eller fysiska Windows/Linux-servrar till en sekundär VMware plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Arkitekturkomponenter

**Område** | **Komponent** | **Detaljer**
--- | --- | ---
**Azure** | Du kan distribuera det här scenariot med InMage Scout. | För att hämta InMage Scout behöver du en Azure-prenumeration.<br/><br/> När du har skapat ett Recovery Services-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.
**Processervern** | Finns på primär plats | Du distribuerar processervern för att hantera cachelagring, komprimering och dataoptimering.<br/><br/> Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.
**Konfigurationsserver** | Finns på sekundär plats | Konfigurationsservern hanterar, konfigurerar och övervakar distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen.
**vContinuum-server** | Valfri. Installeras på samma plats som konfigurationsservern. | Den innehåller en konsol för att hantera och övervaka din skyddade miljö.
**Huvudmålservern** | Finns på den sekundära platsen | Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna.<br/><br/> Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar.<br/><br/> Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. Unified-agenten är installerad på den här servern.
**VMware ESX/ESXi och vCenter-server** |  Virtuella datorer finns på ESX-/ESXi-värdar. Värdar hanteras med en vCenter-server | Du behöver en VMware-infrastruktur för att replikera virtuella VMware-datorer.
**Virtuella datorer/fysiska servrar** |  Unified Agent installeras på virtuella VMware-datorer eller fysiska servrar som du vill replikera. | Agenten fungerar som en kommunikationsprovider mellan alla komponenter.

### <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera.
2. Efter den första replikeringen skickar agenten på varje dator förändringsreplikeringar till processervern.
3. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

**Bild 6: VMware till VMware-replikering**

![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Nästa steg

[Konfigurera](vmware-physical-secondary-disaster-recovery.md) haveriberedskap för virtuella VMware-datorer och fysiska servrar till en sekundär plats.
