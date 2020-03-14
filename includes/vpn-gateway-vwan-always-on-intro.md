---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371019"
---
En ny funktion i VPN-klienten för Windows 10, Always on, är möjligheten att underhålla en VPN-anslutning. Med Always on kan den aktiva VPN-profilen ansluta automatiskt och förbli ansluten baserat på utlösare, till exempel användar inloggning, ändring av nätverks tillstånd eller enhets skärmen aktiv.

Du kan använda Azures virtuella nätverks-gatewayer med Windows 10 Always on för att upprätta permanenta användar tunnlar och enhets tunnlar till Azure. Den här artikeln hjälper dig att konfigurera en Always on VPN-användar tunnel.

Always on VPN-anslutningar omfattar endera av två typer av tunnlar:

* **Enhets tunnel**: ansluter till angivna VPN-servrar innan användarna loggar in på enheten. I anslutnings scenarier för inloggning och enhets hantering används en enhets tunnel.

* **Användar tunnel**: ansluter bara när användare loggar in på enheten. Med hjälp av användar tunnlar kan du få åtkomst till organisations resurser via VPN-servrar.

Enhets tunnlar och användar tunnlar drivs oberoende av deras VPN-profiler. De kan vara anslutna samtidigt, och de kan använda olika autentiseringsmetoder och andra inställningar för VPN-konfigurationen.
