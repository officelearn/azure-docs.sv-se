---
title: Regelbearbetningslogik för Azure Firewall
description: Lär dig mer om bearbetningslogik för Azure-brandväggsregler
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518208"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall

Azure-brandväggen har NAT-regler, nätverksregler och programregler. Reglerna bearbetas enligt regeltypen.

## <a name="network-rules-and-applications-rules"></a>Regler för nätverk och program

Nätverksregler tillämpas först och sedan tillämpningsregler. Reglerna är avslutande. Så om en matchning hittas i nätverksregler bearbetas inte programreglerna.  Om det inte finns någon nätverksregelmatchning och om paketprotokollet är HTTP/HTTPS utvärderas paketet av programreglerna. Om det fortfarande inte hittas någon matchning utvärderas paketet mot infrastrukturregelsamlingen. Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="nat-rules"></a>NAT-regler

Inkommande anslutning kan aktiveras genom att konfigurera DNAT (Destination Network Address Translation) enligt beskrivningen i [Självstudiekurs: Filtrera inkommande trafik med Azure Firewall DNAT med Azure-portalen](../firewall/tutorial-firewall-dnat.md). DNAT-regler tillämpas först. Om en matchning hittas läggs en implicit motsvarande nätverksregel för att tillåta den översatta trafiken till. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Inga programregler tillämpas för dessa anslutningar.

## <a name="inherited-rules"></a>Ärvda regler

Nätverksregelsamlingar som ärvs från en överordnad princip prioriteras alltid ovanför nätverksregelsamlingar som definieras som en del av den nya principen. Samma logik gäller även för programregelsamlingar. Nätverksregelsamlingar bearbetas dock alltid före programregelsamlingar oavsett arv.

Som standard ärver din princip det överordnade principhotinformationsläget. Du kan åsidosätta detta genom att ange hot Intelligence-läget till ett annat värde på sidan principinställningar. Det är bara möjligt att åsidosätta med ett striktare värde. Om du till exempel anger att endast avisering är *aviserad*kan du konfigurera den här lokala principen så att *den aviseras och nekas*, men du kan inte stänga av den.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om förhandsversionen av Azure Firewall Manager](overview.md)