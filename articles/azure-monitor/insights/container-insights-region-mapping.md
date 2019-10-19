---
title: Azure Monitor för områdes mappningar för behållare
description: I den här artikeln beskrivs de region mappningar som stöds mellan Azure Monitor för behållare, Log Analytics arbets yta och anpassade mått.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.openlocfilehash: f22c62dddf2e38fa2c9471ce98b49a8aa32390b3
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554016"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Region mappningar som stöds av Azure Monitor för behållare

 När du aktiverar Azure Monitor för behållare, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett AKS-kluster och samla in anpassade mått som skickas till Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappningar som stöds av Log Analytics Workspace

AKS-kluster resurserna eller Log Analytics arbets ytan kan finnas i andra regioner och följande tabell visar våra mappningar.

|**AKS-kluster region** | **Region för Log Analytics arbets yta** |
|-----------------------|------------------------------------|
|**Centralafrika** | |
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
|USA Gov Virginia |USA Gov Virginia |

<sup>1</sup> på grund av kapacitets begränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar en Log Analytics-arbetsyta. Befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="custom-metrics-supported-regions"></a>Regioner som stöds för anpassade mått

Insamling av mått från Azure Kubernetes Services (AKS)-kluster noder och poddar stöds endast för publicering som anpassade mått i följande [Azure-regioner](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster läser du [så här aktiverar du Azure Monitor för behållare](container-insights-onboard.md) för att förstå kraven och tillgängliga metoder för att aktivera övervakning.  
