---
title: Översikt över SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Översikt över hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39fcf5d0fe2273c4debd3ae5ebe5fd1190ddc959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616958"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Vad är SAP HANA på stora Azure-instanser?

SAP HANA på Azure (Stora instanser) är en unik lösning för Azure. Förutom att tillhandahålla virtuella datorer för distribution och körning av SAP HANA erbjuder Azure dig möjligheten att köra och distribuera SAP HANA på bare-metal-servrar som är dedikerade till dig. SAP HANA på Azure (Stora instanser) bygger på icke-delad värd/server-maskinvara med bare-metal som har tilldelats dig. Servermaskinvaran är inbäddad i större stämplar som innehåller infrastruktur för beräkning/server, nätverk och lagring. Som en kombination är det HANA skräddarsydda datacenter integration (TDI) certifierad. SAP HANA på Azure (Stora instanser) erbjuder olika server-SKU:er eller storlekar. Enheter kan ha 36 Intel CPU-kärnor och 768 GB minne och gå upp till enheter som har upp till 480 Intel CPU-kärnor och upp till 24 TB minne.

Kundisoleringen i infrastrukturstämpeln utförs i klienter, som ser ut som:

- **Nätverk:** Isolering av kunder inom infrastrukturstacken via virtuella nätverk per tilldelad klient. En klient tilldelas en enskild kund. En kund kan ha flera klienter. Nätverksisolering av klienter förbjuder nätverkskommunikation mellan klienter på infrastrukturstämpelnivå, även om klienterna tillhör samma kund.
- **Lagringskomponenter**: Isolering via virtuella lagringsdatorer som har lagringsvolymer tilldelade till dem. Lagringsvolymer kan endast tilldelas en virtuell lagringsdator. En virtuell lagringsdator tilldelas exklusivt till en enda klient i SAP HANA TDI-certifierad infrastrukturstack. Därför kan lagringsvolymer som tilldelats en virtuell lagringsdator endast nås i en specifik och relaterad klient. De visas inte mellan de olika distribuerade klienterna.
- **Server eller värd**: En server eller värdenhet delas inte mellan kunder eller klienter. En server eller värd som distribueras till en kund är en atomisk beräkningsenhet med bare-metal som tilldelas en enda klient. *Ingen* maskinvarupartitioning eller mjuk partitionering används som kan resultera i att du delar en värd eller en server med en annan kund. Lagringsvolymer som har tilldelats den virtuella lagringsdatorn för den specifika klienten monteras på en sådan server. En klient kan ha en till många serverenheter av olika SKU:er som är exklusivt tilldelade.
- I en SAP HANA på Azure-infrastrukturstämpel (stora instanser) distribueras många olika klienter och isoleras mot varandra via klientkoncepten på nätverks-, lagrings- och beräkningsnivå. 


Dessa bare-metal serverenheter stöds för att köra SAP HANA bara. SAP-programlagret eller arbetsbelastningen mellan-ware-lagret körs i virtuella datorer. De infrastrukturstämplar som kör SAP HANA på Azure-enheter (Stora instanser) är anslutna till Azure-nätverkstjänsternas stamnät. På så sätt tillhandahålls anslutning med låg latens mellan SAP HANA på Azure-enheter (stora instanser) och virtuella datorer.

Från och med juli 2019 skiljer vi mellan två olika revideringar av HANA Large Instance-stämplar och placering av distributioner:

- "Revision 3" (Rev 3): Är de frimärken som gjordes tillgängliga för kunden att distribuera före juli 2019
- "Revision 4" (Rev 4): Ny stämpeldesign som distribueras i närheten av Azure VM-värdar och som hittills har släppts i Azure-regionerna i:
    -  USA, västra 2 
    -  USA, östra 
    -  Europa, västra
    -  Europa, norra


Det här dokumentet är ett av flera dokument som täcker SAP HANA på Azure (Stora instanser). I det här dokumentet introduceras den grundläggande arkitekturen, ansvaret och tjänsterna som tillhandahålls av lösningen. Lösningens kapacitet på hög nivå diskuteras också. För de flesta andra områden, till exempel nätverk och anslutning, omfattar fyra andra dokument information och detaljerad information. Dokumentationen av SAP HANA på Azure (Stora instanser) täcker inte aspekter av SAP NetWeaver-installationen eller distributionerna av SAP NetWeaver i virtuella datorer. SAP NetWeaver på Azure omfattas av separata dokument som finns i samma Azure-dokumentationsbehållare. 


De olika dokumenten i HANA Large Instance vägledning täcker följande områden:

- [ÖVERSIKT över OCH ARKITEKTUR för SAP HANA (stora instanser) på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA-infrastruktur (Stora instanser) och anslutning på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [FELSÖKNING och övervakning av SAP HANA (stora instanser) på Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hög tillgänglighet som konfigureras i SUSE med hjälp av STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Operativsystemet säkerhetskopiering och återställning för typ II SKU:er för Revision 3-frimärken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Nästa steg**
- Se [känna till villkoren](hana-know-terms.md)