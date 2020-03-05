---
title: Mått och aviseringar för Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Förstå Azure Monitor mått och aviseringar som är tillgängliga för Virtual Network NAT.
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
ms.date: 03/03/2020
ms.author: allensu
ms.openlocfilehash: 6bffa114b325e8b98f439e7ff2ce21480407a643
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274916"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-mått

Azure Virtual Network NAT-gateway-resurser innehåller flerdimensionella mått. Du kan använda dessa mått för att se hur det fungerar och vid [fel sökning](nat-metrics.md).  Aviseringar kan konfigureras för kritiska problem som SNAT-belastningar.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*

>[!NOTE] 
>Virtual Network NAT är tillgängligt som offentlig för hands version för tillfället. För närvarande är det bara tillgängligt i en begränsad uppsättning [regioner](nat-overview.md#region-availability). Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="metrics"></a>Mått

NAT-gateway-resurser ger följande flerdimensionella mått i Azure Monitor:

| Mått | Beskrivning | Rekommenderad agg regering | Dimensioner |
|---|---|---|---|
| Byte | Antal byte som bearbetats inkommande och utgående | Summa | Riktning (i; Ut), protokoll (6 TCP; 17 UDP) |
| Skickas | Paket som har bearbetats inkommande och utgående | Summa | Riktning (i; Ut), protokoll (6 TCP; 17 UDP) |
| Ignorerade paket | Paket som släppts av NAT-gatewayen | Summa | / |
| Antal SNAT-anslutningar | Tillstånds över gångar per intervall | Summa | Anslutnings tillstånd, protokoll (6 TCP; 17 UDP) |
| Totalt antal SNAT-anslutningar | Aktuella aktiva SNAT-anslutningar (~ SNAT-portar som används) | Summa | Protokoll (6 TCP; 17-tums UDP) |


## <a name="alerts"></a>Aviseringar

Aviseringar för mått kan konfigureras i Azure Monitor för vart och ett av föregående [mått](#metrics).

## <a name="limitations"></a>Begränsningar

Resource Health stöds inte.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Virtual Network NAT](nat-overview.md)
- Lär dig om [NAT gateway-resurs](nat-gateway-resource.md)
- Läs mer om [Azure Monitor](../azure-monitor/overview.md)
