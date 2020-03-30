---
title: Ansluta checkpunktsdata till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Check Point-data till Azure Sentinel.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588424"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Anslut kontrollpunkt till Azure Sentinel



I den här artikeln beskrivs hur du ansluter Check Point-enheten till Azure Sentinel. Med Check Point-datakopplingen kan du enkelt ansluta dina Check Point-loggar med Azure Sentinel, för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Check Point på Azure Sentinel får du mer information om organisationens Internetanvändning och förbättrar dess säkerhetsfunktioner. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Check point-loggar framåt till Syslog-agenten

Konfigurera Check Point-enheten för att vidarebefordra Syslog-meddelanden i CEF-format till din Azure-arbetsyta via Syslog-agenten.

1. Gå till [Export av kontrollpunktsloggen](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Bläddra ned till **Grundläggande distribution** och följ instruktionerna för att konfigurera anslutningen med hjälp av följande riktlinjer:
   - Ställ in **Syslog-porten** på **514** eller den port som du anger på agenten.
     - Ersätt **namn-** och **målserver-IP-adressen** i CLI med Syslog-agentens namn och IP-adress.
     - Ange formatet till **FSE**.
1. Om du använder version R77.30 eller R80.10 bläddrar du upp till **Installationer** och följer instruktionerna för att installera en loggexportör för din version.
1. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).
 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Check Point-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- [Verifiera anslutningen](connect-cef-verify.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


