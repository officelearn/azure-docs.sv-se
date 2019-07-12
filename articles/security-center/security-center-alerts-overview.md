---
title: Säkerhetsaviseringar i Azure Security Center | Microsoft Docs
description: Det här avsnittet beskriver vad är säkerhetsaviseringar och olika typer i Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571678"
---
# <a name="security-alerts-in-azure-security-center"></a>Säkerhetsaviseringar i Azure Security Center

Den här artikeln beskriver vi de olika typerna av säkerhetsaviseringar som är tillgängliga i Azure Security Center (ASC). Det finns en mängd olika aviseringar för många olika resurstyper. ASC genererar varningar för båda resurser som distribueras på Azure och resurser som distribueras lokalt och hybrid-miljöer i molnet. 

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?

Aviseringar är de meddelanden som Security Center genererar när hot på dina resurser. Den prioriterar och visar en lista över aviseringar tillsammans med den information som behövs att snabbt undersöka problemet. Security Center innehåller också rekommendationer för hur du kan avhjälpa angrepp.

## <a name="how-does-security-center-detect-threats"></a>Hur identifierar hot i Security Center?

För att identifiera verkliga hot och minska falska positiva identifieringar, Security Center samlar in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandvägg och slutpunktsskyddslösningar. Security Center analyserar den här informationen korrelerar ofta information från flera källor för att identifiera hot.

ASC övervakar resurser om distribueras på Azure eller distribuerats andra lokalt och hybrid-miljöer i molnet. Läs mer om att identifiera och svara på hot i [hur Security Center identifierar och svarar på hot](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Security aviseringstyper

I följande avsnitt får du via de olika ASC aviseringarna enligt resurstyper:

* [Aviseringar för virtuella IaaS-datorer och servrar](security-center-alerts-iaas.md)
* [Interna beräkningsfunktioner aviseringar](security-center-alerts-compute.md)
* [Data services-aviseringar](security-center-alerts-data-services.md)

I följande avsnitt förklarar hur Security Center använder olika telemetrin som det samlar in från integrera med Azure-infrastrukturen för att kunna tillämpa ytterligare skydd lager för resurser som distribueras på Azure:

* [Tjänstvarningar med lager](security-center-alerts-service-layer.md)
* [Integrering med Azure Security-produkter](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Vad är aviseringar incidenter?

En säkerhetsincident är en samling relaterade aviseringar så i stället för att visa en lista över varje avisering individuellt. Security Center använder fusion för korrelation olika aviseringar och otillförlitliga signaler i säkerhetsincidenter.

Med incidenter kan ger Security Center dig en vy av en attackkampanj och alla relaterade aviseringar. Den här vyn kan du snabbt kan förstå vilka åtgärder angriparen vidtog och vilka resurser som har påverkats. Mer information finns i [molnet Smart Incidenthanteringssystem](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Kom igång med aviseringar

Se följande avsnitt kommer du lär dig mer om de resurser som övervakas av ASC, och riktlinjer för hur du svarar på aviseringar som presenteras av ASC.

* Om du vill se vilka plattformar och funktioner som är skyddade av ASC, se [plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md).  
* Information om vad är säkerhetsincidenter och hur ASC reagerar på dem finns i [hur du hanterar säkerhetsincidenter i Azure Security Center](security-center-incident.md). 
* Läs hur du hanterar aviseringar som du får i [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).
* Verifiera information på hur Security Center är korrekt konfigurerad och för att främja en avisering om test av Se [aviseringar verifiering i Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Uppgradera till Standard för avancerad identifiering rekommenderar vi

För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center Standard. 

1. Security Center-menyn väljer **säkerhetsprincip**.
2. För prenumerationer som du vill flytta till Standard-nivån, klickar du på **redigera inställningar för**. 
3. På sidan Inställningar väljer **prisnivå**. 
   En kostnadsfri utvärderingsversion är tillgänglig i en månad. Läs mer i den [prissättningssidan](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs vad är säkerhetsaviseringar och olika typer av aviseringar som är tillgängliga i Security Center. Mer information finns i följande avsnitt:

* [Planerings- och bruksanvisning för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Vanliga frågor om Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq): Här finns vanliga frågor om att använda tjänsten.

