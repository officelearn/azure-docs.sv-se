---
title: Arkitektur-VMware/fysisk haveriberedskap till en sekundär plats med Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid haveriberedskap av lokala virtuella datorer med VMware eller fysiska Windows/Linux-servrar till en sekundär VMware-webbplats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b0a46dcf8fe298494a53713f122b1bda8ce07e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954579"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Arkitektur för VMware/fysisk serverreplikering till en sekundär lokal plats

I den här artikeln beskrivs den arkitektur och de processer som används vid ställa in haveriberedskapsreplikering, redundans och återställning av lokala virtuella VMware-datorer (VMware) eller fysiska Windows/Linux-servrar till en sekundär VMware-plats med [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Arkitekturkomponenter

**Område** | **Komponent** | **Detaljer**
--- | --- | ---
**Azure** | Du kan distribuera det här scenariot med InMage Scout. | För att hämta InMage Scout behöver du en Azure-prenumeration.<br/><br/> När du har skapat ett Recovery Services-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.
**Bearbeta server** | Finns på primär plats | Du distribuerar processervern för att hantera cachelagring, komprimering och dataoptimering.<br/><br/> Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.
**Konfigurationsserver** | Finns på sekundär plats | Konfigurationsservern hanterar, konfigurerar och övervakar distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen.
**vContinuum-server** | Valfri. Installeras på samma plats som konfigurationsservern. | Den innehåller en konsol för att hantera och övervaka din skyddade miljö.
**Huvudmålservern** | Finns på den sekundära platsen | Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna.<br/><br/> Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar.<br/><br/> Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. Unified-agenten är installerad på den här servern.
**VMware ESX/ESXi och vCenter-server** |  Virtuella datorer finns på ESX-/ESXi-värdar. Värdar hanteras med en vCenter-server | Du behöver en VMware-infrastruktur för att replikera virtuella VMware-datorer.
**Virtuella datorer/fysiska servrar** |  Unified Agent installeras på virtuella VMware-datorer eller fysiska servrar som du vill replikera. | Agenten fungerar som en kommunikationsprovider mellan alla komponenter.

## <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera.
2. Efter den första replikeringen skickar agenten på varje dator förändringsreplikeringar till processervern.
3. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

**Bild 6: VMware till VMware-replikering**

![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Nästa steg

[Konfigurera](vmware-physical-secondary-disaster-recovery.md) haveriberedskap av virtuella datorer och fysiska servrar på en sekundär plats.
