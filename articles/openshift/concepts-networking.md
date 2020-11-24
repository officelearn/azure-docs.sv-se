---
title: Begrepp – nätverks diagram för Azure Red Hat på OpenShift 4
description: Nätverks diagram och översikt för Azure Red Hat OpenShift-nätverk
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: 3417b59d0be9e285f8793ef598abb7f98bda7549
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95527997"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Nätverk i Azure Red Hat på OpenShift 4

Den här guiden omfattar en översikt över nätverk i Azure Red Hat på OpenShift 4-kluster, tillsammans med ett diagram och en lista över viktiga slut punkter.

Mer information om kärn skiftets nätverks koncept finns i avsnittet om [nätverks dokumentation för Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Nätverks koncept i Azure Red Hat OpenShift

![Nätverks diagram för Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

När du distribuerar Azure Red Hat på OpenShift 4, finns hela klustret i ett virtuellt nätverk. I det här virtuella nätverket, noderna huvudnoder och arbetare varje Live i sitt eget undernät. Varje undernät använder en offentlig och en intern belastningsutjämnare.

## <a name="explanation-of-endpoints"></a>Förklaring av slut punkter

I följande lista beskrivs viktiga slut punkter i ett kluster med OpenShift i Azure Red Hat.

* **aro-pls**
    * Det här är en Azure Private-slutpunkt som används av Microsoft och Red Hat Sites Tillförlitlighets tekniker för att hantera klustret.
* **Aro – internt – lb**
    * Den här slut punkten balanserar trafiken till API-servern. Huvudnoderna finns i backend-poolen för den här belastningsutjämnaren.
* **Aro – offentligt – lb**
    * Om API: t är offentligt dirigerar den här slut punkten vägen och balanserar trafiken till API-servern. Den här slut punkten tilldelar en offentlig utgående IP-adress så att Masters kan komma åt Azure Resource Manager och rapportera tillbaka på kluster hälsan.
* **Aro – internt**
    * Den här slut punkten balanserar intern tjänst trafik. Arbetsnoderna i den här belastningsutjämnaren finns i backend-poolen.
    * Den här belastningsutjämnaren skapas inte som standard. Den här belastningsutjämnaren skapas när du skapar en tjänst av typen LoadBalancer med rätt anteckningar. Till exempel: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Nätverks principer (ingress)**
    * Stöds som en del av OpenShift-SDN
    * Som standard aktive ras tvång av kunder
    * V1 NetworkPolicy-kompatibla, men "utgångs-och IPBlock" stöds inte än
    * **aro**
    * Den här slut punkten balanserar trafiken till API-servern. Huvudnoderna finns i backend-poolen för den här belastningsutjämnaren.
  * **Aro – internt – lb**
    * Den här slut punkten används för all offentlig trafik. När du skapar och program och en väg, är detta sökvägen för ingångs trafik.
    * Denna belastningsutjämnare omfattar även utgående Internet anslutning från alla Pod som körs i arbetsnoderna genom Azure Load Balancer utgående regler.
        * För närvarande går det inte att konfigurera regler för utgående trafik. De tilldelar 1 024 TCP-portar till varje nod.
        * DisableOutboundSnat har inte kon figurer ATS i LB-reglerna, så poddar kan få som utgående IP-adress för alla offentliga IP-adresser i den här ALB.
        * Som en följd av de två föregående punkterna är det enda sättet att lägga till tillfälliga SNAT-portar genom att lägga till offentliga LoadBalancer-typ tjänster i ARO.
* **Nätverks principer (utgående)**
    * Utgående principer stöds med funktionen utgående brand vägg i OpenShift.
    * Endast ett per namnrymd/projekt.
    * Utgående principer stöds inte i namn området "default".
    * Utgående princip regler utvärderas i ordning (först till sista).
    * **Aro-utgående – pip**
        * Den här slut punkten fungerar som en offentlig IP-adress (PIP) för arbetsnoderna.
        * Den här slut punkten gör det möjligt för tjänster att lägga till en speciell IP-adress från ett kluster med OpenShift i Azure i en lista med tillåtna.
* **Aro-Node-NSG**
    * När du exponerar en tjänst skapar API: t en regel i den här nätverks säkerhets gruppen så trafikflödet passerar och når noderna.
    * Som standard tillåter den här nätverks säkerhets gruppen all utgående trafik. För närvarande kan utgående trafik bara begränsas till Azure Red Hat OpenShift-kontroll planet.
* **Aro-controlplane-NSG**
    * Den här slut punkten tillåter endast trafik att gå in via port 6443 för huvudnoderna.
* **Azure Container Registry**
    * Detta är ett behållar register som tillhandahålls och används av Microsoft internt.
        * Det här registret tillhandahåller värd plattforms avbildningar och kluster komponenter. Till exempel övervakning eller loggning av behållare.
        * Inte avsedd att användas av Azure Red Hat OpenShift-kunder.  
        * Skrivskyddad.
        * Anslutningar till det här registret sker över tjänstens slut punkt (intern anslutning mellan Azure-tjänster).
        * Detta interna register är inte tillgängligt utanför klustret som standard.
* **Private Link**
    * Tillåter nätverks anslutning från hanterings planet till ett kluster för kluster hantering.
    * Microsoft och Red Hat Sites Tillförlitlighets tekniker som hjälper dig att hantera klustret.

## <a name="networking-basics-in-openshift"></a>Grundläggande nätverk i OpenShift

OpenShift Software Defined Networking (SDN) används för att konfigurera ett överlägg nätverk med öppen vSwitch (OVS), en OpenFlow-implementering baserad på CNI-specifikationen (container Network Interface). SDN har stöd för olika plugin-program och nätverks principer är plugin-programmet som används i Azure Red Hat på OpenShift 4. All nätverkskommunikation hanteras av SDN, så inga extra vägar behövs i dina virtuella nätverk för att uppnå Pod för Pod kommunikation.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift-nätverks information

Följande funktioner är speciella för Azure Red Hat OpenShift:
* Det finns stöd för att ta med ditt eget virtuella nätverk.
* Pod-och service Network-CIDRs kan konfigureras.
* Noder och hanterare finns i olika undernät.
* Noder och huvud undernät för virtuella nätverk ska vara minst/27.
* Pod CIDR bör vara minst/18 i storlek (Pod-nätverket är icke-dirigerbart IP-adresser och används bara inuti OpenShift-SDN).
* Varje nod tilldelas/23 under nätet (512 IP-adresser) för sin poddar. Värdet kan inte ändras.
* Du kan inte koppla en POD till flera nätverk.
* Du kan inte konfigurera utgående statisk IP. (Det här är en OpenShift-funktion. Mer information finns i [Konfigurera utgående IP-adresser](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html).

## <a name="network-settings"></a>Nätverksinställningar

Följande nätverks inställningar är tillgängliga i Azure Red Hat OpenShift 4:

* **API-synlighet** – Ange API-synlighet när du kör [kommandot AZ Aro Create](tutorial-create-cluster.md#create-the-cluster).
    * "Offentlig" – API-servern kan nås av externa nätverk.
    * "Privat"-API-servern har tilldelats en privat IP-adress från huvud under nätet, endast tillgängligt med anslutna nätverk (peer-kopplade virtuella nätverk, andra undernät i klustret). En privat DNS-zon kommer att skapas för kundens räkning.
* **Ingress-synlighet** – Ange API-synlighet när du kör [kommandot AZ Aro Create](tutorial-create-cluster.md#create-the-cluster).
    * "Offentliga" vägar är som standard en offentlig Standard Load Balancer (detta kan ändras).
    * "Privata" vägar kommer att användas som standard till intern belastningsutjämnare (detta kan ändras).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Nätverks säkerhets grupper skapas i nodens resurs grupp, vilket är låst. Nätverks säkerhets grupperna tilldelas direkt till under näten, inte på nodens nätverkskort. Nätverks säkerhets grupperna är oföränderliga, vilket innebär att du inte har behörighet att ändra dem. 

Men med en offentligt synlig API-Server kan du inte skapa nätverks säkerhets grupper och tilldela dem till nätverkskorten.

## <a name="domain-forwarding"></a>Domän vidarebefordring
Azure Red Hat OpenShift använder CoreDNS. Domän vidarebefordran kan konfigureras (mer information finns i dokumentationen om hur du [använder DNS-vidarebefordran](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) ).

För närvarande kan du inte ta med din egen DNS till dina virtuella nätverk.


Mer information om utgående trafik och vilka Azure Red Hat OpenShift som har stöd för utgående finns i dokumentationen om [support policys](support-policies-v4.md) .
