---
title: Ansluta F5-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter F5-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588271"
---
# <a name="connect-f5-to-azure-sentinel"></a>Ansluta F5 till Azure Sentinel

I den här artikeln beskrivs hur du ansluter F5-installationen till Azure Sentinel. Med F5-dataanslutningen kan du enkelt ansluta dina F5-loggar med Azure Sentinel, visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda F5 på Azure Sentinel får du mer information om organisationens Internetanvändning och förbättrar dess säkerhetsfunktioner. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Konfigurera F5 för att skicka CEF-meddelanden

1. Gå till [F5 Konfigurera loggning av programsäkerhetshändelse](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)och följ instruktionerna för att konfigurera fjärrloggning med hjälp av följande riktlinjer:
   - Ställ in **fjärrlagringstypen** på **FSE**.
   - Ställ in **protokollet** på **TCP**.
   - Ange **IP-adressen** till SYSlog-serverns IP-adress.
   - Ställ in **portnumret** till **514**eller den port som du ställer in agenten på.
   - Du kan ange maximal **frågesträngstorlek** till den storlek som du anger i agenten.

1. Om du vill använda det relevanta schemat i `CommonSecurityLog`Log Analytics för CEF-händelser söker du efter .

1. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter F5 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

