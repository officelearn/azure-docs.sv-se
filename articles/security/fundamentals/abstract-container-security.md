---
title: Säkerhet för abstrakta behållare i Microsoft Azure
description: Abstrakt för behållar säkerheten i Microsoft Azure white paper.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: b70744f403c483448a844d3f3decf6ce26f48f06
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727786"
---
# <a name="container-security-in-microsoft-azure"></a>Behållar säkerhet i Microsoft Azure
## <a name="abstract"></a>Abstrakt

Container Technology orsakar en strukturell förändring i den molnbaserade dator världen. Behållare gör det möjligt att köra flera instanser av ett program på en enda instans av ett operativ system och därmed använda resurser mer effektivt. Behållare ger organisationer konsekvens och flexibilitet. De aktiverar kontinuerlig distribution eftersom programmet kan utvecklas på ett skriv bord, testas i en virtuell dator och sedan distribueras för produktion i molnet. Behållare ger smidighet, effektiviserade åtgärder, skalbarhet och lägre kostnader på grund av resurs optimering.

Eftersom container Technology är relativt nytt har många IT-proffs säkerhets problem om bristande synlighet och användning i en produktions miljö. Utvecklings team är ofta inte medvetna om rekommenderade säkerhets metoder. Den här white paper kan hjälpa säkerhets drifts grupper och utvecklare att välja metoder för att skydda behållar utveckling och distributioner på Microsoft Azures plattformen.

Det här dokumentet beskriver behållare, distribution och hantering av behållare och inbyggda plattforms tjänster. Den beskriver också körnings säkerhets problem som uppstår vid användning av behållare på Azure-plattformen. I figurer och exempel fokuserar dokumentet på Docker som behållar modellen och Kubernetes som behållar-Orchestrator. De flesta av säkerhets rekommendationerna gäller även andra behållar modeller från Microsoft-partner på Azure-plattformen.

[Ladda ned white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)