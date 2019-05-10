---
title: 'Perimeternätverket nätverket exempel – skydda nätverk med perimeternätverk som består av en brandvägg, UDR och NSG: er | Microsoft Docs'
description: Skapa ett perimeternätverk (även kallat DMZ) med en brandvägg, en användardefinierad routning (UDR) och nätverkssäkerhetsgrupper (NSG).
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 0a7927868a9a4bebc80ec995baefbae4c45d747f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410480"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Exempel 3: Skapa ett perimeternätverk för att skydda nätverk med en brandvägg, UDR och NSG: er

[Gå tillbaka till gränsen bästa praxis sidan][HOME]

I det här exemplet skapar du ett perimeternätverk (även kallade en DMZ, demilitariserad zon och kontrollerat undernät). Exemplet implementerar en brandvägg, fyra Windows-servrar, en användardefinierad routning (UDR), IP-vidarebefordring och nätverkssäkerhetsgrupper (NSG). Den här artikeln beskriver hur du var och en av relevanta kommandon för att ge en bättre förståelse för varje steg. Trafik scenariot avsnittet beskrivs också i detalj hur trafik fortsätter genom försvarslinjer i perimeternätverket. Slutligen innehåller referensavsnittet koden och instruktioner för att skapa den här miljön så att du kan testa och experimentera med olika scenarier.

![Dubbelriktad perimeternätverk med NVA, NSG och UDR][1]

## <a name="environment-setup"></a>Konfigurera miljön

Det här exemplet används en prenumeration som innehåller följande komponenter:

* Tre molntjänster: SecSvc001 och FrontEnd001 BackEnd001
* Ett virtuellt nätverk (CorpNetwork) med tre undernät: SecNet, FrontEnd och BackEnd
* En virtuell nätverksinstallation: en brandvägg är anslutna till undernätet för SecNet
* En Windows-server som representerar en webbserver för programmet: IIS01
* Två Windows-servrar som representerar programmet backend-servrar: AppVM01, AppVM02
* En Windows-server som representerar en DNS-server: DNS01

Den [refererar till avsnittet](#references) innehåller ett PowerShell-skript som bygger på de flesta av miljön som beskrivs här. Den här artikeln ger inte annars detaljerade anvisningar för att skapa virtuella datorer (VM) och virtuella nätverk.

Att skapa miljön:

1. Spara nätverk XML-konfigurationsfilen ingår i den [referera till avsnittet](#references). Du behöver uppdatera det med namn, plats och IP-adresser som matchar det aktuella scenariot.
1. Uppdatera Användarvariabler i fullständigt skript för att matcha din miljö (till exempel prenumerationer, tjänstnamn och så vidare).
1. Kör skript i PowerShell.

> [!NOTE]
> Den region som anges i PowerShell-skriptet måste matcha den region som angetts i XML-nätverkskonfigurationsfilen.

När skriptet har körts, gör du följande:

1. Konfigurera brandväggsregler. Se den [brandväggsregler](#firewall-rules) avsnittet.
1. Du kan också använda de två skripten i referensavsnittet för att ställa in ett webbprogram på webbservern och app-servern för att tillåta testning av den här DMZ-konfigurationen.

## <a name="user-defined-routing"></a>Användardefinierad Routning

Som standard definieras följande systemvägar som:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal är alltid definierad adress-prefix för det specifika virtuella nätverket. Till exempel den kommer att ändras från virtuellt nätverk till virtuellt nätverk beroende på hur varje specifik virtuellt nätverk har definierats. Återstående systemvägar är statiska och som visas som standard.

För prioritet bearbetas vägar via metoden längsta Prefix-matchning (LPM). Så gäller den mest specifika vägen i tabellen för en viss måladress.

Därför trafik som är avsedd för en server som DNS01 (10.0.2.4) på lokalt nätverk (10.0.0.0/16) dirigeras över det virtuella nätverket på grund av 10.0.0.0/16 vägen.  För 10.0.2.4 är 10.0.0.0/16 vägen den mest specifika vägen. Denna regel gäller även om 10.0.0.0/8 och 0.0.0.0/0 kan också vara tillämpliga. De är dock mindre specifikt, så att de inte påverkar den här trafiken. Trafik till 10.0.2.4 med det lokala virtuella nätverket som dess nästa hopp så vidarebefordras till målet.

Till exempel gäller 10.0.0.0/16 vägen inte för trafik som är avsedd för 10.1.1.1. Systemväg 10.0.0.0/8 beror den mest specifika så att trafiken ignoreras eller ”black holed” nästa hopp är Null.

Om målet inte gäller för någon av Null-prefix eller VNETLocal-prefix, följer trafik den minst specifika vägen (0.0.0.0/0). Dirigeras den till internet som nästa hopp och ut ur Azures internet-anslutning.

Om det finns två identiska prefix i routningstabellen visas utifrån i prioritetsordning efter flödets källattribut:

1. VirtualAppliance: En användardefinierade väg som läggs till manuellt i tabellen.
1. VPNGateway: En dynamisk väg (BGP när det används med hybrid-nätverk) har lagts till av en dynamisk nätverksprotokoll. Dessa vägar kan ändras med tiden när protokollet dynamisk visar automatiskt ändringar i peer-kopplade nätverket.
1. Standard: Systemvägar, det lokala virtuella nätverket och statiska poster som visas i routningstabellen ovan.

> [!NOTE]
> Du kan nu använda en användardefinierad routning (UDR) med ExpressRoute och VPN-gatewayer för att tvinga mellan olika platser för utgående och inkommande trafik ska dirigeras till en virtuell nätverksinstallation (NVA).

### <a name="create-local-routes"></a>Skapa lokala vägar

Det här exemplet används två routningstabeller, ett för frontend och backend-undernät. Varje tabell har lästs in med statiska vägar som är lämpliga för det angivna undernätet. Varje tabell har tre vägar i det här exemplet:

1. Lokal undernätstrafik med inga nästa hopp som definierats. Den här vägen tillåter lokal undernätstrafik passera brandväggen.
2. Trafik i virtuella nätverk med ett nexthop som definierats som brandvägg. Den här vägen åsidosätter Standardregeln som tillåter lokal trafik i virtuella nätverk att dirigera direkt.
3. Alla återstående trafik (0/0) med ett nexthop som definierats som brandväggen.

När routningstabeller skapas, är de bundna till sina undernät. Routningstabellen klientdelsundernätet bör se ut:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

Det här exemplet används följande kommandon för att skapa routningstabellen, lägga till en användardefinierad väg och Binder routningstabellen till ett undernät. Objekt som börjar med `$`, till exempel `$BESubnet`, användardefinierade variabler från skriptet i referensavsnittet.

1. Skapa först routning bastabellen. Följande kodfragment skapar tabellen för backend-undernät. Fullständigt skript skapar även en motsvarande tabell för klientdelens undernät.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. När du har skapat routningstabellen kan du lägga till specifika användardefinierade vägar. Följande kodavsnitt anger att all trafik (0.0.0.0/0) dirigeras genom den virtuella installationen. En variabel `$VMIP[0]` används för att skicka in IP-adressen som tilldelas när den virtuella installationen har skapats tidigare i skriptet. Fullständigt skript skapar även en motsvarande regel i frontend-tabellen.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Föregående post för vägen åsidosätter standardvägen för ”0.0.0.0/0”, men 10.0.0.0/16 Standardregeln fortfarande tillåter trafik inom det virtuella nätverket för att dirigera direkt till målet och inte till den virtuella nätverksinstallationen. För att åtgärda problemet måste du lägga till följande regel:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Nu kan behöva du gör ett val. De två föregående reglerna dirigera all trafik i brandväggen för utvärdering, inklusive trafik i ett enda undernät. Du kanske vill det här beteendet. Men om du inte kan du tillåta trafik i ett undernät för att dirigera lokalt utan inblandning av brandväggen. Lägga till en tredje specifika regeln som direkt dirigerar alla adresser som är avsedd för det lokala undernätet (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Slutligen när routningstabellen skapas och fylls med användardefinierade vägar, måste du binda tabellen till ett undernät. Följande kodavsnitt Binder tabellen för backend-undernät. Fullständigt skript Binder också frontend routningstabellen till klientdelsundernätet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>IP-vidarebefordring

IP-vidarebefordring är en tillhörande funktion till UDR. Den här inställningen på en virtuell installation kan det ta emot trafik som inte är adresserad till installationen och sedan vidarebefordra trafiken till destinationen ultimate.

Till exempel om trafik från AppVM01 gör en begäran till servern DNS01, dirigerar UDR trafik i brandväggen. Med IP-vidarebefordring aktiverad, är trafiken med DNS01 målet (10.0.2.4) accepteras av brandväggsinstallation (10.0.0.4) och sedan vidarebefordras till dess ultimate mål (10.0.2.4). Utan IP-vidarebefordring är aktiverat i brandväggen, accepteras inte trafik av installationen trots routningstabellen har brandväggen som nästa hopp.

> [!IMPORTANT]
> Kom ihåg att aktivera IP-vidarebefordring tillsammans med användardefinierad routning.

IP-vidarebefordring kan aktiveras med ett enda kommando vid tidpunkten för skapandet av virtuell dator. Du anropar den VM-instans som är dina virtuell brandväggsinstallation och aktiverar IP-vidarebefordran. Tänk på att objekt i rött som börjar med `$`, till exempel `$VMName[0]`, användardefinierade variabler från skriptet i referensavsnittet i det här dokumentet. Nollbaserade omges av hakparenteser, `[0]`, representerar den första virtuella datorn i matrisen med virtuella datorer. Den första virtuella datorn (VM 0) måste vara brandväggen att fungera utan modifiering exempel skript. I fullständigt skript grupperas relevanta kodfragmentet med UDR-kommandon mot slutet.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

I det här exemplet skapar en nätverkssäkerhetsgrupp (NSG) och fylla den med en enda regel. Exemplet Binder sedan NSG endast till frontend och backend-undernät (inte SecNet). Regeln du läser in i NSG: N är följande:

* All trafik (alla portar) från internet till hela virtuella nätverket (alla undernät) nekas

Även om NSG: er som används i det här exemplet är deras huvudsakliga syfte som en sekundär försvarslinje mot manuell felkonfiguration. Du vill blockera all inkommande trafik från internet till de klient- eller backend-undernät. Trafik bör endast flödar genom SecNet undernätet i brandväggen, varefter rätta trafik ska dirigeras till klient- eller backend-undernät. Dessutom dirigera UDR-regler all trafik som når frontend- eller backend-undernät i brandväggen. Brandväggen ser det som ett asymmetriskt flöde och släpper den utgående trafiken.

Tre lager av säkerhet skydda frontend och backend-undernät:

1. Inga öppna slutpunkter på molntjänster FrontEnd001 och BackEnd001
1. NSG: er nekar trafik från internet
1. Utelämnar brandväggen asymmetrisk trafik

En intressant punkt om Nätverkssäkerhetsgruppen i det här exemplet är att den innehåller endast en regel som visas nedan. Den här regeln nekar internet-trafik i hela virtuella nätverk, inklusive säkerhet undernätet.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Eftersom NSG: N är bara kopplat till frontend och backend-undernät, används inte regeln för inkommande nätverkstrafik till undernätet för säkerhet. Därför flödar trafiken till undernätet för säkerhet.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Brandväggsregler

Du måste skapa regler i brandväggen. Eftersom brandväggen blockerar eller vidarebefordrar all trafik för inkommande, utgående och intra virtual network, måste många brandväggsregler. Brandväggen har dessutom att bearbeta all inkommande trafik till säkerhetstjänst offentliga IP-adress (på olika portar). Följ rekommenderade metoder för att undvika omarbete senare genom att skapa diagram över logiska flöden innan du konfigurerar undernät och brandväggsregler. Följande bild är en logisk vy för brandväggsregler i det här exemplet:

![Logisk vy för brandväggsreglerna][2]

> [!NOTE]
> Hanteringsportar varierar beroende på den virtuella nätverksinstallationen. Det här exemplet visar en Barracuda NextGen Firewall som använder port 22, 801 och 807. I dokumentationen för installation-leverantör för att hitta exakt vilka portar för hantering av enheten.

### <a name="logical-rule-description"></a>Beskrivning av logisk regel

Den logiska diagrammet ovan visar inte security undernätet eftersom den är den enda resursen i undernätet. Det här diagrammet visar brandväggsregler, hur de logiskt tillåter eller nekar trafikflöden, men inte själva dirigeras sökväg. De externa portar som valts för remote desktop protocol (RDP)-trafik är dessutom högre intervallet portar (8014 – 8026) valt för enklare läsbarhet att anpassas till de två sista oktetterna i de lokala IP-adresserna. Till exempel är lokala serveradressen is 10.0.1.4 associerad med extern port 8014. Du kan dock använda alla högre icke motstridiga portar.

Du behöver följande typer av regler för det här exemplet:

* Externa regler för inkommande trafik:
  1. Hantering av brandväggsregel: gör att trafik skickas till hanteringsportar på den virtuella nätverksinstallationen.
  2. RDP-regler för varje windows server: låter dig hantera enskilda servrar via RDP.  Beroende på ditt nätverks virtuella installation, kan du eventuellt att bifoga reglerna i en.
  3. Programmet trafikregler: en för klientdelen webbtrafik och en för backend-trafik (till exempel webbservern till datanivå). Konfigurationen av dessa regler är beroende av nätverksarkitektur och trafik flöden.

     * Den första regeln kan själva programmet trafik når application server. Till skillnad från reglerna för säkerhet, hantering och så vidare kan programregler externa användare eller tjänster att komma åt programmen. Det här exemplet har en enda webbserver på port 80, vilket gör att en enda brandväggsregel program att omdirigera trafik som är avsedda för en extern IP-adress att dirigera i stället till webbserverns interna IP-adressen. Omdirigerad trafik sessionen mappas av NAT för den interna servern.
     * Regel för nätverkstrafik av andra program är backend-regeln för att tillåta att använda alla portar för att dirigera trafik till AppVM01-servern, men inte AppVM02-server.

* Interna regler för trafiken intra virtual network:
  1. Utgående till internet-regel: tillåter trafik från alla nätverk ska skickas till de valda nätverken. Den här regeln är vanligtvis ett standardvärde i brandväggen, men i ett inaktiverat tillstånd. Aktivera den här regeln i det här exemplet.
  2. DNS-regel: tillåter endast DNS (port 53)-trafik till DNS-servern. De flesta trafik från klientdelen till serverdelen för den här miljön är blockerad. Den här regeln kan särskilt DNS från alla lokala undernätet.
  3. Regel för undernät-till-undernät: gör att alla servrar i backend-undernät att ansluta till en server på klientdelsundernätet, men inte tvärtom.

* Felsäker regel för trafik som inte uppfyller något av ovanstående villkor:
  1. Neka alla regel för nätverkstrafik: alltid sista regeln när det gäller prioritet. Om flödet i nätverkstrafiken inte matchar någon av föregående regler, blockerar den i den här regeln. Det är en standardregel. Eftersom det ofta är aktiverad behövs inga ändringar.

> [!TIP]
> I den andra programmet regeln för trafikbelastningsutjämning tillåts alla portar att det här exemplet är enkel. I ett scenario med verkliga bör du använda viss port och adressintervall för att minska risken för angrepp på den här regeln.


> [!IMPORTANT]
> När du har skapat reglerna, är det viktigt att du läser igenom prioriteten för varje regel för att se till att trafik tillåts eller nekas efter behov. I det här exemplet är reglerna i prioritetsordning. Det är enkelt att blir utelåst från brandväggen om reglerna är felaktigt sorterad. Se till att ange brandväggsregeln för hantering som absolut högsta prioritet.

### <a name="rule-prerequisites"></a>Regel för krav

Offentliga slutpunkter måste anges för den virtuella datorn körs i brandväggen. De här offentliga slutpunkter måste vara öppen så att brandväggen kan bearbeta trafik. Det finns tre typer av trafik i det här exemplet:

1. Hantering av trafik att styra brandvägg och brandväggsregler
1. RDP-trafik för att styra windows-servrar
1. Programtrafik

Vilken typ av trafik som visas i den övre hälften av brandväggen regler logiska diagrammet ovan.

> [!IMPORTANT]
> Kom ihåg att *alla* trafiken går genom brandväggen. Till fjärrskrivbord till IIS01-servern måste du ansluta till brandväggen på port 8014 och Tillåt brandväggen för att dirigera begäran RDP internt till IIS01 RDP-porten. Azure-portalens **Connect** knappen fungerar inte eftersom det finns ingen direkt RDP-väg till IIS01 som visas på portalen. Alla anslutningar från internet är att tjänsten security och en port (till exempel secscv001.cloudapp.net:xxxx). Referera till ovanstående diagram för matchning av extern port och intern IP och port.

Du kan öppna en slutpunkt vid tidpunkten för skapandet av VM eller efter versionen. Exempelskriptet och följande kodavsnitt öppnar du en slutpunkt när den virtuella datorn har skapats.

Tänk på att objekt som börjar med `$`, till exempel `$VMName[$i]`, användardefinierade variabler från skriptet i referensavsnittet. Den `[$i]` representerar matris-numret för en specifik virtuell dator i en matris med virtuella datorer.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Även om det är inte klart visas här på grund av variabler, bör du bara öppna slutpunkter i Molntjänsten säkerhet. Den här försiktighetsåtgärden hjälper till att brandväggen hanterar all inkommande trafik, oavsett om det har dirigeras, mappas om av NAT eller tas bort.

Installera en management-klienten på en dator som kan hantera brandväggen och skapa de nödvändiga konfigurationerna. Information om hur du hanterar din brandvägg eller andra NVA i leverantörens dokumentation. Resten av det här avsnittet samt de **brandväggsregel skapas** avsnitt beskriver konfigurationen av brandväggen. Använd leverantörens Hanteringsklient, inte Azure-portalen eller PowerShell.

Gå till [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) att hämta management-klienten och lär dig hur du ansluter till Barracuda-brandväggen.

När du är inloggad i brandväggen, definierar du nätverks- och objekt innan du skapar brandväggsregler. Dessa två nödvändiga objektklasser kan göra att man skapar reglerna.

Definiera tre namngivna nätverksobjekt för i det här exemplet:

* Klientdelsundernät
* Backend-undernät
* IP-adressen för DNS-server

Skapa ett namngivna nätverk från Barracuda NG Admin klient-instrumentpanelen:

1. Gå till den **konfigurationsfliken**.
1. Välj **Ruleset** i den **användningsinställningar** avsnittet
1. Välj **nätverk** under den **brandväggen objekt** menyn.
1. Välj **New** i den **redigera nätverk** menyn.
1. Skapa nätverksobjektet genom att lägga till namnet och prefixet:

   ![Skapa ett front-ed nätverksobjekt][3]

Föregående steg skapa ett namngivna nätverk för klientdelens undernät. Skapa ett liknande objekt för backend--undernätet. Nu kan undernäten refereras enklare efter namn i brandväggsreglerna.

För DNS-server-objekt:

![Skapa ett DNS-server-objekt][4]

Referens för den här IP-adress används i en DNS-regel senare i dokumentet.

Den andra objektklassen innehåller services-objekt som representerar RDP anslutningsportar för varje server. Det befintliga objektet i RDP-tjänsten är bunden till standard RDP-porten 3389. Därför kan du skapa nya tjänster för att tillåta trafik från externa portar (8014 8026). Du kan också lägga till de nya portarna som den befintliga RDP-tjänsten. Dock för enkel demonstration, kan du se en regel för varje server. Skapa en ny regel för RDP för en server från Barracuda NG Admin klient-instrumentpanelen:

1. Gå till den **konfigurationsfliken**.
1. Välj **Ruleset** i den **användningsinställningar** avsnittet.
1. Välj **Services** under den **brandväggen objekt** menyn.
1. Bläddra nedåt i listan över tjänster och välj **RDP**.
1. Högerklicka och välj Kopiera, högerklicka och välj Klistra in.
1. Det finns nu en RDP-Copy1 service-objekt som kan redigeras. Högerklicka på **RDP-Copy1** och välj **redigera**.
1. Den **redigera serviceobjektet** fönster öppnas och visas som visas här:

   ![Kopia av standard RDP-regel][5]

1. Redigera värdena för att representera RDP-tjänsten för en specifik server. För AppVM01, RDP Standardregeln ska ändras så att den återspeglar en ny tjänst **namn**, **beskrivning**, och externa RDP-porten som används i figur 8-diagram. Tänk på att portarna ändras från standardvärdet 3389 RDP till den externa porten för den här specifika servern. Den externa porten för AppVM01 är till exempel 8025. Den ändrade tjänsteregeln visas här:

   ![AppVM01 regel][6]

Upprepa processen för att skapa RDP-tjänster för de återstående servrarna: AppVM02 DNS01 och IIS01. Dessa tjänster att reglerna i nästa avsnitt enklare att skapa och tydligare.

> [!NOTE]
> En RDP-tjänst för brandväggen är inte nödvändigt eftersom brandväggen VM är en Linux-baserad avbildning så används SSH på port 22 för hantering av virtuell dator i stället för RDP. Dessutom port 22 och två andra portar är tillåtna för anslutning. Se den **management brandväggsregel** i nästa avsnitt.

### <a name="firewall-rules-creation"></a>Skapa regler för brandväggen

Det finns tre typer av brandväggsregler som används i det här exemplet, alla med olika ikoner:

Omdirigeringsregel program: ![Programikon för omdirigering][7]

Mål NAT-regel: ![Mål NAT-ikon][8]

Pass-regel: ![Skicka ikon][9]

Mer information om dessa regler finns på Barracuda-webbplatsen.

Skapa följande regler eller kontrollera befintliga standardregler:

1. Från instrumentpanelen Barracuda NG Admin-klienten går du till den **configuration** fliken.
1. I den **användningsinställningar** väljer **Ruleset**.
1. Den **Main regler** grid visar det befintliga aktiva och inaktiveras regler på den här brandväggen. Välj gröna **+** i det övre högra hörnet för att skapa en ny regel. Om din brandvägg är låst för ändringar kan du se en knapp som markerats **Lås** och kan inte skapa eller redigera regler. Välj den **Lås** knappen för att låsa upp regeluppsättningen och Tillåt redigering. Högerklicka på en regel som du vill redigera och markera **Redigera regel**.

När du skapar eller ändrar några regler, push-installera dem i brandväggen och aktivera dem. I annat fall gälla regeln inte. Push-meddelanden och aktivering processen är beskrivs i [regel aktivering](#rule-activation).

Här följer information om varje regel som krävs för att slutföra det här exemplet:

* **Hantering av brandväggsregel**: Den här appen omdirigering regeln tillåter trafik skickas till hanteringsportar för en virtuell enhet i nätverket, i det här exemplet en Barracuda NextGen-brandväggen. Management-portarna är 801 807 och du kan också 22. De externa och interna portarna är likadana, ingen portöversättning. Den här regeln kallas konfigurera-MGMT-åtkomst. Det finns en standardregel och aktiverat som standard i Barracuda NextGen Firewall, version 6.1.
  
    ![Brandväggsregel för hantering][10]

  > [!TIP]
  > Käll-adressutrymmet i den här regeln är **alla**. Om hantering av IP-adressintervall är kända, minskar vilket minskar det här omfånget också risken för angrepp till hanterade portar.

* **RDP-regler**:  Dessa mål NAT-regler kan hanteringen av de enskilda servrarna via RDP. Fält som är viktiga för dessa regler är:
  * Källa. Tillåt RDP från var som helst genom att använda referensen **alla** i fältet källa.
  * Tjänsten. Använd RDP-service-objekt som du skapade tidigare: **AppVM01 RDP**. Externa portar omdirigera till serverns lokala IP-adress och till standardporten för RDP 3386. Den här specifika regeln är för RDP-åtkomst till AppVM01.
  * Mål. Använd den lokala porten i brandväggen: **DCHP 1 lokala IP** eller **eth0** om du använder statiska IP-adresser. Ordningstalet (eth0 eth1 och så vidare) kan vara annorlunda om din nätverksapparat har flera lokala gränssnitt. Brandväggen använder den här porten för att skicka och det kan vara samma som den mottagande porten. Faktiska routade målet finns på den **mållistan** fält.
  * Omdirigering. Konfigurera för att berätta var du slutligen omdirigera trafiken för den virtuella installationen. Den enklaste omdirigeringen är att placera den IP-Adressen i fältet mållistan. Du kan också ange porten och NAT ska dras om både porten och IP-adressen. Om du inte anger en port, använder den virtuella installationen målporten för den inkommande begäran.

    ![RDP-brandväggsregel][11]

    Skapa fyra regler för RDP:

    | Regelnamn | Server  | Tjänst | Mållistan |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > Begränsa omfånget för fälten käll- och minskar risken för angrepp. Använd den mest begränsad omfattning som tillåter funktioner.

* **Programmet trafikregler**: Det finns två trafikregler för programmet. En är för klientdelen webbtrafik. Den andra täcker backend-trafik som webbservern till datanivån. Dessa regler är beroende av nätverksarkitektur och trafik flöden.
  
  * Klientdelen regeln för webbtrafik:
  
    ![Webb-brandväggsregel][12]
  
    Det här målet NAT-regeln kan själva programmet trafik når application server. Till skillnad från reglerna för säkerhet, hantering och etc. kan regler för program externa användare eller tjänster att komma åt programmen. Det här exemplet har en enda webbserver på port 80, vilket gör att en enda brandväggsregel program att omdirigera trafik som är avsedda för en extern IP-adress att dirigera i stället till webbserverns interna IP-adressen. Omdirigerad trafik sessionen mappas av NAT för den interna servern.

    > [!NOTE]
    > Ingen port har tilldelats i den **mållistan** fält. Därför används den inkommande porten 80 (eller 443 för tjänsten som valts) i omdirigering av webbservern. Om servern lyssnar på en annan port som 8080 måste uppdatera du fältet mållistan till 10.0.1.4:8080 så att även port omdirigeringen.
  
  * Backend-regeln tillåter webbservern att kommunicera med AppVM01 server, men inte AppVM02 via **alla** service:
  
    ![AppVM01 brandväggsregel][13]
  
    Regeln pass tillåter alla IIS-servrar på klientdelsundernätet att nå AppVM01 (10.0.2.5) på alla portar med hjälp av valfritt protokoll så att data kan nås av webbprogrammet.
  
    I den här skärmbilden `<explicit-dest>` används i den **mål** fält för en obestämd 10.0.2.5 som mål. Du kan ange IP-adress uttryckligen som visas i skärmbilden. Du kan också använda en namngiven nätverksobjekt som i förutsättningarna för DNS-servern. Brandväggen-administratören kan välja vilken metod du vill använda. Om du vill lägga till 10.0.2.5 som en explicit mål, dubbelklickar du på den första tomma raden under `<explicit-dest>` och ange adressen i dialogrutan som öppnas.
  
    Med regeln pass krävs inga NAT eftersom det hanterar intern trafik. Du kan ange den **anslutningsmetoden** till `No SNAT`.
  
    > [!NOTE]
    > Källnätverket i den här regeln är en resurs på klientdelens undernät om det finns bara en. Om din arkitektur anger en känd antalet webbservrar, kan du skapa en nätverksobjekt resurs mer specifikt till de exakta IP-adresserna i stället för hela klientdelsundernätet.

    > [!TIP]
    > Den här regeln använder tjänsten **alla** att göra det enklare att konfigurera och använda exempelprogrammet. Det gör att ICMPv4 (ping) i en enda regel. Men om du vill minska attackytan surface över gränsen, rekommenderar vi att begränsa de portar och protokoll tjänsterna till lägsta möjliga som gör att programmet igen.

    > [!TIP]
    > Även om det här exemplet regeln använder `<explicit-dest>` referens, bör du använda en konsekvent metod under brandväggskonfigurationen av. Vi rekommenderar att du använder en namngiven nätverksobjekt för enklare läsbarheten och support. Den `<explicit-dest>` visas här är bara att visa en alternativ metod. Inte Allmänt rekommenderar vi den, särskilt för komplexa konfigurationer.

* **Regel för utgående till internet**: Regeln pass tillåter trafik från alla källnätverket ska skickas till de valda Målnätverk. Barracuda NextGen-brandväggen har vanligtvis den här regeln ”on” som standard, men i ett inaktiverat tillstånd. Högerklicka på den här regeln för att komma åt den **Aktivera regel** kommando. Ändra regeln visas i skärmbilden för att lägga till nätverksobjekt för serversidan och klientsidan undernät i källattributet för den här regeln. Du har skapat dessa nätverksobjekt i avsnittet förutsättningar i den här artikeln.
  
    ![Utgående brandväggsregel][14]

* **DNS-regel**: Regeln pass kan endast DNS (port 53)-trafik skickas till DNS-servern. För den här miljön blockeras de flesta trafik från klientdelen till serverdelen så att den här regeln kan särskilt DNS-trafik.
  
    ![DNS-brandväggsregel][15]
  
    > [!NOTE]
    > Den **anslutningsmetoden** är inställd på `No SNAT` eftersom den här regeln för interna IP-adress till interna IP-adress-trafik och ingen omdirigering via NAT krävs.

* **Undernät-till-undernät regeln**: Den här Standardregeln pass har aktiverats och ändras för att låta alla servrar i backend-undernät att ansluta till en server på klientdelens undernät. Den här regeln coves endast intern trafik så **anslutningsmetoden** kan anges till `No SNAT`.

    ![Intra-VNet-brandväggsregeln][16]
  
    > [!NOTE]
    > Den **dubbelriktad** kryssrutan markeras inte som den här så att den här regeln gäller bara i en riktning. En anslutning kan initieras från backend-undernät till klientdelens nätverk, men inte tvärtom. Om kryssrutan har valts, skulle den här regeln Aktivera dubbelriktad trafik, som vi har angetts som oönskade i vår logiskt diagram.

* **Neka alla trafikregel**: Den här regeln ska alltid vara den sista regeln när det gäller prioritet. Om flödet i nätverkstrafiken inte matchar någon av de föregående reglerna, gör den här regeln att den tas bort. Regeln är ofta aktiverad som standard så att inga ändringar behövs.
  
    ![Regel för Brandvägg för att neka][17]

> [!IMPORTANT]
> När alla föregående regler skapas, granskar du prioriteten för varje regel för att se till att trafik tillåts eller nekas efter behov. I det här exemplet visas reglerna i den ordning som de ska synas i Barracuda Management-klienten huvudsakliga rutnätet för att vidarebefordra regler.

## <a name="rule-activation"></a>Regel för aktivering

När du har ändrat regeluppsättning som uppfyller specifikationerna för logic-diagram, måste du överföra regeluppsättningen till brandväggen och aktivera den.

![Brandväggen regeln aktivering][18]

Ta det övre högra hörnet i hanteringsfönstret av klienten och välja **skicka ändringar** att överföra de ändrade reglerna i brandväggen. Välj **aktivera**.

När du aktiverar regeluppsättningen brandväggen kan har den här exempelmiljön slutförts.

## <a name="traffic-scenarios"></a>Trafik-scenarier

> [!IMPORTANT]
> Kom ihåg att *alla* trafiken går genom brandväggen. Till fjärrskrivbord till IIS01-servern måste du ansluta till brandväggen på port 8014 och Tillåt brandväggen för att dirigera begäran RDP internt till IIS01 RDP-porten. Azure-portalens **Connect** knappen fungerar inte eftersom det finns ingen direkt RDP-väg till IIS01 som visas på portalen. Alla anslutningar från internet är att tjänsten security och en port (till exempel secscv001.cloudapp.net:xxxx). Referera till ovanstående diagram för matchning av extern port och intern IP och port.

Dessa scenarier kan ska följande brandväggsregler finnas:

1. Brandväggshantering (VB Mgmt)
2. RDP till IIS01
3. RDP till DNS01
4. RDP till AppVM01
5. RDP till AppVM02
6. Trafik på webben
7. Trafik till AppVM01
8. Utgående till internet
9. Klientdel till DNS01
10. Intra-undernätstrafik (serverdel till endast klientdel)
11. Neka alla

Din faktiska brandväggen regeluppsättning kräver mest sannolikt fler regler än de i det här exemplet. De sannolikt kommer att ha olika prioritetsnummer. Du bör använda den här listan och tillhörande nummer för deras relativa prioriteten till varandra. Till exempel ”RDP till IIS01” regeln kan vara numret 5 på faktiska brandväggen, men så länge som den nedan ”Brandväggshantering” regel och ovanför den ”RDP till DNS01” regel, uppsättningen justerar avsikt att den här listan. Den här listan kan även förenkla anvisningar för scenarier som följer. Till exempel ”brandväggsregel 9 (DNS)”. Tänk på att de fyra RDP-reglerna kallas ”RDP-regler” när trafik scenariot är inte relaterat till RDP.

Kom också ihåg att nätverkssäkerhetsgrupper (NSG) finns på plats för inkommande Internettrafik på frontend och backend-undernät.

### <a name="allowed-internet-to-web-server"></a>(Tillåts) Internet till webbservern

1. En internet-användare begär HTTP sida från SecSvc001.CloudApp.Net (internet-riktade cloud Services).
1. Molntjänsten skickar trafik via en öppen slutpunkt på port 80 till brandväggen-gränssnittet på 10.0.0.4:80.
1. Inga Nätverkssäkerhetsgrupper är tilldelad till säkerhet undernät så att systemet NSG-reglerna tillåter trafik i brandväggen.
1. Trafiken kommer till en intern IP-adress för brandvägg (is 10.0.1.4).
1. Brandväggen utför regelbearbetningen:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 2 – 5 (RDP-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 6 (App: Web) gäller. Trafiken är tillåtet. Brandväggen ska dras om trafik via NAT till is 10.0.1.4 (IIS01).
1. Klientdelsundernätet utför bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera internet) kan inte användas. Brandväggen dirigeras om den här trafiken via NAT så källadressen är nu i brandväggen. Eftersom brandväggen i säkerhet-undernätet och visas som lokal trafik till klientdelsundernätet NSG, tillåts trafik. Flytta till nästa regel.
   1. Standardreglerna för NSG tillåter trafik för undernät-till-undernät så kan den här trafiken. Stoppa NSG-Regelbearbetning.
1. IIS01 lyssnar för webbtrafik. Den tar emot den här förfrågan och startar bearbetning av begäran.
1. IIS01 försöker initiera en FTP-sessionen till AppVM01 i backend-undernät.
1. UDR-väg på klientdelsundernätet gör brandväggen nästa hopp.
1. Det finns inga regler för utgående trafik på klientdelsundernätet så att trafiken tillåts.
1. Brandväggen börjar Regelbearbetning:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 2 – 5 (RDP-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 6 (App: Web) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 7 (App: serverdel) gäller. Trafiken är tillåtet. Brandväggen vidarebefordrar trafik till 10.0.2.5 (AppVM01).
1. Backend-undernät utför bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera internet) kan inte användas. Flytta till nästa regel.
    1. Standardreglerna för NSG tillåter trafik för undernät-till-undernät. Trafiken är tillåtet. Stoppa NSG-Regelbearbetning.
1. AppVM01 tar emot begäran, initierar sessionen och svarar.
1. Den användardefinierade väg vägen i backend-undernät gör brandväggen nästa hopp.
1. Det finns inga utgående NSG-regler i backend-undernät så svaret tillåts.
1. Eftersom det ger trafik på en upprättad session, skickar svaret tillbaka till webbservern (IIS01) i brandväggen.
1. Klientdelsundernätet utför bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera internet) kan inte användas. Flytta till nästa regel.
    1. Standardreglerna för NSG tillåter trafik för undernät-till-undernät så kan den här trafiken. Stoppa NSG-Regelbearbetning.
1. IIS-servern tar emot svaret och Slutför transaktionen med AppVM01. Sedan servern är klar att skapa HTTP-svar och skickar det till begäranden.
1. Det finns inga utgående NSG-regler på klientdelsundernätet så svaret tillåts.
1. HTTP-svaret når brandväggen. Eftersom det är ett svar på en upprättad NAT-session, godkänner den brandväggen.
1. Brandväggen omdirigerar svaret tillbaka till internet-användare.
1. Det finns inga utgående NSG-regler eller UDR hopp på klientdelsundernätet så svaret tillåts. Internet-användare får sidan begärs.

### <a name="allowed-internet-rdp-to-back-end"></a>(Tillåts) Internet RDP till serversidan

1. En serveradministratör på internet begär en RDP-session till AppVM01 via SecSvc001.CloudApp.Net:8025. 8025 är användartilldelade portnumret för brandväggsregel 4 (RDP AppVM01).
1. Molntjänsten skickar trafik via den öppna slutpunkten på port 8025 till brandväggens gränssnitt på 10.0.0.4:8025.
1. Inga Nätverkssäkerhetsgrupper är tilldelad till säkerhet undernät så att systemet NSG-regler som tillåter trafik i brandväggen.
1. Brandväggen utför regelbearbetningen:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 2 (RDP IIS) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 3 (RDP DNS01) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 4 (RDP AppVM01) gäller så att trafik tillåts. Brandväggen ska dras om det via NAT till 10.0.2.5:3386 (RDP-porten på AppVM01).
1. Backend-undernät utför bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera internet) kan inte användas. Brandväggen dirigeras om den här trafiken via NAT så källadressen är nu i brandväggen som Security-undernätet. Den visas för serverdelsundernätet NSG vara lokal trafik och tillåts. Flytta till nästa regel.
   1. Standardreglerna för NSG tillåter trafik för undernät-till-undernät så kan den här trafiken. Stoppa NSG-Regelbearbetning.
1. AppVM01 lyssnar efter RDP-trafik och svarar.
1. Det finns inga utgående NSG-regler så standardregler gäller. Returtrafiken tillåts.
1. UDR dirigerar trafik i brandväggen som nästa hopp.
1. Eftersom det ger trafik på en upprättad session, skickar svaret tillbaka till internet-användare i brandväggen.
1. RDP-session är aktiverat.
1. AppVM01 frågar efter användarnamn lösenord.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tillåts) Web server DNS-sökning på DNS-server

1. Webbservern IIS01 begär en datafeed HTTP-\:\/\/www.data.gov, men måste matcha adressen.
1. Nätverkskonfigurationen för virtuellt nätverk listor DNS01 (10.0.2.4 i backend-undernät) som den primära DNS-servern. IIS01 skickar en DNS-begäran till DNS01.
1. UDR dirigerar trafik i brandväggen som nästa hopp.
1. Ingen utgående NSG-regler är bundna till klientdelsundernätet. Trafiken är tillåtet.
1. Brandväggen för standardåtgärder Regelbearbetning:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 2 – 5 (RDP-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 6 och 7 (regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregeln 8 (internet) kan inte användas. Flytta till nästa regel.
   1. Brandväggsregel 9 (DNS) gäller. Trafiken är tillåtet. Brandväggen vidarebefordrar trafik till 10.0.2.4 (DNS01).
1. Backend-undernät utför bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera internet) kan inte användas. Flytta till nästa regel.
   1. Standardreglerna för NSG tillåter trafik för undernät-till-undernät. Trafiken är tillåtet. Stoppa NSG-Regelbearbetning.
1. DNS-servern tar emot begäran.
1. DNS-servern har inte den adress som cachelagras och frågar en rot-DNS-server på internet.
1. UDR dirigerar utgående trafik i brandväggen som nästa hopp.
1. Det finns inga utgående NSG-regler i backend-undernät på trafik tillåts.
1. Brandväggen för standardåtgärder Regelbearbetning:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 2 – 5 (RDP-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 6 och 7 (regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregeln 8 (internet) gäller. Trafiken är tillåtet. Sessionen dras via SNAT till rot-DNS-servern på internet.
1. Internet-DNS-servern svarar. Den här sessionen initierades från brandväggen så att svaret accepteras av brandväggen.
1. Den här sessionen redan så brandväggen vidarebefordrar svar till den ursprungliga servern, DNS01.
1. Backend-undernät utför bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera internet) kan inte användas. Flytta till nästa regel.
    1. Standardreglerna för NSG tillåter undernät-till-undernät trafik till den här trafiken är tillåtet. Stoppa NSG-Regelbearbetning.
1. DNS-servern tar emot och cachelagrar svaret och sedan svarar på den första begäran tillbaka till IIS01.
1. Den användardefinierade väg vägen i backend-undernät gör brandväggen nästa hopp.
1. Ingen utgående NSG-regler finns på backend-undernät så att trafiken tillåts.
1. Den här sessionen redan i brandväggen så att brandväggen ska dras om svaret tillbaka till IIS-servern.
1. Klientdelsundernätet utför bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel för inkommande trafik från backend-undernät till klientdelsundernätet så att ingen av NSG-reglerna gäller.
    1. Systemregel standard tillåter trafik mellan undernät. Trafiken är tillåtet.
1. IIS01 tar emot svaret från DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Tillåts) Backend-servern till front-end-server

1. En administratör som har loggat in på AppVM02 via RDP begär en fil direkt från IIS01 servern via Utforskaren i windows.
1. Den användardefinierade väg vägen i backend-undernät gör brandväggen nästa hopp.
1. Det finns inga utgående NSG-regler i backend-undernät så svaret tillåts.
1. Brandväggen utför regelbearbetningen:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 2 – 5 (RDP-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 6 och 7 (regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregeln 8 (internet) kan inte användas. Flytta till nästa regel.
   1. Brandväggsregel 9 (DNS) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 10 (Intra-undernät) gäller. Trafiken är tillåtet. Brandväggen skickar trafik till is 10.0.1.4 (IIS01).
1. Klientdelsundernätet börjar bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera internet) inte tillämpa, gå till nästa regel
   1. Standardreglerna för NSG tillåter trafik för undernät-till-undernät så att trafiken tillåts. Stoppa NSG-Regelbearbetning.
1. Under förutsättning att korrekt autentisering och auktorisering, IIS01 tar emot begäran och svarar.
1. UDR-väg på klientdelsundernätet gör brandväggen nästa hopp.
1. Det finns inga utgående NSG-regler på klientdelsundernätet så svaret tillåts.
1. Den här sessionen redan finns i brandväggen så att det här svaret tillåts. Brandväggen returnerar svaret till AppVM02.
1. Backend-undernät utför bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera internet) kan inte användas. Flytta till nästa regel.
    2. Standardreglerna för NSG tillåter trafik för undernät-till-undernät så att trafiken tillåts. Stoppa NSG-Regelbearbetning.
1. AppVM02 tar emot svaret.

### <a name="denied-internet-direct-to-web-server"></a>(Nekad) Internet direkt till webbservern

1. En internet-användare försöker få åtkomst till webbservern IIS01 via tjänsten FrontEnd001.CloudApp.Net.
1. Det finns inga slutpunkter öppna för HTTP-trafik så att den här trafiken inte passera Molntjänsten. Trafiken når inte servern.
1. Om slutpunkterna är öppna av någon anledning, blockerar NSG (blockera internet) på klientdelsundernätet den här trafiken.
1. Slutligen skickar klientdelsundernätet UDR dirigera all utgående trafik från IIS01 i brandväggen som nästa hopp. Brandväggen ser det som asymmetrisk trafik och släpper det utgående svaret.

>Det finns därför minst tre oberoende försvarslinjer mellan internet och IIS01. Molntjänsten hindrar obehöriga eller olämplig åtkomst.

### <a name="denied-internet-to-back-end-server"></a>(Nekad) Internet till backend-server

1. En internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net.
2. Det finns inga slutpunkter som är öppna för fildelning, så den här begäran inte skicka Molntjänsten. Trafiken når inte servern.
3. Om slutpunkterna är öppna av någon anledning, blockerar NSG (blockera internet) den här trafiken.
4. Slutligen skickar UDR dirigera all utgående trafik från AppVM01 i brandväggen som nästa hopp. Brandväggen ser det som asymmetrisk trafik och släpper det utgående svaret.

> Det finns därför minst tre oberoende försvarslinjer mellan internet och AppVM01. Molntjänsten hindrar obehöriga eller olämplig åtkomst.

### <a name="denied-front-end-server-to-back-end-server"></a>(Nekad) Front-end-server till backend-server

1. IIS01 komprometteras och kör skadlig kod försöker skannerservrar backend-undernät.
1. Klientdelsundernätet UDR vägen skickar all utgående trafik från IIS01 i brandväggen som nästa hopp. Den komprometterade virtuella datorn kan inte ändra den här routning.
1. Brandväggen bearbetar trafiken. Om begäran är AppVM01 eller DNS-server för DNS-sökning, kan brandväggen potentiellt tillåta trafik på grund av brandväggsregler 7 och 9. All annan trafik blockeras av brandväggsregel 11 (neka alla).
1. Om du aktiverar avancerad hotidentifiering i brandväggen kan trafik som innehåller signaturer för kända eller mönster som flaggar en regel för Avancerat förhindras. Det här måttet fungerar även om trafiken är tillåten enligt de grundläggande vidarebefordringsregler som beskrivs i den här artikeln. Avancerad hotidentifiering som inte omfattas i det här dokumentet. Se i leverantörens dokumentation om din specifika nätverksinstallation advanced threat funktioner.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Nekad) Internet-DNS-sökning på DNS-server

1. En internet-användare försöker att leta upp en intern DNS-post på DNS01 genom BackEnd001.CloudApp.Net-tjänsten.
1. Eftersom det finns inga slutpunkter som är öppen för DNS-trafik är passerar inte den här trafiken Molntjänsten. Det inte att nå servern.
1. Om slutpunkterna är öppna av någon anledning, blockerar NSG-regel (blockera internet) på klientdelsundernätet den här trafiken.
1. Slutligen skickar serverdelsundernätet UDR dirigera all utgående trafik från DNS01 i brandväggen som nästa hopp. Brandväggen ser detta som asymmetrisk trafik och släpper det utgående svaret.

> Det finns därför minst tre oberoende försvarslinjer mellan internet och DNS01. Molntjänsten hindrar obehöriga eller olämplig åtkomst.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Nekad) Internet för att SQL-åtkomst via brandväggen

1. En internet-användare begär SQL-data från Molntjänsten SecSvc001.CloudApp.Net mot internet.
1. Det finns inga slutpunkter öppen för SQL så att den här trafiken inte skicka Molntjänsten. Det inte att nå brandväggen.
1. Om SQL-slutpunkter är öppna av någon anledning, utför brandväggen Regelbearbetning:
   1. Brandväggsregel 1 (VB Mgmt) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 2 – 5 (RDP-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregler 6 och 7 (program-regler) gäller inte. Flytta till nästa regel.
   1. Brandväggsregeln 8 (internet) kan inte användas. Flytta till nästa regel.
   1. Brandväggsregel 9 (DNS) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 10 (Intra-undernät) gäller inte. Flytta till nästa regel.
   1. Brandväggsregel 11 (neka alla) gäller. Trafik blockeras. Stoppa regelbearbetningen.

## <a name="references"></a>Referenser

Det här avsnittet innehåller följande objekt:

* Fullständigt skript. Spara den i en PowerShell-skriptfilen.
* Konfiguration av nätverk. Spara den i en fil med namnet NetworkConf2.xml.

Ändra de användardefinierade variablerna i filer som behövs. Kör skriptet och följ sedan instruktionerna för brandväggen regel anges tidigare i den här artikeln.

### <a name="full-script"></a>Fullständigt skript

Kör det här skriptet till när du har angett de användardefinierade variablerna:

1. Ansluta till en Azure-prenumeration
1. Skapa ett nytt lagringskonto
1. Skapa ett nytt virtuellt nätverk och tre undernät som definierats i konfigurationsfilen för nätverk
1. Skapa fem virtuella datorer: en brandvägg och fyra Windows Server-datorer
1. Konfigurera UDR:
   1. Skapa två nya routningstabeller
   1. Lägga till vägar till tabellerna
   1. Binda tabeller till lämpliga undernät
1. Aktivera IP-vidarebefordring på NVA
1. Konfigurera NSG:
   1. Skapa en NSG
   1. Lägg till en regel
   1. Binda NSG: N till lämpliga undernät

Kör det här PowerShell ansluten-skript lokalt på en internet dator eller server.

> [!IMPORTANT]
> När du kör det här skriptet, kan varningar och andra informationsmeddelanden visas i PowerShell. Endast red felmeddelanden är orsaka problem.

```powershell
    <#
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Konfigurationsfilen för nätverk

Spara den här XML-filen med uppdaterade plats. Ändra den `$NetworkConfigFile` variabel i fullständiga skriptet ovan att länka till konfigurationsfilen sparade nätverk.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

## <a name="next-steps"></a>Nästa steg

Du kan installera ett exempelprogram som hjälper med det här exemplet för perimeter-nätverk.

> [!div class="nextstepaction"]
> [Exempelskript för program](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Dubbelriktat perimeternätverk med NVA, NSG och UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logisk vy för brandväggsreglerna"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Skapa ett nätverk på klientsidan-objekt"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Skapa ett DNS-Server-objekt"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopia av standard RDP-regel"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 Rule"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Programikon för omdirigering"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Mål NAT-ikon"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Skicka ikon"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Brandväggsregel för hantering"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "RDP-brandväggsregel"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Webb-brandväggsregel"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "AppVM01 brandväggsregel"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Utgående brandväggsregel"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "DNS-brandväggsregel"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Intra-VNet-brandväggsregeln"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regel för Brandvägg för att neka"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Brandväggen regeln aktivering"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
