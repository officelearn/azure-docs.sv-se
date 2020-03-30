---
title: Anslut Fortinet-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Fortinet-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588203"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Anslut Fortinet till Azure Sentinel



I den här artikeln beskrivs hur du ansluter Din Fortinet-installation till Azure Sentinel. Med Fortinet-datakopplingen kan du enkelt ansluta dina Fortinet-loggar med Azure Sentinel, visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Fortinet på Azure Sentinel får du mer information om organisationens Internetanvändning och förbättrar dess säkerhetsfunktioner. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Vidarebefordra Fortinet-loggar till Syslog-agenten

Konfigurera Fortinet för att vidarebefordra Syslog-meddelanden i CEF-format till din Azure-arbetsyta via Syslog-agenten.

1. Öppna CLI på din Fortinet-apparat och kör följande kommandon:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Ersätt **serverns IP-adress** med agentens IP-adress.
    - Ställ in **syslogporten** på **514** eller porten som ställts in på agenten.
    - Om du vill aktivera CEF-format i tidiga FortiOS-versioner kan du behöva köra kommandouppsättningen **csv inaktivera**.
 
   > [!NOTE] 
   > Mer information finns i [Dokumentbiblioteket i Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Välj din version och använd **handboken** och **loggmeddelandereferensen**.

1. Om du vill använda det relevanta schemat i Azure Monitor `CommonSecurityLog`Log Analytics för Fortinet-händelserna söker du efter .

1. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du ansluter Fortinet-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


