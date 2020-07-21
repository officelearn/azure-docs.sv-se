---
title: Anslut Illusive attack Management Systems-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Illusive angrepps hanterings system data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532167"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Anslut ditt Illusive-angrepps hanterings system till Azure Sentinel

Den här artikeln förklarar hur du ansluter ditt [Illusive-attack hanterings system](https://www.illusivenetworks.com/technology/platform/attack-detection-system) till Azure Sentinel. Med data anslutnings verktyget Illusive attack Management Systems kan du dela Illusives data och incident loggar med Azure Sentinel och visa den här informationen i dedikerade instrument paneler som ger inblick i din organisations risk för attack ytan (ASM-instrumentpanel) och spårar obehörig lateral förflyttning i din organisations nätverk (ADS instrument panel).

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Vidarebefordra Illusive attack Management System-loggar till syslog-agenten  

Konfigurera angrepps hanterings system för att vidarebefordra syslog-meddelanden i CEF-format till Azure-arbetsytan via syslog-agenten.

1. Logga in på Illusive-konsolen och gå till inställningar->rapportering.

1. Hitta syslog-Serversץ

1. Ange följande information:
   - Värdnamn: Linux syslog-agentens IP-adress eller FQDN-värdnamn
   - Port: 514
   - Protokoll: TCP
   - Gransknings meddelanden: skicka gransknings meddelanden till servern

1. Klicka på Lägg till om du vill lägga till syslog-servern.

1. Om du vill använda det relevanta schemat i Log Analytics för Illusive attack Management Systems söker du efter CommonSecurityLog.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Illusive attack Management system till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
