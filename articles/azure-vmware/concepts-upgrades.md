---
title: Koncept – uppgraderingar av privata moln
description: Lär dig mer om de viktiga uppgraderings processerna och funktionerna i Azure VMware-lösningen (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: c5c79625ef661f220a0ba88d6d24ab59e7228195
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740374"
---
# <a name="azure-vmware-solution-avs-upgrade-concepts"></a>Azure VMware Solution (AVS)-uppgraderings koncept

En av de viktigaste fördelarna med Azures privata moln (Azure VMware Solution) är att plattformen upprätthålls för dig. Plattforms underhåll innehåller automatiserade uppgraderingar av ett VMware-validerat program varu paket. Uppgraderingar utförs regelbundet och säkerställer att du alltid har de senaste validerade versionerna av program varan.

## <a name="avs-private-cloud-software-upgrades"></a>AVS-uppgraderingar av privata moln program

Molnets privata moln plattform innehåller vissa versioner av VMware vSphere, ESXi, virtuellt San och NSX-T-program. Program varu paketet för det privata molnet verifieras för användning i nya privata moln installationer och för uppgraderingar av befintliga privata moln.

Uppgraderings processen för livs cykel hantering kräver ingen stillestånds tid för dina privata moln. Med uppgraderings processen ser du till att du automatiskt använder den senaste versionen av det program som verifierats av det privata molnet. Uppgraderingar tillämpas på vanliga takt så att privata moln aldrig är mer än en version som ligger bakom den senaste versionen av den validerade program varu samlingen. Du får ett meddelande om planerade uppgraderingar till ditt privata moln. Du kan skjuta upp uppgraderingen om ditt privata moln finns i en version av den senaste versionen.

Kritiska uppdateringar och uppdateringar tillämpas när de verifieras. Du meddelas i förväg om nödvändiga kritiska uppgraderingar. Den här principen säkerställer att ditt privata moln har viktiga korrigeringar och uppdateringar som tillämpas omedelbart.

VMware-programversioner finns i [artikeln privata moln och kluster begrepp](concepts-private-clouds-clusters.md) och [vanliga frågor och svar](faq.md).

## <a name="next-steps"></a>Nästa steg

Nästa steg är att [skapa ett privat moln](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
