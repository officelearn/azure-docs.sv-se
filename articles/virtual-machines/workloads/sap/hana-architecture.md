---
title: Arkitektur för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Arkitektur för hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614527"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>SAP HANA-arkitektur (stora instanser) på Azure

På en hög nivå har SAP HANA på Azure (Stora instanser) lösningen SAP-programskiktet som finns i virtuella datorer. Databaslagret finns på SAP TDI-konfigurerad maskinvara som finns i en stor instansstämpel i samma Azure-region som är ansluten till Azure IaaS.

> [!NOTE]
> Distribuera SAP-programlagret i samma Azure-region som SAP DBMS-lagret. Den här regeln är väl dokumenterad i publicerad information om SAP-arbetsbelastningar på Azure. 

Den övergripande arkitekturen för SAP HANA på Azure (Stora instanser) tillhandahåller en SAP TDI-certifierad maskinvarukonfiguration, som är en icke-virtualiserad, bare metal, högpresterande server för SAP HANA-databasen. Det ger också möjligheten och flexibiliteten hos Azure att skala resurser för SAP-programlagret för att uppfylla dina behov.

![Arkitektonisk översikt över SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image1-architecture.png)

Arkitekturen som visas är uppdelad i tre avsnitt:

- **Höger**: Visar en lokal infrastruktur som kör olika program i datacenter så att slutanvändare kan komma åt LOB-program, till exempel SAP. Helst är den här lokala infrastrukturen ansluten till Azure med [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: Visar Azure IaaS och, i det här fallet, användning av virtuella datorer för att vara värd för SAP eller andra program som använder SAP HANA som ett DBMS-system. Mindre HANA-instanser som fungerar med det minne som virtuella datorer tillhandahåller distribueras i virtuella datorer tillsammans med deras programlager. Mer information om virtuella datorer finns i [Virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).

   Azure-nätverkstjänster används för att gruppera SAP-system tillsammans med andra program i virtuella nätverk. Dessa virtuella nätverk ansluter till lokala system samt till SAP HANA på Azure (stora instanser).

   För SAP NetWeaver-program och databaser som stöds för att köras i Azure finns i [SAP Support Note #1928533 – SAP-program på Azure: Produkter som stöds och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533). Dokumentation om hur du distribuerar SAP-lösningar på Azure finns i:

  -  [Använda SAP på virtuella Windows-datorer](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Använda SAP-lösningar på virtuella Azure-datorer](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vänster**: Visar SAP HANA TDI-certifierad maskinvara i Azure Large Instance-stämpeln. HANA Large Instance-enheterna är anslutna till de virtuella nätverken för din Azure-prenumeration med samma teknik som anslutningen från lokalt till Azure. Från och med maj 2019 introducerades en optimering som gör det möjligt att kommunicera mellan HANA Large Instance-enheterna och virtuella Azure-datorer utan inblandning av ExpressRoute Gateway. Den här optimeringen som kallas ExpressRoute Fast Path visas i den här arkitekturen (röda linjer). 

Själva Azure Large Instance-stämpeln kombinerar följande komponenter:

- **Datoranvändning:** Servrar som baseras på olika generationer av Intel Xeon-processorer som ger den datorkapacitet som krävs och som är SAP HANA-certifierade.
- **Nätverk:** En enhetlig höghastighetsnätverksinfrastruktur som kopplar samman dator-, lagrings- och LAN-komponenterna.
- **Lagring**: En lagringsinfrastruktur som nås via en enhetlig nätverksinfrastruktur. Den specifika lagringskapacitet som tillhandahålls beror på den specifika SAP HANA-konfigurationen (Stora instanser) som distribueras. Mer lagringskapacitet finns tillgänglig mot en extra månadskostnad.

Inom multi-tenant-infrastrukturen för stämpeln Stor instans distribueras kunder som isolerade klienter. Vid distributionen av klienten namnger du en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumerationen är den som DEN STORA HANA-instansen faktureras mot. Dessa klienter har en 1:1-relation till Azure-prenumerationen. För ett nätverk är det möjligt att komma åt en HANA-enhet för stor instans som distribueras i en klient i en Azure-region från olika virtuella nätverk som tillhör olika Azure-prenumerationer. Dessa Azure-prenumerationer måste tillhöra samma Azure-registrering. 

Precis som med virtuella datorer erbjuds SAP HANA på Azure (stora instanser) i flera Azure-regioner. Om du vill erbjuda funktioner för haveriberedskap kan du välja att anmäla dig. Olika stora instansstämplar inom en geopolitisk region är kopplade till varandra. Till exempel är HANA Stora instansstämplar i USA Väst och USA Öst anslutna via en dedikerad nätverkslänk för haveriberedskapsreplikering. 

På samma sätt som du kan välja mellan olika vm-typer med Virtuella Azure-datorer kan du välja mellan olika SKU:er för STORA HANA-instans som är skräddarsydda för olika arbetsbelastningstyper av SAP HANA. SAP tillämpar förhållandet mellan minne och processor för olika arbetsbelastningar baserat på Intel-processorgenerationerna. I följande tabell visas de SKU-typer som erbjuds.

Du kan hitta tillgängliga [SKU:er Tillgängliga SKU:er för HLI](hana-available-skus.md).

**Nästa steg**
- Referera [till SAP HANA-nätverksarkitektur (Stora instanser)](hana-network-architecture.md)