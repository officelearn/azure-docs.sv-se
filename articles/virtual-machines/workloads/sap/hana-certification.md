---
title: Certifiering av SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Certifiering av SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617234"
---
# <a name="certification"></a>Certifiering

Förutom NetWeaver-certifieringen kräver SAP en särskild certifiering för SAP HANA för att stödja SAP HANA på vissa infrastrukturer, till exempel Azure IaaS.

Kärn-SAP-anmärkningen på NetWeaver och i viss mån SAP HANA-certifiering är [SAP Note #1928533 – SAP-program på Azure: Produkter som stöds och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533).

Certifieringsposterna för SAP HANA på Azure-enheter (Stora instanser) finns på sap [HANA-certifierade IaaS-plattformar.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

SAP HANA på Azure (Stora instanser) typer, som avses i SAP HANA certifierade IaaS-plattformar webbplats, ger Microsoft och SAP-kunder möjlighet att distribuera stora SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA eller andra SAP HANA arbetsbelastningar i Azure. Lösningen bygger på SAP-HANA-certifierad dedikerad hårdvarustämpel[(SAP HANA-skräddarsydd datacenterintegration – TDI](https://scn.sap.com/docs/DOC-63140)). Om du kör en SAP HANA TDI-konfigurerad lösning fungerar alla SAP HANA-baserade program (till exempel SAP Business Suite på SAP HANA, SAP BW på SAP HANA, S4/HANA och BW4/HANA) på maskinvaruinfrastrukturen.

Jämfört med att köra SAP HANA i virtuella datorer har den här lösningen en fördel. Det ger mycket större minnesvolymer. För att aktivera den här lösningen måste du förstå följande nyckelaspekter:

- SAP-programskiktet och icke-SAP-program körs i virtuella datorer som finns i de vanliga Azure-maskinvarustämplarna.
- Kundens lokala infrastruktur, datacenter och programdistributioner är anslutna till molnplattformen via ExpressRoute (rekommenderas) eller ett virtuellt privat nätverk (VPN). Active Directory och DNS utökas också till Azure.
- SAP HANA-databasinstansen för HANA-arbetsbelastning körs på SAP HANA på Azure (Stora instanser). Stämpeln Stor instans är ansluten till Azure-nätverk, så programvara som körs i virtuella datorer kan interagera med HANA-instansen som körs i HANA Large Instance.
- Maskinvara för SAP HANA på Azure (stora instanser) är dedikerad maskinvara som tillhandahålls i en IaaS med SUSE Linux Enterprise Server eller Red Hat Enterprise Linux förinstallerad. Precis som med virtuella datorer är ytterligare uppdateringar och underhåll till operativsystemet ditt ansvar.
- Installation av HANA eller eventuella ytterligare komponenter som krävs för att köra SAP HANA på enheter av HANA Large Instance är ditt ansvar. Alla respektive pågående åtgärder och administration av SAP HANA på Azure är också ditt ansvar.
- Utöver de lösningar som beskrivs här kan du installera andra komponenter i din Azure-prenumeration som ansluter till SAP HANA på Azure (Stora instanser). Exempel är komponenter som möjliggör kommunikation med eller direkt till SAP HANA-databasen, till exempel hoppservrar, RDP-servrar, SAP HANA Studio, SAP Data Services för SAP BI-scenarier eller nätverksövervakningslösningar.
- Precis som i Azure erbjuder HANA Large Instance stöd för funktioner för hög tillgänglighet och haveriberedskap.

**Nästa steg**
- Se [Tillgängliga SKU:er för HLI](hana-available-skus.md) 