---
title: Regelbearbetningslogik för Azure Firewall
description: Lär dig mer om Azure Firewall Rule Processing Logic
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 70026173d1cb932d30a59ea2b876ef22217a81bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563683"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall

Azure-brandväggen har NAT-regler, nätverks regler och program regler. Reglerna bearbetas enligt regel typen.

## <a name="network-rules-and-applications-rules"></a>Regler för nätverks regler och program

Nätverks regler tillämpas först och sedan program regler. Reglerna avslutas. Så om en matchning hittas i nätverks regler bearbetas inte program reglerna.  Om ingen nätverks regel matchar och om paket protokollet är HTTP/HTTPS utvärderas paketet av program reglerna. Om ingen matchning hittas utvärderas paketet mot regel samlingen för infrastrukturen. Om det fortfarande inte finns någon matchning, nekas paketet som standard.

## <a name="nat-rules"></a>NAT-regler

Inkommande anslutningar kan aktive ras genom att konfigurera mål nätverks adress översättning (DNAT) enligt beskrivningen i [Självstudier: filtrera inkommande trafik med Azure FIREWALL DNAt med hjälp av Azure Portal](../firewall/tutorial-firewall-dnat.md). DNAT regler tillämpas först. Om en matchning hittas läggs en implicit motsvarande nätverks regel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Inga program regler tillämpas för dessa anslutningar.

## <a name="inherited-rules"></a>Ärvda regler

Nätverks regel samlingar som har ärvts från en överordnad princip prioriteras alltid ovanför nätverks regel samlingar som definieras som en del av den nya principen. Samma logik gäller även för program regel samlingar. Nätverks regel samlingar bearbetas dock alltid innan program regel samlingar, oavsett arv.

Som standard ärver din princip sitt överordnade princip hot informations läge. Du kan åsidosätta detta genom att ställa in ditt hot informations läge på ett annat värde på sidan princip inställningar. Det går bara att åsidosätta med ett striktare värde. Om du till exempel har angett *enbart avisering*för överordnad princip kan du konfigurera den här lokala principen för att *Varna och neka*, men du kan inte inaktivera den.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Firewall Manager](overview.md)