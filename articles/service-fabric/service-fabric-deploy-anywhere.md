---
title: Översikt över Azure-kluster för serviceinfrastruktur
description: Du kan skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server eller Linux. Det innebär att du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning Windows Server- eller Linux-datorer som är sammankopplade, lokalt, Microsoft Azure eller med alla molnleverantörer.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751155"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Jämföra Azure- och fristående Service Fabric-kluster på Windows Server och Linux

Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret balanserar Service Fabric om tjänstpartitionsrepliker och instanser över det ökade antalet noder. Övergripande programprestanda förbättras och konkurrens om åtkomst till minne minskar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric balanserar återigen om partitionsrepliker och instanser över det minskade antalet noder för att bättre använda maskinvaran på varje nod.

Service Fabric gör det möjligt att skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server eller Linux. Det innebär att du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning Windows Server- eller Linux-datorer som är sammankopplade, oavsett om det är lokalt, Microsoft Azure eller med alla molnleverantörer.

## <a name="benefits-of-clusters-on-azure"></a>Fördelar med kluster på Azure

På Azure tillhandahåller vi integrering med andra Azure-funktioner och tjänster, vilket gör drift och hantering av klustret enklare och mer tillförlitligt.

* **Azure-portal:** Azure-portalen gör det enkelt att skapa och hantera kluster.
* **Azure Resource Manager:** Användning av Azure Resource Manager möjliggör enkel hantering av alla resurser som används av klustret som en enhet och förenklar kostnadsspårning och fakturering.
* **Service Fabric Cluster som en Azure-resurs** Ett Service Fabric-kluster är en Azure-resurs, så du kan modellera det på samma sätt som du gör andra resurser i Azure.
* **Integrering med Azure Infrastructure** Service Fabric koordinater med den underliggande Azure-infrastrukturen för OPERATIVSYSTEM, nätverk och andra uppgraderingar för att förbättra tillgängligheten och tillförlitligheten för dina program.  
* **Diagnostik:** På Azure tillhandahåller vi integrering med Azure-diagnostik och Azure Monitor-loggar.
* **Automatisk skalning:** För kluster på Azure tillhandahåller vi inbyggda funktioner för automatisk skalning på grund av skalningsuppsättningar för virtuella datorer. I lokala och andra molnmiljöer måste du skapa din egen funktion för automatisk skalning eller skala manuellt med hjälp av API:erna som Service Fabric exponerar för skalning av kluster.

## <a name="benefits-of-standalone-clusters"></a>Fördelar med fristående kluster

* Du kan välja vilken molnleverantör som helst som värd för ditt kluster.
* Service Fabric-program, när de skrivits, kan köras i flera värdmiljöer med minimala eller inga ändringar.
* Kunskap om att bygga Service Fabric applikationer bär över från en värdmiljö till en annan.
* Operativ erfarenhet av att köra och hantera Service Fabric-kluster överförs från en miljö till en annan.
* Bred kundräckvidd är obegränsad av värdmiljöbegränsningar.
* Det finns ett extra lager av tillförlitlighet och skydd mot omfattande avbrott eftersom du kan flytta tjänsterna till en annan distributionsmiljö om ett datacenter eller ett molnleverantör har ett strömavbrott.

## <a name="next-steps"></a>Nästa steg

* Läs översikten över [Service Fabric-kluster på Azure](service-fabric-azure-clusters-overview.md)
* Läs översikten över [fristående service fabric-kluster](service-fabric-standalone-clusters-overview.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)