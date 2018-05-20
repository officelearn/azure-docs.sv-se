---
title: Skapa Azure Service Fabric-kluster på Windows Server- och Linux | Microsoft Docs
description: Service Fabric-kluster som körs på Windows Server- och Linux-, vilket betyder att du ska kunna distribuera- och värd för Service Fabric-program var som helst som du kan köra Windows Server- eller Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 3d427d99f6919991c29fc5947ebe0082670a1cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Skapa Service Fabric-kluster på Windows Server- eller Linux
Ett Azure Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som din mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, balanserar Service Fabric service partition repliker och instanser för det ökade antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrens om åtkomst till minnet. Om noderna i klustret inte används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partition repliker och instanser för det minska antalet noder för att bättre utnyttja maskinvara på varje nod.

Service Fabric kan du skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server- eller Linux. Detta innebär att du kan distribuera och köra Service Fabric-program i en miljö där du har en uppsättning Windows Server- eller Linux-datorer som är sammankopplade, är den lokala, Microsoft Azure eller med en molntjänstleverantör.

## <a name="create-service-fabric-clusters-on-azure"></a>Skapa Service Fabric-kluster i Azure
Skapa ett kluster på Azure görs antingen via en mall för modell eller [Azure-portalen](https://portal.azure.com). Läs [skapa ett Service Fabric-kluster med hjälp av en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) eller [skapa ett Service Fabric-kluster från Azure portal](service-fabric-cluster-creation-via-portal.md) för mer information.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Operativsystem som stöds för kluster på Azure
Du kan skapa kluster på virtuella datorer som kör operativsystemen:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Om du vill distribuera Service Fabric på Windows Server 1709 Observera att (1) det inte är en lång sikt servicing branch, så att du kan behöva flytta versioner i framtiden, och (2) om du distribuerar behållare behållare som bygger på Windows Server 2016 fungerar inte på Windows Server  1709, och vice versa (du måste återskapa dem för att distribuera dem).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Skapa fristående Service Fabric-kluster på lokalt eller med en molntjänstleverantör
Service Fabric ger ett installera paket att skapa fristående Service Fabric-kluster lokalt eller på alla moln-providern.

För mer information om hur du skapar fristående Service Fabric-kluster i Windows Server, läsa [Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Fristående stöds för närvarande inte för Linux. Linux stöds på en ruta för utveckling och flera datorer Azure Linux-kluster.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Alla molndistributioner kontra lokala distributioner
Processen för att skapa ett Service Fabric-kluster lokalt liknar processen att skapa ett kluster på ett moln önskat med en uppsättning virtuella datorer. De första stegen för att etablera virtuella datorer regleras av molnleverantören eller lokala miljö som du använder. När du har en uppsättning virtuella datorer med nätverksanslutningar aktiverat mellan dem sedan stegen för att ställa in Service Fabric-paket, redigera inställningar för klustret och köra skapa ett kluster och av hanteringsskript är identiska. Detta säkerställer att din kunskap och erfarenhet av att använda och hantera Service Fabric-kluster kan överföras när du väljer att rikta nya värdbaserade miljöer.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Fördelarna med att skapa fristående Service Fabric-kluster
* Du kan välja alla molntjänstleverantör som värd för klustret.
* Service Fabric-program, en gång skrivs kan köras i flera värdbaserade miljöer med minimal inga ändringar.
* Kunskap för att skapa Service Fabric program följer med från en värdmiljö till en annan.
* Operativ erfarenhet av kör och hanterar Service Fabric-kluster utför över från en miljö till en annan.
* Bred kunden reach är unbounded som värd för begränsningar.
* Det finns en extra nivå av tillförlitlighet och skydd mot omfattande avbrott eftersom du kan flytta tjänsterna över till en annan distributionsmiljö om en dataleverantör för center eller moln har en blackout.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Operativsystem som stöds för fristående kluster
Du kan skapa kluster på virtuella datorer eller datorer som kör dessa (Linux inte stöds ännu):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Fördelarna med Service Fabric-kluster i Azure via fristående Service Fabric-kluster skapas lokalt
Service Fabric-kluster som körs på Azure ger fördelar jämfört med lokalt alternativ, så om du inte har särskilda behov för där du kör ditt kluster, och vi rekommenderar att du kör dem på Azure. I Azure ger vi integrering med andra Azure-funktioner och tjänster som gör åtgärder och hantering av klustret enklare och mer tillförlitlig.

* **Azure-portalen:** Azure-portalen gör det enkelt att skapa och hantera kluster.
* **Azure Resource Manager:** användning av Azure Resource Manager kan enkel hantering av alla resurser som används av klustret som en enhet och förenklar kostnadsspårning och fakturering.
* **Service Fabric-kluster som en Azure-resurs** A Service Fabric-klustret är en Azure-resurs, så du kan utforma precis som andra resurser i Azure.
* **Integrering med Azure-infrastrukturen** Service Fabric samordnar med underliggande Azure-infrastrukturen för OS, nätverk och andra uppgraderingar för bättre tillgänglighet och pålitlighet.  
* **Diagnostik:** i Azure, vi anger integration med Azure-diagnostik och logganalys.
* **Automatisk skalning:** för kluster i Azure som vi tillhandahåller inbyggda funktioner för automatisk skalning på grund av skalningsuppsättningar i virtuella datorer. I lokala och andra miljöer i molnet behöver du skapa en egen funktion eller en skala manuellt med hjälp av API: er som Service Fabric exponerar för skalning kluster som automatisk skalning.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

