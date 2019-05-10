---
title: Ansluta Windows security händelsedata till Sentinel-förhandsversion i Azure | Microsoft Docs
description: Lär dig hur du ansluter Windows security händelsedata till Sentinel-Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 88b066818d53fd92e8238e270b9bc785d4275186
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233949"
---
# <a name="connect-windows-security-events"></a>Ansluta till Windows säkerhetshändelser 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma alla säkerhetshändelser från Windows-servrar som ansluter till din Azure Sentinel-arbetsyta. Den här anslutningen kan du visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden.  Du kan välja vilka händelser att strömma:

- **Alla händelser** -alla Windows säkerhets- och AppLocker-händelser.
- **Vanliga** – en standarduppsättning av händelser i granskningssyfte.
- **Minimal** – en liten uppsättning händelser som kan tyda på potentiella hot. Genom att aktivera det här alternativet kan du inte har fullständiga spårningen.
- **Ingen** -inga säkerhets- eller AppLocker-händelser.

> [!NOTE]
> 
> - Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurera Windows security händelser connector

Att helt integrera dina säkerhetshändelser i Windows med Azure Sentinel:

1. I Sentinel-Azure-portalen väljer **datakopplingar** och klicka sedan på den **Windows säkerhetshändelser** panelen. 
1. Välj vilka datatyper som du vill spela.
1. Klicka på **Uppdatera**.
6. Om du vill använda relevanta schema i Log Analytics för Windows-säkerhetshändelser, Sök efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta cirka 20 minuter tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows säkerhetshändelser till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

