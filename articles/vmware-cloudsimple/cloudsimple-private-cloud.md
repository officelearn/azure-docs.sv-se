---
title: Azure VMware-lösning från CloudSimple - Privata moln
description: Läs mer om CloudSimple Private Clouds och koncept.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024966"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple Private Cloud översikt

CloudSimple omvandlar och utökar VMware-arbetsbelastningar till offentliga moln på några minuter. Med hjälp av CloudSimple-tjänsten kan du distribuera VMware internt på Azure bare metal-infrastruktur. Din distribution finns på Azure-platser och integreras helt med resten av Azure-molnet.

CloudSimple-lösningen ger fullständig VMware-driftkontinuitet. Den här lösningen ger dig de offentliga molnfördelarna med:

* Elasticitet
* Innovation
* Effektivitet

Med CloudSimple drar du nytta av en molnförbrukningsmodell som sänker din totala ägandekostnad. Det erbjuder också på begäran etablering, pay-as-you-grow och kapacitet optimering.

CloudSimple är helt kompatibel med:

* Befintliga verktyg
* Färdigheter
* Processer

Med den här kompatibiliteten kan dina team hantera arbetsbelastningar i Azure-molnet utan att störa dessa typer av principer:

* Nätverk
* Säkerhet  
* Dataskydd  
* Granska

CloudSimple hanterar infrastrukturen och alla nödvändiga nätverks- och hanteringstjänster. CloudSimple-tjänsten gör det möjligt för ditt team att fokusera på:

* Affärsvärde
* Programetablering
* Verksamhetskontinuitet
* Support
* Policyframtvingande

## <a name="private-cloud-environment-overview"></a>Översikt över den privata molnmiljön

Ett privat moln är en isolerad VMware-stack som stöder:

* ESXi värdar
* vCenter
* vSAN (på ett sätt)
* Nsx

Privata moln hanteras via CloudSimple-portalen. De har sin egen vCenter-server i sin egen hanteringsdomän.

Stacken körs på:

* Dedikerade noder
* Isolerade noder av bare metal-hårdvara

Användare konsumerar stacken via inbyggda VMware-verktyg, inklusive:

* vCenter
* NSX-chef

Du kan distribuera dedikerade noder på Azure-platser. Sedan kan du hantera dem med Azure och CloudSimple. Ett privat moln består av ett eller flera vSphere-kluster och varje kluster innehåller 3 till 16 noder.

Du kan skapa ett privat moln med köpta, betala per användning-noder eller reserverade, dedikerade noder.

Du kan ansluta det privata molnet till din lokala miljö och Azure-nätverket med hjälp av följande anslutningar:

* Skydda
* Privat VPN
* Azure ExpressRoute

Den privata molnmiljön är utformad för att eliminera enskilda felpunkter:

* ESXi-kluster är konfigurerade med vSphere hög tillgänglighet och har storlek för att ha minst en reservnod för återhämtning.
* vSAN tillhandahåller redundant primärlagring. vSan kräver minst tre noder för att ge skydd mot ett enda fel. Du kan konfigurera vSAN så att den ger högre återhämtning för större kluster.
* Du kan konfigurera virtuella datorer med v-datorer för v-meddelanden med V:er för V-center, PSC och NSX-hanteraren med LAGRINGSPRINCIPEN RAID-10 för att skydda mot lagringsfel. vSphere HA skyddar mot nod- och nätverksfel.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenarier för distribution av ett privat moln

Här är några exempel på användningsfall för privat molndistribution.

### <a name="data-center-retirement-or-migration"></a>Pensionering eller migrering av datacenter

* Få ytterligare kapacitet när du når gränserna för ditt befintliga datacenter eller uppdatera maskinvara.
* Lägg till nödvändig kapacitet i molnet och eliminera huvudvärken att hantera maskinvaruuppdateringar.
* Minska risken och kostnaden för molnmigreringar jämfört med tidskrävande konverteringar eller bakåtkrekturering.
* Använd välbekanta VMware-verktyg och kunskaper för att påskynda molnmigreringar. I molnet använder du Azure-tjänster för att modernisera dina program i din takt.

### <a name="expand-on-demand"></a>Expandera på begäran

* Expandera till molnet för att möta oförutsedda behov, till exempel nya utvecklingsmiljöer eller säsongskapacitetsskurningar.
* Skapa ny kapacitet på begäran och behåll den bara så länge du behöver den.
* Minska din förhandsinvestering, påskynda etableringshastigheten och minska komplexiteten med samma arkitektur och principer i både lokalt och molnet.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Haveriberedskap och virtuella skrivbord i Azure-molnet

* Upprätta fjärråtkomst till data, appar och skrivbord i Azure-molnet. Med anslutningar med hög bandbredd laddar du upp/hämtar data snabbt för att återhämta sig från incidenter. Nätverk med låg latens ger dig snabba svarstider som användarna förväntar sig av en skrivbordsapp.

* Replikera alla dina principer och nätverk i molnet med CloudSimple-portalen och välbekanta VMware-verktyg. Replikering minskar ansträngningen och risken med att skapa och hantera DR- och VDI-implementeringar.

### <a name="high-performance-applications-and-databases"></a>Högpresterande program och databaser

* Kör dina mest krävande arbetsbelastningar med hyperkonvergerade arkitekturen som tillhandahålls av CloudSimple.
* Kör Oracle, Microsoft SQL-server, mellanprogram och högpresterande no-SQL-databaser.
* Upplev molnet som ditt eget datacenter med höghastighetsnätverksanslutningar på 25 Gbit/s. Med höghastighetsanslutningar kan du köra hybridappar som sträcker sig över lokala, VMware på Azure och azure-privata arbetsbelastningar, utan att kompromissa med prestanda.

### <a name="true-hybrid"></a>Äkta hybrid

* Förena DevOps över VMware- och Azure-tjänster.
* Optimera VMware-administration för Azure-tjänster och lösningar som kan tillämpas i alla dina arbetsbelastningar.
* Få tillgång till offentliga molntjänster utan att behöva utöka ditt datacenter eller bakåtkrekta dina program.
* Centralisera identiteter, åtkomstkontrollprinciper, loggning och övervakning för VMware-program på Azure.

## <a name="limits"></a>Begränsningar

I följande tabell visas nodgränserna för resurser i ett privat moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln | 3 |
| Maximalt antal noder i ett kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett privat moln](create-private-cloud.md)
* Lär dig hur du [konfigurerar en privat molnmiljö](quickstart-create-private-cloud.md)
