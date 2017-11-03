---
title: "DMZ exempel – skapa en DMZ att skydda nätverk med en brandvägg, UDR och NSG | Microsoft Docs"
description: "Skapa en DMZ med en brandvägg, användardefinierade routning (UDR) och Nätverkssäkerhetsgrupper (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exempel 3 – skapa en DMZ att skydda nätverk med en brandvägg, UDR och NSG
[Gå tillbaka till gränsen bästa praxis säkerhetssidan][HOME]

Det här exemplet skapar en DMZ med en brandvägg, fyra windows-servrar, användaren definierat routning, IP-vidarebefordring och Nätverkssäkerhetsgrupper. Det hjälper även genom relevanta kommandon för att ge en bättre förståelse för varje steg. Det finns också ett trafik scenariot avsnitt för att ge en detaljerad steg för steg hur trafik fortsätter via lager i skyddsstrategierna i Perimeternätverket. Slutligen är avsnitt i hänvisning den fullständiga koden och anvisningarna för att skapa den här miljön för att testa och experimentera med olika scenarier. 

![Dubbelriktad DMZ med NVA, NSG och UDR][1]

## <a name="environment-setup"></a>Miljökonfiguration
I det här exemplet finns det en prenumeration som innehåller följande:

* Tre molntjänster: ”SecSvc001”, ”FrontEnd001” och ”BackEnd001”
* Ett virtuellt nätverk ”CorpNetwork” med tre undernät: ”SecNet”, ”FrontEnd” och ”BackEnd”
* En virtuell nätverksenhet, i det här exemplet en brandvägg, som är anslutet till undernätet för SecNet
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar tillbaka programmet avslutas servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

I referensavsnittet nedan finns ett PowerShell-skript som skapar de flesta i miljön som beskrivs ovan. Skapande av virtuella datorer och virtuella nätverk, även om den är klar med exempelskriptet som inte beskrivs i detalj i detta dokument.

Att skapa miljön:

1. Spara nätverket XML-konfigurationsfilen finns i referensavsnittet (uppdaterade med namn, plats och IP-adresser för att matcha det aktuella scenariot)
2. Uppdatera Användarvariabler i skript för att matcha den miljö som skriptet ska köras mot (prenumerationer, tjänstnamn och så vidare)
3. Kör skriptet i PowerShell

**Obs**: den region som visas i PowerShell-skriptet måste matcha den region som visas i nätverket XML-konfigurationsfilen.

När skriptet har körts vidtas efter skriptet följande steg:

1. Konfigurera brandväggsregler detta beskrivs i avsnittet nedan: beskrivning av brandväggen.
2. Du kan också är i referensavsnittet två skript för att konfigurera webbserver- och app-servern med en enkel webbapp för att testa med den här DMZ-konfigurationen.

När skriptet har körts brandväggen regler måste slutföras kan detta beskrivs i avsnittet: brandväggsregler.

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

VNETLocal är alltid definierad address-prefix för VNet för det specifika nätverket (ie ändras från VNet till VNet beroende på hur varje specifik VNet har definierats). De återstående systemvägarna är statiska och standard som ovan.

För prioritet, vägar bearbetas via metoden längsta Prefix-matchning (LPM), därför den mest specifika vägen i tabellen gäller för en viss måladress.

Därför skulle trafik (till exempel till servern DNS01 10.0.2.4) är avsedda för det lokala nätverket (10.0.0.0/16) dirigeras mellan virtuella nätverk till dess mål på grund av 10.0.0.0/16 vägen. Med andra ord för 10.0.2.4, 10.0.0.0/16 vägen är den mest specifika vägen, även om 10.0.0.0/8 och 0.0.0.0/0 kunde gäller även, men eftersom de är mindre specifika de påverkar inte den här trafiken. Därmed skulle trafiken till 10.0.2.4 ha nexthop för det lokala VNet och bara vidarebefordra till målet.

Om trafiken är avsedd för 10.1.1.1 till exempel, 10.0.0.0/16 vägen skulle tillämpas, men 10.0.0.0/8 är den mest specifika och trafiken skulle detta bort (”svart holed”) eftersom nästa hopp är Null. 

Om målet kunde inte tillämpas för Null-prefix eller VNETLocal prefix och det skulle följer den minst specifika vidarebefordra 0.0.0.0/0 och skickas till Internet som nexthop och därmed ut Azures internet kant.

Om det finns två identiska prefix i routningstabellen, följer efter prioritet baserat på vägar ”källattribut”:

1. ”VirtualAppliance” = en definierad väg manuellt lagts till i tabellen
2. ”VPNGateway” = en dynamisk väg BGP (när det används med hybrid nätverk), lagts till av en dynamisk nätverksprotokoll, dessa vägar kan ändras med tiden som dynamiska protokollet automatiskt återger ändringar i peerkoppla nätverk
3. ”Standard” = Systemvägar, lokal VNet och statiska poster som visas i tabellen vägen.

> [!NOTE]
> Du kan nu använda användaren definierat routning (UDR) med ExpressRoute- och VPN-gatewayer att tvinga utgående och inkommande anslutningar mellan lokala trafik vidarebefordras till en virtuell nätverksenhet (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>Skapa de lokala vägarna
I det här exemplet krävs två routningstabeller, en för Frontend och Backend-undernät. Varje tabell har lästs in med statiska vägar som är lämpliga för det angivna undernätet. I det här exemplet har varje tabell tre vägar:

1. Lokal undernätstrafik med ingen nästa hopp som definierats för att tillåta lokal undernätstrafik kringgå brandväggen
2. Trafik i virtuella nätverk med en nästa hopp har definierats som brandväggen, åsidosätts Standardregeln som tillåter lokala VNet-trafik att dirigera direkt
3. Alla återstående trafik (0/0) med en nästa hopp har definierats som brandväggen

När routningstabeller skapas är de kopplade till sina undernät. För undernätet Frontend routningstabell, skapas en gång och bunden till undernätet bör se ut så här:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


I det här exemplet används följande kommandon för att bygga routningstabellen, lägga till en användardefinierad väg och sedan binda routningstabellen till ett undernät (Obs!; eventuella objekt nedan som börjar med ett dollartecken (t.ex.: $BESubnet) är användardefinierade variabler från skriptet i referensavsnittet i det här dokumentet):

1. Först måste du skapa bastabellen routning. Kodutdrag skapandet av tabellen för Backend-undernät. I skriptet skapas även en motsvarande tabell för undernätet Frontend.
   
     Nya AzureRouteTable-Name $BERouteTableName '
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. När du har skapat routningstabellen läggas specifika användardefinierade vägar. I det här liten, kommer all trafik (0.0.0.0/0) att dirigeras via virtuell installation (en variabel, $VMIP [0] används för att skicka in IP-adress som tilldelats när den virtuella installationen skapades tidigare i skriptet). En motsvarande regel skapas också i tabellen klientdel i skriptet.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. Ovanstående väg åsidosätter ”0.0.0.0/0” standardvägen, men 10.0.0.0/16 Standardregeln fortfarande befintliga som vill tillåta trafik inom VNet att dirigera direkt till målet och inte virtuell nätverksenhet. Rätt problemet Följ regeln måste läggas till.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. Det är nu ett val görs. Med ovanstående två vägar dirigerar all trafik till brandväggen för bedömning även trafik i ett enda undernät. Detta kan det vara önskvärt, men för att tillåta trafik i ett undernät för att dirigera lokalt utan medverkan från brandväggen tredje mycket specifik regel kan läggas till. Den här vägen tillstånd för alla adresser destine för det lokala undernätet enkelt kan vidarebefordra det direkt (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Slutligen med routningstabellen skapas och konfigureras med en användardefinierade vägar, måste tabellen nu vara bunden till ett undernät. Routningstabellen klientdelen är även kopplad till undernätet Frontend i skriptet. Här är skriptet bindning för backend-undernät.
   
     Set-AzureSubnetRouteTable - VirtualNetworkName $VNetName '
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP-vidarebefordran
En tillhörande funktion som UDR, är IP-vidarebefordring. Det här är en inställning i en virtuell installation som gör att det kan ta emot trafik som inte är adresserad till enhet och vidarebefordra trafiken till destinationen ultimate.

Exempelvis om trafik från AppVM01 ställer en förfrågan till servern DNS01 skulle UDR vidarebefordra detta i brandväggen. Med IP-vidarebefordran aktiverat, kommer trafiken för DNS01 mål (10.0.2.4) accepteras av installation (10.0.0.4) och sedan vidarebefordras till dess slutliga destinationen (10.0.2.4). Utan IP-vidarebefordran aktiverad i brandväggen, kan trafik inte godkännas av anordningen även om routningstabellen har brandväggen som nexthop. 

> [!IMPORTANT]
> Det är viktigt att komma ihåg att aktivera IP-vidarebefordring tillsammans med användaren definierat routning.
> 
> 

Konfigurera IP-vidarebefordran är ett enda kommando och kan göras vid tidpunkten för skapandet av virtuell dator. För flödet av det här exemplet kodfragmentet mot slutet av skriptet och grupperas med UDR-kommandon:

1. Anropa den VM-instans som är din virtuella installation brandväggen i det här fallet och aktiverar IP-vidarebefordring (Obs!; ett objekt i rött som börjar med ett dollartecken (t.ex.: $VMName[0]) är en användardefinierad variabel från skriptet i referensavsnittet i det här dokumentet. Noll i hakparenteserna, [0], som representerar den första virtuella datorn i matrisen med virtuella datorer för exempelskriptet utan modifikation, den första virtuella datorn (VM 0) måste vara brandväggen):
   
     Get-AzureVM-Name $VMName [0] - ServiceName $ServiceName [0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan in med en enskild regel. Den här gruppen binds sedan bara till Frontend och Backend-undernät (inte SecNet). Deklarativt skapas följande regel:

1. All trafik (alla portar) från Internet till hela VNet (alla undernät) nekas

Även om NSG: er som används i det här exemplet är huvudsakliga syftet som en sekundär försvarslinje mot manuell felaktig konfiguration. Vi vill blockera alla inkommande trafik från internet till antingen klientdelen eller Backend-undernät, trafik bör endast flödar genom brandväggen SecNet undernätet (och om lämpliga in klientdelen eller serverdelen undernät). Dessutom med UDR regler på plats, skulle all trafik som har gjort i klientdelen eller serverdelen undernät dirigeras ut till brandväggen (tack vare UDR). Brandväggen visas detta som en asymmetrisk dataflöde och skulle sjunka utgående trafik. Det finns därför tre säkerhetsnivåer skydda Frontend och Backend-undernät; 1) inga öppna slutpunkter på FrontEnd001 och BackEnd001 molntjänster, 2) NSG: er nekar trafik från Internet, 3) brandväggen släppa asymmetriska trafiken.

En intressant avseende Nätverkssäkerhetsgruppen i det här exemplet är att den innehåller endast en regel som visas nedan, vilket är att neka internet-trafik till hela virtuella nätverket som omfattar säkerhet undernätet. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Men eftersom NSG: N är bara bunden till Frontend och Backend-undernät, regeln inte bearbetas på trafik inkommande till undernätet för säkerhet. Trots att uttrycket NSG anger ingen Internet-trafik till varje adress för virtuella nätverk, eftersom NSG: N inte är bunden till undernätet för säkerhet, därför flödar trafiken till undernätet för säkerhet.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Brandväggsregler
I brandväggen måste vidarebefordran regler skapas. Eftersom brandväggen blockerar eller vidarebefordran alla inkommande, utgående och intra-VNet-trafik krävs många brandväggsregler. Dessutom träffar all inkommande trafik Security Service offentliga IP-adress (olika portar) för att kunna bearbetas av brandväggen. Bästa praxis är att diagram logiska flöden innan du konfigurerar undernäten och brandväggsregler för att undvika omarbeta senare. Följande bild är en logisk vy för brandväggsregler för det här exemplet:

![Logisk vy för brandväggsregler][2]

> [!NOTE]
> Baserat på den virtuella nätverksenhet som används varierar av hanteringsportar. I det här exemplet refererar till en brandvägg för nästa generation av Barracuda som använder port 22, 801 och 807. Läs dokumentationen om enheten leverantör för att hitta exakt vilka portar som används för hantering av den enhet som används.
> 
> 

### <a name="logical-rule-description"></a>Beskrivning av logiska regel
I den logiska diagrammet ovan visas inte säkerhet undernätet eftersom brandväggen inte är den enda resursen i undernätet och det här diagrammet visar brandväggsregler och hur de logiskt Tillåt eller neka trafikflöde och inte den faktiska routade sökvägen. Dessutom externa portar som valts för RDP-trafik är högre låg portar (8014 – 8026) och har valts för något överensstämmer med de två sista oktetterna i lokal IP-adress för att lättare att läsa (t.ex. lokala serveradress 10.0.1.4 är associerad med externa porten 8014), men alla högre icke motstridig portar kan användas.

Vi behöver 7 typer av regler, dessa regeltyper är som följer beskrivs i det här exemplet:

* Externa regler (för inkommande trafik):
  1. Hantering av brandväggsregel: Regeln App omdirigering tillåter trafik skickas till hanteringsportar för virtuell nätverksenhet.
  2. RDP-regler (för varje windows server): dessa fyra regler (en för varje server) kan hantera enskilda servrar via RDP. Detta kan också ihop till en regel beroende på den virtuella nätverksenhet som används.
  3. Regler för nätverkstrafik för programmet: Det finns två programregler för nätverkstrafik, först för webbtrafik klientdelen och andra för backend-trafik (t ex webbservern datanivå). Konfigurationen av dessa regler kommer beroende nätverksarkitekturen (där servrarna placeras) och trafiken flödar (vilken riktning trafikflöde och vilka portar som används).
     * Den första regeln tillåter faktiska programmet trafiken till programservern. Medan de andra reglerna tillåter för säkerhet, hantering, etc., är program vad tillåta externa användare eller tjänster att komma åt program. Det finns en enda webbserver på port 80, vilket en enda brandväggsregel för programmet att omdirigera inkommande trafik till externa IP, web servrar interna IP-adress för det här exemplet. Den omdirigerade trafik sessionen skulle NAT tas till den interna servern.
     * Regel för nätverkstrafik av andra program är serverdelen regeln för att tillåta att webbservern ska kommunicera med AppVM01 server (men inte AppVM02) via alla portar.
* Interna regler (för intra-VNet-trafik)
  1. Utgående till regel för Internet: den här regeln tillåter trafik från alla nätverk ska skickas till de valda nätverken. Den här regeln är vanligtvis en standardregel redan i brandväggen, men i ett inaktiverat tillstånd. Den här regeln ska aktiveras för det här exemplet.
  2. DNS-regel: Den här regeln kan endast DNS (port 53) trafik skickas till DNS-servern. För den här miljön som de flesta trafik från klientdelen till serverdelen blockeras tillåter den här regeln specifikt DNS från alla lokala undernätet.
  3. Undernät undernät regel: den här regeln är att låta alla servrar på backend-undernät kan ansluta till någon server i klientdelens undernät (men inte omvänt).
* Felsäker regel (för trafik som inte uppfyller något av ovanstående):
  1. Neka alla Trafikregel: Detta ska alltid vara sista regeln (vad gäller prioritet) och som sådan om en trafiken flödar inte matchar någon av ovanstående regler tas bort av den här regeln. Det här är en standardregel och vanligtvis aktiverad behövs vanligtvis inga ändringar.

> [!TIP]
> På den andra program trafik regeln, valfri port tillåts för enkelt i detta exempel i ett verkligt scenario mest specifika porten och adressintervall som ska användas för att minska risken för angrepp på den här regeln.
> 
> 

<br />

> [!IMPORTANT]
> När alla ovanstående regler skapas, är det viktigt att granska prioriteten för varje regel för att se till att trafik ska tillåtas eller nekas efter behov. I det här exemplet är reglerna i prioritetsordning. Det är lätt att låsas utanför brandväggen på grund av felaktigt beställda regler. Kontrollera management för själva brandväggen är alltid absolut högsta prioritet regeln minimum.
> 
> 

### <a name="rule-prerequisites"></a>Regel för krav
En förutsättning för den virtuella datorn körs i brandväggen är offentliga slutpunkter. Offentliga slutpunkter måste vara öppna för brandväggen för att bearbeta trafik. Det finns tre typer av trafik i det här exemplet; 1) hanteringstrafik till kontrollen brandväggen och brandväggsregler, 2) RDP-trafik som styr windows-servrar och 3) programmets trafik. Dessa är de tre kolumnerna av trafiktyper i övre hälften av logisk vy för brandväggsregler ovan.

> [!IMPORTANT]
> En nyckel takeway är att komma ihåg att **alla** trafik kommer genom brandväggen. Så till fjärrskrivbord till IIS01-servern, kommer även om den är i Front End-Molntjänsten och på frontend-undernät för att komma åt den här servern vi behöver brandväggen på port 8014 till RDP, och låt brandväggen för att dirigera internt RDP-begäran till IIS01 RDP-porten. Azure portal ”Anslut” knappen fungerar inte eftersom det finns ingen direkt RDP-väg till IIS01 (som portalen kan se). Detta innebär att alla anslutningar från internet Security Service och en Port, t.ex. secscv001.cloudapp.net:xxxx (referens i ovanstående diagram för matchning av extern Port och intern IP-adress och Port).
> 
> 

En slutpunkt kan öppnas antingen vid tidpunkten för att skapa en virtuell dator eller efter version som är klar i exempelskriptet med och nedan i det här kodstycket (Obs!; alla objekt som börjar med ett dollartecken (t.ex.: $VMName[$i]) är en användardefinierad variabel från skriptet i referensavsnittet i det här dokumentet. ”$I” i hakparenteserna, [$i] representerar matris-numret för en specifik virtuell dator i en matris av virtuella datorer):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Även om inte klart visas här beror på användning av variabler, men slutpunkter är **endast** öppnas på Molntjänsten säkerhet. Detta är att se till att all inkommande trafik hanteras (dirigeras, NAT hade, släppa) av brandväggen.

Management-klienten måste installeras på en dator som kan hantera brandväggen och skapa de konfigurationer som krävs. Se dokumentationen från brandväggen (eller andra NVA)-leverantören om hur du hanterar enheten. Resten av det här avsnittet och i nästa avsnitt, brandvägg regler skapas, beskriver konfigurationen av brandväggen, via Hanteringsklient leverantörer (d.v.s. inte Azure-portalen eller PowerShell).

Instruktioner för att klienten ska ladda ned och ansluter till Barracuda som används i det här exemplet finns här: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

När inloggad på brandväggen men innan du skapar brandväggsregler, finns det två nödvändiga objektklasser som kan göra att skapa reglerna enklare; Nätverks- och objekt.

I det här exemplet ska tre namngivna nätverksobjekt definierade (ett för undernätet Frontend och Backend-undernät, även ett nätverksobjekt för IP-adress för DNS-servern). Så här skapar du ett namngivet nätverk. från instrumentpanelen Barracuda NG Admin klienten navigerar du till konfigurationsfliken, i avsnittet konfiguration RuleSet-metod, sedan klickar du på ”nätverk” under menyn Brandväggsobjekt Klicka på ny i menyn Redigera nätverk. Nätverksobjektet kan nu skapas genom att lägga till namnet och prefixet:

![Skapa en FrontEnd-objektet][3]

Detta skapar ett namngivet nätverk för undernätet FrontEnd, en liknande objekt ska skapas för BackEnd-undernät. Nu kan undernäten refereras enklare efter namn i brandväggsreglerna.

För DNS-Server-objekt:

![Skapa en DNS-Server-objekt][4]

Den här enda IP-adress referensen används i en DNS-regel senare i dokumentet.

Andra nödvändiga objekt är Services-objekt. Dessa representerar RDP anslutningsportar för varje server. Eftersom det befintliga objektet i RDP-tjänsten är bunden till RDP standardporten kan 3389, nya tjänster skapas för att tillåta trafik från externa portar (8014 8026). De nya portarna kan också läggas till den befintliga RDP-tjänsten, men för att underlätta demonstration, kan du skapa en regel för varje server. Skapa en ny RDP-regel för en server. från instrumentpanelen Barracuda NG Admin klienten navigerar du till konfigurationsfliken, i avsnittet användningsinställningar Klicka på RuleSet-metod, ”tjänster” Brandväggsobjekt-menyn, bläddrar du nedåt i listan över tjänster och välj tjänsten ”RDP”. Högerklicka och välj Kopiera, högerklicka och välj Klistra in. Det finns nu en RDP-Copy1 Service-objekt som kan redigeras. Högerklicka på RDP-Copy1 och väljer Redigera serviceobjektet redigera kommer popup-fönster upp som visas här:

![Kopia av RDP standardregel][5]

Värdena kan sedan redigeras för att representera RDP-tjänst för en specifik server. För AppVM01 ovan RDP Standardregeln ska ändras för att återspegla en nytt namn, beskrivning och externa RDP-porten som används i figur 8 diagrammet (Obs: portarna ändras från standardvärdet RDP 3389 till den externa porten som används för den här specifika servern, den externa porten är AppVM01 8025) ändrade tjänsten visas nedan :

![AppVM01 regel][6]

Den här processen upprepas för att skapa RDP-tjänster för de återstående servrarna; AppVM02 DNS01 och IIS01. Skapandet av dessa tjänster gör skapa regeln enklare och mer påtagligt i nästa avsnitt.

> [!NOTE]
> En RDP-tjänst för brandväggen behövs inte av två skäl. 1) första brandväggen VM är en avbildning av Linux-baserade så SSH används på port 22 för hantering av virtuell dator i stället för RDP och 2) port 22, och två andra hanteringsportar tillåts i den första management regeln som beskrivs nedan för att tillåta anslutning till.
> 
> 

### <a name="firewall-rules-creation"></a>Skapa regler för brandvägg
Det finns tre typer av brandväggsregler som används i det här exemplet, alla har olika ikoner:

Regeln programmet omdirigera: ![omdirigera programikon][7]

Mål NAT-regel: ![mål NAT-ikon][8]

Regeln Pass: ![skicka ikon][9]

Mer information om dessa regler finns på webbplatsen Barracuda.

För att skapa följande regler (eller verifiera befintliga standardregler) börjar från instrumentpanelen klienten Barracuda NG Admin, gå till konfigurationsfliken klickar du i de användningsinställningar avsnittet RuleSet-metod. Ett rutnät som kallas ”Main regler” visas de befintliga reglerna för aktiva och inaktiva på den här brandväggen. I det övre högra hörnet i det här rutnätet är en liten, grön ”+” knappen, klicka här för att skapa en ny regel (Obs: brandväggen kan vara ”låst” för ändringar, om du ser en knapp som markerats ”låsa” och det inte går att skapa eller redigera regler, klicka på knappen för att ”låsa upp” regeluppsättningen och tillåta redigering). Om du vill redigera en befintlig regel markerar du regeln, högerklickar och väljer Redigera regel.

När reglerna skapas eller ändras, måste vara pushas till brandväggen och sedan aktiveras, om det inte görs regeln ändringarna börjar inte gälla. Push- och aktiveringsbehörigheter process beskrivs nedan information regeln beskrivningar.

Egenskaperna för varje regel som krävs för att slutföra det här exemplet beskrivs enligt följande:

* **Brandväggen Management regeln**: den här appen omdirigera regeln tillåter trafik skickas till hanteringsportar av nätverksenhet virtuella i det här exemplet Barracuda nästa generation brandvägg. Management-portar är 801, 807 och eventuellt 22. Externa och interna portar är samma (dvs. inga port translation). Detta regeln för installationsprogrammet MGMT åtkomst, är en standardregel och aktiverat som standard (i brandväggen för nästa generation av Barracuda version 6.1).
  
    ![Hantering av brandväggsregel][10]

> [!TIP]
> Käll-adressutrymme i den här regeln finns, om hantering av IP-adressintervall är känt att minska det här området skulle också minska angreppsytan till management-portar.
> 
> 

* **RDP-regler**: dessa mål NAT-regler kan hantera enskilda servrar via RDP.
  Det finns fyra viktiga fält som behövs för att skapa den här regeln:
  
  1. Källa – för att Tillåt RDP från var som helst, referensen ”någon” används i fältet källa.
  2. Service – Använd lämpligt serviceobjektet skapat tidigare i det här fallet ”AppVM01 RDP”, externa portar omdirigera till den lokala IP-adressen för servrar och till port 3386 (standardporten RDP). Den här specifika regeln är för RDP-åtkomst till AppVM01.
  3. Målet – måste vara den *lokal port i brandväggen*, ”DCHP 1 lokala-IP- eller eth0 om du använder statiska IP-adresser. Ordningstalet (eth0, eth1 osv.) kan skilja sig om en nätverksenhet har flera lokala gränssnitt. Detta är den port i brandväggen skickar ut från (kan vara samma som den mottagande porten), den faktiska routade destinationen fältet är i mållistan.
  4. Omdirigering av – det här avsnittet visar den virtuella installationen var slutligen dirigera trafiken. Den enklaste omdirigeringen är att placera IP och Port (valfritt) i fältet mållistan. Om ingen port används målport på en inkommande begäran kommer att användas (d.v.s. Ingen översättning) om en port används porten tas också skulle NAT tillsammans med IP adressen.
     
     ![RDP-brandväggsregel][11]
     
     Summan av fyra RDP-regler måste skapas: 
     
     | Regelnamn | Server | Tjänst | Mållistan |
     | --- | --- | --- | --- |
     | RDP-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Begränsa ned omfånget för käll- och -fält kommer att minska risken för angrepp. Den mest begränsat omfång som gör att funktionerna ska användas.
> 
> 

* **Regler för nätverkstrafik i programmet**: det finns två programregler för nätverkstrafik, först för webbtrafik klientdelen och andra för backend-trafik (t ex webbservern datanivå). Reglerna kommer beror på den nätverksarkitekturen (där servrarna placeras) och trafiken flödar (vilken riktning trafikflöde och vilka portar som används).
  
    Först beskrivs är frontend-regel för webbtrafik:
  
    ![Web brandväggsregel][12]
  
    Det här målet NAT-regeln tillåter faktiska programtrafik till programservern. Medan de andra reglerna tillåter för säkerhet, hantering, etc., är program vad tillåta externa användare eller tjänster att komma åt program. I det här exemplet att det finns en enda webbserver på port 80, därför den enda brandväggsregeln för programmet att omdirigera inkommande trafik till externa IP, web servrar interna IP-adress.
  
    **Obs**: att ingen port har tilldelats i fältet mållistan, vilket den inkommande porten 80 (eller 443 för tjänsten som markerats) ska användas i omdirigering av webbservern. Om webbservern lyssnar på en annan port, till exempel port 8080, fältet mållistan kunde uppdateras till 10.0.1.4:8080 för Port omdirigeringen samt.
  
    Nästa program trafik regeln är serverdelen regeln för att tillåta att webbservern ska kommunicera med AppVM01 server (men inte AppVM02) via någon tjänst:
  
    ![AppVM01 brandväggsregel][13]
  
    Den här Pass regeln kan alla IIS-servrar på undernätet Frontend att nå AppVM01 (IP-adress 10.0.2.5) med hjälp av ett protokoll för att komma åt data som krävs av webbprogrammet på alla portar.
  
    I den här skärmbilden en ”\<explicit dest\>” används i fältet mål för en obestämd 10.0.2.5 som mål. Detta kan vara något explicit enligt eller en med namnet nätverksobjekt (som har utförts i förutsättningarna för DNS-servern). Detta är upp till administratören av brandväggen om vilken metod som ska användas. Om du vill lägga till 10.0.2.5 som en Explict Desitnation och dubbelklicka på den första tomma raden under \<explicit dest\> och ange adressen i fönstret som öppnas.
  
    Med regeln skicka krävs ingen NAT eftersom det är intern trafik, så anslutningsmetoden kan vara inställd på ”Nej SNAT”.
  
    **Obs**: Source nätverk i den här regeln är alla resurser på undernätet FrontEnd om det ska bara finnas en eller ett specifikt kända antal webbservrar, nätverksobjekt gick att skapa en resurs för att vara mer specifika för de exakta IP-adresserna i stället för hela undernätet Frontend.

> [!TIP]
> Den här regeln använder tjänsten ”alla” att göra det enklare att konfigurera och använda exempelprogrammet, det gör också att ICMPv4 (ping) i en enda regel. Men rekommenderas detta inte. Portar och protokoll (”tjänsten”) bör begränsas till lägsta möjliga som gör att programmet igen att minska risken för angrepp på den här begränsningen.
> 
> 

<br />

> [!TIP]
> Även om den här regeln visar en referens till en explicit dest används, bör du använda en konsekvent metod under brandväggskonfigurationen av. Du rekommenderas att objektet namngivna nätverket används i hela för enklare läsbarhet och hanterbarhet. Den explicita dest används här endast för att visa en alternativ metod och rekommenderas vanligtvis inte (särskilt för komplexa konfigurationer).
> 
> 

* **Utgående till Internet regeln**: skicka den här regeln tillåter trafik från alla Källnätverk ska skickas till valda Målnätverk. Den här regeln är en standardregel vanligtvis redan Barracuda nästa generation brandväggen, men är i ett inaktiverat tillstånd. Högerklicka på den här regeln kan komma åt kommandot aktivera regeln. Regeln som visas här har ändrats för att lägga till de två lokala undernät som har skapats som referens i avsnittet förutsättningar i det här dokumentet till källattributet för den här regeln.
  
    ![Utgående brandväggsregel][14]
* **DNS-regel**: skicka den här regeln kan endast DNS (port 53) trafik skickas till DNS-servern. Den här regeln kan särskilt DNS för den här miljön som de flesta trafik från klientdelen till serverdelen blockeras.
  
    ![DNS-brandväggsregel][15]
  
    **Obs**: I den här skärmen som visar anslutningsmetoden ingår. Eftersom den här regeln är intern IP-adress till interna IP-adress trafik kan inga NATing krävs, detta anslutningsmetoden är inställt på ”Nej SNAT” för den här regeln Pass.
* **Undernät undernät regeln**: skicka den här regeln är en standardregel som har aktiverats och ändras för att låta alla servrar på backend-undernät kan ansluta till alla servrar i klientdelen undernät. Den här regeln är alla intern trafik så anslutningsmetoden kan vara inställd på Nej SNAT.
  
    ![Inom VNet brandväggsregel][16]
  
    **Obs**: kryssrutan dubbelriktad kontrolleras inte (är inte heller incheckad regler för de flesta), detta är viktig för den här regeln eftersom den gör detta regel ”envägs”, en anslutning kan initieras från backend-undernätet frontend-nätverk, men inte tvärtom. Om kryssrutan är markerad skulle med den här regeln göra dubbelriktad trafik som från våra logiskt diagram inte används.
* **Neka alla Trafikregel**: bör alltid sista regeln (vad gäller prioritet) och som sådan om en trafiken flödar inte matchar någon av de föregående regler tas bort av den här regeln. Det här är en standardregel och vanligtvis aktiverad behövs vanligtvis inga ändringar. 
  
    ![Regel för att neka brandväggen][17]

> [!IMPORTANT]
> När alla ovanstående regler skapas, är det viktigt att granska prioriteten för varje regel för att se till att trafik ska tillåtas eller nekas efter behov. I det här exemplet är regler i den ordning som de ska visas i rutnätet Main vidarebefordra regler i Barracuda Management-klienten.
> 
> 

## <a name="rule-activation"></a>Regeln aktivering
Med RuleSet-metod som ändrats till specificering av logiska diagram, måste den RuleSet-metod har överförts till brandväggen och sedan aktiveras.

![Brandväggen regeln aktivering][18]

Är ett kluster på knapparna i det övre högra hörnet i management-klienten. Klicka på ”Skicka ändringar” för att skicka ändrade regler i brandväggen och sedan på knappen ”Aktivera”.

Det här exemplet miljö bygget har slutförts med aktivering av brandvägg RuleSet-metod.

## <a name="traffic-scenarios"></a>Trafik scenarier
> [!IMPORTANT]
> En nyckel takeway är att komma ihåg att **alla** trafik kommer genom brandväggen. Så till fjärrskrivbord till IIS01-servern, kommer även om den är i Front End-Molntjänsten och på frontend-undernät för att komma åt den här servern vi behöver brandväggen på port 8014 till RDP, och låt brandväggen för att dirigera internt RDP-begäran till IIS01 RDP-porten. Azure portal ”Anslut” knappen fungerar inte eftersom det finns ingen direkt RDP-väg till IIS01 (som portalen kan se). Detta innebär att alla anslutningar från internet Security Service och en Port, t.ex. secscv001.cloudapp.net:xxxx.
> 
> 

Följande brandväggsregler bör vara på plats för dessa scenarier:

1. Brandväggshantering
2. RDP till IIS01
3. RDP till DNS01
4. RDP till AppVM01
5. RDP till AppVM02
6. App-trafik på webben
7. App-trafik till AppVM01
8. Utgående till Internet
9. Klientdel till DNS01
10. Intra-undernätstrafik (serverdel front end)
11. Neka alla

Verklig brandvägg RuleSet-metod har antagligen många andra regler Förutom dessa, reglerna på alla angivna brandväggen har även olika prioritetsnummer än de som visas här. Den här listan och tillhörande nummer är att tillhandahålla relevans mellan bara reglerna elva och den relativa prioriteten bland dem. Med andra ord; den faktiska brandväggen kan den ”RDP till IIS01” vara regeln tal 5, men så länge det är under ”Brandväggshantering” regeln och ovan ”RDP DNS01” regeln skulle justera att den här listan. Listan också underlätta vid den nedan scenarier för att tillåta planeringsaspekter; t.ex. ”FW regeln 9 (DNS)”. Planeringsaspekter, fyra RDP-regler kommer gemensamt kallas även, ”RDP-regler” när trafiken är inte relaterat till RDP.

Återkalla också att Nätverkssäkerhetsgrupper är på plats för inkommande trafik för internet på Frontend och Backend-undernät.

#### <a name="allowed-internet-to-web-server"></a>(Tillåts) Internet till webbservern
1. Internet användaren begär http-sida från SecSvc001.CloudApp.Net (Internet Facing Cloud Service)
2. Cloud service överför trafik via öppna slutpunkter på port 80 till brandväggens gränssnitt på 10.0.0.4:80
3. Inga NSG som tilldelats till undernätet för säkerhet, så system NSG-regler tillåter trafik för brandväggen
4. Trafik träffar interna IP-adressen för brandväggen (10.0.1.4)
5. Brandväggen börjar regeln bearbetning:
   1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
   2. FW regler 2-5 (RDP regler) inte tillämpas, gå till nästa regel
   3. FW regel 6 (App: Web) gäller, tillåts trafik, brandvägg NAT att 10.0.1.4 (IIS01)
6. Undernätet Frontend börjar bearbetning av inkommande regel:
   1. NSG regel 1 (blockera Internet) kan inte användas (den här trafiken har NAT genom brandväggen, vilket källadressen är nu i brandväggen som finns i undernät säkerhet och ses av undernätet Frontend NSG ska ”lokal” trafik och tillåts därför), flyttar till nästa regel
   2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
7. IIS01 lyssnar för webbtrafik, tar emot denna begäran och startar bearbetning av begäran
8. IIS01 försöker initierar en FTP-session till AppVM01 på Backend-undernät
9. UDR väg för undernätet Frontend gör brandväggen nästa hopp
10. Inga regler för utgående trafik på undernätet Frontend, tillåts trafik
11. Brandväggen börjar regeln bearbetning:
    1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
    2. FW regel 2-5 (RDP regler) inte tillämpas, gå till nästa regel
    3. FW regel 6 (App: Web) inte tillämpas, gå till nästa regel
    4. FW regel 7 (App: Backend) gäller, tillåts trafik, brandvägg vidarebefordrar trafik till 10.0.2.5 (AppVM01)
12. Backend-undernät börjar bearbetning av inkommande regel:
    1. NSG regel 1 (blockera Internet) inte tillämpas, gå till nästa regel
    2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
13. AppVM01 tar emot begäran och initierar sessionen och svarar
14. UDR vägen på Backend-undernät gör brandväggen nästa hopp
15. Eftersom det inte finns några utgående NSG-regler på Backend-undernät svaret tillåts
16. Eftersom detta returnerar trafik på en upprättad session skickar brandväggen svaret tillbaka till webbservern (IIS01)
17. Undernätet frontend börjar bearbetning av inkommande regel:
    1. NSG regel 1 (blockera Internet) inte tillämpas, gå till nästa regel
    2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
18. IIS-servern tar emot svaret Slutför transaktionen med AppVM01 och slutför sedan bygga HTTP-svaret, skickas HTTP-svar till begäranden
19. Eftersom det finns inga utgående NSG-regler på undernätet Frontend svaret tillåts
20. HTTP-svaret träffar brandväggen och eftersom det här är svaret på en upprättad session NAT accepteras av brandväggen
21. Brandväggen omdirigerar sedan svaret tillbaka till Internet-användare
22. Eftersom det inte finns inte utgående får NSG-regler eller UDR hopp i Klientdelens undernät svaret tillåts och Internet-användare den begärda webbsidan.

#### <a name="allowed-internet-rdp-to-backend"></a>(Tillåts) Internet RDP till serverdelen
1. Serveradministratören på internet begär RDP-session till AppVM01 via SecSvc001.CloudApp.Net:8025, där 8025 är portnumret för användaren som har tilldelats för brandväggsregeln ”RDP AppVM01”
2. Molntjänsten skickar trafik via öppna slutpunkten på port 8025 till brandväggens gränssnitt på 10.0.0.4:8025
3. Inga NSG som tilldelats till undernätet för säkerhet, så system NSG-regler tillåter trafik för brandväggen
4. Brandväggen börjar regeln bearbetning:
   1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
   2. FW regel 2 (RDP IIS) inte tillämpas, gå till nästa regel
   3. FW regel 3 (RDP DNS01) inte tillämpas, gå till nästa regel
   4. FW regel 4 (RDP AppVM01) gäller, tillåts trafik, brandvägg NAT att 10.0.2.5:3386 (RDP-porten på AppVM01)
5. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG regel 1 (blockera Internet) kan inte användas (den här trafiken har NAT genom brandväggen, vilket källadressen är nu i brandväggen som finns i undernät säkerhet och ses av Backend-undernät NSG ska ”lokal” trafik och tillåts därför), flyttar till nästa regel
   2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
6. AppVM01 lyssnar efter RDP-trafik och svarar
7. Med inga utgående NSG-regler gäller standardregler och returnera trafik tillåts
8. UDR vägar utgående trafik i brandväggen som nästa hopp
9. Eftersom detta returnerar trafik på en upprättad session skickar brandväggen svaret tillbaka till internet-användare
10. RDP-session är aktiverad
11. AppVM01 efterfrågar användarnamn lösenord

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tillåts) Web Server DNS-sökning på DNS-server
1. Web Server, IIS01, måste en datafeed på www.data.gov, men måste matcha adressen.
2. Nätverkskonfigurationen för listorna VNet DNS01 (10.0.2.4 på Backend-undernät) som den primära DNS-servern, IIS01 skickar en DNS-begäran till DNS01
3. UDR vägar utgående trafik i brandväggen som nästa hopp
4. Inga utgående NSG-reglerna är bundna till undernätet Frontend, tillåts trafik
5. Brandväggen börjar regeln bearbetning:
   1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
   2. FW regel 2-5 (RDP regler) inte tillämpas, gå till nästa regel
   3. FW reglerna 6 och 7 (Appregler) inte tillämpas, flyttar till nästa regel
   4. FW regeln 8 (till Internet) inte tillämpas, gå till nästa regel
   5. FW regel 9 (DNS) gäller, tillåts trafik, brandvägg vidarebefordrar trafik till 10.0.2.4 (DNS01)
6. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG regel 1 (blockera Internet) inte tillämpas, gå till nästa regel
   2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
7. DNS-servern tar emot begäran
8. DNS-servern har inte cachelagrade-adress och begär en rot-DNS-server på internet
9. UDR vägar utgående trafik i brandväggen som nästa hopp
10. Inga utgående NSG-regler på Backend-undernät, tillåts trafik
11. Brandväggen börjar regeln bearbetning:
    1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
    2. FW regel 2-5 (RDP regler) inte tillämpas, gå till nästa regel
    3. FW reglerna 6 och 7 (Appregler) inte tillämpas, flyttar till nästa regel
    4. FW regeln 8 (till Internet) gäller, tillåts trafik, sessionen är SNAT ut till rot-DNS-server på Internet
12. Internet-DNS-servern svarar, eftersom denna session har startats från brandväggen svaret accepteras av brandväggen
13. Eftersom det är en upprättad session vidarebefordrar brandväggen svar till den ursprungliga servern, DNS01
14. Backend-undernät börjar bearbetning av inkommande regel:
    1. NSG regel 1 (blockera Internet) inte tillämpas, gå till nästa regel
    2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
15. DNS-servern tar emot och cachelagrar svaret och svarar sedan den ursprungliga begäranden tillbaka till IIS01
16. UDR vägen på backend-undernät gör brandväggen nästa hopp
17. Det finns några utgående NSG-regler på Backend-undernät, tillåts trafik
18. Det här är en upprättad session i brandväggen, svaret vidarebefordras av brandväggen tillbaka till IIS-servern
19. Undernätet frontend börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät till undernätet Frontend, så att ingen av NSG: N regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät skulle göra att den här trafiken så att trafik tillåts
20. IIS01 tar emot svaret från DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Tillåts) Backend-servern till klientdel
1. En administratör som är inloggad på AppVM02 via RDP begär en fil direkt från IIS01 servern via Utforskaren i windows
2. UDR vägen på Backend-undernät gör brandväggen nästa hopp
3. Eftersom det inte finns några utgående NSG-regler på Backend-undernät svaret tillåts
4. Brandväggen börjar regeln bearbetning:
   1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
   2. FW regel 2-5 (RDP regler) inte tillämpas, gå till nästa regel
   3. FW reglerna 6 och 7 (Appregler) inte tillämpas, flyttar till nästa regel
   4. FW regeln 8 (till Internet) inte tillämpas, gå till nästa regel
   5. FW regel 9 (DNS) inte tillämpas, gå till nästa regel
   6. FW regeln 10 (Intra-undernät) gäller, tillåts trafik, brandvägg skickar trafik till 10.0.1.4 (IIS01)
5. Undernätet frontend börjar bearbetning av inkommande regel:
   1. NSG regel 1 (blockera Internet) inte tillämpas, gå till nästa regel
   2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
6. Under förutsättning att korrekt autentisering och auktorisering, IIS01 godkänner begäran och svarar
7. UDR väg för undernätet Frontend gör brandväggen nästa hopp
8. Eftersom det finns inga utgående NSG-regler på undernätet Frontend svaret tillåts
9. Eftersom det är en befintlig session i brandväggen svaret tillåts och brandväggen returnerar svaret på AppVM02
10. Backend-undernät börjar bearbetning av inkommande regel:
    1. NSG regel 1 (blockera Internet) inte tillämpas, gå till nästa regel
    2. Standard NSG-reglerna tillåter undernät undernätstrafik, tillåts trafik, stoppa NSG-Regelbearbetning
11. AppVM02 tar emot svaret

#### <a name="denied-internet-direct-to-web-server"></a>(Nekad) Internet direkt till webbservern
1. Internet-användare försöker komma åt webbservern, IIS01, via tjänsten FrontEnd001.CloudApp.Net
2. Eftersom det inte finns några slutpunkter som är öppen för HTTP-trafik, detta skulle inte passerar Molntjänsten och skulle nå servern
3. Om slutpunkterna öppna av någon anledning skulle NSG: N (blockera Internet) på undernätet Frontend blockera den här trafiken
4. Slutligen klientdel undernät UDR vägen skulle skicka all utgående trafik från IIS01 i brandväggen som nexthop och brandväggen skulle se detta som en asymmetrisk trafik och släpp utgående svar är därför det minst tre oberoende lager i skyddsstrategierna mellan internet och IIS01 via dess Molntjänsten hindrar obehöriga/olämplig åtkomst.

#### <a name="denied-internet-to-backend-server"></a>(Nekad) Internet till Backend-servern
1. Internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom det inte finns några slutpunkter som är öppna för filresursen, detta skulle inte klarar Molntjänsten och skulle nå servern
3. Om slutpunkterna öppna av någon anledning skulle NSG: N (blockera Internet) blockera den här trafiken
4. Slutligen UDR vägen skulle skicka all utgående trafik från AppVM01 i brandväggen som nexthop och brandväggen skulle se detta som en asymmetrisk trafik och släpp utgående svar är därför det minst tre oberoende lager i skyddsstrategierna mellan internet och AppVM01 via dess Molntjänsten hindrar obehöriga/olämplig åtkomst.

#### <a name="denied-frontend-server-to-backend-server"></a>(Nekad) Frontend-server till Backend-servern
1. Anta IIS01 har drabbats och kör skadlig kod som försöker skanna undernät Backend-servrarna.
2. Klientdelens undernät UDR vägen skulle skicka all utgående trafik från IIS01 i brandväggen som nexthop. Detta är inte något som kan ändras av avslöjade VM.
3. Brandväggen kan behandla trafiken, om begäran var AppVM01 eller DNS-server för DNS-sökning trafik potentiellt tillåts genom brandväggen (på grund av FW regler 7 och 9). All annan trafik skulle blockeras av FW regel 11 (neka alla).
4. Om avancerade hotidentifiering har aktiverats på brandväggen (som inte omfattas i det här dokumentet, finns i leverantörens dokumentation för din specifika nätverksenhet advanced threat funktioner), även trafik som skulle vara tillåten av grundläggande vidarebefordringsregler som beskrivs i det här dokumentet kan förhindras om trafiken finns kända signaturer eller mönster som flaggan en regel för avancerade hot.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Nekad) Internet-DNS-sökning på DNS-server
1. Internet-användare försöker att söka efter en intern DNS-post på DNS01 via BackEnd001.CloudApp.Net-tjänsten 
2. Eftersom det inte finns några slutpunkter som är öppen för DNS-trafik, detta skulle inte passerar Molntjänsten och skulle nå servern
3. Om slutpunkterna öppna av någon anledning skulle NSG-regeln (blockera Internet) på undernätet Frontend blockera den här trafiken
4. Slutligen serverdelsvägar undernät UDR skulle skicka all utgående trafik från DNS01 i brandväggen som nexthop och brandväggen skulle se detta som en asymmetrisk trafik och släpp utgående svar är därför det minst tre oberoende lager i skyddsstrategierna mellan internet och DNS01 via dess Molntjänsten hindrar obehöriga/olämplig åtkomst.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Nekad) Internet till SQL-åtkomst genom brandväggen
1. Internet-användare begär SQL-data från SecSvc001.CloudApp.Net (Internet Facing Cloud Service)
2. Eftersom inga slutpunkter är öppen för SQL är detta skulle inte klarar Molntjänsten och skulle nå brandväggen
3. Om SQL-slutpunkter öppna av någon anledning skulle brandväggen börja regeln bearbetning:
   1. FW regel 1 (FW Mgmt) inte tillämpas, gå till nästa regel
   2. FW regler 2-5 (RDP regler) inte tillämpas, gå till nästa regel
   3. FW regel 6 och 7 (programmet regler) inte tillämpas, flyttar till nästa regel
   4. FW regeln 8 (till Internet) inte tillämpas, gå till nästa regel
   5. FW regel 9 (DNS) inte tillämpas, gå till nästa regel
   6. FW regeln 10 (Intra-undernät) inte tillämpas, gå till nästa regel
   7. FW regel 11 (neka alla) gäller, trafik är blockerad, stoppa regel bearbetning

## <a name="references"></a>Referenser
### <a name="main-script-and-network-config"></a>Huvudskriptet och nätverkskonfiguration
Spara fullständig skript i ett PowerShell-skriptfil. Spara konfigurationen nätverk i en fil med namnet ”NetworkConf2.xml”.
Ändra användardefinierade variabler. Kör skriptet och följ brandväggen regeln installationsprogrammet anvisningarna ovan.

#### <a name="full-script"></a>Fullständig skript
Det här skriptet kommer utifrån användardefinierade variabler:

1. Ansluta till en Azure-prenumeration
2. Skapa ett nytt lagringskonto
3. Skapa ett nytt virtuellt nätverk och tre undernät som har definierats i konfigurationsfilen för nätverk
4. Skapa fem virtuella maskiner; Brandvägg för 1 och 4 windows server virtuella datorer
5. Konfigurera UDR inklusive:
   1. Skapa två nya vägtabeller
   2. Lägga till vägar i tabellerna
   3. Binda tabeller till lämpliga undernät
6. Aktivera IP-vidarebefordring på en NVA
7. Konfigurera NSG inklusive:
   1. Skapa en NSG
   2. Lägger till en regel
   3. Bindning NSG: N till lämpliga undernät

Detta PowerShell-skript ska köras lokalt på en internet-ansluten dator eller server.

> [!IMPORTANT]
> När du kör det här skriptet kanske varningar eller andra informationsmeddelanden som visas i PowerShell. Endast felmeddelanden i rött är orsaken till problem.
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
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
Spara XML-filen med uppdaterad plats och lägga till länken till den här filen till variabeln $NetworkConfigFile i skriptet ovan.

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

#### <a name="sample-application-scripts"></a>Exempelskript för programmet
Om du vill installera ett exempelprogram för det här och andra DMZ exempel något finns på följande länk: [exempelskript för programmet][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Dubbelriktad DMZ med NVA, NSG och UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Logisk vy för brandväggsregler"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Skapa en FrontEnd-objektet"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Skapa en DNS-Server-objekt"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopia av RDP standardregel"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 regel"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Omdirigerings-ikon"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Mål NAT-ikon"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Skicka ikon"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Hantering av brandväggsregel"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "RDP-brandväggsregel"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Web brandväggsregel"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "AppVM01 brandväggsregel"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Utgående brandväggsregel"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "DNS-brandväggsregel"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Inom VNet brandväggsregel"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regel för att neka brandväggen"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Brandväggen regeln aktivering"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
