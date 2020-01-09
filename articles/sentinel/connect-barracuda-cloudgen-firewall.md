---
title: Anslut Barracuda CloudGen-brandväggen till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Barracuda CloudGen-brandväggen till Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: cabailey
ms.openlocfilehash: f7d5531c8aba3e2e7abb56d82342bfda79dbc71b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610734"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Ansluta Barracuda CloudGen-brandväggen

Med CGFW-anslutningsprogrammet (Barracuda CloudGen Firewall) kan du enkelt ansluta dina Barracuda CGFW-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig bättre insikt i din organisations nätverk och förbättrar dina säkerhets åtgärder.




## <a name="prerequisites"></a>Krav

- Läs-och skriv behörigheter till Azure Sentinel-arbetsytan.

- Barracuda CloudGen-brandväggen måste konfigureras för att exportera loggar via syslog.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Ansluta Azure Sentinel till Barracuda CloudGen-brandväggen

1. I Azure Portal går du till **Azure Sentinel** > **data Connectors** och väljer sedan **Barracuda CloudGen Firewall** Connector.

2. Välj **Öppna kopplings sida**.

3. Följ anvisningarna på sidan **Barracuda CloudGen Firewall** .


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Barracuda CloudGen-brandväggen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


