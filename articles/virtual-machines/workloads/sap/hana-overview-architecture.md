---
title: Översikt över SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Översikt över hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477808"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Vad är SAP HANA på stora Azure-instanser?

SAP HANA på Azure (stora instanser) är en unik lösning till Azure. Förutom att tillhandahålla virtuella datorer för att distribuera och köra SAP HANA, ger Azure dig möjlighet att köra och distribuera SAP HANA på bare metal-servrar som är dedikerade till dig. SAP HANA på Azure (stora instanser) lösningen bygger på icke-delade/värdservern bare metal-maskinvara som är tilldelad till dig. Servermaskinvaran är inbäddad i större stämplar som innehåller beräkning/server-, nätverks- och lagringsinfrastrukturen. Som en kombination är det HANA skräddarsydda center dataintegrering (TDI) certifierade. SAP HANA på Azure (stora instanser) erbjuder olika server-SKU: er eller storlekar. Enheter kan ha 36 Intel CPU-kärnor och 768 GB minne och gå upp till enheter som har upp till 480 Intel CPU-kärnor och upp till 24 TB minne.

Kundisolering inom infrastruktur stämpeln utförs i klienter, som ser ut som:

- **Nätverk**: Isolering av kunder i infrastrukturen stack via virtuella nätverk per kund tilldelats klienten. En klient har tilldelats en enda kund. En kund kan ha flera klienter. Nätverksisolering med klienter förbjuder nätverkskommunikation mellan klienter i stämpel infrastrukturnivå, även om innehavarna som tillhör samma kund.
- **Lagringskomponenter**: Isolering via storage virtuella datorer som har lagringsvolymer som tilldelats. Lagringsvolymer kan tilldelas till en virtuell dator. En virtuell dator för lagring har tilldelats exklusivt avsedd för en enskild klient i SAP HANA TDI-certifierade infrastruktur-stacken. Därför är lagringsvolymer som tilldelats en virtuell dator för lagring tillgängliga i en specifik och relaterade-klient. De är inte synliga mellan de olika distribuerade klienterna.
- **Servern eller värden**: En server eller värden enhet delas inte mellan kunder eller klienter. En server eller en värd som har distribuerats till en kund är en atomisk bare metal-beräkningsenhet som är tilldelad till en enda klient. *Inte* maskinvara partitionering eller mjuk partitionering används som kan resultera i att du delar en värd eller en server med en annan kund. Lagringsvolymer som är kopplade till den virtuella datorn för lagring av specifik klient monteras till sådana en server. En klient kan ha en till många server enheter av olika SKU: er som tilldelats exklusivt.
- Inom en SAP HANA på Azure (stora instanser) infrastruktur stämpel, många olika klienter distribueras och isolerade mot varandra via klient-koncepten för nätverk, lagring och beräkningsnivå. 


Serverenheterna utan operativsystem stöder endast kör SAP HANA. SAP-programnivån eller arbetsbelastning mitten kod layer körs i virtuella datorer. De stämplar som infrastrukturen som kör SAP HANA på Azure (stora instanser) enheter är anslutna till de Azure-tjänster för fibernätverk. På så sätt kan tillhandahålls med låg latens anslutning mellan virtuella datorer och SAP HANA på Azure (stora instanser)-enheter.

Det här dokumentet är en av flera dokument som behandlar SAP HANA på Azure (stora instanser). Det här dokumentet beskriver de grundläggande arkitektur, ansvarsområden och tjänster som tillhandahålls i lösningen. På hög nivå diskuteras av lösningen också. För de flesta andra områden, till exempel nätverk och anslutning, täcker fyra andra dokument information och information på detaljnivå. I dokumentationen för SAP HANA på Azure (stora instanser) omfattar inte aspekter av SAP NetWeaver-installation eller distributioner av SAP NetWeaver på virtuella datorer. SAP NetWeaver på Azure täcks i separata dokument som finns i samma behållare i Azure-dokumentationen. 


Olika dokument för stora HANA-instansen vägledning täcker följande områden:

- [SAP HANA (stora instanser) översikt och arkitektur på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (stora instanser) felsökning och övervakning i Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hög tillgänglighet som angetts i SUSE med hjälp av STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Säkerhetskopiering och återställning av operativsystem för Type II-SKU:er](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Nästa steg**
- Se [vet villkoren](hana-know-terms.md)