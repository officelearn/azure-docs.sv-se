---
title: SAP HANA-infrastrukturkonfigurationer och åtgärder på Azure | Microsoft-dokument
description: Verksamhetsguide för SAP HANA-system som distribueras på virtuella Azure-datorer.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 07c8f84f2e37abd87953d8e4cb20b37258b25fda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920533"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer
Det här dokumentet innehåller vägledning för att konfigurera Azure-infrastruktur och driva SAP HANA-system som distribueras på virtuella Azure-datorer (VMs). Dokumentet innehåller även konfigurationsinformation för SAP HANA-utskalning för M128s VM SKU. Det här dokumentet är inte avsett att ersätta standarddokumentationen för SAP, som innehåller följande innehåll:

- [Sap-administrationsguide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP-installationsguider](https://service.sap.com/instguides)
- [SAP-anteckningar](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Krav
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenter:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-nätverk och virtuella nätverk](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure-lagring](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Mer information om SAP NetWeaver och andra SAP-komponenter på Azure finns i [AVSNITTET SAP på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) i [Azure-dokumentationen](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Grundläggande inställningsöverväganden
I följande avsnitt beskrivs grundläggande inställningsöverväganden för distribution av SAP HANA-system på virtuella Azure-datorer.

### <a name="connect-into-azure-virtual-machines"></a>Anslut till virtuella Azure-datorer
Som dokumenteras i [Azure virtuella datorer planeringsguide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), det finns två grundläggande metoder för att ansluta till Azure virtuella datorer:

- Anslut via internet och offentliga slutpunkter på en Hoppa VM eller på den virtuella datorn som kör SAP HANA.
- Anslut via en [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) eller Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Anslutning från plats till plats via VPN eller ExpressRoute är nödvändig för produktionsscenarier. Den här typen av anslutning behövs också för scenarier som inte är produktionslagreier som matas in i produktionsscenarier där SAP-programvara används. Följande bild visar ett exempel på anslutning mellan webbplatser:

![Anslutning på flera webbplatser](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Välj Azure VM-typer
De Azure VM-typer som kan användas för produktionsscenarier visas i [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). För scenarier som inte är produktionsscenarier är ett bredare utbud av inbyggda Azure VM-typer tillgängliga.

>[!NOTE]
> För scenarier som inte är produktionsrelaterade använder du de VM-typer som anges i [SAP-anteckningen #1928533](https://launchpad.support.sap.com/#/notes/1928533). Om du vill använda virtuella Azure-datorer för produktionsscenarier kontrollerar du om SAP HANA-certifierade virtuella datorer i listan SAP-publicerade [certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)finns .

Distribuera virtuella datorer i Azure med hjälp av:

- Azure-portalen.
- Azure PowerShell-cmdlets.
- The Azure CLI.

Du kan också distribuera en komplett installerad SAP HANA-plattform på Azure VM-tjänsterna via [SAP Cloud-plattformen](https://cal.sap.com/). Installationsprocessen beskrivs i [Distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) eller med automatiseringen som släpps [här](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> För att kunna använda M208xx_v2 virtuella datorer måste du vara försiktig med att välja din Linux-avbildning från avbildningsgalleriet för Azure VM. För att läsa detaljerna, läs artikeln [Minnesoptimerade virtuella datorstorlekar](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Lagringskonfiguration för SAP HANA
För lagringskonfigurationer och lagringstyper som ska användas med SAP HANA i Azure läser du dokumentet [SAP HANA Azure-konfigurationer för virtuell datorlagring](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Konfigurera virtuella Azure-nätverk
När du har anslutning mellan plats och plats till Azure via VPN eller ExpressRoute måste du ha minst ett virtuellt Azure-nätverk som är anslutet via en virtuell gateway till VPN- eller ExpressRoute-kretsen. I enkla distributioner kan den virtuella gatewayen distribueras i ett undernät till det virtuella Azure-nätverket (VNet) som även är värd för SAP HANA-instanserna. Om du vill installera SAP HANA skapar du ytterligare två undernät i det virtuella Azure-nätverket. Ett undernät är värd för de virtuella datorerna för att köra SAP HANA-instanserna. Det andra undernätet kör Jumpbox eller Management virtuella datorer som värd för SAP HANA Studio, annan programvara eller din programvara.

> [!IMPORTANT]
> Av funktioner, men ännu viktigare av prestandaskäl, stöds det inte att konfigurera [Virtuella Azure-nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/) i kommunikationssökvägen mellan SAP-programmet och DBMS-lagret i ett SAP NetWeaver-, Hybris- eller S/4HANA-baserat SAP-system. Kommunikationen mellan SAP-programskiktet och DBMS-lagret måste vara direkt. Begränsningen inkluderar inte [Azure ASG- och NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview) så länge som ASG- och NSG-reglerna tillåter en direkt kommunikation. Ytterligare scenarier där NVA:er inte stöds finns i kommunikationsvägar mellan virtuella Azure-datorer som representerar Linux Pacemaker-klusternoder och SBD-enheter enligt beskrivningen i [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Eller i kommunikationssökvägar mellan virtuella Azure-datorer och Windows Server SOFS som konfigurerats enligt beskrivningen i [Cluster en SAP ASCS/SCS-instans i ett Windows redundanskluster med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). NVA:er i kommunikationsvägar kan enkelt fördubbla nätverksfördröjningen mellan två kommunikationspartner, kan begränsa dataflödet i kritiska linjer mellan SAP-programlagret och DBMS-lagret. I vissa scenarier som observerats med kunder kan NVA:er orsaka att Pacemaker Linux-kluster misslyckas i fall där kommunikationen mellan Linux Pacemaker-klusternoderna måste kommunicera med sin SBD-enhet via en NVA.  
> 

> [!IMPORTANT]
> En annan design som **INTE** stöds är segregeringen av SAP-programlagret och DBMS-lagret i olika virtuella Azure-nätverk som inte är [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) med varandra. Vi rekommenderar att du segregerar SAP-programlagret och DBMS-lagret med undernät i ett virtuellt Azure-nätverk i stället för att använda olika virtuella Azure-nätverk. Om du bestämmer dig för att inte följa rekommendationen och i stället segregera de två lagren i olika virtuella nätverk, måste de två virtuella nätverken [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Tänk på att nätverkstrafik mellan två [peer-peer-azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuella nätverk är föremål för överföringskostnader. Med den enorma datavolymen i många Terabytes som utbyts mellan SAP-programskiktet och DBMS-lagret kan betydande kostnader ackumuleras om SAP-programlagret och DBMS-lagret är segregerat mellan två peered Azure virtuella nätverk. 

När du installerar de virtuella datorerna för att köra SAP HANA behöver de virtuella datorerna:

- Två virtuella nätverkskort installerade: ett nätverkskort för att ansluta till hanteringsundernätet och ett nätverkskort för att ansluta från det lokala nätverket eller andra nätverk, till SAP HANA-instansen i Azure VM.
- Statiska privata IP-adresser som distribueras för båda virtuella nätverkskort.

> [!NOTE]
> Du bör tilldela statiska IP-adresser via Azure-medel till enskilda virtuella NCC:er. Du bör inte tilldela statiska IP-adresser inom gästoperativsystemet till ett vNIC-nätverk. Vissa Azure-tjänster som Azure Backup Service förlitar sig på det faktum att åtminstone det primära virtuella nätverkskortet är inställt på DHCP och inte statiska IP-adresser. Se även dokumentet [Felsöka Azure virtuell dator backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Om du behöver tilldela flera statiska IP-adresser till en virtuell dator måste du tilldela flera virtuella datorer till en virtuell dator.
>
>

Men för distributioner som är bestående måste du skapa en virtuell datacenternätverksarkitektur i Azure. Den här arkitekturen rekommenderar separation av Azure VNet Gateway som ansluter till lokalt till ett separat Azure VNet.This architecture recommends the separation of the Azure VNet Gateway that connects to on-premises into a separate Azure VNet. Detta separata virtuella nätverk bör vara värd för all trafik som lämnar antingen till lokalt eller till Internet. Med den här metoden kan du distribuera programvara för granskning och loggningstrafik som kommer in i det virtuella datacentret i Azure i det här separata navet VNet. Så du har ett virtuella nätverk som är värd för all programvara och konfigurationer som relaterar till in- och utgående trafik till din Azure-distribution.

Artiklarna [Azure Virtual Datacenter: A Network Perspective](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) och [Azure Virtual Datacenter och Enterprise Control Plane](https://docs.microsoft.com/azure/architecture/vdc/) ger mer information om den virtuella datacentermetoden och relaterad Azure VNet-design.


>[!NOTE]
>Trafik som flödar mellan ett nav-VNet och eker-VNet med [Azure VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) är föremål för extra [kostnader](https://azure.microsoft.com/pricing/details/virtual-network/). Baserat på dessa kostnader kan du behöva överväga att göra kompromisser mellan att köra en strikt hubb och ekernätverksdesign och köra flera [Azure ExpressRoute Gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) som du ansluter till "ekrar" för att kringgå VNet-peering. Azure ExpressRoute Gateways medför dock även extra [kostnader.](https://azure.microsoft.com/pricing/details/vpn-gateway/) Du kan också få ytterligare kostnader för programvara från tredje part som du använder för nätverkstrafikloggning, granskning och övervakning. Beroende på kostnaderna för datautbyte via VNet-peering på ena sidan och kostnader som skapas av ytterligare Azure ExpressRoute Gateways och ytterligare programvarulicenser, kan du välja mikrosegmentering inom ett virtuella nätverk genom att använda undernät som isoleringsenhet i stället för virtuella nätverk.


En översikt över de olika metoderna för att tilldela IP-adresser finns [i IP-adresstyper och allokeringsmetoder i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

För virtuella datorer som kör SAP HANA bör du arbeta med statiska IP-adresser tilldelade. Anledningen är att vissa konfigurationsattribut för HANA-referens-IP-adresser.

[Azure Network Security Groups (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) används för att dirigera trafik som dirigeras till SAP HANA-instansen eller jumpboxen. NSG och så småningom [Application Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) är associerade till SAP HANA undernätet och Management undernätet.

Följande bild visar en översikt över ett grovt distributionsschema för SAP HANA efter en hubb- och eker-VNet-arkitektur:

![Grovt distributionsschema för SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Om du vill distribuera SAP HANA i Azure utan en plats-till-plats-anslutning vill du fortfarande skydda SAP HANA-instansen från det offentliga internet och dölja den bakom en vidarebefordran proxy. I det här grundscenariot förlitar sig distributionen på Azure-inbyggda DNS-tjänster för att matcha värdnamn. I en mer komplex distribution där offentliga IP-adresser används är Azure-inbyggda DNS-tjänster särskilt viktiga. Använd Azure NSGs och [Azure NVA:er](https://azure.microsoft.com/solutions/network-appliances/) för att styra, övervaka routningen från internet till din Azure VNet-arkitektur i Azure. Följande bild visar ett grovt schema för distribution av SAP HANA utan anslutning från plats till plats i en hubb- och eker-VNet-arkitektur:
  
![Grov distributionsschema för SAP HANA utan anslutning från plats till plats](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

En annan beskrivning av hur du använder Azure NVAs för att styra och övervaka åtkomst från Internet utan hubb- och eker-VNet-arkitektur finns i artikeln [Distribuera högtillgängliga virtuella nätverksinstallationer](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurera Azure-infrastruktur för SAP HANA-utskalning
För att ta reda på de Azure VM-typer som är certifierade för antingen OLAP-utskalning eller S/4HANA-utskalning, kontrollera [SAP HANA-maskinvarukatalogen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). En bock i kolumnen "Klustring" anger utskalningsstöd. Programtypen anger om OLAP-utskalning eller S/4HANA-utskalning stöds. Mer information om noder som certifierats i utskalning för var och en av de virtuella datorerna finns i informationen om posterna i den särskilda VM-SKU som anges i MASKINVARUKATALOGEN FÖR SAP HANA.

De minsta OS-versionerna för distribution av utskalningskonfigurationer i virtuella Azure-datorer kontrollerar information om posterna i den särskilda VM-SKU som anges i SAP HANA-maskinvarukatalogen. Av en OLAP-utskalningskonfiguration för n-nod fungerar en nod som huvudnod. De andra noderna upp till gränsen för certifieringen fungerar som arbetsnod. Ytterligare standby-noder räknas inte in i antalet certifierade noder

>[!NOTE]
> Azure VM-utskalningsdistributioner av SAP HANA med standby-nod är endast möjliga med azure [netapp-fillagring.](https://azure.microsoft.com/services/netapp/) Ingen annan SAP HANA-certifierad Azure-lagring tillåter konfiguration av SAP HANA-väntenoder
>

För /hana/shared rekommenderar vi även användning av [Azure NetApp-filer](https://azure.microsoft.com/services/netapp/). 

En typisk grundläggande design för en enda nod i en skalningskonfiguration kommer att se ut:

![Utskalningsgrunderna för en enskild nod](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

Den grundläggande konfigurationen av en VM-nod för SAP HANA-utskalning ser ut:

- För **/hana/shared**använder du den inbyggda NFS-tjänsten som tillhandahålls via Azure NetApp Files. 
- Alla andra diskvolymer delas inte mellan de olika noderna och baseras inte på NFS. Installationskonfigurationer och steg för utskalning av HANA-installationer med icke-delade **/hana/data** och **/hana/log** tillhandahålls ytterligare senare i det här dokumentet. För HANA-certifierad lagring som kan användas kontrollerar du artikeln [SAP HANA Azure-konfigurationer för virtuell datorlagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).


Dimensionering av volymer eller diskar, måste du kontrollera dokumentet [SAP HANA TDI lagringskrav](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), för den storlek som krävs beroende på antalet arbetsnoder. Dokumentet släpper en formel som du behöver använda för att få önskad kapacitet för volymen

De andra designkriterierna som visas i grafiken i den enda nodkonfigurationen för en utskalning av SAP HANA-vm är det virtuella nätverket eller bättre undernätskonfigurationen. SAP rekommenderar starkt en separation av klienten/programmet som står inför trafik från kommunikationen mellan HANA-noderna. Som visas i grafiken uppnås detta mål genom att ha två olika virtuella NIC-enheter anslutna till den virtuella datorn. Båda vNICs är i olika undernät, har två olika IP-adresser. Du kan sedan styra trafikflödet med routningsregler med NSG eller användardefinierade vägar.

Särskilt i Azure finns det inga medel och metoder för att upprätthålla kvaliteten på tjänsten och kvoter på specifika virtuella NCC. Därför öppnar separationen av klient-/programläge och kommunikation inom noden inga möjligheter att prioritera en trafikström över den andra. I stället förblir separationen ett mått på säkerhet när det gäller att skydda den intranodkommunikation som utförs av skalningskonfigurationerna.  

>[!NOTE]
>SAP rekommenderar att nätverkstrafik separeras till klient-/programsidan och intranodtrafiken enligt beskrivningen i det här dokumentet. Därför rekommenderas att en arkitektur sätts på plats som visas i den senaste grafiken. Kontakta också din säkerhets- och efterlevnadsgrupp för krav som avviker från rekommendationen 
>

Ur nätverkssynpunkt skulle den minsta nödvändiga nätverksarkitekturen se ut:

![Utskalningsgrunderna för en enskild nod](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Installera SAP HANA-utskalning n Azure
Installera en skalnings-utSas-konfiguration, måste du utföra grova steg av:

- Distribuera nya eller anpassa en befintlig Azure VNet-infrastruktur
- Distribuera nya virtuella datorer med Azure Managed Premium Storage, Ultra-diskvolymer och/eller NFS-volymer baserat på ANF
- - Anpassa nätverksdirigering för att se till att till exempel kommunikationen inom noden mellan virtuella datorer inte dirigeras via en [NVA](https://azure.microsoft.com/solutions/network-appliances/). 
- Installera HUVUDnoden SAP HANA.
- Anpassa konfigurationsparametrarna för SAP HANA-huvudnoden
- Fortsätt med installationen av SAP HANA-arbetsnoderna

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installation av SAP HANA i skalningskonfiguration
När din Azure VM-infrastruktur distribueras och alla andra förberedelser görs måste du installera SAP HANA-utskalningskonfigurationerna i följande steg:

- Installera SAP HANA-huvudnoden enligt SAP:s dokumentation
- Om du använder Azure Premium Storage eller Ultra-disklagring med icke-delade diskar med /hana/data och /hana/log måste du ändra filen global.ini och lägga till parametern 'basepath_shared = no' till global.ini-filen. Med den här parametern kan SAP HANA köras i utskalning utan delade **/hana/data-** och **/hana/log-volymer** mellan noderna. Information dokumenteras i [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991). Om du använder NFS-volymer baserat på ANF för /hana/data och /hana/log behöver du inte göra den här ändringen
- Efter den slutliga ändringen av parametern global.ini startar du om SAP HANA-instansen
- Lägg till ytterligare arbetsnoder. Se <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>även . Ange det interna nätverket för SAP HANA-internodkommunikation under installationen eller efteråt med hjälp av till exempel den lokala hdblcm. Mer detaljerad dokumentation finns i [även SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Information om hur du konfigurerar ett SAP HANA-utskalningssystem med standby-nod på SUSE Linux beskrivs i detalj i [Distribuera ett SAP HANA-utskalningssystem med väntenod på Azure-virtuella datorer med hjälp av Azure NetApp-filer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse). Motsvarande dokumentation för Red Hat finns i artikeln [Distribuera ett SAP HANA-utskalningssystem med väntenod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 för virtuella Azure-datorer

Förutom SAP HANA-certifieringarna på virtuella azure M-serien stöds SAP HANA Dynamic Tiering 2.0 också på Microsoft Azure (se SAP HANA Dynamic Tiering-dokumentationslänkar längre ner). Även om det inte finns någon skillnad i att installera produkten eller använda den, till exempel via SAP HANA Cockpit inuti en Virtuell Azure-dator, finns det några viktiga objekt som är obligatoriska för officiell support på Azure. Dessa viktiga punkter beskrivs nedan. Under hela artikeln kommer förkortningen "DT 2.0" att användas i stället för det fullständiga namnet Dynamic Tiering 2.0.

SAP HANA Dynamic Tiering 2.0 stöds inte av SAP BW eller S4HANA. Huvudsakliga användningsfall just nu är inbyggda HANA-program.


### <a name="overview"></a>Översikt

Bilden nedan ger en översikt över DT 2.0-supporten på Microsoft Azure. Det finns en uppsättning obligatoriska krav som måste följas för att uppfylla den officiella certifieringen:

- DT 2.0 måste installeras på en dedikerad Azure VM. Den kanske inte körs på samma virtuella dator där SAP HANA körs
- SAP HANA och DT 2.0 virtuella datorer måste distribueras inom samma Azure Vnet
- Virtuella datorerna SAP HANA och DT 2.0 måste distribueras med Azure-accelererade nätverk aktiverade
- Lagringstypen för virtuella DT 2.0-datorer måste vara Azure Premium Storage
- Flera Azure-diskar måste vara kopplade till den virtuella datorn för DT 2.0
- Det krävs för att skapa en programvarurazzia / randig volym (antingen via LVM eller mdadm) med striping över Azure-diskarna

Mer information kommer att förklaras i följande avsnitt.

![Översikt över SAP HANA DT 2.0-arkitektur](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedikerad Azure VM för SAP HANA DT 2.0

På Azure IaaS stöds DT 2.0 endast på en dedikerad virtuell dator. Det är inte tillåtet att köra DT 2.0 på samma Azure VM där HANA-instansen körs. Inledningsvis två VM-typer kan användas för att köra SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Se beskrivning av vm-typ [här](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Med tanke på den grundläggande idén om DT 2.0, som handlar om avlastning "varma" data för att spara kostnader är det vettigt att använda motsvarande VM storlekar. Det finns ingen strikt regel men när det gäller möjliga kombinationer. Det beror på den specifika kundarbetsbelastningen.

Rekommenderade konfigurationer skulle vara:

| VIRTUELL DATORTYP FÖR SAP HANA | Typ av virtuell dator med DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms (M64ms) | E32sv3 |
| M64s | E32sv3 |


Alla kombinationer av SAP HANA-certifierade virtuella datorer i M-serien med DT 2.0-virtuella datorer som stöds (M64-32ms och E32sv3) är möjliga.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure-nätverk och SAP HANA DT 2.0

Installera DT 2.0 på en dedikerad virtuell dator kräver nätverksdataflöde mellan DT 2.0 VM och SAP HANA VM på 10 Gb minimum. Därför är det obligatoriskt att placera alla virtuella datorer i samma Azure Vnet och aktivera Azure-accelererade nätverk.

Se ytterligare information om Azure-accelererade nätverk [här](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM-lagring för SAP HANA DT 2.0

Enligt vägledning om bästa praxis för DT 2.0 bör disk-IO-dataflödet vara minst 50 MB/sek per fysisk kärna. Om man tittar på specifikationen för de två Azure VM-typerna, som stöds för DT 2.0, ser den maximala disk-IO-dataflödesgränsen för den virtuella datorn ut:

- E32sv3 : 768 MB/sek (ej ansluten) vilket innebär ett förhållande på 48 MB/sek per fysisk kärna
- M64-32ms: 1000 MB/sek (ej ansluten) vilket innebär ett förhållande på 62,5 MB/sek per fysisk kärna

Det krävs för att koppla flera Azure-diskar till DT 2.0 VM och skapa en programvarurazzia (striping) på OS-nivå för att uppnå maxgränsen för diskdataflöde per virtuell dator. En enda Azure-disk kan inte tillhandahålla dataflödet för att nå max VM-gränsen i detta avseende. Azure Premium-lagring är obligatoriskt för att köra DT 2.0. 

- Information om tillgängliga Azure-disktyper finns [här](../../windows/disks-types.md)
- Detaljer om att skapa programvara raid via mdadm kan hittas [här](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Information om hur du konfigurerar LVM för att skapa en randig volym för maximalt dataflöde hittar du [här](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Beroende på storlekskraven finns det olika alternativ för att nå maxdataflödet för en virtuell dator. Här är möjliga datavolymdiskkonfigurationer för varje DT 2.0 VM-typ för att uppnå den övre gränsen för vm-dataflöde. Den virtuella datorn E32sv3 bör betraktas som en ingångsnivå för mindre arbetsbelastningar. Om det skulle visa sig att det inte är tillräckligt snabbt kan det vara nödvändigt att ändra storlek på den virtuella datorn till M64-32ms.
Eftersom M64-32ms VM har mycket minne, kan IO-belastningen inte nå gränsen, särskilt för läsintensiva arbetsbelastningar. Därför kan färre diskar i stripe-uppsättningen vara tillräckligt beroende på kundens specifika arbetsbelastning. Men för att vara på den säkra sidan diskkonfigurationerna nedan valdes för att garantera maximal genomströmning:


| VM-SKU | Disk Config 1 | Disk Config 2 | Disk Config 3 | Disk Config 4 | Disk Config 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Särskilt om arbetsbelastningen är läsintensiv kan den öka I/O-prestanda för att aktivera Azure-värdcachen "skrivskyddad" som rekommenderas för datavolymerna för databasprogramvara. Medan Azure-värddiskcachen för transaktionsloggen måste vara "ingen". 

När det gäller storleken på loggvolymen en rekommenderad utgångspunkt är en heuristisk av 15% av datastorleken. Skapandet av loggvolymen kan åstadkommas med hjälp av olika Azure-disktyper beroende på kostnad och dataflödeskrav. För loggvolymen krävs hög I/O-dataflöde.  Vid användning av vm-typen M64-32ms är det obligatoriskt att aktivera [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Azure Write Accelerator ger optimal diskskrivningssvarstid för transaktionsloggen (endast tillgänglig för M-serien). Det finns några objekt att tänka på men som det maximala antalet diskar per vm-typ. Information om Write Accelerator hittar du [här](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Här är några exempel på hur du dimensionerar loggvolymen:

| datavolymstorlek och disktyp | loggvolym och disktyp config 1 | loggvolym och disktyp config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Precis som för SAP HANA-utskalning måste katalogen /hana/shared delas mellan SAP HANA-datorn och den virtuella datorn för DT 2.0. Samma arkitektur som för SAP HANA-utskalning med dedikerade virtuella datorer, som fungerar som en NFS-server med hög tillgänglig, rekommenderas. För att tillhandahålla en delad säkerhetskopieringsvolym kan samma design användas. Men det är upp till kunden om HA skulle vara nödvändigt eller om det är tillräckligt att bara använda en dedikerad virtuell dator med tillräcklig lagringskapacitet för att fungera som en backup-server.



### <a name="links-to-dt-20-documentation"></a>Länkar till DT 2.0-dokumentation 

- [INSTALLATIONS- och uppdateringsguide för SAP HANA Dynamic Tiering](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA Dynamiska nivåindelningskurser och resurser](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamisk nivåindelning PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamiska nivåindelningsförbättringar](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Åtgärder för distribution av SAP HANA på virtuella Azure-datorer
I följande avsnitt beskrivs några av de åtgärder som är relaterade till distribution av SAP HANA-system på virtuella Azure-datorer.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Säkerhetskopiera och återställa åtgärder på virtuella Azure-datorer
I följande dokument beskrivs hur du säkerhetskopierar och återställer DIN SAP HANA-distribution:

- [Översikt över SAP HANA-säkerhetskopiering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA-säkerhetskopiering på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA lagring ögonblicksbild riktmärke](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Starta och starta om virtuella datorer som innehåller SAP HANA
En framträdande funktion i Det offentliga Azure-molnet är att du bara debiteras för dina datorminuter. När du till exempel stänger av en virtuell dator som kör SAP HANA debiteras du bara för lagringskostnaderna under den tiden. En annan funktion är tillgänglig när du anger statiska IP-adresser för dina virtuella datorer i den första distributionen. När du startar om en virtuell dator med SAP HANA startas den virtuella datorn om med sina tidigare IP-adresser. 


### <a name="use-saprouter-for-sap-remote-support"></a>Använda SAProuter för SAP-fjärrsupport
Om du har en plats-till-plats-anslutning mellan dina lokala platser och Azure, och du kör SAP-komponenter, kör du förmodligen redan SAProuter. I så fall fyller du i följande objekt för fjärrsupport:

- Underhåll den privata och statiska IP-adressen för den virtuella datorn som är värd för SAP HANA i SAProuter-konfigurationen.
- Konfigurera NSG för undernätet som är värd för HANA VM för att tillåta trafik genom TCP/IP-port 3299.

Om du ansluter till Azure via Internet och du inte har en SAP-router för den virtuella datorn med SAP HANA måste du installera komponenten. Installera SAProuter i en separat virtuell dator i undernätet Hantering. Följande bild visar ett grovt schema för distribution av SAP HANA utan anslutning från plats till plats och med SAProuter:

![Grov distributionsschema för SAP HANA utan anslutning från plats till plats och SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Var noga med att installera SAProuter i en separat virtuell dator och inte i din Jumpbox VM. Den separata virtuella datorn måste ha en statisk IP-adress. Om du vill ansluta din SAProuter till SAProuter som är värd för SAP kontaktar du SAP för en IP-adress. (SAProuter som är värd för SAP är motsvarigheten till DEN SAProuter-instans som du installerar på den virtuella datorn.) Använd IP-adressen från SAP för att konfigurera SAProuter-instansen. I konfigurationsinställningarna är den enda nödvändiga porten TCP-port 3299.

Mer information om hur du konfigurerar och underhåller fjärrsupportanslutningar via SAProuter finns i [SAP-dokumentationen](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hög tillgänglighet med SAP HANA på inbyggda virtuella Azure-datorer
Om du kör SUSE Linux Enterprise Server eller Red Hat kan du skapa ett Pacemaker-kluster med STONITH-enheter. Du kan använda enheterna för att ställa in en SAP HANA-konfiguration som använder synkron replikering med HANA System Replication och automatisk redundans. Mer information finns i avsnittet "nästa steg".

## <a name="next-steps"></a>Efterföljande moment
Bekanta dig med de artiklar som anges
- [Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer på Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

