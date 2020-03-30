---
title: Vad är en skyddad virtuell hubb?
description: Läs mer om skyddade virtuella hubbar
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518442"
---
# <a name="what-is-a-secured-virtual-hub"></a>Vad är en skyddad virtuell hubb?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk som möjliggör anslutning från andra resurser. När en virtuell hubb skapas från ett virtuellt WAN i Azure-portalen skapas ett virtuellt nav-virtuellt virtuellt nätverk och gateways (valfritt) som dess komponenter.

En *säker* virtuell hubb är en [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) med tillhörande säkerhet och routningsprinciper som konfigurerats av Azure Firewall Manager. Använd säkra virtuella hubbar för att enkelt skapa hub-and-spoke och transitiva arkitekturer med inbyggda säkerhetstjänster för trafikstyrning och skydd. 

Du kan använda en säker virtuell hubb som ett hanterat centralt virtuellt virtuellt nätverk utan anslutningsanslutning på för-prem. Den ersätter det centrala virtuella nätverk som tidigare krävdes för en Azure-brandväggsdistribution. Eftersom det skyddade virtuella navet tillhandahåller automatisk routning behöver du inte konfigurera egna UDRs (användardefinierade vägar) för att dirigera trafik genom brandväggen.

Det är också möjligt att använda säkra virtuella hubbar som en del av en fullständig Virtual WAN-arkitektur. Den här arkitekturen ger säker, optimerad och automatiserad filialanslutning till och via Azure. Du kan välja tjänster för att skydda och styra din nätverkstrafik, inklusive Azure-brandväggen och andra tredjepartsleverantörer av säkerhet som en tjänst (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Skapa ett säkert virtuellt nav

Med Hjälp av Brandväggshanteraren i Azure-portalen kan du antingen skapa en ny säker virtuell hubb eller konvertera en befintlig virtuell hubb som du tidigare skapade med Azure Virtual WAN.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en säker virtuell hubb och använder den för att skydda och styra ett nav- och ekernätverk finns i [Självstudiekurs: Skydda ditt molnnätverk med Azure Firewall Manager med Azure-portalen](secure-cloud-network.md).