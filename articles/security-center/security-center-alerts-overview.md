---
title: Säkerhets aviseringar i Azure Security Center | Microsoft Docs
description: I det här avsnittet beskrivs vad är säkerhets aviseringar och de olika typer som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: 25fb94afdfe68510c66cff130a3abc8b74d7311d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647310"
---
# <a name="security-alerts-in-azure-security-center"></a>Säkerhets aviseringar i Azure Security Center

Den här artikeln visar de olika typerna av säkerhets aviseringar som är tillgängliga i Azure Security Center (ASC). Det finns flera olika aviseringar för många olika resurs typer. ASC genererar aviseringar för båda resurser som distribueras på Azure och resurser som distribueras i lokala miljöer och hybrid moln miljöer. 

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?

Aviseringar är de meddelanden som Security Center genererar när de identifierar hot på dina resurser. Den prioriterar och listar aviseringarna tillsammans med den information som behövs för att du snabbt ska kunna undersöka problemet. Security Center innehåller också rekommendationer för hur du kan åtgärda ett angrepp.

## <a name="how-does-security-center-detect-threats"></a>Hur identifierar Security Center hot?

Security Center samlar in, analyserar och integrerar loggdata från dina Azure-resurser, nätverket och anslutna partner lösningar som brand Väggs-och slut punkts skydds lösningar för att identifiera verkliga hot och minska antalet falska positiva identifieringar. Security Center analyserar informationen, som ofta korrelerar information från flera källor, för att identifiera hot.

ASC övervakar resurserna oavsett om de distribueras på Azure eller distribueras på andra lokala miljöer och hybrid moln miljöer. Läs mer om att identifiera och svara på hot i [hur Security Center identifierar och svarar på hot](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Säkerhets aviserings typer

Följande avsnitt vägleder dig genom de olika ASC-aviseringarna enligt resurs typer:

* [IaaS VM & Server-aviseringar](security-center-alerts-iaas.md)
* [Interna beräknings aviseringar](security-center-alerts-compute.md)
* [Aviseringar för data tjänster](security-center-alerts-data-services.md)

I följande avsnitt förklaras hur Security Center utnyttjar olika telemetri som samlas in från integrering med Azure-infrastrukturen för att kunna använda ytterligare skydds lager för resurser som distribueras i Azure:

* [Aviseringar för service nivå](security-center-alerts-service-layer.md)
* [Integrering med Azures säkerhets produkter](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Vad är varnings incidenter?

En säkerhets incident är en samling relaterade aviseringar i stället för att lista varje avisering individuellt. Security Center använder [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md) för att korrelera olika aviseringar och låg åter givning av signaler till säkerhets incidenter.

Med hjälp av incidenter ger Security Center en enda vy över en angrepps kampanj och alla relaterade aviseringar. Med den här vyn kan du snabbt förstå vilka åtgärder som angriparen tog och vilka resurser som påverkas. Mer information finns i [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Kom igång med aviseringar

I följande avsnitt kan du läsa mer om de resurser som övervakas av ASC, och rikt linjer för hur du svarar på de aviseringar som presenteras av ASC.

* Om du vill se vilka plattformar och funktioner som skyddas av ASC, se [plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md).  
* Information om vad som är säkerhets incidenter och hur ASC svarar på dem finns i [hantera säkerhets incidenter i Azure Security Center](security-center-incident.md). 
* Information om hur du hanterar de aviseringar du får finns i [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).
* Information om hur du verifierar att Security Center är korrekt konfigurerad och för att stimulera en test avisering finns [i varningar validering i Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Uppgradera till standard för avancerad identifiering

För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center Standard. 

1. På Security Center-menyn väljer du **säkerhets princip**.
2. Klicka på **Redigera inställningar**för prenumerationer som du vill flytta till standard nivån. 
3. På sidan Inställningar väljer du **pris nivå**. 
   En kostnads fri utvärderings version är tillgänglig i en månad. Mer information finns på [sidan med priser](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig vad som är säkerhets aviseringar och de olika typerna av aviseringar som är tillgängliga i Security Center. Mer information finns i följande avsnitt:

* [Planerings- och bruksanvisning för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center vanliga frågor och svar](https://docs.microsoft.com/azure/security-center/security-center-faq): Här finns vanliga frågor om att använda tjänsten.

