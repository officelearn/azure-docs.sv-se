---
title: Säkerhets aviseringar i Azure Security Center | Microsoft Docs
description: I det här avsnittet beskrivs vilka säkerhets aviseringar som är och vilka olika typer som är tillgängliga i Azure Security Center.
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
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013299"
---
# <a name="security-alerts-in-azure-security-center"></a>Säkerhetsaviseringar i Azure Security Center

I Azure Security Center finns det flera olika aviseringar för många olika resurs typer. Security Center genererar aviseringar för resurser som har distribuerats i Azure och även för resurser som distribueras i lokala miljöer och hybrid moln miljöer. 

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?

Aviseringar är de meddelanden som Security Center genererar när de identifierar hot på dina resurser. Den prioriterar och listar aviseringarna, tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center innehåller också rekommendationer för hur du kan åtgärda ett angrepp.

## <a name="how-does-security-center-detect-threats"></a>Hur identifierar Security Center hot?

Security Center samlar in, analyserar och integrerar loggdata från dina Azure-resurser och nätverket för att identifiera verkliga hot och minska antalet falska positiva identifieringar. Den fungerar även med anslutna partner lösningar som brand Väggs-och slut punkts skydds lösningar. Security Center analyserar informationen, som ofta korrelerar information från flera källor, för att identifiera hot.

Security Center övervakar resurserna i vilken miljö de är. Mer information om att upptäcka och svara på hot finns i [hur Security Center identifierar och svarar på hot](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Säkerhets aviserings typer

Följande avsnitt vägleder dig genom de olika aviseringarna, beroende på resurs typer:

* [Aviseringar om virtuella datorer och servrar i IaaS](security-center-alerts-iaas.md)
* [Interna beräknings aviseringar](security-center-alerts-compute.md)
* [Aviseringar för data tjänster](security-center-alerts-data-services.md)

I följande avsnitt förklaras hur Security Center använder olika telemetri som samlas in från integrering med Azure-infrastrukturen för att kunna använda ytterligare skydds lager för resurser som distribueras i Azure:

* [Aviseringar för service nivå](security-center-alerts-service-layer.md)
* [Integrering med Azures säkerhets produkter](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Vad är säkerhets incidenter?

En säkerhets incident är en samling relaterade aviseringar i stället för att lista varje avisering individuellt. Security Center använder [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md) för att korrelera olika aviseringar och låg åter givning av signaler till säkerhets incidenter.

Med hjälp av incidenter ger Security Center en enda vy över en angrepps kampanj och alla relaterade aviseringar. Med den här vyn kan du snabbt förstå vilka åtgärder som angriparen tog och vilka resurser som påverkades. Mer information finns i [Cloud Smart Alert-korrelation](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Kom igång med aviseringar

Här är artiklar som hjälper dig att förstå mer om de resurser som övervakas av Security Center och ger rikt linjer för hur du svarar på de aviseringar som presenteras.

* [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)  
* [Hantera säkerhets incidenter i Azure Security Center](security-center-incident.md) 
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Aviseringsverifiering i Azure Security Center](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Uppgradera till standard för avancerad identifiering

För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center Standard. 

1. På **Security Center** -menyn väljer du **säkerhets princip**.
2. För de prenumerationer som du vill flytta till standard nivån väljer du **Redigera inställningar**. 
3. På sidan Inställningar väljer du **pris nivå**. 
   En kostnads fri utvärderings version är tillgänglig i en månad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de olika typerna av aviseringar som är tillgängliga i Security Center. Mer information finns i:

* [Planerings- och bruksanvisning för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Vanliga frågor och svar om Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq).

