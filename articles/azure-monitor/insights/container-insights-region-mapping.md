---
title: Azure Monitor för områdes mappningar för behållare
description: I den här artikeln beskrivs de region mappningar som stöds mellan Azure Monitor för behållare, Log Analytics arbets yta och anpassade mått.
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: references_regions
ms.openlocfilehash: 3e8ead78c5e0e534e07c1e2ab0e25eb3f5a90c38
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194992"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Region mappningar som stöds av Azure Monitor för behållare

 När du aktiverar Azure Monitor för behållare, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett AKS-kluster och samla in anpassade mått som skickas till Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappningar som stöds av Log Analytics Workspace

AKS-kluster resurserna eller Log Analytics arbets ytan kan finnas i andra regioner och följande tabell visar våra mappningar.

|**AKS-kluster region** | **Region för Log Analytics arbets yta** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |Västeuropa |
|SouthAfricaWest |Västeuropa |
|**Australien** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asien och stillahavsområdet** | |
|Asienöstra |Asienöstra |
|Sydostasien |Sydostasien |
|**Brasilien** | |
|Centrala | Usasödracentrala |
|**Kanada** ||
|Indiensödra |Indiensödra |
|Indien |Indiensödra |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|Europanorra |Europanorra |
|UKSouth |UKSouth |
|Västrastorbritannien |UKSouth |
|Västeuropa |Västeuropa |
|**Indien** | |
|Kanada |Kanada |
|Australienöstra |Kanada |
|Usavästracentrala |Kanada |
|**Japan** | |
|Japanöstra |Japanöstra |
|Japanvästra |Japanöstra |
|**Korea** | |
|Centrala |Centrala |
|Koreasödra |Centrala |
|**USA** | |
|Centra lus |Centra lus|
|Platsen eastus |Platsen eastus |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|Östra USA<sup>1</sup>|
|US Gov, Virginia |US Gov, Virginia |

<sup>1</sup> på grund av kapacitets begränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar en Log Analytics-arbetsyta. Befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="custom-metrics-supported-regions"></a>Regioner som stöds för anpassade mått

Insamling av mått från Azure Kubernetes Services (AKS)-kluster noder och poddar stöds endast för publicering som anpassade mått i följande [Azure-regioner](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster läser du [så här aktiverar du Azure Monitor för behållare](container-insights-onboard.md) för att förstå kraven och tillgängliga metoder för att aktivera övervakning.  
