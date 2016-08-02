<properties 
   pageTitle="Vad är användardefinierade vägar och IP-vidarebefordring?"
   description="Lär dig hur du använder användardefinierade vägar (UDR) och IP-vidarebefordring för att vidarebefordra trafik till nätverks-virtuella installationer i Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# Vad är användardefinierade vägar och IP-vidarebefordring?
När du lägger till virtuella datorer (VM:ar) till ett virtuellt nätverk (VNet) i Azure, märker du att VM:arna kan kommunicera automatiskt med varandra över nätverket. Du behöver inte ange någon gateway, även om VM:arna befinner sig på olika undernät. Samma sak gäller för kommunikation från VM:arna till det offentliga Internet samt till ditt lokala nätverk när det finns en hybridanslutning från Azure till ditt eget datacenter.

Det här flödet av kommunikation är möjligt eftersom Azure använder en uppsättning systemvägar för att definiera hur IP-trafiken flödar. Systemvägar kontrollerar kommunikationsflödet i följande scenarier:

- Från inom samma undernät.
- Från ett undernät till ett annat inom ett VNet.
- Från VM:ar till Internet.
- Från ett VNet till ett annat VNet via en VPN-gateway.
- Från ett VNet till ditt lokala nätverk via en VPN-gateway.

Bilden nedan visar en enkel installation med ett VNet, två undernät och några VM:ar, samt de systemvägar som låter IP-trafiken flöda.

![Systemvägar i Azure](./media/virtual-networks-udr-overview/Figure1.png)

Även om användningen av systemvägar förenklar trafik automatiskt för din distribution så finns det fall där du kan vilja kontrollera routingen för paket genom en virtuell installation. Du kan göra det genom att skapa användardefinierade vägar som anger next hop för paket som flödar till ett specifikt undernät så att de istället går till din virtuella installation och som aktiverar IP-vidarebefordring för VM:en som kör den virtuella installationen.

Bilden nedan visar ett exempel på användardefinierade vägar och IP-vidarebefordran för att tvinga paket som skickas till ett undernät från ett annat att gå via en virtuell installation på ett tredje undernät.

![Systemvägar i Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Användardefinierade vägar tillämpas bara på trafik som lämnar ett undernät. Du kan inte skapa vägar för att specificera hur trafik kommer in i ett undernät från Internet, till exempel. Den installation som du vidarebefordrar trafik till kan inte heller vara i samma undernät som trafiken kommer från. Skapa alltid ett separat undernät för dina installationer. 

## Routeresurs
Paketen dirigeras över ett TCP/IP-nätverk som baseras på en routingtabell som definierats vid varje nod på det fysiska nätverket. En routingtabell är en samling individuella vägare som används för att bestämma var paket ska vidarebefordras baserat på mål-IP-adress. En väg består av följande:

|Egenskap|Beskrivning|Villkor|Överväganden|
|---|---|---|---|
| Adressprefix | Mål-CIDR som vägen gäller för, till exempel 10.1.0.0/16.|Måste vara ett giltigt CIDR-intervall som representerar adresser på det offentliga Internet, Azure-virtuella nätverk eller lokala datacenter.|Kontrollera att **Adressprefix** inte innehåller adressen för **Nexthop-värdet**, annars hamnar dina paket i en evig loop från källan till nexthop utan att någonsin nå målet. |
| Nexthop-typ | Den typ av Azure-hop som paketet ska skickas till. | Måste vara ett av följande värden: <br/> **Lokala**. Representerar det lokala virtuella nätverket. Om du till exempel har två undernät, 10.1.0.0/16 och 10.2.0.0/16 i samma virtuella nätverk, kommer väger för varje undernät i routingtabellen ha nexthop-värdet *Lokal*. <br/> **VPN-gateway**. Representerar en Azure S2S VPN-gateway. <br/> **Internet**. Representerar standard Internet-gatewayen från Azure-infrastrukturen. <br/> **Virtuell installation**. Representerar en virtuell installation som du lagt till i ditt Azure-virtuella nätverk. <br/> **NULL**. Representerar ett svart hål. Paket som vidarebefordras till en svart hål, vidarebefordras inte alls.| Du kan använda en **NULL**-typ för att stoppa paket från att flöda till ett givet mål. | 
| Nexthop-värde | Nexthop-värdet innehåller IP-adressen som paket ska vidarebefordras till. Nexthop-värden tillåts bara i vägar där nexthop-typen är *virtuell installation*.| Måste vara en nåbar IP-adress. | Om IP-adressen representerar en VM, bör du se till att aktivera [IP-vidarebefordring](#IP-forwarding) i Azure för den VM:n. |

### Systemvägar
Varje undernät som skapats i ett virtuellt nätverk, kopplas automatiskt till en routingtabell som innehåller följande regler för systemvägar:

- **Lokal VNet-regel**: Den här regeln skapas automatiskt för varje undernät i ett virtuellt nätverk. Det anger att det finns en direktlänk mellan VM:arna på VNet och att det inte finns något mellanliggande nexthop.
- **Lokal regel**: Den här regeln gäller för all trafik till det lokala adressintervallet och använder VPN-gatewayer som nexthop-mål.
- **Internetregel**: Den här regeln hanterar all trafik med mål i det offentliga Internet och använder sig av infrastrukturens internet-gateway som nexthop för all trafik med mål i Internet.

### Användardefinierade vägar
För de flesta miljöer behöver du bara systemvägarna som definierats i Azure. Du kan dock behöva skapa en routingtabell och lägga till en eller flera vägar i vissa fall, som när du vill:

- Tvinga tunneling till Internet via ditt lokala nätverk.
- Använda virtuella installationer i din Azure-miljö.

I ovanstående scenarier, behöver du skapa en routingtabell och lägga till användardefinierade vägar till den. Du kan ha tabeller med flera vägar och samma routingtabell kan vara kopplad till en eller flera undernät. Varje undernät kan bara vara kopplat till en enda vägtabell. Alla VM:ar och molntjänster i ett undernät använder routingtabellen som är kopplad till det undernätet.

Undernät förlitar sig på systemvägar tills att en routingtabell kopplas till undernätet. När det finns en koppling, så sköts routningen baserat på längsta prefix-matchning (LPM) bland användardefinierade vägar och systemvägar. Om det finns fler än en väg med samma LPM-matchning så väljs en väg baserat på dess ursprung i följande ordning:

1. Användardefinierad väg
1. BGP-väg (när ExpressRoute används)
1. Systemväg

Information om hur man skapar användardefinierade vägar finns i [Så här skapar du vägar och aktiverar IP-vidarebefordran i Azure](virtual-networks-udr-how-to.md#How-to-manage-routes).

>[AZURE.IMPORTANT] Användardefinierade vägar tillämpas endast på virtuella Azure-datorer och Azure-molntjänster. Om du till exempel vill lägga till en virtuell brandväggsinstallation mellan ditt lokala nätverk och Azure, behöver du skapa en användardefinierad väg för dina Azure-routningstabeller som vidarebefordrar all trafik till det lokala adressutrymmet till den virtuella installationen. Inkommande trafik från det lokala adressutrymmet kommer dock att flöda genom din VPN-gateway eller ExpressRoute-krets och rakt till Azure-miljön, vilket kringgår den virtuella installationen.

### BGP-vägar
Om du har en ExpressRoute-anslutning mellan ditt lokala nätverk och Azure, kan du aktivera BGP för att sprida väga från ditt lokala nätverk till Azure. BGP-vägarna används på samma sätt som systemvägar och användardefinierade vägar på varje Azure-undernät. Mer information finns i [Introduktion till ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Du kan konfigurera Azure-miljön att använda sig av tvingad tunneling genom ditt lokala nätverk genom att skapa en användardefinierad väg för undernätet 0.0.0.0/0 som använder VPN-gatewayen som nexthop. Det fungerar dock bara om du använder en VPN-gateway, inte ExpressRoute. Tvingad tunneling med ExpressRoute konfigureras via BGP.

## IP-vidarebefordran
Som det beskrivs ovan, är en av de huvudsakliga skälen att skapa en användardefinierad väg att vidarebefordra trafik till en virtuell installation. En virtuell installation är helt enkelt en VM som kör ett program som används för att hantera nätverkstrafik på något sätt, som en brandvägg eller en NAT-enhet.

Den här virtuella installations-VM:en måste kunna ta emot inkommande trafik som inte är adresserad till den. För att låta en VM ta emot trafik som är adresserad till andra mål, behöver du aktivera IP-vidarebefordran för VM:en. Det är en Azure-inställning, inte en inställning i gästoperativsystemet.

## Nästa steg

- Lär dig hur du [skapar vägar i Resource Manager-distributionsmodellen](virtual-network-create-udr-arm-template.md) och kopplar dem till undernät. 
- Lär dig hur du [skapar vägar i den klassiska distributionsmodellen](virtual-network-create-udr-classic-ps.md) och kopplar dem till undernät.



<!--HONumber=Jun16_HO2-->


