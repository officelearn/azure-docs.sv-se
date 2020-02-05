---
title: Azure VMware-lösningar (AVS)-AVS privata moln
description: Lär dig mer om molnets privata moln och begrepp.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024966"
---
# <a name="avs-private-cloud-overview"></a>Översikt över AVS-privata moln

AVS transformerar och utökar VMware-arbetsbelastningar till offentliga moln på några minuter. Med hjälp av AVS-tjänsten kan du distribuera VMware internt i Azure Bare Metal-infrastruktur. Din distribution finns på Azure-platser och integreras helt med resten av Azure-molnet.

AVS-lösningen ger fullständig VMware-operationell kontinuitet. Den här lösningen ger dig fördelarna med offentliga moln för:

* Elasticitet
* Innovation
* Effektivitet

Med AVS drar du nytta av en moln förbruknings modell som sänker den totala ägande kostnaden. Den erbjuder även etablering på begäran, betala per användning och kapacitets optimering.

AVS är helt kompatibelt med:

* Befintliga verktyg
* Kunskaper
* Processer

Den här kompatibiliteten gör det möjligt för dina team att hantera arbets belastningar i Azure-molnet, utan att störa dessa typer av principer:

* Nätverk
* Säkerhet  
* Dataskydd  
* Granska

AVS hanterar infrastrukturen och alla nödvändiga nätverks-och hanterings tjänster. Med AVS-tjänsten kan ditt team fokusera på:

* Affärs värde
* Program etablering
* Kontinuitet i verksamheten
* Support
* Policyframtvingande

## <a name="avs-private-cloud-environment-overview"></a>Översikt över molnets privata moln miljö

Ett privat AVS-moln är en isolerad VMware-stack som stöder:

* ESXi-värdar
* vCenter
* vSAN
* NSX

Molnets privata moln hanteras via AVS-portalen. De har sina egna vCenter-servrar i sin egen hanterings domän.

Stacken körs på:

* Dedikerade noder
* Isolerade Bare Metal-maskinvarukonfigurationer

Användare använder stacken via inbyggda VMware-verktyg, inklusive:

* vCenter
* NSX Manager

Du kan distribuera dedikerade noder på Azure-platser. Sedan kan du hantera dem med Azure och AVS. Ett privat AVS-moln består av ett eller flera vSphere-kluster, och varje kluster innehåller 3 till 16 noder.

Du kan skapa ett privat AVS-moln med hjälp av köpta noder, betala per användning eller reserverade, dedikerade noder.

Du kan ansluta det privata moln molnet till din lokala miljö och Azure-nätverket med följande anslutningar:

* Säkert
* Privat VPN
* Azure ExpressRoute

Molnets privata moln miljö är utformad för att eliminera enskilda felpunkter:

* ESXi-kluster konfigureras med vSphere hög tillgänglighet och har minst en reserv nod för återhämtning.
* Virtuellt San tillhandahåller redundant primär lagring. Virtuellt San kräver minst tre noder för att ge skydd mot ett enskilt haveri. Du kan konfigurera virtuellt San för att ge högre återhämtnings kapacitet för större kluster.
* Du kan konfigurera vCenter-, PSC-och NSX Manager-VM: ar med RAID-10 Storage-principer för att skydda mot lagrings problem. vSphere HA skyddar mot nod-och nätverks problem.

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>Scenarier för distribution av ett privat AVS-moln

Här är några exempel på användnings fall för distribution av molnets privata moln.

### <a name="data-center-retirement-or-migration"></a>Pension eller migrering av data Center

* Få ytterligare kapacitet när du når gränserna för ditt befintliga data Center eller uppdatera maskin vara.
* Lägg till nödvändig kapacitet i molnet och Undvik att hantera maskin varu uppdateringar.
* Minska risken och kostnaden för migrering av moln jämfört med tids krävande konverteringar eller omarkitektur.
* Använd välkända VMware-verktyg och-kunskaper för att påskynda migreringen av molnet. I molnet använder du Azure-tjänster för att modernisera dina program i din takt.

### <a name="expand-on-demand"></a>Expandera på begäran

* Utöka molnet för att möta oväntade behov, till exempel nya utvecklings miljöer eller säsongs kapacitets burst.
* Skapa ny kapacitet på begäran och behåll det bara så länge du behöver det.
* Minska din första investering, påskynda etableringen och minska komplexiteten med samma arkitektur och principer i både lokalt och i molnet.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Haveri beredskap och virtuella skriv bord i Azure-molnet

* Upprätta fjärråtkomst till data, appar och skriv bord i Azure-molnet. Med anslutningar med hög bandbredd laddar du upp/ladda ned data snabbt för att återställa från incidenter. Nätverk med låg latens ger snabba svars tider som användarna förväntar sig från en Skriv bords app.

* Replikera alla principer och nätverk i molnet med hjälp av AVS-portalen och välkända VMware-verktyg. Replikering minskar ansträngningen och risken för att skapa och hantera DR-och VDI-implementeringar.

### <a name="high-performance-applications-and-databases"></a>Program och databaser med hög prestanda

* Kör dina mest krävande arbets belastningar med den konvergerade arkitekturen som tillhandahålls av AVS.
* Kör Oracle, Microsoft SQL Server, mellanprogram och databaser med höga prestanda SQL.
* Upplev molnet som ditt eget Data Center med höghastighets nätverks anslutningar med 25 Gbit/s. Med höghastighets anslutningar kan du köra hybrid program som omfattar lokala, VMware på Azure och privata Azure-arbetsbelastningar, utan att kompromissa med prestanda.

### <a name="true-hybrid"></a>True hybrid

* Förena DevOps över VMware-och Azure-tjänster.
* Optimera VMware-administration för Azure-tjänster och-lösningar som kan tillämpas på alla dina arbets belastningar.
* Få åtkomst till offentliga moln tjänster utan att behöva expandera ditt data Center eller skapa en ny arkitektur för dina program.
* Centralisera identiteter, åtkomst kontroll principer, loggning och övervakning av VMware-program på Azure.

## <a name="limits"></a>Begränsningar

I följande tabell visas de olika noderna för resurser i ett privat moln moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat AVS-moln | 3 |
| Maximalt antal noder i ett kluster i ett privat AVS-moln | 16 |
| Maximalt antal noder i ett privat AVS-moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett privat AVS-moln](create-private-cloud.md)
* Lär dig hur du [konfigurerar en moln miljö för privata moln](quickstart-create-private-cloud.md)
