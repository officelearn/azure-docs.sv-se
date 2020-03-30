---
title: Översikt över fristående serviceinfrastrukturkluster
description: Service Fabric-kluster körs på Windows Server och Linux, vilket innebär att du kan distribuera och vara värd för Service Fabric-program var du än kan köra Windows Server eller Linux.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465639"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Översikt över fristående servicekoncidenter

Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret balanserar Service Fabric om tjänstpartitionsrepliker och instanser över det ökade antalet noder. Övergripande programprestanda förbättras och konkurrens om åtkomst till minne minskar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric balanserar återigen om partitionsrepliker och instanser över det minskade antalet noder för att bättre använda maskinvaran på varje nod.

En nodtyp definierar storlek, tal och egenskaper för en uppsättning noder i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen.

Processen för att skapa ett Service Fabric-kluster lokalt liknar processen att skapa ett kluster i valfritt moln med en uppsättning virtuella datorer. De första stegen för att etablera de virtuella datorerna styrs av molnleverantören eller den lokala miljön som du använder. När du har en uppsättning virtuella datorer med nätverksanslutning aktiverad mellan dem är stegen för att konfigurera Service Fabric-paketet, redigera klusterinställningarna och köra klusterskapande och hanteringsskript identiska. Detta säkerställer att din kunskap och erfarenhet av att driva och hantera Service Fabric-kluster kan överföras när du väljer att rikta in dig på nya värdmiljöer.

## <a name="cluster-security"></a>Klustersäkerhet
Ett Service Fabric-kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare ansluter till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret.

### <a name="node-to-node-security"></a>Nod-till-nod säkerhet
Nod-till-nod-säkerhet skyddar kommunikationen mellan de virtuella datorerna eller datorerna i ett kluster. Det här säkerhetsscenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värdprogram och tjänster i klustret. Service Fabric använder X.509-certifikat för att skydda ett kluster och tillhandahålla programsäkerhetsfunktioner.  Ett klustercertifikat krävs för att skydda klustertrafik och tillhandahålla kluster- och serverautentisering.  Självsignerade certifikat kan användas för testkluster, men ett certifikat från en betrodd certifikatutfärdare bör användas för att skydda produktionskluster.

Windows-säkerhet kan också aktiveras för ett fristående Windows-kluster. Om du har Windows Server 2012 R2 och Windows Active Directory rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänstkonton. Annars använder du Windows-säkerhet med Windows-konton.

Mer information finns [i Node-till-nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Säkerhet för klient-till-nod
Klient-till-nod-säkerhet autentiserar klienter och hjälper till att skydda kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet hjälper till att säkerställa att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienter identifieras unikt genom antingen sina X.509-certifikatsäkerhetsautentiseringsuppgifter. Valfritt antal valfria klientcertifikat kan användas för att autentisera administratörs- eller användarklienter med klustret.

Förutom klientcertifikat kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns i [säkerhet för klient-till-nod](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Service Fabric stöder också åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika användargrupper. Detta bidrar till att göra klustret säkrare. Två åtkomstkontrolltyper stöds för klienter som ansluter till ett kluster: Administratörsroll och Användarroll.  

Mer information finns i [Rollbaserad åtkomstkontroll (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Skalning

Ansökan kräver förändring med tiden. Du kan behöva öka klusterresurserna för att möta ökad programarbetsbelastning eller nätverkstrafik eller minska klusterresurser när efterfrågan sjunker. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra nodernas resurser). Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret. När klustret skalas skalas även dina program automatiskt.

Mer information finns i [Skala fristående kluster](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Uppgradering

Ett fristående kluster är en resurs som du äger helt och hållet. Du är ansvarig för att korrigera det underliggande operativsystemet och initiera tyguppgraderingar. Du kan ange att klustret ska ta emot automatiska körningsuppgraderingar när Microsoft släpper en ny version eller välja en runtime-version som stöds. Förutom infrastrukturuppgraderingar kan du även korrigera operativsystemet och uppdatera klusterkonfigurationen, till exempel certifikat eller programportar. 

Mer information finns i [Uppgradera fristående kluster](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Du kan skapa kluster på virtuella datorer eller datorer som kör dessa operativsystem (Linux stöds ännu inte):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur du skyddar,](service-fabric-cluster-security.md) [skalar](service-fabric-cluster-scaling-standalone.md)och [uppgraderar](service-fabric-cluster-upgrade-standalone.md) fristående kluster.

Läs mer om [supportalternativ för Service Fabric.](service-fabric-support.md)
