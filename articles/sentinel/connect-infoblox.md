---
title: Anslut Infoblox NIOS-data (Network Identity Opera ting system) till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Infoblox NIOS-data (Network Identity Opera ting system) till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532174"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Anslut din Infoblox-NIOS till Azure Sentinel

Den här artikeln förklarar hur du ansluter din [Nios-installation (Infoblox Network Identity Opera ting system)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) till Azure Sentinel. Med Infoblox NIOS data Connector kan du enkelt ansluta dina Infoblox-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Infoblox NIOS och Azure Sentinel använder syslog.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Vidarebefordra Infoblox-loggar till syslog-agenten  

Konfigurera Infoblox så att syslog-meddelanden vidarebefordras till din Azure-arbetsyta via syslog-agenten.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Infoblox Nios** Connector.

1. Välj **Öppna kopplings sida**.

1. Följ instruktionerna på sidan **INFOBLOX Nios** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under syslog.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Infoblox-NIOS till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.