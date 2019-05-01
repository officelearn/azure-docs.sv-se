---
title: Privata moln i VMware-lösning genom CloudSimple - Azure
description: Läs mer om CloudSimple privata moln och begrepp.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577052"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple privat moln-översikt

CloudSimple transformerar och utökar VMware-arbetsbelastningar till offentliga moln på några minuter. Med tjänsten CloudSimple kan använda du VMware internt i Azure bare metal-infrastrukturen. Distributionen ligger på Azure-platser och integreras helt med resten av Azure-molnet.

* CloudSimple lösningen tillhandahåller fullständig kontinuiteten i VMware. Den här lösningen ger dig fördelarna med molnet:
  * Elasticitet
  * Innovation
  * Effektivitet
* Med CloudSimple dra nytta av en cloud-förbrukningsmodell som sänker den totala ägandekostnaden. Den erbjuder också på begäran etablering, betala som du-växer och optimering av kapacitet.
* CloudSimple är helt kompatibel med:
  * Befintliga verktyg
  * Färdigheter
  * Processer
* Denna kompatibilitet gör det möjligt för dina team att hantera arbetsbelastningar på Azure-molnet, utan att störa dina principer:
  * Nätverk
  * Säkerhet  
  * Dataskydd  
  * Granska
* CloudSimple hanterar infrastrukturen och alla nödvändiga nätverks- och hantering av tjänster. Med CloudSimple kan ditt team kan fokusera på:
  * Affärsvärde
  * Programetablering
  * Verksamhetskontinuitet
  * Support
  * Policyframtvingande

## <a name="private-cloud-environment-overview"></a>Översikt över miljö för privat moln

Ett privat moln är en isolerad VMware-stacken, till exempel dessa miljöer:

* ESXi-värdar
* vCenter
* vSAN
* NSX

Privata moln som hanteras av en vCenter-server i sin egen hanteringsdomänen.

Stacken körs på:

* Dedikerade noder
* Isolerade bare metal maskinvarunoder

Användarna använder stack via native VMware-verktyg, bland annat:

* vCenter
* NSX Manager

Du kan distribuera dedikerade noder i Azure-platser. Du kan sedan hantera dem med Azure och CloudSimple. Ett privat moln består av en eller flera vSphere-kluster och varje kluster innehåller 3 till 16 noder.

Du kan skapa ett privat moln med köpt noder:

* Betala per användning-noder
* Reserverade, dedikerade noder

Du kan ansluta det privata molnet till din lokala miljö och Azure-nätverket med hjälp av följande anslutningar:

* Skydda
* Privat VPN
* Azure ExpressRoute

Privat moln-miljön har utformats för att undvika att ha en enskild felpunkt:

* ESXi-kluster har konfigurerats med hög tillgänglighet för vSphere och storlek för att ha minst en extra nod för återhämtning.
* virtuellt SAN tillhandahåller redundanta primär lagring. virtuellt SAN-nätverk kräver minst tre noder för att ge skydd mot ett enstaka fel. Du kan konfigurera virtuellt SAN-nätverk för att ge högre återhämtning för större kluster.
* Du kan konfigurera vCenter och PSC NSX Manager virtuella datorer med RAID 10-principen för lagring som skydd mot fel för lagring. Sedan kan skyddas de av vSphere HA mot fel på noden.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenarier för att distribuera ett privat moln

* **Data center dras tillbaka eller migrering**

  * Få ytterligare kapacitet när du når databasens begränsningar av ditt befintliga datacenter eller uppdaterar maskinvara.
  * Lägg till nödvändiga kapacitet i molnet och slipper besväret med att hantera uppdateringar av maskinvara.
  * Minska risken och kostnaderna för molnmigreringar, jämfört med tidskrävande konverteringar eller rearchitecture.
  * Använd välkända VMware-verktyg och kunskaper för att påskynda molnmigreringar. Använda Azure-tjänster i molnet, modernisera dina program i din takt.

* **Expandera på begäran**

  * Utöka till molnet för att uppfylla oväntade behov, till exempel nya utvecklingsmiljöer eller toppar på säsongens kapacitet.
  * Skapa ny kapacitet på begäran och hålla dem så länge du behöver den.
  * Minska dina direkta investeringar, snabbare hastighet för etablering och minska komplexiteten med samma arkitektur och principer för både lokalt och i molnet.

* **Haveriberedskap och virtuella skrivbord i Azure-molnet**

  * Upprätta fjärråtkomst till data, appar och stationära datorer i Azure-molnet. Med snabba anslutningar kan du ladda upp / ned data snabbt för att komma tillrätta med incidenter. Låg latens nätverk ger du snabba svar gånger som användarna förväntar sig från en skrivbordsapp.

  * Replikera alla principer och nätverk i molnet med CloudSimple portal och välbekanta VMware-verktyg. Replikeringen minskar ansträngning och risken för att skapa och hantera DR och VDI-implementeringar.

* **Program med höga prestanda och databaser**

  * Kör dina mest krävande arbetsbelastningar med hyperkonvergerat-arkitekturen som tillhandahålls av CloudSimple.
  * Kör Oracle, Microsoft SQL server, mellanprogram system och högpresterande icke-SQL-databaser.

  * Upplev molnet som ditt eget datacenter med hög hastighet 25 Gbit/s-nätverksanslutningar. Snabba anslutningar kan du köra hybridappar som täcker användning lokalt VMware på Azure, och Azure privata arbetsbelastningar, utan att kompromissa med prestanda.

* **Verklig**

  * Förena DevOps för VMware och Azure-tjänster.
  * Optimera VMware administration för Azure-tjänster och lösningar som kan användas i alla arbetsbelastningar.
  * Komma åt offentliga molntjänster utan att behöva Utöka ditt datacenter eller Omforma dina program.
  * Centralisera identiteter, principer för åtkomstkontroll, loggning och övervakning för VMware-program på Azure.

## <a name="limits"></a>Limits

Tabellen nedan visar noden gränserna på resurser för ett privat moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antalet noder för att skapa ett privat moln | 3 |
| Högsta antalet noder i ett kluster på ett privat moln | 16 |
| Högsta antalet noder i ett privat moln | 64 |
| Minsta antalet noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Lär dig hur du [konfigurera en privat molnmiljö](quickstart-create-private-cloud.md)