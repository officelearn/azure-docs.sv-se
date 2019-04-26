---
title: Azure brandväggen regeln standardbearbetningslogiken
description: Lär dig mer om Azure-brandväggsregel bearbetningslogiken
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193653"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure brandväggen regeln standardbearbetningslogiken
Azure-brandväggen har NAT-regler, regler och regler för program. Regler bearbetas enligt regeltypen.


## <a name="network-rules-and-applications-rules"></a>Regler och regler för program 
Nätverksregler är tillämpade regler för första sedan programmet. Reglerna avslutas. Så om en matchning hittas i Nätverksregler inte bearbetas regler för program.  Om det inte finns någon nätverksregelmatchning och om paketprotokollet är HTTP/HTTPS utvärderas paketet av programreglerna. Om du fortfarande ingen matchning hittas utvärderas paketet mot regelsamlingen infrastruktur. Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="nat-rules"></a>NAT-regler
Inkommande anslutning kan aktiveras genom att konfigurera mål Network adress Translation (DNAT) enligt beskrivningen i [självstudien: Filtrera inkommande trafik med Azure-brandväggen DNAT med Azure portal](tutorial-firewall-dnat.md). DNAT regler tillämpas först. Om en matchning hittas läggs en implicit motsvarande regel för att tillåta översatta trafik. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Inga programregler tillämpas för dessa anslutningar.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuera och konfigurera en brandvägg för Azure](tutorial-firewall-deploy-portal.md).