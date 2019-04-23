---
title: Anslut Barracuda data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Barracuda data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8e92fd3918230b48449926dcbb7528d919fd96f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798238"
---
# <a name="connect-your-barracuda-appliance"></a>Ansluta din Barracuda-installation 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Barracuda Web Application Firewall (WAF)-anslutningsappen kan du enkelt ansluta dina Barracuda-loggar med din Azure Sentinel, att visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden. Azure Sentinel drar nytta av den interna integreringen mellan **Barracuda** och Microsoft Azure OMS och ger sömlös integrering. 


> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurera och ansluta Barracuda WAF
Barracuda Web Application Firewall kan integrera och exportloggar direkt till Azure Sentinel via Azure OMS-servern.
1. Gå till [Barracuda WAF configuration flow](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), och följ anvisningarna för att upprätta anslutningen med hjälp av dessa parametrar:
    - **Arbetsyte-ID**: Kopiera värdet för arbetsyte-ID från sidan Azure Sentinel Barracuda connector.
    - **Primär nyckel**: Kopiera värdet för den primära nyckeln från sidan Azure Sentinel Barracuda connector.
2. Sentinel-Azure-portalen går du till arbetsytan där du har distribuerat Azure Sentinel och väljer ellipsen (...) i slutet av raden och välj **avancerade inställningar**. 
1. Välj **Data** och sedan **Syslog**.
1. Kontrollera att funktionen som du anger i Barracuda finns och ange allvarlighetsgraden och klicka på **spara**.
6. Om du vill använda relevanta schemat i Log Analytics för Barracuda-händelser, söka efter **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Barracuda installationer till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

