---
title: Ansluta Windows-brandväggen data till Azure Sentinel-förhandsgranskning | Microsoft Docs
description: Lär dig hur du ansluter Windows-brandväggen data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9388e267c52ef53b59aacad844e964d3cfeb13d7
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233810"
---
# <a name="connect-windows-firewall"></a>Ansluta till Windows-brandvägg

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connector för Windows-brandväggen kan du enkelt ansluta dina brandväggar loggar Windows om de är anslutna till din Azure Sentinel-arbetsyta. Den här anslutningen kan du visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden.  


> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="enable-the-connector"></a>Aktivera anslutningsprogrammet 

1. I Sentinel-Azure-portalen väljer **datakopplingar** och klicka sedan på den **Windows-brandväggen** panelen. 
1. Välj vilka datatyper som du vill spela.
1. Klicka på **Installera**.
6. Om du vill använda relevanta schemat i Log Analytics för Windows-brandväggen, Sök efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows-brandväggen till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

