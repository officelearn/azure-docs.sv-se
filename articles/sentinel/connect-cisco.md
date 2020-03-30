---
title: Anslut Cisco-data till Azure Sentinel| Microsoft-dokument
description: Läs om hur du ansluter Cisco-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588407"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Anslut Cisco ASA till Azure Sentinel



I den här artikeln beskrivs hur du ansluter din Cisco ASA-installation till Azure Sentinel. Med Cisco ASA-dataanslutningen kan du enkelt ansluta dina Cisco ASA-loggar med Azure Sentinel, för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Cisco ASA på Azure Sentinel får du mer information om organisationens Internetanvändning och förbättrar dess säkerhetsfunktioner. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Vidarebefordra Cisco ASA-loggar till Syslog-agenten

Cisco ASA stöder inte FSE, så loggarna skickas som Syslog och Azure Sentinel-agenten vet hur de ska tolkas som om de vore FSE-loggar. Konfigurera Cisco ASA för att vidarebefordra Syslog-meddelanden till din Azure-arbetsyta via Syslog-agenten:

1. Gå till [Skicka Syslog-meddelanden till en extern Syslog-server](https://aka.ms/asi-syslog-cisco-forwarding)och följ instruktionerna för att konfigurera anslutningen. Använd dessa parametrar när du uppmanas:
    - Ställ in **porten** på 514 eller porten som du anger i agenten.
    - Ange **syslog_ip** till agentens IP-adress.

1. Om du vill använda det relevanta schemat i `CommonSecurityLog`Log Analytics för Cisco-händelserna söker du efter .

1. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Cisco ASA-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


