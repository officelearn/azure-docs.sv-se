---
title: Anslut Forcepoint-produkter till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Forcepoint-produkter till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588237"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Ansluta dina Forcepoint-produkter till Azure Sentinel

> [!IMPORTANT]
> Forcepoint-produktdataappen i Azure Sentinel är för närvarande i offentlig förhandsversion. Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


I den här artikeln beskrivs hur du ansluter dina Forcepoint-produkter till Azure Sentinel. 

Med Powerpoint-dataanslutningarna kan du ansluta Powerpoint Cloud Access Security Broker och Forcepoint Next Generation Firewall loggar med Azure Sentinel. På så sätt kan du automatiskt exportera användardefinierade loggar till Azure Sentinel i realtid. Kopplingen ger dig berikad insyn i användaraktiviteter som registrerats av Forcepoint-produkter. Det möjliggör också ytterligare korrelation med data från Azure-arbetsbelastningar och andra feeds och förbättrar övervakningsfunktionen med arbetsböcker i Azure Sentinel.

> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Vidarebefordra Forcepoint-produktloggar till Syslog-agenten 

Konfigurera Forcepoint-produkten för att vidarebefordra Syslog-meddelanden i CEF-format till din Azure-arbetsyta via Syslog-agenten.

1. Konfigurera Forcepoint-produkten till Azure Sentinel-integrering enligt beskrivningen i följande installationsguider:
 - [Integrationsguide för Forcepoint CASB](https://frcpnt.com/casb-sentinel)
 - [Integrationsguide för Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Sök efter CommonSecurityLog om du vill använda det relevanta schemat i Log Analytics med DeviceVendor-namnet innehåller 'Forcepoint'. 

3. Fortsätt till [STEG 3: Validera anslutningen](connect-cef-verify.md).



## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Forcepoint-produkter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).

- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.