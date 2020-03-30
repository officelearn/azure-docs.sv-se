---
title: Verifiera anslutning till Azure Sentinel| Microsoft-dokument
description: Verifiera anslutningen av din säkerhetslösning för att se till att FSE-meddelanden vidarebefordras till Azure Sentinel.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588441"
---
# <a name="step-3-validate-connectivity"></a>STEG 3: Validera anslutningen



När du har distribuerat agenten och konfigurerat säkerhetslösningen för att vidarebefordra CEF-meddelanden använder du den här artikeln för att förstå hur du verifierar anslutningen mellan Azure Sentinel och din säkerhetslösning. 

## <a name="how-to-validate-connectivity"></a>Så här validerar du anslutningen

1. Öppna Logganalys för att se till att loggar tas emot med commonsecuritylog-schemat.<br> Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics. 

1. Innan du kör skriptet rekommenderar vi att du skickar meddelanden från säkerhetslösningen för att se till att de vidarebefordras till den Syslog-proxydator som du har konfigurerat. 
1. Du måste ha förhöjda behörigheter (sudo) på din maskin. Kontrollera att du har Python på datorn med följande kommando:`python –version`
1. Kör följande skript för att kontrollera anslutningen mellan agenten, Azure Sentinel och din säkerhetslösning. Den kontrollerar att demon vidarebefordran är korrekt konfigurerad, lyssnar på rätt portar, och att ingenting blockerar kommunikationen mellan demonen och Log Analytics-agenten. Skriptet skickar också utkast meddelanden "TestCommonEventFormat" för att kontrollera end-to-end-anslutning. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

