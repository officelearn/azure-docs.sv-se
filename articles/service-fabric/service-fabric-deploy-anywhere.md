---
title: Översikt över Azure och fristående Service Fabric-kluster | Microsoft Docs
description: Du kan skapa Service Fabric-kluster på virtuella datorer eller datorer som kör Windows Server eller Linux. Det innebär att du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning av Windows Server eller Linux-datorer som är sammankopplade-on-premises, Microsoft Azure, eller med någon annan molnleverantör.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 6d5169d8ea4480e95e09228f9eb02bd78fdd0be8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393511"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Jämförelse mellan Azure och fristående Service Fabric-kluster i Windows Server och Linux
Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, balanserar Service Fabric service partitionsrepliker och instanser i det större antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrensen för åtkomst till minnet. Om noderna i klustret inte som används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partitionsrepliker och instanser över minskade antalet noder för att bättre utnyttja maskinvaran på varje nod.

Service Fabric kan för att skapa Service Fabric-kluster på virtuella datorer eller datorer som kör Windows Server eller Linux. Det innebär att du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning med Windows Server eller Linux-datorer som är sammankopplade, oavsett om det körs lokalt, Microsoft Azure, eller med någon annan molnleverantör.

## <a name="benefits-of-clusters-on-azure"></a>Fördelarna med kluster på Azure
På Azure ger vi integrering med andra Azure-funktioner och tjänster som gör åtgärder och hantering av klustret enklare och mer tillförlitlig.

* **Azure-portalen:** Azure-portalen gör det enkelt att skapa och hantera kluster.
* **Azure Resource Manager:** Använda Azure Resource Manager låter dig enkelt hantera alla resurser som används av klustret som en enhet och förenklar kostnadsspårning och fakturering.
* **Service Fabric-kluster som en Azure-resurs** A Service Fabric-kluster är en Azure-resurs, så du kan utforma den precis som andra resurser i Azure.
* **Integrering med Azure-infrastrukturen** Service Fabric samordnar med underliggande Azure-infrastrukturen för OS, nätverk och andra uppgraderingar att förbättra tillgängligheten och tillförlitligheten för dina program.  
* **Diagnostik:** Vi ger integrering med Azure-diagnostik i Azure, och Azure Monitor-loggar.
* **Automatisk skalning:** Kluster på Azure kan tillhandahåller vi inbyggda funktioner för automatisk skalning på grund av skalningsuppsättningar för virtuella datorer. I både lokala och andra miljöer i molnet behöver du skapa din egen funktion eller en skala manuellt med hjälp av API: er som exponerar Service Fabric för att skala kluster som automatisk skalning.

## <a name="benefits-of-standalone-clusters"></a>Fördelar med fristående kluster
* Du kan välja någon annan molnleverantör som värd för ditt kluster.
* Service Fabric-program, skrivs en gång, kan köras i flera värdmiljöer med minimal att inga ändringar.
* Kunskap för att skapa Service Fabric-program överförs från en värdmiljö till en annan.
* Operativa upplevelse av att köra och hantera Service Fabric-kluster innehåller över från en miljö till en annan.
* Bred kundkrets är obegränsade av värdbegränsningar miljö.
* Det finns en extra nivå av tillförlitlighet och skyddar mot utökas avbrott eftersom du kan flytta tjänsterna över till en annan distributionsmiljö om en dataleverantör för center eller i molnet har en inaktiverad.

## <a name="next-steps"></a>Nästa steg

* Läs en översikt över [Service Fabric-kluster på Azure](service-fabric-azure-clusters-overview.md)
* Läs en översikt över [fristående Service Fabric-kluster](service-fabric-standalone-clusters-overview.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)