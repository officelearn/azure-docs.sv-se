---
title: "Nätverkssäkerhetsgrupper i Azure | Microsoft Docs"
description: "Lär dig mer om hur du isolerar och styr trafikflödet i dina virtuella nätverk med hjälp av den distribuerade brandväggen i Azure genom att använda nätverkssäkerhetsgrupper."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 4043c68a3c8559eab6f5e4352bb599015366e5b5
ms.contentlocale: sv-se
ms.lasthandoff: 06/01/2017


---
# Filtrera nätverkstrafik med nätverkssäkerhetsgrupper
<a id="filter-network-traffic-with-network-security-groups" class="xliff"></a>

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). Nätverkssäkerhetsgrupper kan kopplas till undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverkskort (NIC) som är anslutna till virtuella datorer (Resource Manager). När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafiken kan begränsas ytterligare genom att en nätverkssäkerhetsgrupp associeras med en virtuell dator eller ett nätverkskort.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln täcker bägge modellerna, men Microsoft rekommenderar de flesta nya distributioner att använda Resource Manager-modellen.

## NSG-resurs
<a id="nsg-resource" class="xliff"></a>
Nätverkssäkerhetsgrupper har följande egenskaper:

| Egenskap | Beskrivning | Villkor | Överväganden |
| --- | --- | --- | --- |
| Namn |Namn för NSG:n |Måste vara unikt i regionen.<br/>Kan innehålla bokstäver, siffror, understreck, punkter och bindestreck.<br/>Måste börja med en bokstav eller en siffra.<br/>Måste sluta med en bokstav, en siffra eller understreck.<br/>Får inte vara längre än 80 tecken. |Eftersom du kan behöva skapa flera nätverkssäkerhetsgrupper bör du se till att du har en namngivningskonvention som gör det lätt att identifiera nätverkssäkerhetsgruppernas funktion. |
| Region |Den Azure-[region](https://azure.microsoft.com/regions) som nätverkssäkerhetsgruppen skapas i. |Nätverkssäkerhetsgrupper kan bara vara kopplade till resurser i samma region som nätverkssäkerhetsgruppen. |Information om hur många nätverkssäkerhetsgrupper du kan skapa per region finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#virtual-networking-limits-classic).|
| Resursgrupp |Den [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups) som nätverkssäkerhetsgruppen finns i. |Även om en nätverkssäkerhetsgrupp finns i en viss resursgrupp kan den vara kopplad till resurser i valfri resursgrupp, förutsatt att resursen tillhör samma Azure-region som nätverkssäkerhetsgruppen. |Resursgrupper används för att hantera flera resurser tillsammans, som en distributionsenhet.<br/>Om du vill kan du gruppera nätverkssäkerhetsgruppen med resurser som den är associerad med. |
| Regler |Inkommande eller utgående regler som definierar vilken trafik som tillåts eller nekas. | |Mer information finns i avsnittet om [NSG-regler](#Nsg-rules) i den här artikeln. |

> [!NOTE]
> Slutpunktsbaserade ACL:er och nätverkssäkerhetsgrupper stöds inte på samma VM-instans. Om du vill använda en NSG och redan har en slutpunkts-ACL på plats så kan du först ta bort slutpunkts-ACL:n. Information om hur du tar bort en åtkomstkontrollista finns i artikeln [Managing Access Control Lists (ACLs) for Endpoints by using PowerShell](virtual-networks-acl-powershell.md) (Hantera åtkomstkontrollistor (ACL:er) för slutpunkter med hjälp av PowerShell).
> 

### NSG-regler
<a id="nsg-rules" class="xliff"></a>
NSG-regler har följande egenskaper:

| Egenskap | Beskrivning | Villkor | Överväganden |
| --- | --- | --- | --- |
| **Namn** |Regelns namn. |Måste vara unikt i regionen.<br/>Kan innehålla bokstäver, siffror, understreck, punkter och bindestreck.<br/>Måste börja med en bokstav eller en siffra.<br/>Måste sluta med en bokstav, en siffra eller understreck.<br/>Får inte vara längre än 80 tecken. |Eftersom du kan ha flera regler i en nätverkssäkerhetsgrupp är det bra om du följer en namngivningskonvention som gör det lätt att identifiera regelns funktion. |
| **Protokoll** |Protokoll att matcha för regeln. |TCP, UDP eller * |Användningen av * som protokoll innefattar ICMP (enbart öst-väst-trafik), samt UDP och TCP, och kan minska antalet regler som du behöver.<br/>Användningen av * kan dock bli för bred, och därför rekommenderar vi endast att du använder * om det verkligen behövs. |
| **Källportintervall** |Källportintervall att matcha för regeln. |Enskilda portnummer mellan 1 och 65535, portintervall (exempel: 1-65635) eller * (för alla portar). |Källportar kan vara tillfälliga. Såvida inte klientprogrammet använder en viss port bör du använda * i de flesta fall.<br/>Försök att använda portintervall i så stor utsträckning som möjligt för att undvika att använda flera regler.<br/>Flera portar eller portintervall kan inte grupperas med kommatecken. |
| **Målportintervall** |Målportintervall att matcha för regeln. |Enskilda portnummer mellan 1 och 65535, portintervall (exempel: 1-65535) eller \* (för alla portar). |Försök att använda portintervall i så stor utsträckning som möjligt för att undvika att använda flera regler.<br/>Flera portar eller portintervall kan inte grupperas med kommatecken. |
| **Källadress-prefix** |Källadressprefix eller tagg att matcha för regeln. |Enskild IP-adress (exempel: 10.10.10.10), IP-undernät (exempel: 192.168.1.0/24), [standardtagg](#default-tags) eller * (för alla adresser). |Överväg att använda intervall, standardtaggar och * för att minska antalet regler. |
| **Måladress-prefix** |Måladressprefix eller tagg att matcha för regeln. | Enskild IP-adress (exempel: 10.10.10.10), IP-undernät (exempel: 192.168.1.0/24), [standardtagg](#default-tags) eller * (för alla adresser). |Överväg att använda intervall, standardtaggar och * för att minska antalet regler. |
| **Riktning** |Trafikriktning att matcha för regeln. |Inkommande eller utgående. |Regler för inkommande och utgående bearbetas separat, baserat på riktning. |
| **Prioritet** |Regler kontrolleras enligt prioritetsordning. När villkoren för en regel uppfylls testas inga fler regler. | Tal mellan 100 och 4096. | Överväg att skapa regler som hoppar mellan prioriteter i steg om 100 för varje regel så att det finns plats för nya regler som du kanske skapar senare. |
| **Åtkomst** |Typ av åtkomst som ska tillämpas om regeln matchar. | Tillåt eller neka. | Tänk på att paketet ignoreras om ingen ”tillåt”-regel hittas för ett paket. |

Nätverkssäkerhetsgrupper innehåller två regeluppsättningar: inkommande och utgående. En regels prioritet måste vara unik inom varje uppsättning. 

![NSG-regelbearbetning](./media/virtual-network-nsg-overview/figure3.png) 

Föregående bild visar hur NSG-regler bearbetas.

### Standardtaggar
<a id="default-tags" class="xliff"></a>
Standardtaggar är systemdefinierade identifierare för en viss kategori av IP-adresser. Du kan använda standardtaggar i egenskaperna för **källadress-prefix** och **måladress-prefix** för alla regler. Det finns tre standardtaggar som du kan använda:

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** för klassisk): Den här taggen innehåller adressutrymmet för det virtuella nätverket (CIDR-intervall definierade i Azure), alla anslutna lokala adressutrymmen och anslutna virtuella Azure-nätverk (lokala nätverk).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** för klassisk): Den här taggen anger belastningsutjämnaren för Azures infrastruktur. Taggen översätts till en IP-adress för Azure-datacentret som Azures hälsoavsökning kommer från.
* **Internet** (Resource Manager) (**INTERNET** för klassisk): Den här taggen anger IP-adressutrymmet som är utanför det virtuella nätverket och som kan nås av det offentliga Internet. Intervallet omfattar det [offentliga IP-adressutrymmet som ägs av Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Standardregler
<a id="default-rules" class="xliff"></a>
Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar. 

Standardreglerna tillåter och nekar trafik på följande sätt:
- **Virtuellt nätverk:** Trafik som kommer från eller som går till ett virtuellt nätverk tillåts både i inkommande och utgående riktning.
- **Internet:** Utgående trafik tillåts, men inkommande trafik blockeras.
- **Belastningsutjämnare:** Tillåt att Azures belastningsutjämnare avsöker hälsotillståndet för dina virtuella datorer och rollinstanser. Du kan åsidosätta den här regeln om du inte använder en belastningsutjämnad uppsättning.

**Inkommande standardregler**

| Namn | Prioritet | Käll-IP-adress | Källport | Mål-IP-adress | Målport | Protokoll | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVNetInBound |65000 | VirtualNetwork | * | VirtualNetwork | * | * | Tillåt |
| AllowAzureLoadBalancerInBound | 65001 | AzureLoadBalancer | * | * | * | * | Tillåt |
| DenyAllInBound |65500 | * | * | * | * | * | Neka |

**Utgående standardregler**

| Namn | Prioritet | Käll-IP-adress | Källport | Mål-IP-adress | Målport | Protokoll | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVnetOutBound | 65000 | VirtualNetwork | * | VirtualNetwork | * | * | Tillåt |
| AllowInternetOutBound | 65001 | * | * | Internet | * | * | Tillåt |
| DenyAllOutBound | 65500 | * | * | * | * | * | Neka |

## Koppla NSG:er
<a id="associating-nsgs" class="xliff"></a>
Du kan koppla en nätverkssäkerhetsgrupp till virtuella datorer, nätverkskort och undernät, beroende på vilken distributionsmodell du använder, enligt följande:

* **VM (endast klassisk):** Säkerhetsregler används för all trafik till/från den virtuella datorn. 
* **Nätverkskort (endast Resource Manager):** Säkerhetsregler används för all trafik till/från nätverkskortet som nätverkssäkerhetsgruppen är kopplad till. På en virtuell dator med flera nätverkskort kan du använda olika (eller samma) nätverkssäkerhetsgrupp för varje nätverkskort. 
* **Undernät (Resource Manager och klassisk):** Säkerhetsregler används för all trafik till/från resurser som är anslutna till det virtuella nätverket.

Du kan koppla olika nätverkssäkerhetsgrupper till en virtuell dator (eller ett nätverkskort, beroende på distributionsmodell) och det undernät som ett nätverkskort eller en virtuell dator är ansluten till. Säkerhetsregler används för trafiken, efter prioritet, i varje nätverkssäkerhetsgrupp, i följande ordning:

- **Inkommande trafik**

  1. **NSG som tillämpas på undernät:** Om en nätverkssäkerhetsgrupp för ett undernät har en matchande regel som nekar trafik, ignoreras paketet.

  2. **NSG som tillämpas på nätverkskort** (Resource Manager) eller virtuell dator (klassisk): Om en nätverkssäkerhetsgrupp för en virtuell dator eller ett nätverkskort har en matchande regel som nekar trafik, ignoreras paket på den virtuella datorn/nätverkskortet även om en undernäts-NSG har en matchande regel som tillåter trafik.

- **Utgående trafik**

  1. **NSG som tillämpas på nätverkskort** (Resource Manager) eller virtuell dator (klassisk): Om en nätverkssäkerhetsgrupp för en virtuell dator eller ett nätverkskort har en matchande regel som nekar trafik, ignoreras paketet.

  2. **NSG som tillämpas på undernät:** Om en nätverkssäkerhetsgrupp för ett undernät har en matchande regel som nekar trafik, ignoreras paket även om en nätverkssäkerhetsgrupp för den virtuella datorn/nätverkskortet har en matchande regel som tillåter trafik.

> [!NOTE]
> Även om du kan bara koppla en enda NSG till ett undernät, VM eller NIC så kan du koppla samma NSG till hur många resurser du vill.
>

## Implementering
<a id="implementation" class="xliff"></a>
Du kan implementera nätverkssäkerhetsgrupper i Resource Manager-distributionsmodellen eller i den klassiska distributionsmodellen med hjälp av följande verktyg:

| Distributionsverktyg | Klassisk | Resource Manager |
| --- | --- | --- |
| Azure Portal   | Ja | [Ja](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Ja](virtual-networks-create-nsg-classic-ps.md) | [Ja](virtual-networks-create-nsg-arm-ps.md) |
| Azure CLI **V1**   | [Ja](virtual-networks-create-nsg-classic-cli.md) | [Ja](virtual-networks-create-nsg-cli-nodejs.md) |
| Azure CLI **V2**   | Nej | [Ja](virtual-networks-create-nsg-arm-cli.md) |
| Azure Resource Manager-mall   | Nej  | [Ja](virtual-networks-create-nsg-arm-template.md) |

## Planering
<a id="planning" class="xliff"></a>
Innan du implementerar nätverkssäkerhetsgrupper måste du besvara följande frågor:

1. Vilka typer av resurser vill du filtrera trafik till och från? Du kan ansluta resurser, t.ex nätverkskort (Resource Manager), virtuella datorer (klassisk), molntjänster, programtjänstmiljöer och skalningsuppsättningar för virtuella datorer. 
2. Är de resurser som du vill filtrera trafik till/från anslutna till undernät i befintliga virtuella nätverk?

Mer information om hur du planerar nätverkssäkerheten i Azure finns i artikeln om [molntjänster och nätverkssäkerhet](../best-practices-network-security.md). 

## Designöverväganden
<a id="design-considerations" class="xliff"></a>
När du vet svaren på frågorna i avsnittet [Planering](#Planning) går du igenom följande avsnitt innan du definierar dina nätverkssäkerhetsgrupper:

### Begränsningar
<a id="limits" class="xliff"></a>
Det finns gränser för hur många nätverkssäkerhetsgrupper du kan ha i en prenumeration och antalet regler per NSG. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

### Utformning av VNet och undernät
<a id="vnet-and-subnet-design" class="xliff"></a>
Eftersom NSG:er kan tillämpas på undernät, kan du minimera antalet NSG:er genom att gruppera dina resurser efter undernät och tillämpa NSG:er på undernätet.  Om du bestämmer dig att tillämpa NSG:er på undernät kan du upptäcka att befintliga VNet och undernät inte har definierats med NSG:er i åtanke. Du kan behöva definiera nya virtuella datorer och undernät beroende på nätverkssäkerhetsgruppens design och distribuera nya resurser till dina nya undernät. Sedan kan du definiera en migreringsstrategi för att flytta befintliga resurser till de nya undernäten. 

### Särskilda regler
<a id="special-rules" class="xliff"></a>
Om du blockerar trafik som tillåts av följande regler kan din infrastruktur inte kommunicera med viktiga Azure-tjänster:

* **Värdnodens virtuella IP-adress:** Grundläggande infrastrukturtjänster som DHCP, DNS och hälsoövervakning tillhandahålls via den virtualiserade värd-IP-adressen 168.63.129.16. Den här offentliga IP-adressen tillhör Microsoft och är den enda virtualiserade IP-adressen som används i alla regioner för det här ändamålet. Den här IP-adressen mappar till den fysiska IP-adressen för serverdatorn (värdnoden) som är värd för den virtuella datorn. Värdnoden agerar som ett DHCP-relä, rekursiv DNS-matchare och avsökningskälla för belastningsutjämnaren, hälsoavsökningen och datorhälsoavsökningen. Kommunikation till den här IP-adressen är inte ett angrepp.
* **Licensiering (nyckelhanteringstjänst):** Windows-avbildningar som körs på virtuella datorer måste vara licensierade. Licensieringen kontrolleras genom att en begäran skickas till nyckelhanteringstjänstens värdservrar som hanterar sådana frågor. Begäran är en utgående begäran via port 1688.

### ICMP-trafik
<a id="icmp-traffic" class="xliff"></a>
De nuvarande NSG-reglerna tillåter bara protokollen *TCP* eller *UDP*. Det finns ingen specifik tagg för *ICMP*. ICMP-trafik tillåts dock inom ett virtuellt nätverk av standardregeln AllowVNetInBound, som tillåter trafik till och från alla portar och protokoll i det virtuella nätverket.

### Undernät
<a id="subnets" class="xliff"></a>
* Tänk över hur många nivåer din arbetsbelastning behöver. Varje nivå kan isoleras med ett undernät, med en NSG tillämpad på undernätet. 
* Om du behöver implementera ett undernät för en VPN-gateway, eller en ExpressRoute-krets, tilldelar du **inte** en nätverkssäkerhetsgrupp till det undernätet. Om du gör det kan anslutningar mellan virtuella nätverk eller i det lokala systemet misslyckas. 
* Om du behöver implementera en virtuell nätverksenhet (NVA) ansluter du den till dess eget undernät och skapar användardefinierade vägar (UDR) till och från enheten. Du kan implementera en NSG på undernätsnivå för att filtrera trafik in eller ut från det undernätet. Mer information om UDR:er finns i artikeln om [användardefinierade vägar](virtual-networks-udr-overview.md).

### Belastningsutjämnare
<a id="load-balancers" class="xliff"></a>
* Ha i åtanke belastningsutjämnings- och NAT-reglerna (Network Address Translation) för varje belastningsutjämnare som används av var och en av dina arbetsbelastningar. NAT-regler är bundna till en backend-pool som innehåller nätverkskort (Resource Manager) eller rollinstanser för virtuella datorer/molntjänster (klassisk). Överväg att skapa en nätverkssäkerhetsgrupp för varje serverdelspool så att endast trafik som mappas genom regeln implementeras i belastningsutjämnarna. Genom att skapa en nätverkssäkerhetsgrupp för varje serverdelspool ser du till att trafik som kommer direkt till serverdelspoolen (i stället för via belastningsutjämnaren) filtreras.
* I klassiska distributioner skapar du slutpunkter som mappar portar på en belastningsutjämnare till portar på dina VM:ar eller rollinstanser. Du kan också skapa din egen separata offentliga belastningsutjämnare via Resource Manager. Målporten för inkommande trafik är själva porten på den virtuella datorn eller rollinstansen, inte porten som exponeras av en belastningsutjämnare. Källporten och adressen för anslutningen till den virtuella datorn är en port och en adress på fjärrdatorn på Internet, inte porten och adressen som exponeras av belastningsutjämnaren.
* När du skapar nätverkssäkerhetsgrupper för att filtrera trafik via en intern belastningsutjämnare (ILB) kommer källporten och källadressintervallet som tillämpas från den ursprungliga datorn, inte belastningsutjämnaren. Målporten och måladressutrymmet kommer från måldatorn, inte belastningsutjämnaren.

### Annat
<a id="other" class="xliff"></a>
* Slutpunktsbaserade åtkomstkontrollistor (ACL) och nätverkssäkerhetsgrupper stöds inte på samma VM-instans. Om du vill använda en NSG och redan har en slutpunkts-ACL på plats så kan du först ta bort slutpunkts-ACL:n. Information om hur du tar bort en slutpunkts-ACL finns i artikeln [Manage endpoint ACLs](virtual-networks-acl-powershell.md) (Hantera slutpunkts-ACL:er).
* I Resource Manager kan du använda en nätverkssäkerhetsgrupp som är kopplad till ett nätverkskort för virtuella datorer med flera nätverkskort för att aktivera hantering (fjärråtkomst) för varje enskilt nätverkskort. Genom att koppla unika nätverkssäkerhetsgrupper till varje nätverkskort kan du avgränsa trafiktyperna mellan nätverkskort.
* Precis som med belastningsutjämnare, när du filtrerar trafik från andra VNet, måste du använda källadressintervallet från fjärrdatorn och inte gatewayen som ansluter till VNet:en.
* Många Azure-tjänster kan inte anslutas till virtuella nätverk. Om en Azure-resurs inte är ansluten till ett virtuellt nätverk kan du inte använda en nätverkssäkerhetsgrupp för att filtrera trafik till resursen.  Läs dokumentationen för de tjänster som du använder för att avgöra om tjänsten kan anslutas till ett virtuellt nätverk.

## Exempeldistribution
<a id="sample-deployment" class="xliff"></a>
I den här artikeln ska vi illustrera hur informationen används med hjälp av ett vanligt scenario med ett program med två nivåer, som du ser i följande bild:

![NSG:er](./media/virtual-network-nsg-overview/figure1.png)

Som du ser i diagrammet är de virtuella datorerna *Web1* och *Web2* anslutna till undernätet *FrontEnd*, och de virtuella datorerna *DB1* och *DB2* är anslutna till undernätet *BackEnd*.  Båda undernäten är en del av VNet:et *TestVNet*. Varje programkomponent körs på en virtuell Azure-dator som är ansluten till ett virtuellt nätverk. Scenariot har följande krav:

1. Avgränsning av trafik mellan WEB- och DB-servrar.
2. Regler för belastningsutjämning vidarebefordrar trafik från belastningsutjämnaren till alla webbservrar på port 80.
3. NAT-regler för belastningsutjämnaren vidarebefordrar trafik som kommer till belastningsutjämnaren på port 50001 till port 3389 på den virtuella datorn WEB1.
4. Ingen åtkomst till de virtuella datorerna på klient- eller serversidan från Internet, förutom krav 2 och 3.
5. Ingen utgående Internetåtkomst från WEB- eller DB-servrarna.
6. Åtkomst från undernätet på klientsidan tillåts till port 3389 för alla webbservrar.
7. Åtkomst från undernätet på klientsidan tillåts till port 3389 för alla databasservrar.
8. Åtkomst från undernätet på klientsidan tillåts till port 1433 för alla databasservrar.
9. Avgränsning av hanteringstrafik (port 3389) och databastrafik (1433) på olika nätverkskort på DB-servrar.

Krav 1–6 (utom krav 3 och 4) är alla begränsade till undernätsutrymmen. Följande nätverkssäkerhetsgrupper uppfyller föregående krav och minimerar antalet nätverkssäkerhetsgrupper som krävs:

### FrontEnd
<a id="frontend" class="xliff"></a>
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet | Tillåt | 100 | Internet | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet | Tillåt | 200 | Internet | * | * | 3389 | TCP |
| Deny-Inbound-All | Neka | 300 | Internet | * | * | * | TCP |

**Regler för utgående trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All |Neka |100 | * | * | Internet | * | * |

### BackEnd
<a id="backend" class="xliff"></a>
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Neka | 100 | Internet | * | * | * | * |

**Regler för utgående trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Neka | 100 | * | * | Internet | * | * |

Följande nätverkssäkerhetsgrupper skapas och associeras med nätverkskort på följande virtuella datorer:

### WEB1
<a id="web1" class="xliff"></a>
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet | Tillåt | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Tillåt | 200 | Internet | * | * | 80 | TCP |

> [!NOTE]
> Källadressintervallet för föregående regler är **Internet**, inte den virtuella IP-adressen för belastningsutjämnaren. Källporten är *, inte 500001. NAT-regler för belastningsutjämnare är inte samma som NSG-säkerhetsregler. NSG-säkerhetsreglerna är alltid relaterade till den ursprungliga källan och slutdestinationen för trafiken, **inte** belastningsutjämnaren mellan de två. 
> 
> 

### WEB2
<a id="web2" class="xliff"></a>
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet | Neka | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Tillåt | 200 | Internet | * | * | 80 | TCP |

### DB-servrar (nätverkskort för hantering)
<a id="db-servers-management-nic" class="xliff"></a>
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end | Tillåt | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### DB-servrar (nätverkskort för databastrafik)
<a id="db-servers-database-traffic-nic" class="xliff"></a>
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end | Tillåt | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

Eftersom vissa nätverkssäkerhetsgrupper är kopplade till enskilda nätverkskort är reglerna avsedda för resurser som distribueras via Resource Manager. Regler kombineras för undernät och nätverkskort, beroende på hur de är associerade. 

## Nästa steg
<a id="next-steps" class="xliff"></a>
* [Distribuera nätverkssäkerhetsgrupper (Resource Manager)](virtual-networks-create-nsg-arm-pportal.md).
* [Distribuera nätverkssäkerhetsgrupper (klassisk)](virtual-networks-create-nsg-classic-ps.md).
* [Hantera NSG-loggar](virtual-network-nsg-manage-log.md).
* [Felsöka nätverkssäkerhetsgrupper] (virtual-network-nsg-troubleshoot-portal.md)

