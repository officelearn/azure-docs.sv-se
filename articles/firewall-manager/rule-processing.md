---
title: Regelbearbetningslogik för Azure Firewall
description: Lär dig mer om Azure Firewall Rule Processing Logic
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73518208"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall

Azure-brandväggen har NAT-regler, nätverks regler och program regler. Reglerna bearbetas enligt regel typen.

## <a name="network-rules-and-applications-rules"></a>Regler för nätverks regler och program

Nätverks regler tillämpas först och sedan program regler. Reglerna avslutas. Så om en matchning hittas i nätverks regler bearbetas inte program regler.  Om det inte finns någon nätverksregelmatchning och om paketprotokollet är HTTP/HTTPS utvärderas paketet av programreglerna. Om ingen matchning hittas utvärderas paketet mot regel samlingen för infrastrukturen. Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="nat-rules"></a>NAT-regler

Inkommande anslutningar kan aktive ras genom att konfigurera mål nätverks adress översättning (DNAT) enligt beskrivningen i [Självstudier: filtrera inkommande trafik med Azure FIREWALL DNAt med hjälp av Azure Portal](../firewall/tutorial-firewall-dnat.md). DNAT regler tillämpas först. Om en matchning hittas läggs en implicit motsvarande nätverks regel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Inga program regler tillämpas för dessa anslutningar.

## <a name="inherited-rules"></a>Ärvda regler

Nätverks regel samlingar som har ärvts från en överordnad princip prioriteras alltid ovanför nätverks regel samlingar som definieras som en del av den nya principen. Samma logik gäller även för program regel samlingar. Nätverks regel samlingar bearbetas dock alltid innan program regel samlingar, oavsett arv.

Som standard ärver principen den överordnade principens hot informations läge. Du kan åsidosätta detta genom att ställa in ditt hot informations läge på ett annat värde på sidan princip inställningar. Det går bara att åsidosätta med ett striktare värde. Om du till exempel har angett *enbart avisering*för överordnad princip kan du konfigurera den här lokala principen för att *Varna och neka*, men du kan inte inaktivera den.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om för hands versionen av Azure Firewall Manager](overview.md)