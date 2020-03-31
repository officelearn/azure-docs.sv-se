---
title: Förhandsversion av distributionsdistributionen av Azure Firewall Manager
description: Lär dig de distributionssteg på hög nivå som krävs för förhandsversionen av Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443133"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Förhandsversion av distributionsdistributionen av Azure Firewall Manager

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Det finns mer än ett sätt att distribuera Förhandsversionen av Azure Firewall Manager, men följande allmänna process rekommenderas.

## <a name="general-deployment-process"></a>Allmän distributionsprocess

### <a name="hub-virtual-networks"></a>Virtuella hubbnätverk

1.  Skapa en brandväggsprincip

    - Skapa en ny policy
<br>*Eller*<br>
    - Härleda en basprincip och anpassa en lokal princip
<br>*Eller*<br>
    - Importera regler från en befintlig Azure-brandvägg. Se till att ta bort NAT-regler från principer som ska tillämpas över flera brandväggar
1. Skapa din hubb- och ekerarkitektur
   - Skapa ett virtuellt navnätverk med Azure Firewall Manager och peer spoke virtuella nätverk till den med hjälp av virtuell nätverks peering
<br>*Eller*<br>
    - Skapa ett virtuellt nätverk och lägga till virtuella nätverksanslutningar och peer spoke virtuella nätverk till den med hjälp av virtuella nätverk peering

3. Välj säkerhetsleverantörer och associera brandväggsprincipen. För närvarande är endast Azure-brandväggen en provider som stöds.

   - Detta görs medan du skapar ett virtuellt navnätverk
<br>*Eller*<br>
    - Konvertera ett befintligt virtuellt nätverk till ett virtuellt navnätverk. Det är också möjligt att konvertera flera virtuella nätverk.

4. Konfigurera användardefinierande vägar för att dirigera trafik till brandväggen för virtuella navnätverk.


### <a name="secured-virtual-hubs"></a>Säkrade virtuella hubbar

1. Skapa din hubb- och ekerarkitektur

   - Skapa en säker virtuell hubb med Azure Firewall Manager och lägg till virtuella nätverksanslutningar.<br>*Eller*<br>
   - Skapa en virtuell WAN-hubb och lägg till virtuella nätverksanslutningar.
2. Välj säkerhetsleverantörer

   - Klar när du skapar en säker virtuell hubb.<br>*Eller*<br>
   - Konvertera en befintlig virtuell WAN-hubb till Secure Virtual Hub.
3. Skapa en brandväggsprincip och associera den med navet

   - Gäller endast om du använder Azure-brandväggen.
   - SECaaS-principer (Security as a Service) konfigureras via partnerhanteringserfarenhet.
4. Konfigurera ruttinställningar för att dirigera trafik till din säkra nav

   - Dirigera enkelt trafik till din säkra hubb för filtrering och loggning utan användardefinierade vägar (UDR) på ekriska virtuella nätverk med hjälp av sidan Säker inställning för virtuell hubbväg.

> [!NOTE]
> - Du kan inte ha mer än ett nav per virtuellt wan per region. Men du kan lägga till flera virtuella WANs i regionen för att uppnå detta.
> - Du kan inte ha överlappande IP-utrymmen för hubbar i en vWAN.
> - Nav-VNet-anslutningarna måste finnas i samma region som navet.

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skydda ditt molnnätverk med förhandsversionen av Azure Firewall Manager med Azure-portalen](secure-cloud-network.md)