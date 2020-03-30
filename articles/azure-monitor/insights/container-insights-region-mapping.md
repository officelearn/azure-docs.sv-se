---
title: Azure Monitor för behållare regionmappningar
description: I den här artikeln beskrivs de regionmappningar som stöds mellan Azure Monitor för behållare, Log Analytics Workspace och anpassade mått.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403430"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Regionmappningar som stöds av Azure Monitor för behållare

 När du aktiverar Azure Monitor för behållare stöds endast vissa regioner för att länka en Log Analytics-arbetsyta och ett AKS-kluster och samla in anpassade mått som skickas till Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappningar för logganalysarbetsyta

AKS-klusterresurserna eller log analytics-arbetsytan kan finnas i andra regioner och i följande tabell visas våra mappningar.

|**AKS-klusterregion** | **Regionen Logganalysarbetsyta** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |Västeuropa |
|Södra Sydvästra |Västeuropa |
|**Australien** | |
|AustralienEast |AustralienEast |
|AustralienCentralen |AustralienCentralen |
|AustralienCentral2 |AustralienCentralen |
|AustralienEast |AustralienEast |
|**Asien och stillahavsområdet** | |
|Östasien |Östasien |
|Sydöstran |Sydöstran |
|**Brasilien** | |
|BrasilienSouth | Södra CentralEN |
|**Kanada** ||
|CanadaCentral |CanadaCentral |
|KanadaEast |CanadaCentral |
|**Europa** | |
|FranceCentral (på andra) |FranceCentral (på andra) |
|FrankrikeSouth |FranceCentral (på andra) |
|Nordeuropeiska |Nordeuropeiska |
|UkSouth (svenska) |UkSouth (svenska) |
|StorbritannienVäst |UkSouth (svenska) |
|Västeuropa |Västeuropa |
|**Indien** | |
|CentralIndia (centralindia) |CentralIndia (centralindia) |
|SouthIndia (på andra sätt) |CentralIndia (centralindia) |
|WestIndia (på andra) |CentralIndia (centralindia) |
|**Japan** | |
|JapanEast |JapanEast |
|JapanVäst |JapanEast |
|**Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth ( |KoreaCentral |
|**USA** | |
|CentralUS (CentralUS) |CentralUS (CentralUS)|
|EastUS (östra) |EastUS (östra) |
|ÖstraUS2 |ÖstraUS2 |
|WestUS (på andra sätt) |WestUS (på andra sätt) |
|WestUS2 (På andra sätt) |WestUS2 (På andra sätt) |
|Västra CentralaUS<sup>1</sup>|EastUS<sup>1</sup>|
|US Gov, Virginia |US Gov, Virginia |

<sup>1</sup> På grund av kapacitetsbegränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar en Log Analytics-arbetsyta. Befintliga kopplade resurser i regionen bör dock fortsätta att fungera.

## <a name="custom-metrics-supported-regions"></a>Anpassade mått som stöds regioner

Samla in mått från AKS-klusterkluster (Azure Kubernetes Services) stöds för publicering som anpassade mått endast i följande [Azure-regioner](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka AKS-klustret läser du [Så här aktiverar du Azure Monitor för behållare för](container-insights-onboard.md) att förstå kraven och tillgängliga metoder för att aktivera övervakning.  
