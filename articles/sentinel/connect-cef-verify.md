---
title: Verifiera anslutningen till Azure Sentinel | Microsoft Docs
description: Verifiera anslutningen till din säkerhetslösning för att se till att CEF-meddelanden vidarebefordras till Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588441"
---
# <a name="step-3-validate-connectivity"></a>STEG 3: verifiera anslutningen



När du har distribuerat agenten och konfigurerat din säkerhetslösning för att vidarebefordra CEF-meddelanden kan du använda den här artikeln för att förstå hur du verifierar anslutningen mellan Azure Sentinel och din säkerhetslösning. 

## <a name="how-to-validate-connectivity"></a>Så här verifierar du anslutningen

1. Öppna Log Analytics för att se till att loggarna tas emot med CommonSecurityLog-schemat.<br> Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

1. Innan du kör skriptet rekommenderar vi att du skickar meddelanden från din säkerhetslösning för att se till att de vidarebefordras till den syslog-proxyserver som du har konfigurerat. 
1. Du måste ha förhöjd behörighet (sudo) på datorn. Se till att du har python på datorn med hjälp av följande kommando: `python –version`
1. Kör följande skript för att kontrol lera anslutningen mellan agenten, Azure Sentinel och din säkerhets lösning. Den kontrollerar att daemon-vidarebefordran har kon figurer ATS korrekt, lyssnar på rätt portar och att ingenting blockerar kommunikationen mellan daemonen och den Log Analytics agenten. Skriptet skickar även de blå meddelandena "TestCommonEventFormat" för att kontrol lera anslutning från slut punkt till slut punkt. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

