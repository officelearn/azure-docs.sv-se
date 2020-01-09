---
title: Anslut F5-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter F5-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: cf6dc6977ff066b646beac4db5562ae8d97ab066
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610564"
---
# <a name="connect-f5-to-azure-sentinel"></a>Anslut F5 till Azure Sentinel

Den här artikeln förklarar hur du ansluter din F5-enhet till Azure Sentinel. Med F5-datakopplingen kan du enkelt ansluta dina F5-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda F5 i Azure Sentinel får du mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Konfigurera din F5 för att skicka CEF-meddelanden

1. Gå till [F5 Konfigurera loggning av program säkerhets händelser](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)och följ anvisningarna för att konfigurera fjärrloggning med hjälp av följande rikt linjer:
   - Ange **typen av Fjärrlagring** till **CEF**.
   - Ställ in **protokollet** på **TCP**.
   - Ange **IP-adressen** till syslog-serverns IP-adress.
   - Ange **port numret** till **514**, eller den port som du anger att agenten ska använda.
   - Du kan ange max storleken för **frågesträngen** till den storlek som du har angett i din agent.

1. Om du vill använda det relevanta schemat i Log Analytics för CEF-händelserna söker du efter `CommonSecurityLog`.

1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter F5 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

