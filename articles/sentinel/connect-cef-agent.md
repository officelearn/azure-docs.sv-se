---
title: Distribuera agenten för att ansluta CEF-data till Azure Sentinel Preview | Microsoft Docs
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588475"
---
# <a name="step-1-deploy-the-agent"></a>Steg 1: Distribuera agenten


I det här steget måste du välja den Linux-dator som ska fungera som en proxy mellan Azure Sentinel och din säkerhets lösning. Du måste köra ett skript på den proxyserver som:
- Installerar den Log Analytics agenten och konfigurerar den efter behov för att lyssna efter syslog-meddelanden.
- Konfigurerar syslog-daemonen att lyssna på syslog-meddelanden med TCP-port 514 och vidarebefordrar sedan endast CEF-meddelanden till Log Analytics agent med TCP-port 25226.
- Ställer in syslog-agenten för att samla in data och skicka dem säkert till Azure Sentinel, där det är parsat och berikat.
 
## <a name="deploy-the-agent"></a>Distribuera agenten
 
1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **vanligt händelse format (CEF)** och **öppnar sedan kopplings sidan**. 

1. Under **Installera och konfigurera syslog-agenten**väljer du dator typ, antingen Azure, annat moln eller lokalt. 
   > [!NOTE]
   > Eftersom skriptet i nästa steg installerar Log Analytics agenten och ansluter datorn till din Azure Sentinel-arbetsyta, kontrol lera att datorn inte är ansluten till någon annan arbets yta.
1. Du måste ha förhöjd behörighet (sudo) på datorn. Se till att du har python på datorn med hjälp av följande kommando: `python –version`

1. Kör följande skript på din proxyserver.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Kontrol lera att du inte får några fel eller varnings meddelanden medan skriptet körs.

Fortsätt till [steg 2: Konfigurera säkerhets lösningen för att vidarebefordra CEF-meddelanden](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

