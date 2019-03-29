---
title: Samla in data för Windows-brandväggen i förhandsversionen av Azure Sentinel | Microsoft Docs
description: Lär dig mer om att samla in data för Windows-brandväggen i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3839d81f70b8bc6dcb1da3c4dd77f52443294707
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574848"
---
# <a name="connect-windows-firewall"></a>Ansluta till Windows-brandvägg

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connector för Windows-brandväggen kan du enkelt ansluta dina brandväggar loggar Windows om de är anslutna till din Azure Sentinel-arbetsyta. Den här anslutningen kan du visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden.  


> [!NOTE]
> 
> - Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="enable-the-connector"></a>Aktivera anslutningsprogrammet 

1. I Sentinel-Azure-portalen väljer **datainsamling** och klicka sedan på den **Windows-brandväggen** panelen. 
1. Välj vilka datatyper som du vill spela.
1. Klicka på **Installera**.
6. Om du vill använda relevanta schemat i Log Analytics för Windows-brandväggen, Sök efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows-brandväggen till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

