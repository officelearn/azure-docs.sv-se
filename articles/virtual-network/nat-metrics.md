---
title: Mått och aviseringar för Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Förstå Azure Monitor-mått och aviseringar som är tillgängliga för NAT för virtuellt nätverk.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 4f1760c32117b34d4d453964473cba3c7d07f725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79202186"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-mått

Azure Virtual Network NAT gateway-resurser ger flerdimensionella mått. Du kan använda dessa mått för att observera åtgärden och för [felsökning](nat-metrics.md).  Aviseringar kan konfigureras för kritiska problem som SNAT-utmattning.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuellt nätverk NAT för utgående till Internet">
</p>

*Bild: Virtuellt nätverk NAT för utgående till Internet*

## <a name="metrics"></a>Mått

NAT-gatewayresurser tillhandahåller följande flerdimensionella mått i Azure Monitor:

| Mått | Beskrivning | Rekommenderad aggregering | Dimensioner |
|---|---|---|---|
| Byte | Byte som bearbetas inkommande och utgående | Summa | Riktning (In; Ut), Protokoll (6 TCP; 17 UDP) |
| Paket | Paket som bearbetas inkommande och utgående | Summa | Riktning (In; Ut), Protokoll (6 TCP; 17 UDP) |
| Tappade paket | Paket som tappats av NAT-gatewayen | Summa | / |
| Antal SNAT-anslutningar | Tillståndsövergångar per intervall | Summa | Anslutningstillstånd, protokoll (6 TCP; 17 UDP) |
| Totalt antal SNAT-anslutningar | Aktuella aktiva SNAT-anslutningar (~ SNAT-portar som används) | Summa | Protokoll (6 TCP; 17 UDP) |


## <a name="alerts"></a>Aviseringar

Aviseringar för mått kan konfigureras i Azure Monitor för vart och ett av föregående [mått](#metrics).

## <a name="limitations"></a>Begränsningar

Resurshälsa stöds inte.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [NAT för virtuellt nätverk](nat-overview.md)
* Lär dig mer om [NAT-gatewayresurs](nat-gateway-resource.md)
* Läs mer om [Azure Monitor](../azure-monitor/overview.md)
* Lär dig mer om [felsökning av NAT-gatewayresurser](troubleshoot-nat.md).
* [Berätta vad du ska bygga härnäst för VIRTUELLT NÄTVERK NAT i UserVoice](https://aka.ms/natuservoice).


