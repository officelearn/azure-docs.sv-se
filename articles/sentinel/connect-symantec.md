---
title: Anslut Symantec ICDX data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Symantec ICDX data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 0410b052f17a868aed70ce407b9c9fdefbe023df
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233632"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Ansluta din Symantec ICDX-installation 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDX-anslutningsappen kan du enkelt ansluta alla dina säkerhetslösningen för Symantec med din Azure Sentinel, att visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden. Integreringen mellan Symantec ICDX och Azure Sentinel gör användning av REST API.


> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurera och ansluta Symantec ICDX 

Symantec ICDX kan integrera och exportloggar direkt till Azure Sentinel.

1. Öppna hanteringskonsolen för ICDX.
2. Välj i den vänstra navigeringsmenyn **Configuration** och sedan den **vidarebefordrare** fliken.
3. I Microsoft Azure Log Analytics-raden klickar du på **mer**, följt av **redigera**. 
4. I den **vidarebefordrare för Microsoft Azure Log Analytics** egenskapsfönstret anger du följande:
    - Lämna anpassat loggnamn som standard, SymantecICDX.
    - Kopiera arbetsytans ID och klistra in den i den **Kundidentifierare** fält. Kopiera den **primärnyckel** och klistra in den i fältet delad nyckel. Du kan kopiera dessa värden från Sentinel-Azure-portalen genom att välja **datakopplingar** och sedan **Symantec ICDX**.
6. Om du vill använda relevanta schemat i Log Analytics för Symantec ICDX-händelser, söka efter **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Symantec ICDX till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

