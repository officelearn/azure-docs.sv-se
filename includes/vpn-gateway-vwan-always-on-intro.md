---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80116923"
---
En ny funktion i VPN-klienten för Windows 10, Always on, är möjligheten att underhålla en VPN-anslutning. Med Always on kan den aktiva VPN-profilen ansluta automatiskt och förbli ansluten baserat på utlösare, till exempel användar inloggning, ändring av nätverks tillstånd eller enhets skärmen aktiv.

Du kan använda gatewayer med Windows 10 Always on för att upprätta permanenta användar tunnlar och enhets tunnlar till Azure. Den här artikeln hjälper dig att konfigurera en Always on VPN-användar tunnel.

Always on VPN-anslutningar omfattar endera av två typer av tunnlar:

* **Enhets tunnel**: ansluter till angivna VPN-servrar innan användarna loggar in på enheten. I anslutnings scenarier för inloggning och enhets hantering används en enhets tunnel.

* **Användar tunnel**: ansluter bara när användare loggar in på enheten. Med hjälp av användar tunnlar kan du få åtkomst till organisations resurser via VPN-servrar.

Enhets tunnlar och användar tunnlar drivs oberoende av deras VPN-profiler. De kan vara anslutna samtidigt, och de kan använda olika autentiseringsmetoder och andra inställningar för VPN-konfigurationen.
