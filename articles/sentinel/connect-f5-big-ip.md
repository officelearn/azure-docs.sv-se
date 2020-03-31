---
title: Anslut F5 BIG-IP-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter F5 BIG-IP-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588288"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Anslut din F5 BIG-IP-apparat 

> [!IMPORTANT]
> F5 BIG-IP-dataanslutningen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med F5 BIG-IP-anslutningen kan du enkelt ansluta alla dina F5 BIG-IP-loggar med Azure Sentinel, visa arbetsböcker, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer insikt i organisationens nätverk och förbättrar dina funktioner för säkerhetsdrift. Integrering mellan F5 BIG-IP och Azure Sentinel använder REST API.


> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Konfigurera och ansluta F5 BIG-IP 

F5 BIG-IP kan integrera och exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **Datakopplingar** och väljer **F5 BIG-IP** och sedan **öppna kopplingssidan**. 
1. Om du vill ansluta din F5 BIG-IP måste du lägga upp en JSON-deklaration till systemets API-slutpunkt. Instruktioner om hur du gör detta finns i [Integrera F5 BIG-IP med Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Från F5 BIG-IP-anslutningssidan kopierar du arbetsyte-ID:t och primärnyckeln och klistrar in dem enligt anvisningarna under [Strömmande data till Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. När du har slutfört F5 BIG-IP-instruktionerna visas de anslutna datatyperna på sidan Azure Sentinel-anslutning.
1. Om du vill använda det relevanta schemat i Log Analytics för F5 BIG-IP-händelserna söker du efter **F5Telemetry_LTM_CL,** **F5Telemetry_system_CL**och **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter F5 BIG-IP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


