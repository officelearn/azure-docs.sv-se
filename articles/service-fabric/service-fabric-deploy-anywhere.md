---
title: Skapa Service Fabric-kluster i Windows Server och Linux | Microsoft Docs
description: Service Fabric-kluster som körs på Windows Server och Linux, vilket innebär att du ska kunna distribuera och värden Service Fabric-program var som helst som du kan köra Windows Server eller Linux.
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
ms.openlocfilehash: 80b331d32fe1e7bb4eb331bd981106968bc73bed
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163217"
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Skapa Service Fabric-kluster i Windows Server eller Linux
Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, balanserar Service Fabric service partitionsrepliker och instanser i det större antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrensen för åtkomst till minnet. Om noderna i klustret inte som används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partitionsrepliker och instanser över minskade antalet noder för att bättre utnyttja maskinvaran på varje nod.

Service Fabric kan för att skapa Service Fabric-kluster på virtuella datorer eller datorer som kör Windows Server eller Linux. Detta innebär att du kan distribuera och köra Service Fabric-program i alla miljöer där du har en uppsättning med Windows Server eller Linux-datorer som är sammankopplade, oavsett om det körs lokalt, Microsoft Azure eller med någon annan molnleverantör.

## <a name="create-service-fabric-clusters-on-azure"></a>Skapa Service Fabric-kluster i Azure
Skapa ett kluster i Azure görs via en Resource Manager-mall eller [Azure-portalen](https://portal.azure.com). Läs [skapa Service Fabric-kluster med hjälp av Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) eller [skapa ett Service Fabric-kluster från Azure-portalen](service-fabric-cluster-creation-via-portal.md) för mer information.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Operativsystem som stöds för kluster på Azure
Du kan skapa kluster på virtuella datorer som kör dessa operativsystem:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Om du vill distribuera Service Fabric i Windows Server 1709, Observera att (1) den inte är en lång sikt installationsbransch, så du kan behöva flytta versioner i framtiden, och (2) om du distribuerar behållare, fungerar behållare som bygger på Windows Server 2016 inte på Windows Server  1709, och vice versa (du måste återskapa dem för att distribuera dem).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Skapa fristående Service Fabric-kluster på lokalt eller med någon annan molnleverantör
Service Fabric tillhandahåller ett installationspaket för att skapa fristående Service Fabric-kluster lokalt eller på någon annan molnleverantör.

För mer information om hur du konfigurerar fristående Service Fabric-kluster på Windows Server, läsa [skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Fristående kluster stöds för närvarande inte för Linux. Linux stöds på en ruta för utveckling och Azure Linux-multidatorkluster.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Alla molndistributioner jämfört med lokala distributioner
Processen för att skapa ett Service Fabric-kluster lokalt liknar processen med att skapa ett kluster i alla moln valfri med en uppsättning virtuella datorer. De första stegen för att etablera de virtuella datorerna regleras av molnleverantör eller en lokal miljö som du använder. När du har en uppsättning virtuella datorer med nätverksanslutningar aktiverat mellan dem kan sedan stegen för att ställa in Service Fabric-paketet, redigera inställningar för klustret och köra skapa ett kluster och av hanteringsskript är identiska. Detta säkerställer att din kunskap och erfarenhet av att använda och hantera Service Fabric-kluster kan överföras när du väljer att rikta in nya värdmiljöer.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Fördelarna med att skapa fristående Service Fabric-kluster
* Du kan välja någon annan molnleverantör som värd för ditt kluster.
* Service Fabric-program, skrivs en gång, kan köras i flera värdmiljöer med minimal att inga ändringar.
* Kunskap för att skapa Service Fabric-program överförs från en värdmiljö till en annan.
* Operativa upplevelse av att köra och hantera Service Fabric-kluster innehåller över från en miljö till en annan.
* Bred kundkrets är obegränsade av värdbegränsningar miljö.
* Det finns en extra nivå av tillförlitlighet och skyddar mot utökas avbrott eftersom du kan flytta tjänsterna över till en annan distributionsmiljö om en dataleverantör för center eller i molnet har en inaktiverad.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Operativsystem som stöds för fristående kluster
Du kan skapa kluster på virtuella datorer eller datorer som kör dessa (Linux inte stöds ännu):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Fördelarna med Service Fabric-kluster på Azure via fristående Service Fabric-kluster som skapats lokalt
Service Fabric-kluster som körs på Azure tillhandahåller alternativ för fördelar jämfört med lokala platser, så om du inte har särskilda behov för där du kör dina kluster, och vi rekommenderar att du kör dem på Azure. På Azure ger vi integrering med andra Azure-funktioner och tjänster som gör åtgärder och hantering av klustret enklare och mer tillförlitlig.

* **Azure-portalen:** Azure-portalen gör det enkelt att skapa och hantera kluster.
* **Azure Resource Manager:** användning av Azure Resource Manager låter dig enkelt hantera alla resurser som används av klustret som en enhet och förenklar kostnadsspårning och fakturering.
* **Service Fabric-kluster som en Azure-resurs** A Service Fabric-kluster är en Azure-resurs, så du kan utforma den precis som andra resurser i Azure.
* **Integrering med Azure-infrastrukturen** Service Fabric samordnar med underliggande Azure-infrastrukturen för OS, nätverk och andra uppgraderingar att förbättra tillgängligheten och tillförlitligheten för dina program.  
* **Diagnostik:** på Azure, ger vi integrering med Azure-diagnostik och Log Analytics.
* **Automatisk skalning:** för kluster på Azure kan vi tillhandahålla inbyggda funktioner för automatisk skalning på grund av skalningsuppsättningar för virtuella datorer. I både lokala och andra miljöer i molnet behöver du skapa din egen funktion eller en skala manuellt med hjälp av API: er som exponerar Service Fabric för att skala kluster som automatisk skalning.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

