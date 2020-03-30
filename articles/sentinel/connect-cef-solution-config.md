---
title: Konfigurera säkerhetslösningen för att ansluta CEF-data till Azure Sentinel Preview| Microsoft-dokument
description: Lär dig hur du konfigurerar din säkerhetslösning för att ansluta CEF-data till Azure Sentinel.
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588458"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STEG 2: Konfigurera din säkerhetslösning för att skicka CEF-meddelanden

I det här steget kommer du att utföra nödvändiga konfigurationsändringar på själva säkerhetslösningen för att skicka loggar till FSE-agenten.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurera en lösning med en anslutning

Om säkerhetslösningen redan har en befintlig anslutning använder du de anslutningsspecifika instruktionerna på följande sätt:

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 (andra)](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Konfigurera en annan lösning
Om det inte finns någon anslutning för din specifika säkerhetslösning använder du följande allmänna instruktioner för vidarebefordrande av loggar till FSE-agenten.

1. Gå till den specifika konfigurationsartikeln för steg om hur du konfigurerar din lösning för att skicka CEF-meddelanden. Om din lösning inte finns med i listan måste du ange dessa värden på den här enheten så att den skickar nödvändiga loggar i det format som krävs till Azure Sentinel Syslog-agenten, baserat på Log Analytics-agenten. Du kan ändra dessa parametrar i din installation, så länge du också ändrar dem i Syslog-demonen på Azure Sentinel-agenten.
    - Protokoll = TCP
    - Port = 514
    - Format = FSE
    - IP-adress - se till att skicka FSE-meddelanden till IP-adressen för den virtuella datorn du dedicerat för detta ändamål.

   > [!NOTE]
   > Den här lösningen stöder Syslog RFC 3164 eller RFC 5424.


1. Om du vill använda det relevanta schemat i `CommonSecurityLog`Log Analytics för CEF-händelser söker du efter .

1. Fortsätt till STEG 3: [validera anslutningen](connect-cef-verify.md).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

