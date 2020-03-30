---
title: Anslut Barracuda-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Barracuda-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588492"
---
# <a name="connect-your-barracuda-appliance"></a>Anslut din Barracuda-apparat 



Med WAF-anslutningen (Barracuda Web Application Firewall) kan du enkelt ansluta dina Barracuda-loggar till din Azure Sentinel, för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer insikt i organisationens nätverk och förbättrar dina funktioner för säkerhetsdrift. Azure Sentinel utnyttjar den inbyggda integrationen mellan **Barracuda** och Log Analytics-agenten för att tillhandahålla sömlös integrering. 


> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurera och ansluta Barracuda WAF
Barracuda Web Application Firewall kan integrera och exportera loggar direkt till Azure Sentinel via Log Analytics-agent.
1. Gå till [Barracuda WAF konfigurationsflöde](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), och följ instruktionerna för att ställa in anslutningen, med hjälp av dessa parametrar:
    - **Arbetsområdes-ID:** kopiera värdet för ditt arbetsyte-ID från azure Sentinel Barracuda-anslutningssidan.
    - **Primärnyckel:** kopiera värdet för din primärnyckel från azure Sentinel Barracuda-anslutningssidan.
1. Om du vill använda det relevanta schemat i Log Analytics för Barracuda-händelserna söker du efter **CommonSecurityLog** och **barracuda_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Barracuda-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


