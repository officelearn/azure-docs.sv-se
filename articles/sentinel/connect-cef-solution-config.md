---
title: Konfigurera din säkerhets lösning för att ansluta CEF-data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du konfigurerar din säkerhets lösning för att ansluta CEF-data till Azure Sentinel.
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
ms.openlocfilehash: e2ed3680a0867ab8f7e2ad41603883f07a4be427
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655756"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STEG 2: Konfigurera säkerhets lösningen för att skicka CEF-meddelanden

I det här steget ska du utföra nödvändiga konfigurations ändringar för själva säkerhetslösningen för att skicka loggar till CEF-agenten.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurera en lösning med en koppling

Om din säkerhetslösning redan har en befintlig koppling använder du de anslutningsspecifika instruktionerna på följande sätt:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Konfigurera andra lösningar

Om det inte finns någon koppling för din säkerhets lösning använder du följande allmänna instruktioner för att vidarebefordra loggar till CEF-agenten.

1. Gå till den speciella konfigurations artikeln för steg om hur du konfigurerar din lösning för att skicka CEF-meddelanden. Om din lösning inte finns med på den här enheten måste du ange dessa värden så att installations programmet skickar nödvändiga loggar i det format som krävs till Azure Sentinel syslog-agenten, baserat på Log Analytics agenten. Du kan ändra dessa parametrar i din installation så länge du också ändrar dem i syslog-daemonen på Azure Sentinel-agenten.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-adress – se till att skicka CEF-meddelanden till IP-adressen för den virtuella dator som du dedikerat för detta ändamål.

   > [!NOTE]
   > Den här lösningen stöder Syslog RFC 3164 eller RFC 5424.

1. Om du vill använda det relevanta schemat i Log Analytics för CEF-händelserna söker du efter `CommonSecurityLog` .

1. Fortsätt till steg 3: [Verifiera anslutningen](connect-cef-verify.md).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).