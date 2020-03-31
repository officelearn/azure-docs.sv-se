---
title: Anslut Barracuda CloudGen-brandväggen till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Barracuda CloudGen-brandväggen till Azure Sentinel.
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
ms.openlocfilehash: aaedbfdd3b1bbbc653756d74ee86fc277b21caec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588509"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Ansluta Barracuda CloudGen-brandväggen

Med CGFW-anslutningen (Barracuda CloudGen Firewall) kan du enkelt ansluta dina Barracuda CGFW-loggar med Azure Sentinel, för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer insikt i organisationens nätverk och förbättrar dina funktioner för säkerhetsdrift.




## <a name="prerequisites"></a>Krav

- Läs- och skrivbehörigheter till Arbetsytan Azure Sentinel.

- Barracuda CloudGen-brandväggen måste vara konfigurerad för att exportera loggar via Syslog.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Ansluta Azure Sentinel till Barracuda CloudGen-brandväggen

1. I Azure-portalen navigerar du till **Azure Sentinel** > **Data-kopplingar** och väljer sedan **Barracuda CloudGen-brandväggskopplingen.**

2. Välj **Öppna kopplingssida**.

3. Följ instruktionerna på sidan **Barracuda CloudGen-brandvägg.**


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Barracuda CloudGen-brandväggen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


