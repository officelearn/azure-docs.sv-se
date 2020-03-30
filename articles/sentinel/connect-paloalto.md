---
title: Anslut Palo Alto Networks-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Palo Alto Networks-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588135"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Ansluta Palo Alto-nätverk till Azure Sentinel



I den här artikeln beskrivs hur du ansluter din Palo Alto Networks-installation till Azure Sentinel. Med Dataanslutningen Palo Alto Networks kan du enkelt ansluta dina Palo Alto-nätverksloggar med Azure Sentinel, visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Palo Alto-nätverk på Azure Sentinel får du mer information om organisationens Internetanvändning och förbättrar dess säkerhetsfunktioner. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Vidarebefordra Palo Alto-nätverk loggar till Syslog-agenten

Konfigurera Palo Alto-nätverk för att vidarebefordra Syslog-meddelanden i CEF-format till din Azure-arbetsyta via Syslog-agenten:
1.  Gå till [konfigurationsguider för common event format (CEF)](https://docs.paloaltonetworks.com/resources/cef) och ladda ned pdf-filen för din typ av apparat. Följ alla instruktioner i guiden för att ställa in din Palo Alto Networks-apparat för att samla in CEF-händelser. 

1.  Gå till [Konfigurera Syslog-övervakning](https://aka.ms/asi-syslog-paloalto-forwarding) och följ steg 2 och 3 för att konfigurera CEF-händelsebefordring från palo alto-nätverksinstallationen till Azure Sentinel.

    1. Se till att ange **Syslog-serverformatet** till **BSD**.

       > [!NOTE]
       > Kopierings-/inklistringsåtgärderna från PDF-filen kan ändra texten och infoga slumpmässiga tecken. Undvik detta genom att kopiera texten till en redigerare och ta bort alla tecken som kan bryta loggformatet innan du klistrar in den, som du kan se i det här exemplet.
 
        ![FSE textkopiering problem](./media/connect-cef/paloalto-text-prob1.png)

1. Om du vill använda det relevanta schemat i Logganalys för palo altonätverkshändelserna söker du efter **CommonSecurityLog**.

1. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Palo Alto Networks-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


