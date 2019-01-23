---
title: Perimeternätverksexempel – skapa ett perimeternätverk för att skydda nätverk med en brandvägg, UDR och NSG | Microsoft Docs
description: Bygg en DMZ med brandvägg, användardefinierad routning (UDR) och Nätverkssäkerhetsgrupper (NSG)
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
ms.openlocfilehash: 36d6733ddc73ace2026ea838cf8f701db95469e6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448474"
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exempel 3 – skapa ett perimeternätverk för att skydda nätverk med en brandvägg, UDR och NSG
[Gå tillbaka till gränsen bästa praxis sidan][HOME]

Det här exemplet skapar en DMZ med en brandvägg, fyra windows-servrar, användaren definierats routning, IP-vidarebefordring och Nätverkssäkerhetsgrupper. Den visar även hur var och en av relevanta kommandon för att ge en bättre förståelse för varje steg. Det finns också ett avsnitt för trafik scenariot att ge en djupgående steg för steg hur trafik fortsätter genom försvarslinjer i Perimeternätverket. Slutligen är avsnittet i referenserna den fullständiga koden och anvisningarna för att skapa den här miljön för att testa och experimentera med olika scenarier. 

![Dubbelriktat perimeternätverk med NVA, NSG och UDR][1]

## <a name="environment-setup"></a>Konfigurera miljön
I det här exemplet finns det en prenumeration som innehåller följande:

* Tre molntjänster: ”SecSvc001”, ”FrontEnd001” och ”BackEnd001”
* Ett virtuellt nätverk ”CorpNetwork” med tre undernät: ”SecNet”, ”FrontEnd” och ”serverdel”
* En virtuell nätverksinstallation, i det här exemplet en brandvägg, som är anslutna till undernätet för SecNet
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar programmet tillbaka sluta servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

I referensavsnittet nedan finns ett PowerShell-skript som skapar de flesta av miljön som beskrivs ovan. Att skapa virtuella datorer och virtuella nätverk, även om utförs av exempelskript, som inte beskrivs i detalj i det här dokumentet.

Att skapa miljön:

1. Spara nätverk XML-konfigurationsfilen i referensavsnittet (uppdateras med namn, plats och IP-adresser för att matcha det aktuella scenariot)
2. Uppdatera Användarvariabler i skriptet så att den matchar den miljö som skriptet ska köras mot (prenumerationer, tjänstnamn osv.)
3. Kör skriptet i PowerShell

**Obs!** Den region som visas i PowerShell-skriptet måste matcha den region som visas i nätverket XML-konfigurationsfilen.

När skriptet har körts kan följande efterskript steg vidtas:

1. Konfigurera brandväggsreglerna beskrivs detta i avsnittet nedan: Beskrivning av brandväggen.
2. Du kan också är i referensavsnittet två skript för att konfigurera webbserver- och app-servern med en enkel webbapp för att testa med den här perimeternätverkskonfigurationen.

När skriptet har körts brandväggen regler måste slutföras beskrivs detta i avsnittet: Brandväggsregler.

## <a name="user-defined-routing-udr"></a>Användardefinierad routning (UDR)
Som standard definieras följande systemvägar som:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal är alltid definierad adress-prefix för det virtuella nätverket för den specifika nätverk (d.v.s. den kommer att ändras från VNet till VNet beroende på hur varje specifik virtuellt nätverk har definierats). Återstående systemvägar är statiska och standard som ovan.

För prioritet, vägar bearbetas via metoden längsta Prefix-matchning (LPM), därför den mest specifika vägen i tabellen skulle gälla för en viss måladress.

Därför dirigeras trafik (till exempel till DNS01-servern, 10.0.2.4) som är avsedd för det lokala nätverket (10.0.0.0/16) över det virtuella nätverket till dess mål på grund av 10.0.0.0/16 vägen. Med andra ord för 10.0.2.4, 10.0.0.0/16 vägen är den mest specifika vägen, även om 10.0.0.0/8 och 0.0.0.0/0 kunde gäller även, men eftersom de är mindre specifika de påverkar inte den här trafiken. Trafiken till 10.0.2.4 skulle därför ha ett nexthop för det lokala virtuella nätverket och helt enkelt dirigera till mål.

Om trafik är avsedd för 10.1.1.1 till exempel, 10.0.0.0/16 vägen inte tillämpa, men 10.0.0.0/8 är den mest specifika och trafiken skulle detta bort (”black holed”) eftersom nästa hopp är Null. 

Om målet inte gäller för någon av Null-prefix eller VNETLocal-prefix kommer det följer den minst specifika dirigera 0.0.0.0/0 och skickas till Internet som nästa hopp och därmed ut Azures internet-anslutning.

Om det finns två identiska prefix i routningstabellen, följer efter prioritet baserat på vägar ”källa”-attribut:

1. ”VirtualAppliance” = en definierade väg läggs till manuellt i tabellen
2. ”VPNGateway” = en dynamisk väg (BGP när det används med hybrid-nätverk), läggs till av en dynamisk nätverksprotokoll, dessa vägar kan ändras med tiden när protokollet dynamisk visar automatiskt ändringar i peer-kopplade nätverket
3. ”Standard” = Systemvägarna, det lokala virtuella nätverket och statiska poster som visas i routningstabellen ovan.

> [!NOTE]
> Du kan nu använda användardefinierad routning (UDR) med ExpressRoute och VPN-gatewayer att tvinga utgående och inkommande mellan lokala trafiken ska dirigeras till en virtuell nätverksinstallation (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>Skapa lokala vägar
I det här exemplet krävs två routningstabeller, en för Frontend och Backend-undernät. Varje tabell har lästs in med statiska vägar som är lämpliga för det angivna undernätet. Varje tabell har tre vägar i det här exemplet:

1. Lokal undernätstrafik med inga nästa hopp som definierats för att tillåta lokal undernätstrafik passera brandväggen
2. Trafik i virtuella nätverk med en nästa hopp har definierats som brandvägg, åsidosätts Standardregeln som tillåter lokal VNet-trafik för dirigering direkt
3. Alla återstående trafik (0/0) med en nästa hopp har definierats som brandväggen

När routningstabeller skapas är de bundna till sina undernät. För undernätet på klientsidan routningstabell, skapas en gång och bunden till undernätet bör se ut så här:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


I det här exemplet används följande kommandon för att skapa routningstabellen, lägga till en användardefinierad väg och Binder routningstabellen till ett undernät (Obs!; eventuella objekt nedan som börjar med ett dollartecken (t.ex.: $BESubnet) är användardefinierade variabler i skriptet i den referensavsnittet i det här dokumentet):

1. Först måste du skapa grundläggande routningstabellen. Det här kodfragmentet visar tabellen för Backend-undernät. I skriptet skapas också en motsvarande tabell för undernätet på klientsidan.
   
     Ny AzureRouteTable-namnge $BERouteTableName '
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. När routningstabellen har skapats kan specifikt användardefinierade vägar läggas till. I det här liten, kommer all trafik (0.0.0.0/0) att dirigeras via den virtuella installationen (en variabel, $VMIP [0] används för att skicka in IP-adressen som tilldelas när den virtuella installationen har skapats tidigare i skriptet). I skriptet skapas också en motsvarande regel i Frontend-tabellen.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. Posten ovan vägen åsidosätter ”0.0.0.0/0” standardvägen, men 10.0.0.0/16 Standardregeln fortfarande befintliga som skulle tillåta trafik inom det virtuella nätverket kan dirigera direkt till målet och inte till den virtuella nätverksinstallationen. Till rätt problemet Följ regeln läggas.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. Det finns nu ett val görs. Med ovanstående två vägar dirigerar all trafik till brandväggen för utvärdering, även trafik i ett enda undernät. Detta kan det vara önskvärt, men för att tillåta trafik i ett undernät för att dirigera lokalt utan inblandning av brandväggen tredje mycket specifik regel kan läggas till. Den här vägen för aviseringar som alla adresser destine för det lokala undernätet behöver bara vidarebefordra det direkt (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Slutligen med routningstabellen skapas och konfigureras med en användardefinierad routning, måste tabellen nu vara bundet till ett undernät. I skriptet routningstabellen klientdelen också är bundet till undernätet på klientsidan. Här är skriptet bindning för backend-undernät.
   
     Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP-vidarebefordran
En tillhörande funktion till UDR, är IP-vidarebefordran. Det här är en inställning på en virtuell installation som kan det ta emot trafik som inte är adresserad till installationen och sedan vidarebefordra trafiken till destinationen ultimate.

Till exempel om trafik från AppVM01 gör en begäran till servern DNS01 skulle UDR vidarebefordra detta i brandväggen. Med IP-vidarebefordring aktiverad, att trafik för DNS01 mål (10.0.2.4) accepteras av installationen (10.0.0.4) och sedan vidarebefordras till dess ultimate mål (10.0.2.4). Utan IP-vidarebefordring aktiverad i brandväggen, skulle trafik inte accepteras av installationen trots routningstabellen har brandväggen som nästa hopp. 

> [!IMPORTANT]
> Det är viktigt att komma ihåg att aktivera IP-vidarebefordran tillsammans med användaren definierats routning.
> 
> 

Konfigurera IP-vidarebefordran är ett enda kommando och kan göras vid tidpunkten för skapandet av virtuell dator. Kodfragmentet är mot slutet av skriptet och grupperas med UDR-kommandon för flödet av det här exemplet:

1. Anropa den VM-instans som är din virtuella installation brandväggen i det här fallet och aktivera IP-vidarebefordran (Obs!; ett objekt i rött som börjar med ett dollartecken (t.ex.: $VMName[0]) är en användardefinierad variabel från skriptet i referensavsnittet i det här dokumentet. Nollbaserade omges av hakparenteser [0] representerar den första virtuella datorn i matrisen med virtuella datorer för exempelskript för att fungera utan modifiering, den första virtuella datorn (VM 0) måste vara brandväggen):
   
     Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan läsa in med en enda regel. Den här gruppen binds sedan endast till de Frontend och Backend-undernät (inte SecNet). Deklarativt följande regel håller på att skapas:

1. All trafik (alla portar) från Internet till det hela virtuella nätverket (alla undernät) nekas

Även om NSG: er som används i det här exemplet, är dess huvudsakliga syfte som en sekundär försvarslinje mot manuell felkonfiguration. Vi vill blockera alla inkommande trafik från internet till antingen klientdelen eller serverdelen undernät, trafik endast ska flöda via SecNet undernätet i brandväggen (och sedan om lämpliga in på klientdelen eller serverdelen undernät). Dessutom med UDR-regler på plats, skulle all trafik som gjorde det i klientdelen eller serverdelen undernät omdirigerad ut till brandväggen (tack vare UDR). Brandväggen visas följande som en asymmetrisk flöde och skulle släpp den utgående trafiken. Det finns därför tre lager av säkerhet som skyddar Frontend och Backend-undernät; (1) inga öppna slutpunkter på FrontEnd001 och BackEnd001 molntjänster, 2) NSG: er nekar trafik från Internet, 3) brandväggen släpper asymmetrisk trafiken.

En intressant avseende Nätverkssäkerhetsgruppen i det här exemplet är att den innehåller endast en regel som visas nedan, vilket är att neka internet-trafik till det hela virtuella nätverket som skulle inkludera Security-undernätet. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Men eftersom NSG: N är bara kopplat till Frontend och Backend-undernät, regeln inte bearbetas på trafik inkommande till undernätet för säkerhet. Även om NSG-regel säger utan Internet-trafik till en adress på det virtuella nätverket, eftersom NSG: N har aldrig kopplad till undernätet för säkerhet, därför flödar trafik till undernätet för säkerhet.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Brandväggsregler
I brandväggen måste vidarebefordra regler skapas. Eftersom brandväggen blockerar eller vidarebefordran alla inkommande, utgående och intra-VNet-trafik krävs många brandväggsregler. Dessutom överskrids all inkommande trafik säkerhetstjänst offentliga IP-adress (på olika portar), för att bearbetas av brandväggen. Ett bra tips är att diagram logiska flöden innan du konfigurerar undernäten och brandväggsregler för att undvika att omarbeta senare. Följande bild är en logisk vy för brandväggsregler i det här exemplet:

![Logisk vy för brandväggsreglerna][2]

> [!NOTE]
> Baserat på den virtuella nätverksinstallationen som används kan varierar hanteringsportar. I det här exemplet refererar till en Barracuda NextGen Firewall som använder port 22, 801 och 807. I dokumentationen för installation-leverantör för att hitta exakt portarna som används för hantering av enhet som används.
> 
> 

### <a name="logical-rule-description"></a>Beskrivning av logisk regel
I den logiska diagrammet ovan visas inte security undernätet eftersom den är den enda resursen i undernätet och det här diagrammet visar brandväggsreglerna och hur de logiskt tillåter eller nekar trafikflöden och inte den faktiska routade sökvägen. Dessutom externa portar som valts för RDP-trafik är högre intervallet portar (8014 – 8026) och har valts för något överensstämmer med de två sista oktetterna i den lokala IP-adressen för enklare läsbarheten (t.ex. lokala serveradress is 10.0.1.4 är associerad med extern port 8014), men alla högre icke motstridiga portar kan användas.

I det här exemplet behöver vi 7 typer av regler, dessa regeltyper beskrivs på följande sätt:

* Externa regler (för inkommande trafik):
  1. Brandväggsregel för hantering: Den här appen omdirigeringsregel tillåter trafik skickas till hanteringsportar på den virtuella nätverksinstallationen.
  2. RDP-regler (för varje windows-server): Dessa fyra regler (en för varje server) gör att hanteringen av de enskilda servrarna via RDP. Detta kan också använda paketeras i en regel beroende på den virtuella nätverksinstallationen som används.
  3. Regler för trafik för program: Det finns två program trafikregler, först för webbtrafik klientdelen och andra för backend-server-trafik (t.ex webbservern till datanivå). Konfigurationen av dessa regler kommer beror på nätverksarkitekturen (där dina servrar placeras) och trafik flöden (vilken riktning trafiken flödar och vilka portar används).
     * Den första regeln kan själva programmet trafiken till programservern. Medan de andra reglerna tillåter för säkerhet, hantering, o.s.v., är program-regler vad som ska tillåta externa användare eller tjänster att komma åt programmen. Det finns en webbserver på port 80 i det här exemplet, därför ett program för en enda brandväggsregel omdirigerar inkommande trafik till den externa IP-Adressen, web servrar interna IP-adress. Omdirigerad trafik sessionen skulle NAT tas till den interna servern.
     * Regel för nätverkstrafik av andra program är backend-regel som tillåter webbservern att kommunicera med AppVM01 server (men inte AppVM02) via någon annan port.
* Interna regler (för intra-VNet-trafik)
  1. Utgående till Internetregel: Den här regeln ska tillåta trafik från alla nätverk ska skickas till de valda nätverken. Den här regeln är vanligtvis en standardregel redan i brandväggen, men i ett inaktiverat tillstånd. Den här regeln ska aktiveras för det här exemplet.
  2. DNS-regel: Den här regeln tillåter endast DNS (port 53)-trafik skickas till DNS-servern. För den här miljön som de flesta trafik från klientdelen till serverdelen blockeras, kan den här regeln särskilt DNS från alla lokala undernätet.
  3. Undernätet till undernätet regel: Den här regeln är att låta alla servrar i serverdelen undernät att ansluta till en server på klientdelens undernät (men inte tvärtom).
* Felsäker regel (för trafik som inte uppfyller något av ovanstående):
  1. Deny All Traffic Rule: Detta bör alltid vara den sista regeln (när det gäller prioritet) och därför om en trafiken flödar inte matchar någon av de föregående regler som den kommer att tas bort av den här regeln. Det här är en standardregel och vanligtvis aktiverad behövs vanligtvis inga ändringar.

> [!TIP]
> Alla portar tillåts för enkelt för det här exemplet i ett scenario med verkliga den mest specifika porten på den andra program trafik regeln och adressintervall som ska användas för att minska risken för angrepp på den här regeln.
> 
> 

<br />

> [!IMPORTANT]
> När alla ovanstående regler skapas, är det viktigt att granska prioriteten för varje regel för att se till att trafik ska tillåtas eller nekas efter behov. I det här exemplet är reglerna i prioritetsordning. Det är enkelt att vara utelåst från brandväggen på grund av felaktigt sorterad regler. Kontrollera hanteringen för brandväggen själva är alltid den absoluta högsta prioritet regeln som ett minimum.
> 
> 

### <a name="rule-prerequisites"></a>Regel för krav
En förutsättning för den virtuella datorn körs i brandväggen är offentliga slutpunkter. Offentliga slutpunkter måste vara öppna för brandväggen för att bearbeta trafik. Det finns tre typer av trafik i det här exemplet; (1) hanteringstrafik kontroll brandväggen och brandväggsregler, 2) RDP-trafik för att styra windows-servrar och 3) programtrafik. Det här är tre kolumner i trafiktyper i övre hälften av logisk vy för brandväggsregler ovan.

> [!IMPORTANT]
> En viktig takeway är att komma ihåg att **alla** kommer trafik genom brandväggen. Så till fjärrskrivbord till IIS01-servern, ska även om det är i Front End-Molntjänsten och på frontend-undernätet för att komma åt den här servern vi behöva RDP i brandväggen på port 8014, och låt brandväggen för att dirigera begäran RDP internt till RDP-Por IIS01 t. Azure portal ”Anslut” knappen fungerar inte eftersom det finns ingen direkt RDP-väg till IIS01 (så länge portalen kan se). Det innebär att alla anslutningar från internet kommer att tjänsten Security och en Port, t.ex. secscv001.cloudapp.net:xxxx (referens i ovanstående diagram för mappningen för extern Port och intern IP-adress och Port).
> 
> 

En slutpunkt kan öppnas antingen vid tidpunkten för skapandet av VM eller anslår versionen som är klar i exempelskriptet och nedan i det här kodfragmentet (Obs!; alla objekt som börjar med ett dollartecken (t.ex.: $VMName[$i]) är en användardefinierad variabel från skriptet i referens AVS n i det här dokumentet. ”$I” i hakparenteserna, [$i] representerar matris-numret för en specifik virtuell dator i en matris med virtuella datorer):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Även om inte tydligt visas här på grund av användningen av variabler, men slutpunkter är **endast** öppnas på Molntjänsten säkerhet. Detta är att se till att all inkommande trafik hanteras (dirigeras, NAT hade, släppa) av brandväggen.

En management-klienten måste installeras på en dator som kan hantera brandväggen och skapa de konfigurationer som krävs. Se dokumentationen från din brandvägg (eller andra NVA)-leverantören om hur du hanterar enheten. Resten av det här avsnittet och i nästa avsnitt, brandvägg regler skapas, beskriver konfigurationen av brandväggen, via leverantörer management-klienten (dvs. inte Azure-portalen eller PowerShell).

Instruktioner för att klienten ska ladda ned och ansluter till Barracuda som används i det här exemplet finns här: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

När du har loggat till brandväggen men innan du skapar brandväggsregler, finns det två nödvändiga objektklasser som kan göra att man skapar reglerna enklare; Nätverks- och objekt.

I det här exemplet ska tre namngivna nätverksobjekt vara definierade (ett för undernätet på klientsidan och Backend-undernät, även ett nätverksobjekt för IP-adressen för DNS-servern). Du skapar ett namngivna nätverk; från och med Barracuda NG Admin klient-instrumentpanelen, navigerar du till konfigurationsfliken, i konfigurationsavsnittet Operational RuleSet-metod, sedan klickar du på ”nätverk” under menyn brandväggen objekt Klicka på New på menyn Redigera nätverk. Nätverksobjektet kan nu skapas genom att lägga till namnet och prefixet:

![Skapa ett nätverk på klientsidan-objekt][3]

Detta skapar ett namngivna nätverk för undernätet på klientsidan, ett liknande objekt ska skapas för BackEnd-undernät. Nu kan undernäten refereras enklare efter namn i brandväggsreglerna.

För DNS-Server-objekt:

![Skapa ett DNS-Server-objekt][4]

Referens för den här IP-adress ska användas i en DNS-regel senare i dokumentet.

Andra nödvändiga objekten är Services-objekt. Dessa representerar RDP anslutningsportar för varje server. Eftersom det befintliga objektet i RDP-tjänsten är bunden till standard-RDP-porten kan 3389, nya tjänster skapas för att tillåta trafik från externa portar (8014 8026). De nya portarna kan också läggas till den befintliga RDP-tjänsten, men för att underlätta demonstration, kan du skapa en regel för varje server. Skapa en ny regel för RDP för en server. från och med Barracuda NG Admin klient-instrumentpanelen, navigerar du till konfigurationsfliken, i avsnittet användningsinställningar Klicka på regeluppsättning, ”tjänster” under menyn brandväggen objekt, rulla nedåt i listan över tjänster och välj tjänsten ”RDP”. Högerklicka på och välj Kopiera, högerklicka och välj Klistra in. Det finns nu en RDP-Copy1 Service-objekt som kan redigeras. Högerklicka på RDP-Copy1 och välj Redigera, redigera objektet fönster visas upp som visas här:

![Kopia av standard RDP-regel][5]

Värdena kan sedan redigeras för att representera RDP-tjänsten för en specifik server. För AppVM01 ovan RDP Standardregeln ska ändras så att den återspeglar en nytt namn, beskrivning och externa RDP-porten som används i figur 8 diagrammet (Obs: ändra portarna från 3389 standardvärdet RDP till den externa porten som används för den här specifika servern När det gäller AppVM01 externa porten är 8025) den ändrade tjänsten visas nedan:

![AppVM01 regel][6]

Den här processen upprepas för att skapa RDP-tjänster för de återstående servrarna; AppVM02 DNS01 och IIS01. Skapandet av dessa tjänster blir regeln skapades enklare och tydligare i nästa avsnitt.

> [!NOTE]
> En RDP-tjänst för brandväggen behövs inte för två skäl. (1) första brandväggen VM är en Linux-baserad avbildning så att SSH ska användas på port 22 för hantering av virtuell dator i stället för RDP och 2) port 22 och två andra hanteringsportar tillåts i den första management-regeln som beskrivs nedan för att tillåta anslutning till.
> 
> 

### <a name="firewall-rules-creation"></a>Skapa regler för brandväggen
Det finns tre typer av brandväggsregler som används i det här exemplet, de alla har olika ikoner:

Programmet omdirigerings-regel: ![Programikon för omdirigering][7]

Mål-NAT-regel: ![Mål NAT-ikon][8]

Pass-regel: ![Skicka ikon][9]

Mer information om dessa regler finns på webbplatsen Barracuda.

Från och med Barracuda NG Admin-klienten instrumentpanelen, gå till konfigurationsfliken för att skapa följande regler (eller verifiera befintliga standardregler), i de användningsinställningar avsnittet klickar du på RuleSet-metod. Ett rutnät som kallas ”Main regler” visar befintliga aktiva och inaktiva regler på den här brandväggen. I det övre högra hörnet av det här rutnätet är en liten, grön ”+” knappen, klicka här för att skapa en ny regel (Obs: brandväggen kan vara i ”låst” för ändringar, om du ser en knapp markerad ”låsa” och du kan inte skapa eller redigera regler, klicka på knappen för att ”låsa upp” regeluppsättningen och  Tillåt redigering). Om du vill redigera en befintlig regel, Välj regeln, högerklickar och väljer Redigera regel.

När reglerna skapas och/eller ändras, de måste flyttas till brandväggen och därefter aktiverar du, om detta inte görs regeln ändringarna börjar inte gälla. Push-meddelanden och aktivering processen beskrivs nedan information om regeln beskrivningar.

Egenskaperna för varje regel som krävs för att slutföra det här exemplet beskrivs på följande sätt:

* **Brandväggsregel Management**: Den här appen omdirigeringsregel tillåter trafik skickas till hanteringsportar för en virtuell enhet i nätverket, i det här exemplet en Barracuda NextGen-brandväggen. Management-portarna är 801, 807 och eventuellt 22. De externa och interna portarna är desamma (d.v.s. Ingen port translation). Detta regeln för installationen-MGMT-åtkomst, finns en standardregel och aktiverat som standard (i Barracuda NextGen Firewall version 6.1).
  
    ![Brandväggsregel för hantering][10]

> [!TIP]
> Käll-adressutrymmet i den här regeln är valfri, om hantering av IP-adressintervall är kända, vilket minskar det här omfånget skulle också minska risken för angrepp till hanterade portar.
> 
> 

* **RDP-regler**:  Dessa mål NAT-regler kan hanteringen av de enskilda servrarna via RDP.
  Det finns fyra viktiga fält som behövs för att skapa den här regeln:
  
  1. Källa – för att tillåta RDP från var som helst, referensen ”alla” används i fältet källa.
  2. Tjänsten – använda lämpliga serviceobjektet skapat tidigare i det här fallet ”AppVM01 RDP”, externa portar omdirigera till den lokala IP-adressen för servrar och till port 3386 (standardporten RDP). Den här specifika regeln är för RDP-åtkomst till AppVM01.
  3. Målet – bör vara den *lokal port i brandväggen*, ”DCHP 1 lokala IP” eller eth0 om du använder statiska IP-adresser. Ordningstalet (eth0, eth1 osv.) kan skilja sig om din nätverksapparat har flera lokala gränssnitt. Den porten i brandväggen skickar ut från (kan vara samma som den mottagande porten), faktiska routade målet är i fältet mållistan.
  4. Omdirigering – det här avsnittet visar den virtuella installationen var du vill omdirigera slutligen den här trafiken. Den enklaste omdirigeringen är att placera IP och Port (valfritt) i fältet mållistan. Om ingen port används målporten för den inkommande begäran kommer att användas (d.v.s. inga translation) om en port är utsedd porten kommer också att skulle NAT tillsammans med IP adressen.
     
     ![RDP-brandväggsregel][11]
     
     Totalt fyra RDP-regler måste skapas: 
     
     | Regelnamn | Server | Tjänst | Mållistan |
     | --- | --- | --- | --- |
     | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Teknikområde omfånget för fälten käll- och minskar risken för angrepp. Den mest begränsat omfång som gör att funktionerna ska användas.
> 
> 

* **Programmet trafikregler**: Det finns två program trafikregler, först för webbtrafik klientdelen och andra för backend-server-trafik (t.ex webbservern till datanivå). De här reglerna kommer beror på nätverksarkitekturen (där dina servrar placeras) och trafik flöden (vilken riktning trafiken flödar och vilka portar används).
  
    Först beskrivs är klientdelen-regel för webbtrafik:
  
    ![Webb-brandväggsregel][12]
  
    Det här målet NAT-regeln tillåter trafik själva programmet att nå programservern. Medan de andra reglerna tillåter för säkerhet, hantering, o.s.v., är program-regler vad som ska tillåta externa användare eller tjänster att komma åt programmen. Det finns en webbserver på port 80 i det här exemplet, därför den enda brandväggsregeln för application omdirigerar inkommande trafik till den externa IP-Adressen, web servrar interna IP-adress.
  
    **Obs**: att ingen port har tilldelats i fältet mållistan, alltså den inkommande porten 80 (eller 443 för tjänsten som valts) ska användas i omdirigering av webbservern. Om servern lyssnar på en annan port, till exempel port 8080, fältet mållistan kunde uppdateras till 10.0.1.4:8080 så att även Port omdirigeringen.
  
    Nästa program trafik regeln är backend-regel som tillåter webbservern att kommunicera med AppVM01 server (men inte AppVM02) via valfri tjänst:
  
    ![AppVM01 brandväggsregel][13]
  
    Regeln Pass tillåter alla IIS-servrar på undernätet på klientsidan för att nå AppVM01 (IP-adressen 10.0.2.5) med hjälp av alla protokoll som ska få åtkomst till data som krävs av webbprogrammet på alla portar.
  
    I den här skärmbilden en ”\<explicit dest\>” används i fältet mål för en obestämd 10.0.2.5 som mål. Det kan antingen explicit enligt eller en med namnet nätverksobjekt (som gjordes i förutsättningarna för DNS-servern). Det här är upp till administratören för brandväggen om vilka metoden ska användas. Om du vill lägga till 10.0.2.5 som en Explict Desitnation dubbelklickar du på den första tomma raden under \<explicit dest\> och ange adressen i fönstret som öppnas.
  
    Med regeln skicka krävs inga NAT eftersom detta är intern trafik så anslutningsmetoden kan anges till ”Nej SNAT”.
  
    **Obs!** Källnätverket i den här regeln är alla resurser på undernätet på klientsidan, om det ska bara finnas en eller ett visst kända antal webbservrar, nätverksobjekt gick att skapa en resurs mer specifikt till de exakta IP-adresserna i stället för hela Frontend-undernätet.

> [!TIP]
> Den här regeln använder tjänsten ”alla” att göra det enklare att konfigurera och använda exempelprogrammet, detta innebär även att ICMPv4 (ping) i en enda regel. Detta är dock inte alltid. Portar och protokoll (”tjänsten”) bör begränsas till det minimum som är möjligt och som gör att programmet igen att minska risken för angrepp på gränsen.
> 
> 

<br />

> [!TIP]
> Även om den här regeln visar en explicit dest referens som används, är det en konsekvent metod som ska användas i brandväggskonfigurationen. Vi rekommenderar att det namngivna nätverksobjektet användas i hela för enklare Läs- och support. Den explicita dest används här endast för att visa en alternativ metod och rekommenderas vanligtvis inte (särskilt för komplexa konfigurationer).
> 
> 

* **Utgående till Internet regeln**: Regeln Pass ska tillåta trafik från alla källnätverket ska skickas till de valda Målnätverk. Den här regeln finns en standardregel vanligtvis redan Barracuda NextGen-brandväggen, men är i ett inaktiverat tillstånd. Högerklicka på den här regeln kan komma åt kommandot aktivera regeln. Regeln visas här har ändrats för att lägga till två lokala undernät som har skapats som referenser i avsnittet förutsättningar i det här dokumentet i källattributet för den här regeln.
  
    ![Utgående brandväggsregel][14]
* **DNS-regel**: Regeln Pass kan endast DNS (port 53)-trafik skickas till DNS-servern. Den här regeln kan särskilt DNS för den här miljön som de flesta trafik från klientdelen till serverdelen blockeras.
  
    ![DNS-brandväggsregel][15]
  
    **Obs!** Som visar anslutningsmetoden ingår i den här skärmen. Eftersom den här regeln är för interna IP-adress till interna IP-adress-trafik, inga NATing krävs, detta anslutningsmetoden är inställt på ”Nej SNAT” för den här regeln Pass.
* **Undernätet till undernätet regeln**: Regeln Pass är en standardregel som har aktiverats och ändras för att låta alla servrar i serverdelen undernät att ansluta till en server på klientdelens undernät. Den här regeln är alla intern trafik så anslutningsmetoden vara inställt på Nej SNAT.
  
    ![Intra-VNet-brandväggsregeln][16]
  
    **Obs!** Dubbelriktad kryssrutan inte är markerat (eller är det här alternativet är markerat i de flesta regler), detta är viktig för den här regeln eftersom den gör det regeln ”envägs”, en anslutning kan initieras från backend-undernät till klientdelens nätverk, men inte tvärtom. Om kryssrutan checkades skulle den här regeln Aktivera dubbelriktad trafik som inte är det önskade från våra logiskt diagram.
* **Neka alla Trafikregel**: Detta bör alltid vara den sista regeln (när det gäller prioritet) och därför om en trafiken flödar inte matchar någon av de föregående regler som den kommer att tas bort av den här regeln. Det här är en standardregel och vanligtvis aktiverad behövs vanligtvis inga ändringar. 
  
    ![Regel för Brandvägg för att neka][17]

> [!IMPORTANT]
> När alla ovanstående regler skapas, är det viktigt att granska prioriteten för varje regel för att se till att trafik ska tillåtas eller nekas efter behov. I det här exemplet är reglerna i den ordning de ska visas i rutnätet Main för att vidarebefordra regler i Barracuda Management-klienten.
> 
> 

## <a name="rule-activation"></a>Regel för aktivering
Med RuleSet-metod som har ändrats för att specifikation av logic-diagram, måste ruleset överförs till brandväggen och sedan aktiveras.

![Brandväggen regeln aktivering][18]

I det övre högra hörnet av management-klienten finns i ett kluster med knappar. Klicka på ”Skicka ändringar” för att skicka ändrade regler i brandväggen och sedan på knappen ”Aktivera”.

Det här exemplet miljö-versionen har slutförts med aktivering brandväggen RuleSet-metod.

## <a name="traffic-scenarios"></a>Trafik-scenarier
> [!IMPORTANT]
> En viktig takeway är att komma ihåg att **alla** kommer trafik genom brandväggen. Så till fjärrskrivbord till IIS01-servern, ska även om det är i Front End-Molntjänsten och på frontend-undernätet för att komma åt den här servern vi behöva RDP i brandväggen på port 8014, och låt brandväggen för att dirigera begäran RDP internt till RDP-Por IIS01 t. Azure portal ”Anslut” knappen fungerar inte eftersom det finns ingen direkt RDP-väg till IIS01 (så länge portalen kan se). Det innebär att alla anslutningar från internet kommer att tjänsten Security och en Port, t.ex. secscv001.cloudapp.net:xxxx.
> 
> 

Dessa scenarier kan ska följande brandväggsregler finnas:

1. Brandväggshantering
2. RDP till IIS01
3. RDP till DNS01
4. RDP till AppVM01
5. RDP till AppVM02
6. Trafik på webben
7. Trafik till AppVM01
8. Utgående till Internet
9. Klientdelen till DNS01
10. Intra-undernätstrafik (serverdel till endast klientdel)
11. Neka alla

Faktiska brandväggen RuleSet-metod har antagligen många andra regler Förutom dessa, reglerna på alla angivna brandväggen kommer även att ha olika prioritetsnummer än de som visas här. Den här listan och tillhörande nummer är att tillhandahålla relevans mellan bara reglerna elva och den relativa prioriteten vilken av dessa. Med andra ord; på den faktiska brandväggen kan den ”RDP till IIS01” vara regeln tal 5, men så länge den hamnar under ”brandväggen Management”-regeln och ovanför ”RDP till DNS01” regeln skulle justera avsikt att den här listan. Listan ska också underlätta den nedan scenarier för att tillåta kortfattat; t.ex. ”FW regeln 9 (DNS)”. Även kortfattat, fyra RDP-reglerna gemensamt kallas, ”RDP-regler” när trafik scenariot är inte relaterat till RDP.

Kom också ihåg att Nätverkssäkerhetsgrupper är på plats för inkommande Internettrafik på Frontend och Backend-undernät.

#### <a name="allowed-internet-to-web-server"></a>(Tillåts) Internet till webbservern
1. Internet-begäranden HTTP användarsidan från SecSvc001.CloudApp.Net (Internet som riktas mot Cloud Services)
2. Cloud service skickar trafik via öppna slutpunkter på port 80 till brandväggens gränssnitt på 10.0.0.4:80
3. Ingen Nätverkssäkerhetsgrupp som tilldelats Security undernät, så systemet NSG-regler tillåta trafik till brandväggen
4. Trafiken kommer till den interna IP-adress för brandvägg (is 10.0.1.4)
5. Brandväggen börjar Regelbearbetning:
   1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
   2. VB regler 2 – 5 (RDP-regler) inte tillämpa, flytta till nästa regel
   3. VB regel 6 (App: Web) gäller, trafik tillåts, brandvägg NAT den till is 10.0.1.4 (IIS01)
6. Undernätet på klientsidan börjar bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera Internet) gäller inte (den här trafiken har NAT skulle genom brandväggen, därför källadressen är nu i brandväggen som finns i säkerhet-undernät och setts av NSG för undernätet på klientsidan ska vara ”local” trafik och tillåts därför), flytta till nästa regel
   2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
7. IIS01 lyssnar för webbtrafik, får den här förfrågan och startar bearbetning av begäran
8. IIS01 försöker initierar en FTP-sessionen till AppVM01 på Backend-undernät
9. UDR-väg på Frontend-undernätet gör brandväggen nästa hopp
10. Inga utgående regler på Frontend-undernätet trafik tillåts
11. Brandväggen börjar Regelbearbetning:
    1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
    2. VB-regel 2 – 5 (RDP-regler) inte tillämpa, flytta till nästa regel
    3. VB regel 6 (App: Web) inte tillämpa, gå till nästa regel
    4. VB regel 7 (App: Serverdel) gäller, trafik tillåts, brandvägg vidarebefordrar trafik till 10.0.2.5 (AppVM01)
12. Backend-undernät börjar bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera Internet) inte tillämpa, gå till nästa regel
    2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
13. AppVM01 tar emot begäran och initierar sessionen och svarar
14. UDR-väg på Backend-undernät gör brandväggen nästa hopp
15. Eftersom det finns inga utgående NSG-regler på Backend-undernät som svaret tillåts
16. Eftersom detta returnerar trafik på en upprättad session skickar brandväggen svaret tillbaka till webbservern (IIS01)
17. Frontend-undernätet börjar bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera Internet) inte tillämpa, gå till nästa regel
    2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
18. IIS-servern tar emot svaret, Slutför transaktionen med AppVM01 och slutför sedan att skapa HTTP-svaret, skickas HTTP-svar till begäranden
19. Eftersom det finns inga utgående regler i NSG på undernätet på klientsidan svaret tillåts
20. HTTP-svaret kommer till brandväggen och eftersom detta är svaret på en upprättad session NAT godkänns av brandväggen
21. Brandväggen omdirigerar sedan svaret tillbaka till Internet-användare
22. Eftersom det finns inte utgående får NSG-regler eller UDR hopp på undernätet på klientsidan svaret tillåts och Internet-användare den webbsida som begärdes.

#### <a name="allowed-internet-rdp-to-backend"></a>(Tillåts) Internet RDP till serverdelen
1. Serveradministratören på internet begär RDP-session till AppVM01 via SecSvc001.CloudApp.Net:8025, där 8025 är användartilldelade portnumret för brandväggsregeln ”RDP till AppVM01”
2. Molntjänsten skickar trafik via den öppna slutpunkten på port 8025 till brandväggens gränssnitt på 10.0.0.4:8025
3. Ingen Nätverkssäkerhetsgrupp som tilldelats Security undernät, så systemet NSG-regler tillåta trafik till brandväggen
4. Brandväggen börjar Regelbearbetning:
   1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
   2. VB regel 2 (RDP IIS) inte tillämpa, gå till nästa regel
   3. VB regel 3 (RDP DNS01) inte tillämpa, gå till nästa regel
   4. VB regel 4 (RDP AppVM01) gäller, trafik tillåts, brandvägg NAT den till 10.0.2.5:3386 (RDP-porten på AppVM01)
5. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera Internet) gäller inte (den här trafiken har NAT skulle genom brandväggen, därför källadressen är nu i brandväggen som finns i säkerhet-undernät och setts av NSG för Backend-undernät ska vara ”local” trafik och tillåts därför), flytta till nästa regel
   2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
6. AppVM01 lyssnar efter RDP-trafik och svarar
7. Med ingen utgående NSG-regler gäller för standard och återvändande trafik tillåts
8. UDR dirigerar trafik i brandväggen som nästa hopp
9. Eftersom detta returnerar trafik på en upprättad session skickar brandväggen svaret tillbaka till internet-användare
10. RDP-session är aktiverat
11. AppVM01 frågar efter användarnamn lösenord

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tillåts) Web Server DNS-sökning på DNS-server
1. Web Server, IIS01, måste en data-feed på www.data.gov, men måste matcha adressen.
2. Nätverkskonfigurationen för VNet-listor DNS01 (10.0.2.4 på Backend-undernät) som den primära DNS-servern, IIS01 skickar en DNS-begäran till DNS01
3. UDR dirigerar trafik i brandväggen som nästa hopp
4. Ingen utgående NSG-regler är bundna till undernätet på klientsidan, tillåts trafik
5. Brandväggen börjar Regelbearbetning:
   1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
   2. VB-regel 2 – 5 (RDP-regler) inte tillämpa, flytta till nästa regel
   3. VB regler 6 och 7 (regler) inte tillämpa, flytta till nästa regel
   4. VB regeln 8 (till Internet) inte tillämpa, flytta till nästa regel
   5. VB regel 9 (DNS) gäller, trafik tillåts, brandvägg vidarebefordrar trafik till 10.0.2.4 (DNS01)
6. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera Internet) inte tillämpa, gå till nästa regel
   2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
7. DNS-servern tar emot begäran
8. DNS-servern har inte den adress som cachelagras och frågar en rot-DNS-server på internet
9. UDR dirigerar trafik i brandväggen som nästa hopp
10. Ingen utgående NSG-regler på Backend-undernät tillåts trafik
11. Brandväggen börjar Regelbearbetning:
    1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
    2. VB-regel 2 – 5 (RDP-regler) inte tillämpa, flytta till nästa regel
    3. VB regler 6 och 7 (regler) inte tillämpa, flytta till nästa regel
    4. VB regeln 8 (till Internet) gäller, trafik tillåts, session är SNAT ut till rot-DNS-servern på Internet
12. Internet-DNS-servern svarar, eftersom den här sessionen initierades från brandväggen, svaret accepteras av brandväggen
13. Eftersom det här är en upprättad session vidarebefordrar brandväggen svar till den ursprungliga servern, DNS01
14. Backend-undernät börjar bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera Internet) inte tillämpa, gå till nästa regel
    2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
15. DNS-servern tar emot och cachelagrar svaret och sedan svarar på den första begäran tillbaka till IIS01
16. UDR-väg på backend-undernät gör brandväggen nästa hopp
17. Det finns inga utgående NSG-regler på Backend-undernät, tillåts trafik
18. Det här är en upprättad session i brandväggen, svaret vidarebefordras av brandväggen tillbaka till IIS-servern
19. Frontend-undernätet börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät för Frontend-undernätet, så att ingen av NSG-regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät skulle tillåta den här trafiken så att trafiken tillåts
20. IIS01 tar emot svaret från DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Tillåts) Backend-servern till Frontend-server
1. En administratör som har loggat in på AppVM02 via RDP begär en fil direkt från IIS01 servern via Utforskaren i windows
2. UDR-väg på Backend-undernät gör brandväggen nästa hopp
3. Eftersom det finns inga utgående NSG-regler på Backend-undernät som svaret tillåts
4. Brandväggen börjar Regelbearbetning:
   1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
   2. VB-regel 2 – 5 (RDP-regler) inte tillämpa, flytta till nästa regel
   3. VB regler 6 och 7 (regler) inte tillämpa, flytta till nästa regel
   4. VB regeln 8 (till Internet) inte tillämpa, flytta till nästa regel
   5. VB regel 9 (DNS) inte tillämpa, gå till nästa regel
   6. VB regeln 10 (Intra-undernät) gäller, trafik tillåts, brandvägg skickar trafik till is 10.0.1.4 (IIS01)
5. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. NSG-regel 1 (blockera Internet) inte tillämpa, gå till nästa regel
   2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
6. Under förutsättning att korrekt autentisering och auktorisering, IIS01 tar emot begäran och svarar
7. UDR-väg på Frontend-undernätet gör brandväggen nästa hopp
8. Eftersom det finns inga utgående regler i NSG på undernätet på klientsidan svaret tillåts
9. Eftersom det här är en befintlig session i brandväggen svaret tillåts och brandväggen returnerar svaret till AppVM02
10. Backend-undernät börjar bearbetning av inkommande regel:
    1. NSG-regel 1 (blockera Internet) inte tillämpa, gå till nästa regel
    2. Standardreglerna för NSG tillåter undernät undernätstrafik, trafik tillåts, stoppa NSG-Regelbearbetning
11. AppVM02 tar emot svaret

#### <a name="denied-internet-direct-to-web-server"></a>(Nekad) Internet direkt till webbservern
1. Internet-användare försöker komma åt webbservern, IIS01, via tjänsten FrontEnd001.CloudApp.Net
2. Eftersom det finns inga slutpunkter som är öppna för HTTP-trafik är detta skulle inte passerar Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning skulle NSG (blockera Internet) på undernätet på klientsidan blockera den här trafiken
4. Slutligen Frontend-undernätet UDR vägen skickar all utgående trafik från IIS01 till som nästa hopp-brandväggen och brandväggen skulle se detta som en asymmetrisk trafik och släpp det utgående svaret Thus som det finns minst tre oberoende försvarslinjer mellan internet och IIS01 via dess molntjänst som förhindrar obehörig/oönskad åtkomst.

#### <a name="denied-internet-to-backend-server"></a>(Nekad) Internet till Backend-servern
1. Internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom det finns inga slutpunkter som är öppna för filresursen är detta skickar inte Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning skulle NSG (blockera Internet) blockera den här trafiken
4. Slutligen UDR vägen skickar all utgående trafik från AppVM01 till som nästa hopp-brandväggen och brandväggen skulle se detta som en asymmetrisk trafik och släpp den utgående svar vilket det finns minst tre oberoende försvarslinjer mellan internet och AppVM01 via dess molntjänst som förhindrar obehörig/oönskad åtkomst.

#### <a name="denied-frontend-server-to-backend-server"></a>(Nekad) Frontend-server till Backend-servern
1. Anta IIS01 har drabbats och kör skadlig kod försöker skanna undernät Backend-servrarna.
2. Frontend-undernätet UDR vägen skulle skicka all utgående trafik från IIS01 i brandväggen som nästa hopp. Detta är inte något som kan ändras av den komprometterade virtuella datorn.
3. Brandväggen skulle behandla trafiken, om förfrågan var AppVM01 och DNS-server för DNS-sökning som trafik potentiellt bli nekad av brandväggen (på grund av FW regler 7 och 9). All annan trafik blockeras av FW regel 11 (neka alla).
4. Om avancerad hotidentifiering har aktiverats i brandväggen (som inte omfattas i det här dokumentet, finns i leverantörens dokumentation om din specifika nätverksinstallation advanced threat funktioner), även trafik som ska tillåtas av de grundläggande vidarebefordringsregler beskrivs i det här dokumentet kan förhindras om trafiken innehåller signaturer för kända eller mönster som flagga en regel för Avancerat.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Nekad) Internet-DNS-sökning på DNS-server
1. Internet-användare försöker att söka efter en intern DNS-post på DNS01 genom BackEnd001.CloudApp.Net-tjänsten 
2. Eftersom det finns inga slutpunkter som är öppen för DNS-trafik är detta skulle inte passerar Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning skulle NSG-regel (blockera Internet) på undernätet på klientsidan blockera den här trafiken
4. Slutligen serverdelsvägar undernät UDR skulle skicka all utgående trafik från DNS01 till som nästa hopp-brandväggen och brandväggen skulle se detta som en asymmetrisk trafik och släpp det utgående svaret Thus som det finns minst tre oberoende försvarslinjer mellan den Internet och DNS01 via dess molntjänst som förhindrar obehörig/oönskad åtkomst.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Nekad) Internet för att SQL-åtkomst via brandväggen
1. Internet-användare begär SQL-data från SecSvc001.CloudApp.Net (Internet som riktas mot Cloud Services)
2. Eftersom det finns inga slutpunkter som är öppen för SQL är detta skickar inte Molntjänsten och skulle nå brandväggen
3. Om SQL-slutpunkter öppna av någon anledning skulle brandväggen börja Regelbearbetning:
   1. VB regel 1 (VB Mgmt) inte tillämpa, gå till nästa regel
   2. VB regler 2 – 5 (RDP-regler) inte tillämpa, flytta till nästa regel
   3. VB regel 6 och 7 (program-regler) inte tillämpa, flytta till nästa regel
   4. VB regeln 8 (till Internet) inte tillämpa, flytta till nästa regel
   5. VB regel 9 (DNS) inte tillämpa, gå till nästa regel
   6. VB regeln 10 (Intra-undernät) inte tillämpa, flytta till nästa regel
   7. VB regel 11 (neka alla) gäller, trafik är blockerad, stoppa regelbehandling

## <a name="references"></a>Referenser
### <a name="main-script-and-network-config"></a>Huvudskriptet och konfigurationer för nätverk
Spara fullständigt skript i en PowerShell-skriptfilen. Spara konfiguration för nätverk i en fil med namnet ”NetworkConf2.xml”.
Ändra variablerna användardefinierade efter behov. Kör skriptet och följ brandväggen regeln installationsprogrammet anvisningarna ovan.

#### <a name="full-script"></a>Fullständigt skript
Det här skriptet kommer baserat på användardefinierade variabler:

1. Ansluta till en Azure-prenumeration
2. Skapa ett nytt lagringskonto
3. Skapa ett nytt virtuellt nätverk och tre undernät som definierats i konfigurationsfilen för nätverk
4. Skapa fem virtuella datorer; 1-brandväggen och 4 windows server virtuella datorer
5. Konfigurera UDR, inklusive:
   1. Skapa två nya routningstabeller
   2. Lägga till vägar till tabellerna
   3. Binda tabeller till lämpliga undernät
6. Aktivera IP-vidarebefordring på NVA
7. Konfigurera NSG, inklusive:
   1. Skapa en Nätverkssäkerhetsgrupp
   2. Lägger till en regel
   3. Bindning NSG: N till lämpliga undernät

Det här PowerShell-skriptet ska köras lokalt på en internet-ansluten dator eller server.

> [!IMPORTANT]
> När skriptet har körts kanske varningar eller andra informationsmeddelanden som pop i PowerShell. Endast felmeddelanden i rött är orsaka problem.
> 
> 

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
       - IP Forwading from the FireWall out to the internet
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

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
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
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
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

      # Assoicate the Route Tables with the Subnets
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
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Konfigurationsfilen för nätverk
Spara den här xml-filen med uppdaterade plats och lägga till länken till den här filen till variabeln $NetworkConfigFile i skriptet ovan.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>Exempelskript för program
Om du vill installera ett exempelprogram för detta och andra DMZ-exempel finns en på följande länk: [Exempelskript för program][SampleApp]

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
[SampleApp]: ./virtual-networks-sample-app.md
