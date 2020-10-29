---
title: Nätverks-och anslutnings barhet för Azure VMware-lösningar
description: Azure VMware-lösning nätverks-och anslutnings beskrivning.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925020"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware-lösningen erbjuder en privat moln miljö som är tillgänglig från lokala och Azure-baserade miljöer eller resurser. Tjänster som Azure ExpressRoute och VPN-anslutningar levererar anslutningen. Dessa tjänster kräver vissa nätverks adress intervall och brand Väggs portar för att aktivera tjänsterna.

När du distribuerar ett privat moln, skapas privata nätverk för hantering, etablering och vMotion. Använd de här privata nätverken för att få åtkomst till vCenter och NSX-T Manager och vMotion eller distribution av virtuella datorer.  ExpressRoute Global Reach används för att ansluta privata moln till lokala miljöer. Anslutningen kräver ett virtuellt nätverk med en ExpressRoute-krets i din prenumeration.

Resurser, t. ex. webb servrar och virtuella datorer, är tillgängliga för Internet via de offentliga Azure-funktionerna för virtuella WAN-nätverk.  Som standard är Internet åtkomst inaktive rad för nya privata moln. Mer information finns i [så här använder du de offentliga IP-funktionerna i Azure VMware-lösningen](../public-ip-usage.md).