---
title: Anslut kontroll punkts data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter kontroll punkts data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 94658e34679a4cf99ad0e1ad02e60c6a1d190361
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610683"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Anslut Check Point till Azure Sentinel



Den här artikeln förklarar hur du ansluter din Check Point-installation till Azure Sentinel. Med Check Point data Connector kan du enkelt ansluta dina check punkts loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Check Point på Azure Sentinel får du mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Vidarebefordra check punkts loggar till syslog-agenten

Konfigurera Check Point-enheten för att vidarebefordra syslog-meddelanden i CEF-format till din Azure-arbetsyta via syslog-agenten.

1. Gå till [Check Point log export](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Rulla ned till **grundläggande distribution** och följ anvisningarna för att konfigurera anslutningen med hjälp av följande rikt linjer:
   - Ställ in **syslog-porten** på **514** eller porten som du har angett för agenten.
     - Ersätt **namnet** och **mål serverns IP-adress** i CLI med syslog-AGENTens namn och IP-adress.
     - Ange formatet till **CEF**.
1. Om du använder version R 77.30 eller R 80.10 rullar du upp till **installationer** och följer anvisningarna för att installera en logg exportör för din version.
1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).
 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter kontroll punkts apparater till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- [Verifiera anslutningen](connect-cef-verify.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


