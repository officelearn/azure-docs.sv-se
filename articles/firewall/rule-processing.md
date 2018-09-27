---
title: Azure brandväggen regeln standardbearbetningslogiken
description: Lär dig mer om Azure-brandväggsregel bearbetningslogiken
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228441"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure brandväggen regeln standardbearbetningslogiken
Azure-brandväggen har NAT-regler, regler och regler för program. Regler bearbetas enligt regeltypen.


## <a name="network-rules-and-applications-rules"></a>Regler och regler för program 
Nätverksregler är tillämpade regler för första sedan programmet. Reglerna avslutas. Så om en matchning hittas i Nätverksregler inte bearbetas regler för program.  Om det finns inga nätverk regeln matchning och om paketprotokollet HTTP/HTTPS, utvärderas sedan paketet av regler för program. Om du fortfarande ingen matchning hittas utvärderas paketet mot regelsamlingen infrastruktur. Om det fortfarande finns ingen matchning nekas paketet som standard.

## <a name="nat-rules"></a>NAT-regler
Inkommande anslutning kan aktiveras genom att konfigurera mål Network adress Translation (DNAT) enligt beskrivningen i [självstudie: filtrera inkommande trafik med Azure-brandväggen DNAT med Azure portal](tutorial-firewall-dnat.md). DNAT regler tillämpas först. Om en matchning hittas läggs en implicit motsvarande regel för att tillåta översatta trafik. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en regelsamling för nätverk med neka-regler som matchar den översatta trafiken. Inga programregler tillämpas för dessa anslutningar.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuera och konfigurera en brandvägg för Azure](tutorial-firewall-deploy-portal.md).