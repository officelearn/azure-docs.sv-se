---
title: Nätverks-och anslutnings barhet för Azure VMWare-lösningar
description: Azure VMWare-lösning nätverks-och anslutnings beskrivning.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574472"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware-lösningen erbjuder en privat moln miljö som är tillgänglig från lokala och Azure-baserade miljöer eller resurser. Tjänster som Azure ExpressRoute och VPN-anslutningar levererar anslutningen. Dessa tjänster kräver vissa nätverks adress intervall och brand Väggs portar för att aktivera tjänsterna.

När du distribuerar ett privat moln, skapas privata nätverk för hantering, etablering och vMotion. Använd de här privata nätverken för att få åtkomst till vCenter och NSX-T Manager och vMotion eller distribution av virtuella datorer.  ExpressRoute Global Reach används för att ansluta privata moln till lokala miljöer. Anslutningen kräver ett virtuellt nätverk med en ExpressRoute-krets i din prenumeration.



>[!NOTE]
>Åtkomst till Internet och Azure-tjänster tillhandahålls och tillhandahålls för att använda virtuella datorer i produktions nätverk när du distribuerar ett privat moln.  Som standard är Internet åtkomst inaktive rad för nya privata moln och kan när som helst aktive ras eller inaktive ras.