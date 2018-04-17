---
title: Azure Virtual Network (VNet) planerings- och designguide | Microsoft Docs
description: Lär dig hur du planerar och utformar virtuella nätverk i Azure baserat på dina krav för isolering, anslutning och plats.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: 6e41dae2f4e93fe2e3cef689596612a6a192c844
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="plan-and-design-azure-virtual-networks"></a>Planera och utforma Azure-nätverk
Det är enkelt att skapa ett virtuellt nätverk om du vill experimentera med, men troligen har du ska distribuera flera virtuella nätverk över tid för att stödja produktion behoven för din organisation. Med lite planering och design, kommer du att kunna distribuera Vnet och ansluta de resurser du behöver mer effektivt. Om du inte är bekant med VNets rekommenderar vi som du [Lär dig mer om Vnet](virtual-networks-overview.md) och [hur du distribuerar](quick-create-portal.md) något innan du fortsätter.

## <a name="plan"></a>Planera
Det är viktigt för lyckad goda kunskaper om Azure-prenumerationer, regioner och nätverksresurser. Du kan använda listan med överväganden nedan som utgångspunkt. När du förstår de överväganden kan definiera du kraven för utformningen av din nätverk.

### <a name="considerations"></a>Överväganden
Innan du svara på den planering frågor nedan, Tänk på följande:

* Allt som du skapar i Azure består av en eller flera resurser. En virtuell dator (VM) är en resurs, nätverkskortet (NIC) som används av en virtuell dator är en resurs, offentliga IP-adressen används av ett nätverkskort är en resurs är VNet som nätverkskortet är anslutet till en resurs.
* Du skapar resurser inom en [Azure-region](https://azure.microsoft.com/regions/#services) och prenumeration. Resurser kan bara vara ansluten till ett virtuellt nätverk som finns i samma region och prenumeration resursen är i.
* Du kan ansluta virtuella nätverk till varandra med hjälp av:
    * **[Virtuellt nätverk peering](virtual-network-peering-overview.md)**: virtuella nätverk måste finnas i samma Azure-region. Bandbredden mellan resurser i peerkoppla virtuella nätverk är samma som om resurserna som är anslutna till samma virtuella nätverk.
    * **En Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: virtuella nätverk kan finnas i samma eller olika Azure regioner. Bandbredden mellan resurser i virtuella nätverk som är anslutna via en VPN-Gateway begränsas av bandbredden för VPN-Gateway.
* Du kan ansluta Vnet till ditt lokala nätverk med hjälp av en av de [anslutningsalternativ](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) tillgängliga i Azure.
* Olika resurser kan grupperas i [resursgrupper](../azure-resource-manager/resource-group-overview.md#resource-groups), vilket gör det enklare att hantera resurser som en enhet. En resursgrupp kan innehålla resurser från flera områden, så länge resurserna som tillhör samma prenumeration.

### <a name="define-requirements"></a>Definiera krav
Använd frågorna nedan som utgångspunkt för utformningen av din Azure-nätverk.    

1. Vilka Azure platser ska du använda värden Vnet?
2. Behöver du ange kommunikation mellan dessa Azure platser?
3. Behöver du ange kommunikation mellan din Azure-VNet(s) och din lokala datacenter(s)?
4. Hur många infrastruktur som en tjänst (IaaS) virtuella datorer, cloud services-roller och web apps gör du behöver för din lösning?
5. Behöver du isolera trafik baserat på grupper med virtuella datorer (d.v.s. frontend-webbservrar och backend-databasservrar)?
6. Behöver du kontrollera trafikflödet använder virtuella installationer?
7. Användarna måste ha olika uppsättningar av behörigheter till olika Azure-resurser?

### <a name="understand-vnet-and-subnet-properties"></a>Förstå VNet och undernät egenskaper
VNet och undernät resurser hjälpa dig att definiera en säkerhetsgräns för arbetsbelastningar som körs i Azure. Ett virtuellt nätverk kännetecknas av en samling adressutrymmen, definierad som CIDR-block.

> [!NOTE]
> Nätverksadministratörer är bekanta med CIDR-notering. Om du inte är bekant med CIDR, [mer information om den](http://whatismyipaddress.com/cidr).
>
>

Vnet innehåller följande egenskaper.

| Egenskap | Beskrivning | Villkor |
| --- | --- | --- |
| **Namn** |Namn på virtuella nätverk |Sträng på upp till 80 tecken. Kan innehålla bokstäver, siffror, understreck, punkter eller bindestreck. Måste börja med en bokstav eller en siffra. Måste sluta med en bokstav, en siffra eller understreck. Kan innehåller gemener och versaler. |
| **Plats** |Azure-plats (kallas även region). |Måste vara ett giltigt Azure-platser. |
| **addressSpace** |Samling av adressprefix som utgör VNet i CIDR-notation. |Måste vara en matris med giltig CIDR-Adressblock, inklusive offentliga IP-adressintervall. |
| **Undernät** |Samling av undernät som utgör VNet |Se undernät egenskaper tabellen nedan. |
| **dhcpOptions** |Objekt som innehåller en obligatorisk egenskap med namnet **dnsServers**. | |
| **dnsServers** |Matris för DNS-servrar som används av VNet. Om ingen server har angetts används intern namnmatchning för Azure. |Måste vara en matris med upp till 10 DNS-servrar efter IP-adress. |

Ett undernät är en underordnad resurs i ett VNet och hjälper dig att definiera segmenten i adressutrymmen i CIDR-block med IP-adressprefix. Nätverkskorten kan läggas till i undernät och anslutna till virtuella datorer kan man har anslutning för olika arbetsbelastningar.

Undernät innehåller följande egenskaper.

| Egenskap | Beskrivning | Villkor |
| --- | --- | --- |
| **Namn** |Namn på undernät |Sträng på upp till 80 tecken. Kan innehålla bokstäver, siffror, understreck, punkter eller bindestreck. Måste börja med en bokstav eller en siffra. Måste sluta med en bokstav, en siffra eller understreck. Kan innehåller gemener och versaler. |
| **Plats** |Azure-plats (kallas även region). |Måste vara ett giltigt Azure-platser. |
| **addressPrefix** |Enkel adressprefixet som utgör undernät i CIDR-notation |Måste vara ett enda CIDR-block som är en del av en av de VNet-adressutrymmen. |
| **networkSecurityGroup** |NSG tillämpas för undernätet | |
| **Migreringstillståndet** |Routningstabellen tillämpad på undernätet | |
| **ipConfigurations** |Samling av IP-konfigurationsobjekt som används av nätverkskort som är anslutna till undernätet | |

### <a name="name-resolution"></a>Namnmatchning
Som standard används ditt VNet [Azure-tillhandahållna namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) att matcha namn i det virtuella nätverket och på Internet. Men om du ansluter din Vnet till ditt lokala datacenter, måste du ange [DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md) att matcha namn mellan ditt nätverk.  

### <a name="limits"></a>Begränsningar
Granska de nätverk gränserna i den [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) att tillse att din design inte krockar med någon av gränserna. Vissa begränsningar kan utökas genom att öppna ett supportärende.

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan använda [Azure RBAC](../role-based-access-control/built-in-roles.md) att styra åtkomstnivån som olika användare behöva olika resurser i Azure. På så sätt kan du särskilja arbetet som utförs av ditt team utifrån deras behov.

När det gäller de virtuella nätverken är berörda användare i den **Network-deltagare** roll har full kontroll över Azure Resource Manager nätverksresurser på virtuella datorer. På liknande sätt kan användare i den **klassiska Network-deltagare** roll har full kontroll över klassiska virtuella nätverksresurser.

> [!NOTE]
> Du kan också [skapa egna roller](../role-based-access-control/role-assignments-portal.md) avgränsa administrativa behov.
>
>

## <a name="design"></a>Design
När du väl vet svaren på frågorna i den [planera](#Plan) avsnittet kan du granska följande innan du definierar ditt Vnet.

### <a name="number-of-subscriptions-and-vnets"></a>Antalet prenumerationer och Vnet
Du bör överväga att skapa flera Vnet i följande scenarier:

* **Virtuella datorer som måste placeras på olika platser i Azure**. Vnet i Azure är regionala. De kan sträcka sig över platser. Därför behöver du minst ett virtuellt nätverk för varje Azure-plats som du vill värden virtuella datorer i.
* **Arbetsbelastningar som måste vara helt isolerade från varandra**. Du kan skapa separata Vnet som även använder samma IP-adressutrymmen för att isolera olika arbetsbelastningar från varandra.

Tänk på att de gränser som du ser ovan är per region per prenumeration. Det innebär att du kan använda flera prenumerationer för att öka gränsen för resurser som du kan upprätthålla i Azure. Du kan använda en plats-till-plats-VPN eller ExpressRoute-krets för att ansluta Vnet för olika prenumerationer.

### <a name="subscription-and-vnet-design-patterns"></a>Prenumerationen och designmönster för virtuella nätverk
I tabellen nedan visas några vanliga designmönster för prenumerationer och Vnet.

| Scenario | Diagram | Tekniker | Nackdelar |
| --- | --- | --- | --- |
| En prenumeration, två Vnet per program |![Enstaka prenumeration](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Endast en prenumeration för att hantera. |Maximalt antal Vnet per Azure-region. Du behöver flera prenumerationer efter. Granska de [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) artikeln för information. |
| En prenumeration per app, två Vnet per program |![Enstaka prenumeration](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Använder bara två Vnet per prenumeration. |Svårare att hantera när det finns för många appar. |
| En prenumeration per affärsenhet, två Vnet per program. |![Enstaka prenumeration](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Balansera mellan antalet prenumerationer och virtuella nätverk. |Maximalt antal Vnet per affärsenhet (prenumeration). Granska de [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) artikeln för information. |
| En prenumeration per affärsenhet, två Vnet per grupp av appar. |![Enstaka prenumeration](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Balansera mellan antalet prenumerationer och virtuella nätverk. |Appar måste separat med hjälp av undernät och NSG: er. |

### <a name="number-of-subnets"></a>Antalet undernät
Du bör flera undernät i ett VNet i följande scenarier:

* **Det finns inte tillräckligt med privata IP-adresser för alla nätverkskort i ett undernät**. Om din undernätsadressutrymmet inte innehåller tillräckligt med IP-adresser för antalet nätverkskort i undernät, måste du skapa flera undernät. Tänk på att Azure reserverar 5 privata IP-adresser från varje undernät som inte kan användas: första och sista adresser adressutrymme (för undernätsadress och multicast) och 3 adresser som ska användas internt (som DHCP och DNS).
* **Säkerhet**. Du kan använda undernät till olika grupper med virtuella datorer från varandra för arbetsbelastningar som har ett flera lager struktur och tillämpa olika [nätverkssäkerhetsgrupper (NSG: er)](virtual-networks-nsg.md#subnets) för dessa undernät.
* **Hybridanslutning**. Du kan använda VPN-gateway och ExpressRoute-kretsar till [ansluta](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) ditt Vnet till varandra och till din lokala data center(s). VPN-gatewayer och ExpressRoute-kretsar kräver ett undernät sina egna ska skapas.
* **Virtuella installationer**. Du kan använda en virtuell installation, till exempel en brandvägg, WAN accelerator eller VPN-gateway i ett Azure VNet. När du gör detta behöver du [dirigera trafik](virtual-networks-udr-overview.md) de enheterna och isolera dem i sina egna undernät.

### <a name="subnet-and-nsg-design-patterns"></a>Designmönster för undernät och NSG
I tabellen nedan visas några vanliga designmönster för att använda undernät.

| Scenario | Diagram | Tekniker | Nackdelar |
| --- | --- | --- | --- |
| Enskilt undernät, NSG: er per programnivå per program |![Enskilt undernät](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Endast ett undernät för att hantera. |Flera NSG: er krävs för att isolera varje program. |
| Ett undernät per app, NSG: er per program lager |![Undernät per program](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Färre NSG: er att hantera. |Flera undernät för att hantera. |
| Ett undernät per programnivå NSG: er per app. |![Undernät per lager](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Balansera mellan antalet undernät och NSG: er. |Maximalt antal NSG: er per prenumeration. Granska de [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) artikeln för information. |
| Ett undernät per programnivå per app, NSG: er per undernät |![Undernät per lager per program](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Eventuellt mindre antal NSG: er. |Flera undernät för att hantera. |

## <a name="sample-design"></a>Exempel design
Studera följande scenario för att illustrera tillämpningen av informationen i den här artikeln.

Du arbetar för ett företag som har 2 datacenter i Nordamerika och två Datacenter Europa. Du har identifierat 6 olika Internetriktade program från kunden underhålls av 2 olika affärsenheter som du vill migrera till Azure som en pilot. Grundläggande arkitektur för programmen är följande:

* App1, App2, App3 och App4 är webbprogram på Linux-servrar som kör Ubuntu. Varje program som ansluter till en separat programserver som är värd för RESTful-tjänster på Linux-servrar. RESTful-tjänster ansluta till en serverdel MySQL-databas.
* App5 och App6 är webbprogram som finns på Windows-servrar som kör Windows Server 2012 R2. Varje program som ansluter till en serverdel SQL Server-databas.
* Alla appar som för närvarande finns på en av företagets datacenter i Nordamerika.
* Lokalt Datacenter använder 10.0.0.0/8 adressutrymme.

Du måste skapa ett virtuellt nätverk-lösning som uppfyller följande krav:

* Varje affärsenhet bör inte påverkas av resurser som används för andra enheter.
* Du bör minimera Vnet och undernät för att underlätta hanteringen.
* Varje affärsenhet ska ha en enda test/utveckling VNet som används för alla program.
* Varje program finns i 2 olika Azure-Datacenter per kontinent (Nordamerika och Europa).
* Varje program är helt isolerade från varandra.
* Varje program kan användas av kunder via Internet med HTTP.
* Varje program kan användas av användare som är anslutna till de lokala datacenter med hjälp av en krypterad tunnel.
* Anslutning till lokalt Datacenter ska använda den befintliga VPN-enheter.
* Företagets nätverk gruppen ska ha fullständig kontroll över konfigurationen av virtuellt nätverk.
* Utvecklare i varje affärsenhet ska endast kunna distribuera virtuella datorer till befintliga undernät.
* Att kommer migreras alla program som de är till Azure (lift och SKIFT).
* Databaserna i varje plats ska replikeras till andra Azure platser en gång om dagen.
* Varje program bör använda 5 frontend-webbservrar, 2 programservrar (om det behövs) och 2-databasservrar.

### <a name="plan"></a>Planera
Bör du börja planera genom att besvara frågan i designen i [definiera krav](#Define-requirements) enligt nedan.

1. Vilka Azure platser ska du använda värden Vnet?

    2 platser i Nordamerika och Europa 2 platser. Du bör välja de baserade på den fysiska platsen för din befintliga lokala datacenter. På så sätt anslutningen från din fysiska platser till Azure har en bättre svarstid.
2. Behöver du ange kommunikation mellan dessa Azure platser?

    Ja. Eftersom databaserna måste replikeras till alla platser.
3. Behöver du ange kommunikation mellan din Azure-VNet(s) och dina lokala data center(s)?

    Ja. Eftersom användare som är anslutna till måste lokala Datacenter kunna komma åt program via en krypterad tunnel.
4. Hur många virtuella IaaS-datorer behöver för din lösning?

    200 IaaS-VM. App1, App2, App3 och App4 kräver 5 webbservrar som varje, 2 programservrarna som varje och 2-databasservrar. Det är totalt 9 virtuella IaaS-datorer per program eller 36 IaaS-VM. App5 och App6 kräver 5 webbservrar och 2-databasservrar. Det är totalt 7 virtuella IaaS-datorer per program eller 14 IaaS-VM. Du måste därför 50 IaaS-VM för alla program i varje Azure-region. Eftersom vi behöver använda 4 regioner kan finnas det 200 IaaS-VM.

    Du måste också ange DNS-servrar i varje virtuellt nätverk eller i ditt lokala Datacenter för att matcha namn mellan Azure IaaS-VM och ditt lokala nätverk.
5. Behöver du isolera trafik baserat på grupper med virtuella datorer (d.v.s. frontend-webbservrar och backend-databasservrar)?

    Ja. Varje program bör vara helt isolerade från varandra och varje program lager ska också isoleras.
6. Behöver du kontrollera trafikflödet använder virtuella installationer?

    Nej. Virtuella installationer kan användas för att ge mer kontroll över trafikflödet, inklusive mer detaljerad loggning av data plan.
7. Användarna måste ha olika uppsättningar av behörigheter till olika Azure-resurser?

    Ja. Nätverk gruppen behöver fullständig behörighet för de virtuella nätverksinställningarna när utvecklare bara ska kunna distribuera sina virtuella datorer till befintlig undernät.

### <a name="design"></a>Design
Du bör följa designen anger prenumerationer, Vnet, undernät och NSG: er. Diskuteras NSG: er här, men du får lära dig mer om [NSG: er](virtual-networks-nsg.md) innan du avslutar din design.

**Antalet prenumerationer och Vnet**

Följande krav är relaterade till prenumerationer och Vnet:

* Varje affärsenhet bör inte påverkas av resurser som används för andra enheter.
* Du bör minimera Vnet och undernät.
* Varje affärsenhet ska ha en enda test/utveckling VNet som används för alla program.
* Varje program finns i 2 olika Azure-Datacenter per kontinent (Nordamerika och Europa).

Baserat på dessa krav, behöver du en prenumeration för varje affärsenhet. På så sätt kan förbrukningen av resurser från en affärsenhet inte räknas som en gränserna för andra enheter. Och eftersom du vill minimera antalet Vnet, bör du använda den **en prenumeration per affärsenhet, två Vnet per grupp appar** mönstret som du ser nedan.

![Enstaka prenumeration](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Du måste också ange adressutrymmet för varje virtuellt nätverk. Eftersom du behöver anslutningen mellan lokala data datacenter och Azure-regioner adressutrymmet som används för Azure Vnet kan inte vara i konflikt med det lokala nätverket och det adressutrymme som används av varje virtuellt nätverk får inte vara i konflikt med andra befintliga Vnet. Du kan använda adressutrymmen i tabellen nedan för att uppfylla kraven.  

| **Prenumeration** | **Virtuella nätverk** | **Azure-region** | **Adressutrymme** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |Västra USA |172.16.0.0/16 |
| BU1 |ProdBU1US2 |Östra USA |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Norra Europa |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Västra Europa |172.19.0.0/16 |
| BU1 |TestDevBU1 |Västra USA |172.20.0.0/16 |
| BU2 |TestDevBU2 |Västra USA |172.21.0.0/16 |
| BU2 |ProdBU2US1 |Västra USA |172.22.0.0/16 |
| BU2 |ProdBU2US2 |Östra USA |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Norra Europa |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Västra Europa |172.25.0.0/16 |

**Antalet undernät och NSG: er**

Följande krav är relaterade till undernät och NSG: er:

* Du bör minimera Vnet och undernät.
* Varje program är helt isolerade från varandra.
* Varje program kan användas av kunder via Internet med HTTP.
* Varje program kan användas av användare som är anslutna till de lokala datacenter med hjälp av en krypterad tunnel.
* Anslutning till lokalt Datacenter ska använda den befintliga VPN-enheter.
* Databaserna i varje plats ska replikeras till andra Azure platser en gång om dagen.

Baserat på dessa krav, kan du använda ett undernät per programnivå och använder NSG: er att filtrera trafik per program. På så sätt kan du bara ha 3 undernät i varje virtuellt nätverk (klientdel programnivå och dataskiktet) och en NSG per program per undernät. I det här fallet bör du använda den **ett undernät per programnivå NSG: er per app** designmönstret. Bilden nedan visar användningen av design mönster representerar den **ProdBU1US1** VNet.

![Ett undernät per lager, en NSG per program per lager](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Du också behöva skapa en extra undernät för VPN-anslutningen mellan Vnet och ditt lokala datacenter. Och du måste ange adressutrymmet för varje undernät. Bilden nedan visar ett exempel lösning för **ProdBU1US1** VNet. Du kan replikera det här scenariot för varje virtuellt nätverk. Varje färg motsvarar ett annat program.

![Exempel VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Åtkomstkontroll**

Följande krav är relaterade till åtkomstkontroll:

* Företagets nätverk gruppen ska ha fullständig kontroll över konfigurationen av virtuellt nätverk.
* Utvecklare i varje affärsenhet ska endast kunna distribuera virtuella datorer till befintliga undernät.

Baserat på dessa krav, kan du lägga till användare från nätverk teamet till inbyggt **Network-deltagare** roll i varje prenumeration, och skapa en anpassad roll för programutvecklare i varje prenumeration som ger dem behörighet att lägga till virtuella datorer till befintliga undernät.

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett virtuellt nätverk](quick-create-portal.md).
* Förstå hur du [belastningsutjämna](../load-balancer/load-balancer-overview.md) IaaS-VM och [hantera routning över flera Azure-regioner](../traffic-manager/traffic-manager-overview.md).
* Lär dig mer om [nätverkssäkerhetsgrupper](security-overview.md) en NSG-lösning.
* Lär dig mer om din [mellan platser och VNet anslutningsalternativ](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
