---
title: Fristående Service Fabric-kluster översikt | Microsoft Docs
description: Service Fabric-kluster som körs på Windows Server och Linux, vilket innebär att du ska kunna distribuera och värden Service Fabric-program var som helst som du kan köra Windows Server eller Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 5d3421c4d48a6bf8416a774c4b4e5e7852a83f06
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967567"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Översikt över Service Fabric fristående kluster

Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras. En dator eller virtuell dator som ingår i ett kluster kallas för en nod i klustret. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, balanserar Service Fabric service partitionsrepliker och instanser i det större antalet noder. Övergripande förbättrar programmets prestanda och minskar konkurrensen för åtkomst till minnet. Om noderna i klustret inte som används effektivt, kan du minska antalet noder i klustret. Service Fabric balanserar igen partitionsrepliker och instanser över minskade antalet noder för att bättre utnyttja maskinvaran på varje nod.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning noder i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen.

Processen för att skapa ett Service Fabric-kluster lokalt liknar processen med att skapa ett kluster i alla moln valfri med en uppsättning virtuella datorer. De första stegen för att etablera de virtuella datorerna regleras av molnleverantör eller en lokal miljö som du använder. När du har en uppsättning virtuella datorer med nätverksanslutningar aktiverat mellan dem kan sedan stegen för att ställa in Service Fabric-paketet, redigera inställningar för klustret och köra skapa ett kluster och av hanteringsskript är identiska. Detta säkerställer att din kunskap och erfarenhet av att använda och hantera Service Fabric-kluster kan överföras när du väljer att rikta in nya värdmiljöer.

## <a name="cluster-security"></a>Klustersäkerhet
Service Fabric-kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare från att ansluta till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret.

### <a name="node-to-node-security"></a>Nod-till-nod-säkerhet
Nod-till-nod-säkerhet skyddar kommunikationen mellan virtuella datorer eller datorer i ett kluster. Det här scenariot security säkerställer att endast de datorer som har behörighet att ansluta till klustret kan delta i som är värd för program och tjänster i klustret. Service Fabric använder X.509-certifikat för att skydda ett kluster och säkerhetsfunktioner för programmet.  Ett klustercertifikat krävs för att skydda trafik och ange klustret och server-autentisering.  Självsignerat signerade-certifikat kan användas för testkluster, men ett certifikat från en betrodd certifikatutfärdare som ska användas för att skydda produktionskluster.

Windows-säkerhet kan också aktiveras för ett fristående kluster i Windows. Om du har Windows Server 2012 R2 och Windows Active Directory, rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänstkonton. Annars kan du använda Windows-säkerhet med Windows-konton.

Mer information finns [nod till nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Klient-till-nod-säkerhet
Klient-till-nod-säkerhet autentiserar klienter och hjälper till att säker kommunikation mellan en klient och enskilda noder i klustret. Den här typen av security hjälper till att säkerställa att endast behöriga användare har åtkomst till klustret och de program som distribueras i klustret. Klienter som är unikt identifieras via antingen deras säkerhetsreferenser för X.509-certifikat. Valfritt antal valfria klientcertifikat kan användas för att autentisera klienter för administratör eller användare med klustret.

Förutom klientcertifikat, kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns [klient-till-nod-säkerhet](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Service Fabric stöder också åtkomstkontroll som begränsar åtkomsten till vissa klusteråtgärder för olika grupper av användare. Detta hjälper att skydda klustret. Två typer av access control har stöd för klienter som ansluter till ett kluster: Administratörsrollen och användarrollen.  

Mer information finns i [rollbaserad åtkomstkontroll (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Skalning

Programbegäran ändras med tiden. Du kan behöva öka klusterresurser för att uppfylla ökade programtrafik för arbetsbelastning eller ditt nätverk eller minska klusterresurser när behovet sjunker. När du har skapat ett Service Fabric-kluster, kan du skala klustret horisontellt (ändra antalet noder) eller lodrätt (ändra resurser noder). Du kan skala klustret när som helst, även när arbetsbelastningar sedan körs på klustret. När klustret skalas skalas programmen automatiskt samt.

Mer information finns i [skalning fristående kluster](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Uppgraderar

Ett fristående kluster är en resurs som du helt egna. Du är ansvarig för uppdatering av det underliggande Operativsystemet och initierar infrastrukturuppgraderingar. Du kan ange att ta emot automatiska runtime uppgraderingar, när Microsoft publicerar en ny version eller välja en stöds runtime-versionen som du vill. Du kan också uppdatera Operativsystemet och uppdatera klusterkonfiguration som certifikat och programportar förutom fabric-uppgraderingar. 

Mer information finns i [uppgradera fristående kluster](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Du kan skapa kluster på virtuella datorer eller datorer som kör dessa (Linux inte stöds ännu):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>Nästa steg
Läs mer om [skydda](service-fabric-cluster-security.md), [skalning](service-fabric-cluster-scaling-standalone.md), och [uppgraderar](service-fabric-cluster-upgrade-standalone.md) fristående kluster.

Lär dig mer om [Service Fabric-supportalternativ](service-fabric-support.md).