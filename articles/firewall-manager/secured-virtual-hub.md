---
title: Vad är en säker virtuell hubb?
description: Läs mer om skyddade virtuella hubbar
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518442"
---
# <a name="what-is-a-secured-virtual-hub"></a>Vad är en säker virtuell hubb?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk som möjliggör anslutning från andra resurser. När en virtuell hubb skapas från ett virtuellt WAN-nätverk i Azure Portal skapas en virtuell hubb och gateway (valfritt) som dess komponenter.

En *säker* virtuell hubb är en [virtuell WAN-hubb i Azure](../virtual-wan/virtual-wan-about.md#resources) med associerade säkerhets-och routningsprinciperna som kon figurer ATS av Azure Firewall Manager. Använd skyddade virtuella hubbar för att enkelt skapa nav-och ekrar och transitiva arkitekturer med interna säkerhets tjänster för trafik styrning och skydd. 

Du kan använda en säker virtuell hubb som en hanterad Central VNet utan lokal anslutning. Den ersätter det centrala VNet som tidigare krävdes för en Azure Firewall-distribution. Eftersom den säkra virtuella hubben tillhandahåller automatisk routning behöver du inte konfigurera egna UDR (användardefinierade vägar) för att dirigera trafik genom brand väggen.

Det går också att använda säkra virtuella hubbar som en del av en fullständig virtuell WAN-arkitektur. Den här arkitekturen ger säker, optimerad och automatiserad gren anslutning till och via Azure. Du kan välja vilka tjänster som ska skydda och styra nätverks trafiken, inklusive Azure-brandväggen och andra SECaaS-leverantörer (Security as a Service).

## <a name="create-a-secured-virtual-hub"></a>Skapa en säker virtuell hubb

Med hjälp av brand Väggs hanteraren i Azure Portal kan du antingen skapa en ny säker virtuell hubb eller konvertera ett befintligt virtuellt nav som du tidigare har skapat med Azure Virtual WAN.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en säker virtuell hubb och använder den för att skydda och styra ett nav och eker-nätverk finns i [Självstudier: skydda ditt moln nätverk med Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md).