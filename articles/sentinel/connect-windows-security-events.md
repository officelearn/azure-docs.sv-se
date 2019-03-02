---
title: Samla in händelsedata för Windows-säkerhet i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig hur du samlar in händelsedata för Windows-säkerhet i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 270254ae4f66fa3642df844f384e15564945628e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242164"
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

>[!NOTE]

> - Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurera Windows security händelser connector

Att helt integrera dina säkerhetshändelser i Windows med Azure Sentinel:

1. I Sentinel-Azure-portalen väljer **datainsamling** och klicka sedan på den **Windows säkerhetshändelser** panelen. 
1. Välj vilka datatyper som du vill spela.
1. Klicka på **Uppdatera**.


## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta cirka 20 minuter tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows säkerhetshändelser till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

