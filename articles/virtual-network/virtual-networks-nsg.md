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
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6ea9db6ac7a7ba63652b860c22899a8616ea81bc
ms.lasthandoff: 04/03/2017


---
# <a name="control-network-traffic-flow-with-network-security-groups"></a>Styra flödet i nätverkstrafiken med nätverkssäkerhetsgrupper

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till dina VM-instanser i ett virtuellt nätverk. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Dessutom kan trafik till en enskild VM begränsas ytterligare genom att koppla en NSG direkt till den VM:en.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln täcker bägge modellerna, men Microsoft rekommenderar de flesta nya distributioner att använda Resource Manager-modellen.

## <a name="nsg-resource"></a>NSG-resurs
NSG:er innehåller följande egenskaper.

| Egenskap | Beskrivning | Villkor | Överväganden |
| --- | --- | --- | --- |
| Namn |Namn för NSG:n |Måste vara unikt för regionen<br/>Kan innehålla bokstäver, siffror, understreck, punkter och bindestreck<br/>Måste börja med en bokstav eller en siffra<br/>Måste sluta med en bokstav, en siffra eller understreck<br/>Kan ha upp till 80 tecken |Eftersom du kan behöva skapa flera NSG:er, bör du se till att du har en namngivningskonvention som gör det lätt att identifiera funktionen för din NSG |
| Region |Azure-region där NSG:n finns |NSG:er kan bara tillämpas på resurser inom den region där den skapas |Se [begränsningar](#Limits) nedan för att förstå hur många NSG:er som får finnas i en region |
| Resursgrupp |Den resursgrupp din NSG tillhör |Även om en NSG tillhör en resursgrupp, kan den vara knuten till resurser i valfri resursgrupp, så länge som resursen är del av samma Azure-region som NSG:n |Resursgrupper används för att hantera flera resurser tillsammans, som en distributionsenhet<br/>Du kan gruppera NSG:n med de resurser som den är kopplad till |
| Regler |Regler som definierar vilken trafik som tillåts eller nekas | |Se [NSG-regler](#Nsg-rules) nedan |

> [!NOTE]
> Slutpunktsbaserade ACL:er och nätverkssäkerhetsgrupper stöds inte på samma VM-instans. Om du vill använda en NSG och redan har en slutpunkts-ACL på plats så kan du först ta bort slutpunkts-ACL:n. Information om hur du gör detta finns i [Hantera åtkomstkontrollistor (ACL:er) för slutpunkter med hjälp av PowerShell](virtual-networks-acl-powershell.md).
> 

### <a name="nsg-rules"></a>NSG-regler
NSG-regler har följande egenskaper:

| Egenskap | Beskrivning | Villkor | Överväganden |
| --- | --- | --- | --- |
| **Namn** |Namn för regeln |Måste vara unikt för regionen<br/>Kan innehålla bokstäver, siffror, understreck, punkter och bindestreck<br/>Måste börja med en bokstav eller en siffra<br/>Måste sluta med en bokstav, en siffra eller understreck<br/>Kan ha upp till 80 tecken |Du kan ha flera regler inom en NSG så se till att du följer en namngivningskonvention som gör att du kan identifiera funktionen för din regel |
| **Protokoll** |Protokoll att matcha för regeln |TCP, UDP eller \* |Att använda \* som ett protokoll inkluderar ICMP (enbart Öst-Väst-trafik), samt UDP och TCP och kan minska antalet regler du behöver<br/>Att använda \* kan samtidigt vara en för bred lösning så se till att bara använda det när det verkligen behövs |
| **Källportintervall** |Källportintervall att matcha för regeln |Enskilda portnummer mellan 1 och 65535, portintervall (1-65635) eller \* (för alla portar) |Källportar kan vara tillfälliga. Om inte klientprogrammet använder en viss port, bör du använda ”*” i de flesta fall.<br/>Försök att använda portintervall i så stor utsträckning som möjligt för att undvika att använda flera regler<br/>Flera portar eller portintervall kan inte grupperas med kommatecken |
| **Målportintervall** |Målportintervall att matcha för regeln |Enskilda portnummer mellan 1 och 65535, portintervall (1-65535) eller \* (för alla portar) |Försök att använda portintervall i så stor utsträckning som möjligt för att undvika att använda flera regler<br/>Flera portar eller portintervall kan inte grupperas med kommatecken |
| **Källadress-prefix** |Källadress-prefix eller tagg att matcha för regeln |Enskild IP-adress (till exempel 10.10.10.10), IP-undernät (till exempel 192.168.1.0/24), [standardtagg](#default-tags) eller * (för alla adresser) |Överväg att använda intervall, standardtaggar och * för att minska antalet regler |
| **Måladress-prefix** |Måladress-prefix eller tagg att matcha för regeln |enskild IP-adress (till exempel 10.10.10.10), IP-undernät (till exempel 192.168.1.0/24), [standardtagg](#default-tags) eller * (för alla adresser) |Överväg att använda intervall, standardtaggar och * för att minska antalet regler |
| **Riktning** |Trafikriktning att matcha för regeln |inkommande eller utgående |Regler för inkommande och utgående bearbetas separat, baserat på riktning |
| **Prioritet** |Reglerna kontrolleras i prioritsordning, när en regel väl matchats så testas inga fler regler |Tal mellan 100 och 4096 |Överväg att skapa regler som hoppar i prioritet med 100 för varje regel för att på så sätt skapa utrymme för nya regler mellan de befintliga reglerna |
| **Åtkomst** |Typ av åtkomst som ska tillämpas om regeln matchar |tillåt eller neka |Tänk på att om en regel för tillåt inte hittas för ett paket, släpps paketet |

NSG:er innehåller två regeluppsättningar: inkommande och utgående. En regels prioritet måste vara unik inom varje uppsättning. 

![NSG-regelbearbetning](./media/virtual-network-nsg-overview/figure3.png) 

Ovanstående bild visar hur NSG-regler bearbetas.

### <a name="default-tags"></a>Standardtaggar
Standardtaggar är systemdefinierade identifierare för en viss kategori av IP-adresser. Du kan använda standardtaggar i egenskaperna för **källadress-prefix** och **måladress-prefix** för alla regler. Det finns tre standardtaggar som du kan använda.

* **VIRTUAL_NETWORK** (**VirtualNetwork** om du använder Azure Resource Manager): Den här standardtaggen anger allt adressutrymme för nätverket. Det inkluderar virtuellt nätverks-adressutrymme (CIDR-intervallen som definieras i Azure) samt alla anslutna lokala adressutrymmen och anslutna Azure-VNet (lokala nätverk).
* **AZURE_LOADBALANCER** (**AzureLoadBalancer** om du använder Azure Resource Manager): Den här standardtaggen anger belastningsutjämnaren i Azures infrastruktur. Det innebär den Azure datacenter-IP-adress som Azures-hälsoavsökning kommer från.
* **INTERNET:** Den här standardtaggen omfattar IP-adressutrymmet som är utanför det virtuella nätverket och som nås via offentligt Internet. Det här intervallet inkluderar även [Azure-ägt offentligt IP-adressutrymme](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="default-rules"></a>Standardregler
Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar. 

Som standardreglerna nedan, tillåts trafik som startar och slutar i ett virtuellt nätverk både i inkommande och utgående riktningar. Anslutning till Internet tillåts för utgående anslutningar, men är blockerat som standard för inkommande anslutningar. Det finns en standardregel som låter Azures belastningsutjämnare att avsöka hälsostatus för dina VM:ar och rollinstanser. Du kan åsidosätta den här regeln om du inte använder en belastningsutjämnad uppsättning.

**Inkommande standardregler**

| Namn | Prioritet | Käll-IP-adress | Källport | Mål-IP-adress | Målport | Protokoll | Åtkomst |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TILLÅT INKOMMANDE VNET |65000 |VIRTUAL_NETWORK |* |VIRTUAL_NETWORK |* |* |TILLÅT |
| TILLÅT INKOMMANDE AZURE BELASTNINGSUTJÄMNARE |65001 |AZURE_LOADBALANCER |* |* |* |* |TILLÅT |
| NEKA ALLA INKOMMANDE |65500 |* |* |* |* |* |NEKA |

**Utgående standardregler**

| Namn | Prioritet | Käll-IP-adress | Källport | Mål-IP-adress | Målport | Protokoll | Åtkomst |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TILLÅT VNET UTGÅENDE |65000 |VIRTUAL_NETWORK |* |VIRTUAL_NETWORK |* |* |TILLÅT |
| TILLÅT INTERNET UTGÅENDE |65001 |* |* |INTERNET |* |* |TILLÅT |
| NEKA ALLA UTGÅENDE |65500 |* |* |* |* |* |NEKA |

## <a name="associating-nsgs"></a>Koppla NSG:er
Du kan koppla en NSG till VM:ar, nätverkskort och undernät, beroende på den distributionsmodell du använder.

* **Koppla en NSG till en VM (endast klassiska distributioner).** När du kopplar en NSG till en VM, tillämpas nätverksåtkomstreglerna i NSG:n på all trafik mot och från VM:n. 
* **Koppla en NSG till ett nätverkskort (enbart Resource Manager-distributioner).** När du kopplar en NSG till ett nätverkskort, tillämpas nätverksåtkomstreglerna för den NSG:n bara på det nätverkskortet. Det innebär att i en VM med flera nätverkskort, så påverkar inte en NSG som kopplats till ett nätverkskort trafiken som går via andra nätverkskort. 
* **Koppla en NSG till ett undernät (alla distributioner)**. När du kopplar en NSG till ett undernät, tillämpas nätverksåtkomstreglerna i NSG:n på alla IaaS- och PaaS-resurser på undernätet. 

Du kan koppla olika NSG:er till en VM (eller nätverkskort, beroende på distributionsmodell) och de undernät som ett nätverkskort eller VM är bunden till. När det sker så tillämpas alla nätverksåtkomstregler på trafiken efter prioritet i varje nätverkssäkerhetsgrupp, i följande ordning:

- **Inkommande trafik**

  1. **Nätverkssäkerhetsgrupp som tillämpas på undernät:** Om en nätverkssäkerhetsgrupp som tillämpas på ett undernät har en matchande regel som nekar trafik, ignoreras paketet.

  2. **Nätverkssäkerhetsgrupp som tillämpas på ett nätverkskort** (Resource Manager) eller på en VM (klassisk): Om en nätverkssäkerhetsgrupp som tillämpas på en virtuell dator eller ett nätverkskort har en matchande regel som nekar trafik, ignoreras paketet på den virtuella datorn eller nätverkskortet, även om nätverkssäkerhetsgruppen som tillämpas på undernätet har en matchande regel som tillåter trafik.

- **Utgående trafik**

  1. **Nätverkssäkerhetsgrupp som tillämpas på ett nätverkskort** (Resource Manager) eller på en VM (klassisk): Om en nätverkssäkerhetsgrupp som tillämpas på en virtuell dator eller ett nätverkskort har en matchande regel som nekar trafik, ignoreras paketet.

  2. **Nätverkssäkerhetsgrupp som tillämpas på ett undernät:** Om en nätverkssäkerhetsgrupp som tillämpas på ett undernät har en matchande regel som nekar trafik, ignoreras paketet här, även om nätverkssäkerhetsgruppen för den virtuella datorn eller nätverkskortet har en matchande regel som tillåter trafik.

> [!NOTE]
> Även om du kan bara koppla en enda NSG till ett undernät, VM eller NIC så kan du koppla samma NSG till hur många resurser du vill.
>

## <a name="implementation"></a>Implementering
Du kan implementera NSG:er i den klassiska eller Resource Manager distributionsmodellerna med hjälp av de olika verktyg som listas nedan.

| Distributionsverktyg | Klassisk | Resource Manager |
| --- | --- | --- |
| Klassisk portal | Nej  | Nej |
| Azure Portal   | Ja | [Ja](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Ja](virtual-networks-create-nsg-classic-ps.md) | [Ja](virtual-networks-create-nsg-arm-ps.md) |
| Azure CLI      | [Ja](virtual-networks-create-nsg-classic-cli.md) | [Ja](virtual-networks-create-nsg-arm-cli.md) |
| ARM-mall   | Nej  | [Ja](virtual-networks-create-nsg-arm-template.md) |

## <a name="planning"></a>Planering
Innan du implementerar nätverkssäkerhetsgrupper måste du besvara följande frågor:

1. Vilka typer av resurser vill du vill filtrera trafik till eller från (nätverkskort i samma VM, VM:ar eller andra resurser som molntjänster eller programtjänstmiljöer som är anslutna till samma undernät eller mellan resurser som anslutits till olika undernät)?
2. Är de resurser du vill filtrera trafik till och från anslutna till undernät i befintliga VNet eller kommer de att anslutas till nya VNet eller undernät?

Mer information om planering för nätverkssäkerhet i Azure finns i [metodtips för molntjänster och nätverkssäkerhet](../best-practices-network-security.md). 

## <a name="design-considerations"></a>Designöverväganden
När du väl vet svaren på frågorna i [Planerings](#Planning)-avsnittet, kan du granska följande innan du definierar dina NSG:er.

### <a name="limits"></a>Begränsningar
Du behöver tänka på följande begränsningar när du utformar dina NSG:er.

| **Beskrivning** | **Standardgräns** | **Effekter** |
| --- | --- | --- |
| Antal NSG:er som du kan koppla till ett undernät, VM eller nätverkskort |1 |Det innebär att du inte kan kombinera NSG:er. Se till att alla regler som behövs för en viss uppsättning resurser finns inkluderade i en enda NSG. |
| NSG:er per region per prenumeration |100 |Som standard skapas en ny NSG för varje VM som du skapar i Azure-portalen. Om du tillåter det här standardbeteendet kommer du ganska snabbt att få slut på NSG:er. Se till att du har den begränsningen i åtanke vid utformningen och skilj dina resurser åt på olika regioner eller prenumerationer vid behov. |
| NSG-regler per NSG |200 |Använd ett brett IP-adress- och portintervall för att säkerställa att du inte överskrider den här gränsen. |

> [!IMPORTANT]
> Se till att du kollar igenom alla [begränsningar relaterade till nätverkstjänster i Azure](../azure-subscription-service-limits.md#networking-limits) innan du utformar din lösning. Vissa begränsningar kan utökas genom att öppna ett supportärende.
> 
> 

### <a name="vnet-and-subnet-design"></a>Utformning av VNet och undernät
Eftersom NSG:er kan tillämpas på undernät, kan du minimera antalet NSG:er genom att gruppera dina resurser efter undernät och tillämpa NSG:er på undernätet.  Om du bestämmer dig att tillämpa NSG:er på undernät kan du upptäcka att befintliga VNet och undernät inte har definierats med NSG:er i åtanke. Du kan behöva definiera nya VNet och undernät för att stödja din NSG-utformning. Och distribuera dina nya resurser till dina nya undernät. Sedan kan du definiera en migreringsstrategi för att flytta befintliga resurser till de nya undernäten. 

### <a name="special-rules"></a>Särskilda regler
Du måste ta hänsyn till de särskilda reglerna som anges nedan. Se till att du inte blockerar trafik som tillåts av de reglerna, annars kommer din infrastruktur inte att kunna kommunicera med grundläggande Azure-tjänster.

* **Virtuell IP-adress till värdnoden:** grundläggande infrastrukturtjänster som DHCP, DNS och hälsoövervakning sker via den virtualiserade värd-IP-adressen 168.63.129.16. Den här offentliga IP-adressen tillhör Microsoft och kommer att vara den enda virtualiserade IP-adressen som används i alla regioner för det här ändamålet. Den här IP-adressen mappar till den fysiska adressen för serverdatorn (värdnoden) som är värd för den virtuella datorn. Värdnoden agerar som ett DHCP-relä, rekursiv DNS-matchare och avsökningskälla för belastningsutjämnaren, hälsoavsökningen och datorhälsoavsökningen. Kommunikation till den här IP-adressen ska inte ses som en attack.
* **Licensiering (nyckelhanteringstjänsten):** Windows-avbildningar som kör på de virtuella datorerna ska vara licensierade. För att göra det, skickas en licensieringsbegäran till nyckelhanteringstjänstens värdservrar som hanterar sådana frågor. Det kommer alltid ske på den utgående porten 1688.

### <a name="icmp-traffic"></a>ICMP-trafik
De nuvarande NSG-reglerna tillåter bara protokollen *TCP* eller *UDP*. Det finns ingen specifik tagg för *ICMP*. Men ICMP-trafik tillåts dock inom ett virtuellt nätverk som standard via den ingående VNet-regeln (standardregel 65000 inkommande) som tillåter trafik från/till alla portar och protokoll inom VNet.

### <a name="subnets"></a>Undernät
* Tänk över hur många nivåer din arbetsbelastning behöver. Varje nivå kan isoleras med ett undernät, med en NSG tillämpad på undernätet. 
* Om du behöver implementera ett undernät för en VPN-gateway, eller en ExpressRoute-krets, se då till att du **INTE** tilldelar en NSG till det undernätet. Om du gör det fungerar inte anslutningen inom VNet eller på plats.
* Om du behöver implementera en virtuell aparat genomföra en virtuell installation, se till att distribuera den virtuella installationen på sitt eget undernät så att dina användardefinierade vägar (UDR:er) fungerar som de ska. Du kan implementera en NSG på undernätsnivå för att filtrera trafik in eller ut från det undernätet. Läs mer om [hur du styr trafikflöde och använder virtuella installationer](virtual-networks-udr-overview.md).

### <a name="load-balancers"></a>Belastningsutjämnare
* Se över reglerna för belastningsutjämning och NAT för varje belastningsutjämnare som används för var och en av dina arbetsbelastningar. Reglerna är bundna till en serverdelspool som innehåller nätverkskort (Resource Manager distributioner) eller VM:ar/rollinstanser (klassiska distributioner). Överväg att skapa en NSG för varje serverdelspool så att bara trafik som mappats genom regeln implementeras i belastningsutjämnarna. Det garanterar att även trafik som kommer direkt till serverdelspoolen utan att gå igenom belastningsutjämnaren filtreras.
* I klassiska distributioner skapar du slutpunkter som mappar portar på en belastningsutjämnare till portar på dina VM:ar eller rollinstanser. Du kan också skapa din egen individuella, offentliga belastningsutjämnare i en Resource Manager distribution. Om du begränsar trafiken till VM:ar och rollinstanser som är del av en serverdelspool i en belastningsutjämnare med hjälp av NSG:er, bör du tänka på att målporten för den inkommande trafiken, är den faktiska porten i VM:en eller rollinstansen och inte den port som exponeras av belastningsutjämnaren. Tänk också på källport och adress för anslutningen till VM:en är en port och en adress på fjärrdatorn på Internet, inte porten och adressen som exponeras av belastningsutjämnaren.
* Precis som med Internetriktade belastningsutjämnare, så behöver du förstå att källporten och adressintervallet som appliceras, när du skapar NSG:er för att filtrera trafik genom en intern belastningsutjämnare (ILB), kommer från datorn som inlett samtalet och inte belastningsutjämnaren. Målporten och adressintervallet är också relaterade till datorn som tar emot trafiken och inte belastningsutjämnaren.

### <a name="other"></a>Annat
* Slutpunktsbaserade ACL:er och NSG:er stöds inte på samma VM-instans. Om du vill använda en NSG och redan har en slutpunkts-ACL på plats så kan du först ta bort slutpunkts-ACL:n. Information om hur du gör detta finns i [Hantera slutpunkts-ACL:er](virtual-networks-acl-powershell.md).
* I Resource Manager-distributioner, kan du använda en NSG som kopplats till ett nätverkskort för VM:ar med flera nätverkskort för att möjliggöra hantering (fjärråtkomst) via nätverkskort, vilket segregerar trafiken.
* Precis som med belastningsutjämnare, när du filtrerar trafik från andra VNet, måste du använda källadressintervallet från fjärrdatorn och inte gatewayen som ansluter till VNet:en.
* Många Azure-tjänster kan inte anslutas till Azure-virtuella nätverk och trafik till och från dem kan därför inte filtreras med NSG:er.  Läs i dokumentationen för de tjänster du använder för att avgöra om de kan anslutas till VNet eller inte.

## <a name="sample-deployment"></a>Exempeldistribution
För att illustrera hur man tillämpar informationen i den här artikeln, ska vi definiera NSG:er att filtrera nätverkstrafik för en arbetsbelastningslösning med två nivåer och följande krav:

1. Trafikavgränsning mellan klientdelen (Windows-webbservrar) och serverdelen (SQL Database-servrar).
2. Regler för belastningsutjämning vidarebefordrar trafik till belastningsutjämnaren till alla webbservrar på port 80.
3. NAT-regler som vidarebefordrar inkommande trafik på port 50001 till port 3389 på en enda VM i klientdelen.
4. Ingen åtkomst till klientdels- eller serverdels-VM:ar från Internet med undantag av krav nummer 1.
5. Ingen åtkomst från klientdelen eller serverdelen till Internet.
6. Åtkomst till port 3389 till valfri webbserver i klientdelen för trafik som kommer från klientdelen och själva undernätet.
7. Enbart åtkomst till port 3389 för alla SQL Server-VM:ar i serverdelen från klientdelens undernät.
8. Enbart åtkomst till port 1433 för alla SQL Server-VM:ar i serverdelen från klientdelens undernät.
9. Uppdelning av hanteringstrafik (port 3389) och databastrafik (1433) på olika nätverkskort i serverdelens VM:ar.

![NSG:er](./media/virtual-network-nsg-overview/figure1.png)

Som det visas i diagrammet ovan, är VM:arna *Web1* och *Web2* anslutna till undernätet *FrontEnd* och VM:arna *DB1* och *DB2* är anslutna till undernätet *BackEnd*.  Båda undernäten är en del av VNet:et *TestVNet*. Alla resurser är tilldelade till Azure-regionen *USA, västra*.

Kraven för 1 – 6 (med undantag av 3) ovan är begränsade till undernätsområden. För att minimera antalet regler som krävs för varje NSG och göra det lätt att lägga till ytterligare VM:ar i undernät som kör samma typer av arbetsbelastningar som de befintliga VM:arna, kan vi implementera följande NSG:er på undernätsnivå.

### <a name="nsg-for-frontend-subnet"></a>NSG för undernätet FrontEnd
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| tillåt HTTP |Tillåt |100 |INTERNET |\* |\* |80 |TCP |
| tillåt RDP från FrontEnd |Tillåt |200 |192.168.1.0/24 |\* |\* |3389 |TCP |
| neka allt från Internet |Neka |300 |INTERNET |\* |\* |\* |TCP |

**Utgående regler**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| neka Internet |Neka |100 |\* |\* |INTERNET |\* |\* |

### <a name="nsg-for-backend-subnet"></a>NSG för BackEnd-undernät
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| neka Internet |Neka |100 |INTERNET |\* |\* |\* |\* |

**Utgående regler**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| neka Internet |Neka |100 |\* |\* |INTERNET |\* |\* |

### <a name="nsg-for-single-vm-nic-in-frontend-for-rdp-from-internet"></a>NSG för enskild VM (nätverkskort) i FrontEnd för RDP från Internet
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| tillåt RDP från Internet |Tillåt |100 |INTERNET |* |\* |3389 |TCP |

> [!NOTE]
> Observera hur källadress-intervallet för den här regeln är **Internet** och inte VIP för belastningsutjämnaren. Källporten är **\***, inte 500001. Blanda inte ihop NAT-regler/belastningsutjämnings-regler och NSG-regler. NSG-reglerna är alltid relaterade till den ursprungliga källan och slutdestinationen för trafiken, **INTE** belastningsutjämnaren mellan de två. 
> 
> 

### <a name="nsg-for-management-nics-in-backend"></a>NSG för hanterings-nätverkskort i BackEnd
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| tillåt RDP från klientdel |Tillåt |100 |192.168.1.0/24 |* |\* |3389 |TCP |

### <a name="nsg-for-database-access-nics-in-back-end"></a>NSG för databasåtkomst-nätverkskort i serverdelen
**Regler för inkommande trafik**

| Regel | Åtkomst | Prioritet | Källadress-intervall | Källport | Måladress-intervall | Målport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| tillåt SQL från klientdelen |Tillåt |100 |192.168.1.0/24 |* |\* |1433 |TCP |

Eftersom vissa av de ovanstående NSG:erna måste kopplas till individuella nätverkskort, behöver du distribuera det här scenariot som en Resource Manager distribution. Observera hur reglerna kombineras för undernäts- och nätverkskortsnivå, beroende på hur de ska användas. 

## <a name="next-steps"></a>Nästa steg
* [Distribuera NSG:er i den klassiska distributionsmodellen](virtual-networks-create-nsg-classic-ps.md).
* [Distribuera NSG:er i Resource Manager](virtual-networks-create-nsg-arm-pportal.md).
* [Hantera NSG-loggar](virtual-network-nsg-manage-log.md).

