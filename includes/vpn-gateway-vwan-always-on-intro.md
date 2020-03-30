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
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116923"
---
En ny funktion i Windows 10 VPN-klienten, Always On, är möjligheten att upprätthålla en VPN-anslutning. Med Always On kan den aktiva VPN-profilen ansluta automatiskt och förbli ansluten baserat på utlösare, till exempel användarinloggning, ändring av nätverkstillstånd eller enhetsskärmen aktiv.

Du kan använda gateways med Windows 10 Always On för att upprätta beständiga användartunnlar och enhetstunnlar till Azure. Den här artikeln hjälper dig att konfigurera en Alltid på VPN-användare tunnel.

Alltid på VPN-anslutningar innehåller någon av två typer av tunnlar:

* **Enhetstunnel:** Ansluter till angivna VPN-servrar innan användare loggar in på enheten. Förinstallera anslutningsscenarier och enhetshantering använder en enhetstunnel.

* **Användartunnel:** Ansluts först när användare har loggat in på enheten. Genom att använda användartunnlar kan du komma åt organisationsresurser via VPN-servrar.

Enhetstunnlar och användartunnlar fungerar oberoende av sina VPN-profiler. De kan anslutas samtidigt, och de kan använda olika autentiseringsmetoder och andra VPN-konfigurationsinställningar, beroende på vad som är lämpligt.
