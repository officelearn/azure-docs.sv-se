---
title: Certifiering av SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Certifiering av SAP HANA på Azure (stora instanser).
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
ms.openlocfilehash: 15de566d756d6b0f7719eabf74ee9c7ac66659d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794009"
---
# <a name="certification"></a>Certifiering

Förutom NetWeaver-certifiering kräver SAP en särskild certifiering för SAP HANA för SAP HANA på vissa infrastrukturer, till exempel Azure IaaS.

Kärnan i NetWeaver och en grad certifiering för SAP HANA, SAP-kommentar är [SAP Obs! #1928533 – SAP-program i Azure: Produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533).

Certification-poster för SAP HANA på Azure (stora instanser) enheter finns i den [IaaS-plattformar för SAP HANA-certifierade](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) plats. 

SAP HANA på Azure (stora instanser)-typer, som anges i SAP HANA-certifierade IaaS-plattformar plats, ger Microsoft och SAP-kunder kan distribuera stora SAP Business Suite, SAP BW, s/4 HANA, BW/4HANA eller andra SAP HANA-arbetsbelastningar i Azure. Lösningen bygger på stämpel för SAP HANA-certifierade dedikerad maskinvara ([SAP HANA skräddarsydda center dataintegrering – TDI](https://scn.sap.com/docs/DOC-63140)). Om du kör en lösning för SAP HANA TDI-konfigurerats, fungerar alla SAP HANA-baserade program (till exempel SAP Business Suite på SAP HANA, SAP BW på SAP HANA och S4/HANA BW4/HANA) på maskinvaru-infrastruktur.

Den här lösningen har jämfört med att köra SAP HANA på virtuella datorer, en fördel. Den innehåller för mycket större minne-volymer. Om du vill aktivera den här lösningen, måste du förstå följande viktiga aspekter:

- SAP-program lager och icke-SAP program körs i virtuella datorer som finns i stämplarna som vanliga Azure-maskinvaran.
- Kunden lokal infrastruktur, Datacenter, och distribution av program som är anslutna till molnplattform via ExpressRoute (rekommenderas) eller ett virtuellt privat nätverk (VPN). Active Directory och DNS har också utökats till Azure.
- SAP HANA-databasinstansen för HANA-arbetsbelastningar körs på SAP HANA på Azure (stora instanser). Stor instans stämpeln är ansluten till Azure-nätverk så programvara som körs på virtuella datorer kan interagera med den HANA-instansen som körs i stora HANA-instansen.
- Maskinvara för SAP HANA på Azure (stora instanser) är dedikerad maskinvara som anges i en IaaS med SUSE Linux Enterprise Server eller Red Hat Enterprise Linux förinstallerad. Är ditt ansvar precis som med virtuella datorer, ytterligare uppdateringar och underhåll för operativsystemet.
- Installation av HANA eller de ytterligare komponenterna som krävs för att köra SAP HANA på enheter av stora HANA-instansen är ditt ansvar. Alla respektive pågående åtgärder och administration av SAP HANA på Azure är också ditt ansvar.
- Förutom de lösningar som beskrivs här kan du installera andra komponenter i Azure-prenumerationen som ansluter till SAP HANA på Azure (stora instanser). Exempel är komponenter som aktiverar kommunikation med eller direkt till SAP HANA databasservrar, till exempel jump-servrar, RDP, SAP HANA-Studio, SAP-datatjänster för SAP BI-scenarier eller network övervakningslösningar.
- Som i Azure erbjuder stöd för hög tillgänglighet och funktioner för haveriberedskap i stora HANA-instansen.

**Nästa steg**
- Se [tillgängliga SKU: er för HLI](hana-available-skus.md) 