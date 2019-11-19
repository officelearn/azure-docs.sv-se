---
title: Regelbearbetningslogik för Azure Firewall
description: Azure-brandväggen har NAT-regler, nätverks regler och program regler. Reglerna bearbetas enligt regel typen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166792"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall
Azure-brandväggen har NAT-regler, nätverks regler och program regler. Reglerna bearbetas enligt regel typen.


## <a name="network-rules-and-applications-rules"></a>Regler för nätverks regler och program 
Nätverks regler tillämpas först och sedan program regler. Reglerna avslutas. Så om en matchning hittas i nätverks regler bearbetas inte program regler.  Om det inte finns någon nätverksregelmatchning och om paketprotokollet är HTTP/HTTPS utvärderas paketet av programreglerna. Om ingen matchning hittas utvärderas paketet mot regel samlingen för infrastrukturen. Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="nat-rules"></a>NAT-regler
Inkommande anslutningar kan aktive ras genom att konfigurera mål nätverks adress översättning (DNAT) enligt beskrivningen i [Självstudier: filtrera inkommande trafik med Azure FIREWALL DNAt med hjälp av Azure Portal](tutorial-firewall-dnat.md). DNAT regler tillämpas först. Om en matchning hittas läggs en implicit motsvarande nätverks regel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Inga program regler tillämpas för dessa anslutningar.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).