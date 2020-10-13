---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829033"
---
En ny funktion i VPN-klienten för Windows 10, Always on, är möjligheten att underhålla en VPN-anslutning. Med Always on kan den aktiva VPN-profilen ansluta automatiskt och förbli ansluten baserat på utlösare, till exempel användar inloggning, ändring av nätverks tillstånd eller enhets skärmen aktiv.

Du kan använda gatewayer med Windows 10 Always on för att upprätta permanenta användar tunnlar och enhets tunnlar till Azure.

Always on VPN-anslutningar omfattar endera av två typer av tunnlar:

* **Enhets tunnel**: ansluter till angivna VPN-servrar innan användarna loggar in på enheten. I anslutnings scenarier för inloggning och enhets hantering används en enhets tunnel.

* **Användar tunnel**: ansluter bara när användare loggar in på enheten. Med hjälp av användar tunnlar kan du få åtkomst till organisations resurser via VPN-servrar.

Enhets tunnlar och användar tunnlar drivs oberoende av deras VPN-profiler. De kan vara anslutna samtidigt, och de kan använda olika autentiseringsmetoder och andra inställningar för VPN-konfigurationen.
