---
title: Arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Arkitektur att distribuera SAP HANA på Azure (stora instanser).
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
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80ef63cdd9de8cb2340fe15d761402bb9f00fae9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795950"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Arkitektur för SAP HANA (stora instanser) på Azure

SAP HANA på Azure (stora instanser)-lösningen har SAP-programnivån som finns i virtuella datorer på en hög nivå. Lagret databasen finns på SAP TDI konfigurerade maskinvara finns i en stor instans stämpel i samma Azure-region som är ansluten till Azure IaaS.

> [!NOTE]
> Distribuera SAP-programnivån i samma Azure-region som SAP DBMS-lager. Den här regeln är väl dokumenterat i publicerad information om SAP-arbetsbelastningar på Azure. 

Den övergripande arkitekturen för SAP HANA på Azure (stora instanser) innehåller en SAP TDI-certifierade maskinvarukonfigurationen, vilket är en icke-virtualiserade, bare metal, högpresterande server för SAP HANA-databas. Det ger också möjlighet och Azures flexibilitet att skala resurser för SAP-programnivån som uppfyller dina behov.

![Översikt över arkitekturen för SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image1-architecture.png)

Arkitekturen som visas är uppdelad i tre delar:

- **Right**: Visar en lokal infrastruktur som kör olika program i data centers så att slutanvändarna kan komma åt LOB-program, till exempel SAP. Vi rekommenderar den här lokala infrastruktur sedan är ansluten till Azure med [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: Visar Azure IaaS och i så fall använder virtuella datorer som värd för SAP eller andra program som använder SAP HANA som en DBMS system. Mindre HANA-instanser som fungerar med det minne som tillhandahåller virtuella datorer distribueras på virtuella datorer tillsammans med deras programnivån. Mer information om virtuella datorer finns i [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/).

   Azure network-tjänster använder för att gruppera SAP-system tillsammans med andra program i virtuella nätverk. Dessa virtuella nätverk ansluta till lokala system samt att SAP HANA på Azure (stora instanser).

   SAP NetWeaver-program och databaser som stöds för att köra i Azure finns i [SAP Support Obs! #1928533 – SAP-program i Azure: Produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533). Dokumentation om hur du distribuerar SAP-lösningar på Azure finns:

  -  [Använd SAP på Windows-datorer](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Använda SAP-lösningar på Azure virtual machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vänster**: Visar SAP HANA TDI-certifierade-maskinvaran i stämpeln stora Azure-instanser. Stora HANA-instansen enheter är anslutna till de virtuella nätverken för din prenumeration genom att använda samma teknik som anslutningen från en lokal plats till Azure.

Stora Azure-instanser stämpeln själva kombinerar följande komponenter:

- **Databehandling**: Servrar som baseras på Intel Xeon E7-8890v3 eller Intel Xeon E7-8890v4 processorer som ger den nödvändiga kapaciteten för databehandling och är SAP HANA-certifierade.
- **Nätverk**: En enhetlig höghastighetsnätverk-infrastrukturresurs som sammanbinder databehandling, lagring och LAN-komponenter.
- **Storage**: En lagringsinfrastruktur som nås via en enhetlig nätverksresurserna. Den specifika lagringskapacitet som tillhandahålls är beroende av specifika SAP HANA på Azure (stora instanser) konfiguration som har distribuerats. Mer lagringskapacitet finns på en månatlig avgift.

Kunder distribueras i flera innehavare-infrastrukturen i stor instans stämpeln som isolerade klienter. Vid distribution av klienten namnge en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumeration är det som den stora HANA-instansen som faktureras mot. Dessa klienter har ett 1:1-förhållande till Azure-prenumerationen. Det är möjligt att få åtkomst till en enhet för stora HANA-instansen som distribueras i en klient i en Azure-region från olika virtuella nätverk som tillhör olika Azure-prenumerationer för ett nätverk. Dessa Azure-prenumerationer måste tillhöra samma Azure-registrering. 

Precis som med virtuella datorer, erbjuds SAP HANA på Azure (stora instanser) i flera Azure-regioner. För att erbjuda disaster recovery-funktioner, kan du anmäla dig. Olika stora instanser stämplar inom en geo-politiska region är anslutna till varandra. Till exempel är HANA stora instans tidsstämplar i västra USA och östra USA anslutna via ett dedikerat nätverkslänk för disaster recovery-replikering. 

Precis som du kan välja mellan olika typer av virtuella datorer med Azure virtuella datorer, kan du välja mellan olika SKU: er för stora HANA-instansen som är skräddarsydda för arbetsbelastningen för olika typer av SAP HANA. SAP gäller minne till processor socket förhållanden för varierande arbetsbelastningar som baseras på Intel-processor generationer. I följande tabell visas de SKU-typer som erbjuds.

Du kan hitta tillgängliga SKU: er [tillgängliga SKU: er för HLI](hana-available-skus.md).

**Nästa steg**
- Se [nätverksarkitektur för SAP HANA (stora instanser)](hana-network-architecture.md)