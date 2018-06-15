---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3d76f955f8d8d3d2b269c09387717a6571adf8c3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197881"
---
Den virtuella nätverksgatewayen använder specifika undernät som kallas gateway-undernät. Gateway-undernätet ingår i det virtuella nätverkets IP-adressintervall som du anger när du konfigurerar det virtuella nätverket. Det innehåller de IP-adresser som gateway-resurserna och tjänsterna för det virtuella nätverket använder. Undernätet måste ha namnet ”GatewaySubnet' för att Azure ska kunna distribuera gateway-resurserna. Du kan inte ange ett annat undernät som gateway-resurserna ska distribueras till. Om du inte har ett undernät med namnet GatewaySubnet, kommer du inte kunna skapa din VPN-gateway.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. Hur många IP-adresser som behövs beror på vilken konfiguration av VPN-gatewayen som du vill skapa. Vissa konfigurationer kräver fler IP-adresser än andra. Vi rekommenderar att du skapar ett gateway-undernät som använder /27 eller /28.

Kontrollera ditt VNet-adressintervall om du ser ett fel som anger att adressutrymmet överlappar ett undernät, eller att undernätet inte ligger inom adressutrymmet för det virtuella nätverket. Du kanske inte har tillräckligt med tillgängliga IP-adresser i adressintervallet som du har skapat för ditt virtuella nätverk. Till exempel om ditt standardundernät omfattar hela adressintervallet, finns det inga IP-adresser kvar till att skapa extra undernät. Du kan antingen justera dina undernät i det befintliga adressutrymmet för att frigöra IP-adresser, eller ange ett ytterligare adressintervall och skapa gateway-undernätet där.