---
title: Anslut Zscaler-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Zscaler-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587999"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Ansluta Zscaler Internet Access till Azure Sentinel

> [!IMPORTANT]
> Zscaler-dataanslutningen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln beskrivs hur du ansluter Zscaler Internet Access-installationen till Azure Sentinel. Med Zscaler-dataanslutningen kan du enkelt ansluta dina Zscaler Internet Access (ZIA) loggar med Azure Sentinel, för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Zscaler på Azure Sentinel får du mer information om organisationens Internetanvändning och förbättrar dess säkerhetsfunktioner. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Konfigurera din Zscaler för att skicka CEF-meddelanden

1. På Zscaler-apparaten måste du ange dessa värden så att installationen skickar nödvändiga loggar i det format som krävs till Azure Sentinel Syslog-agenten, baserat på Log Analytics-agenten. Du kan ändra dessa parametrar i din installation, så länge du också ändrar dem i Syslog-demonen på Azure Sentinel-agenten.
    - Protokoll = TCP
    - Port = 514
    - Format = FSE
    - IP-adress - se till att skicka FSE-meddelanden till IP-adressen för den virtuella datorn du dedicerat för detta ändamål.
 Mer information finns i [distributionsguiden för Zscaler och Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Den här lösningen stöder Syslog RFC 3164 eller RFC 5424.


1. Om du vill använda det relevanta schemat i `CommonSecurityLog`Log Analytics för CEF-händelser söker du efter .
1. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Zscaler Internet Access till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


