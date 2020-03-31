---
title: Distribuera agenten för att ansluta FSE-data till Azure Sentinel Preview| Microsoft-dokument
description: Lär dig hur du distribuerar agenten för att ansluta CEF-data till Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588475"
---
# <a name="step-1-deploy-the-agent"></a>Steg 1: Distribuera agenten


I det här steget måste du välja den Linux-dator som ska fungera som en proxy mellan Azure Sentinel och din säkerhetslösning. Du måste köra ett skript på proxydatorn som:
- Installerar Log Analytics-agenten och konfigurerar den efter behov för att lyssna efter Syslog-meddelanden.
- Konfigurerar Syslog-demonen för att lyssna på Syslog-meddelanden med TCP-port 514 och vidarebefordrar sedan endast CEF-meddelandena till Log Analytics-agenten med TCP-port 25226.
- Ställer in Syslog-agenten för att samla in data och skicka dem säkert till Azure Sentinel, där den tolkas och berikas.
 
## <a name="deploy-the-agent"></a>Distribuera agenten
 
1. I Azure Sentinel-portalen klickar du på **Datakopplingar** och väljer **Cef (Common Event Format)** och sedan **öppna kopplingssidan**. 

1. Under **Installera och konfigurera Syslog-agenten**väljer du din datortyp, antingen Azure, andra moln eller lokalt. 
   > [!NOTE]
   > Eftersom skriptet i nästa steg installerar Log Analytics-agenten och ansluter datorn till din Azure Sentinel-arbetsyta kontrollerar du att den här datorn inte är ansluten till någon annan arbetsyta.
1. Du måste ha förhöjda behörigheter (sudo) på din maskin. Kontrollera att du har Python på datorn med följande kommando:`python –version`

1. Kör följande skript på proxydatorn.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Kontrollera att du inte får några felmeddelanden eller varningsmeddelanden när skriptet körs.

Fortsätt till [STEG 2: Konfigurera säkerhetslösningen för att vidarebefordra CEF-meddelanden](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

