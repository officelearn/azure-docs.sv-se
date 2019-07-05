---
title: Azure Monitor för behållare region mappningar
description: Den här artikeln beskriver region mappningarna mellan Azure Monitor för behållare, Log Analytics-arbetsytan och anpassade mått.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518084"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Region-mappningar som stöds av Azure Monitor för behållare

 När du aktiverar Azure Monitor för behållare, endast vissa regioner som stöds för att länka en Log Analytics-arbetsyta och ett AKS-kluster och samla in anpassade mått skickas till Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics-arbetsytan stöds mappningar

AKS-klusterresurser eller Log Analytics-arbetsytan kan finnas i andra regioner och i följande tabell visar våra mappningar.

|**Region för AKS-kluster** | **Log Analytics-arbetsyta region** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |Västeuropa |
|SouthAfricaWest |Västeuropa |
|**Australien** | |
|Australien |Australien |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|Australien |Australien |
|**Asien och stillahavsområdet** | |
|Asienöstra |Asienöstra |
|SoutheastAsia |SoutheastAsia |
|**Brasilien** | |
|Brasiliensödra | SouthCentralUS |
|**Kanada** ||
|CanadaCentral |CanadaCentral |
|Kanadaöstra |CanadaCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|Europanorra |Europanorra |
|Södrastorbritannien |Södrastorbritannien |
|UKWest |Södrastorbritannien |
|Västeuropa |Västeuropa |
|**Indien** | |
|Indiencentrala |Indiencentrala |
|Indiensödra |Indiencentrala |
|Indienvästra |Indiencentrala |
|**Japan** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> på grund av begränsningar i kapaciteten, regionen som inte är tillgänglig när du skapar nya resurser. Detta inkluderar en Log Analytics-arbetsyta. Redan befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="custom-metrics-supported-regions"></a>Anpassade mått regioner som stöds

Samla in mått från Azure Kubernetes Services (AKS) kluster noder och poddar som stöds för publicering som anpassade mått i följande [Azure-regioner](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster, granska [så här aktiverar du i Azure Monitor för behållare](container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning.  