---
title: Validera VPN-dataflöde till ett virtuellt Microsoft Azure-nätverk
description: Syftet med det här dokumentet är att hjälpa en användare att validera nätverksdataflödet från sina lokala resurser till en virtuell Azure-dator.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: dcf86deda32069bf9711dbeb733dc9361e22a771
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631774"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Verifiera VPN-dataflöde till ett virtuellt nätverk

Med en VPN-gatewayanslutning kan du upprätta säker, korslokal anslutning mellan ditt virtuella nätverk inom Azure och din lokala IT-infrastruktur.

Den här artikeln visar hur du validerar nätverksdataflöde från lokala resurser till en virtuell Azure-dator (VM).

> [!NOTE]
> Den här artikeln är avsedd att hjälpa till att diagnostisera och åtgärda vanliga problem. Om du inte kan lösa problemet med hjälp av följande information [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Översikt

VPN-gatewayanslutningen omfattar följande komponenter:

* Lokal VPN-enhet (Visa en lista över [validerade VPN-enheter](vpn-gateway-about-vpn-devices.md#devicetable).)
* Offentligt internet
* Azure VPN-gateway
* Azure VM

Följande diagram visar den logiska anslutningen för ett lokalt nätverk till ett virtuellt Azure-nätverk via VPN.

![Logisk anslutning av kundnätverk till MSFT-nätverk med VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Beräkna det maximala förväntade ingående/utgående

1. Bestäm programmets grundläggande dataflödeskrav.
1. Bestäm gränserna för ditt Azure VPN-gatewaydataflöde. Mer information finns i avsnittet "Gateway SKU: er" i [Om VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Bestäm vägledningen för [Azure VM-dataflöde](../virtual-machines/virtual-machines-windows-sizes.md) för din vm-storlek.
1. Bestäm bandbredden för Internet-leverantören.
1. Beräkna ditt förväntade dataflöde genom att ta minst bandbredd för antingen den virtuella datorn, VPN Gateway eller Internet-leverantören. som mäts i Megabit per sekund (/) dividerat med åtta (8).

Om det beräknade dataflödet inte uppfyller programmets grundläggande dataflödeskrav måste du öka bandbredden för den resurs som du identifierade som flaskhalsen. Information om hur du ändrar storlek på en Azure VPN-gateway finns i [Ändra en gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Om du vill ändra storlek på en virtuell dator finns i [Ändra storlek på en virtuell dator](../virtual-machines/virtual-machines-windows-resize-vm.md). Om du inte upplever den förväntade internetbandbredden kan du också kontakta Internet-leverantören.

> [!NOTE]
> VPN Gateway-dataflöde är ett aggregat av alla anslutningar från plats till plats\VNET-till-VNET eller Point-to-Site.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validera nätverksdataflödet med hjälp av prestandaverktyg

Denna validering bör utföras under icke-rusningstid, eftersom mätbelastningsmättnad för VPN-tunnelgenomströmning under testningen inte ger korrekta resultat.

Verktyget vi använder för detta test är iPerf, som fungerar på både Windows och Linux och har både klient- och serverlägen. Det är begränsat till 3Gbps för Windows virtuella datorer.

Det här verktyget utför inga läs-/skrivåtgärder till disken. Det producerar enbart egengenererad TCP-trafik från ena änden till den andra. Den genererar statistik baserat på experiment som mäter den tillgängliga bandbredden mellan klient- och servernoder. När du testar mellan två noder fungerar en nod som server och den andra noden fungerar som en klient. När det här testet är slutfört rekommenderar vi att du vänder på rollerna för noderna för att testa både uppladdning och hämta dataflöde på båda noderna.

### <a name="download-iperf"></a>Ladda ner iPerf

Ladda ner [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Mer information finns [i iPerf-dokumentation](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > De produkter från tredje part som beskrivs i den här artikeln tillverkas av företag som är oberoende av Microsoft. Microsoft lämnar inga garantier, underförstådda eller på annat sätt, om dessa produkters prestanda eller tillförlitlighet.

### <a name="run-iperf-iperf3exe"></a>Kör iPerf (iperf3.exe)

1. Aktivera en NSG/ACL-regel som tillåter trafik (för testning av offentliga IP-adresser på Azure VM).

1. Aktivera ett brandväggsundantag för port 5001 på båda noderna.

   **Windows:** Kör följande kommando som administratör:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Om du vill ta bort regeln när testningen är klar kör du det här kommandot:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux-avbildningar har tillåtande brandväggar. Om det finns ett program som lyssnar på en port tillåts trafiken. Anpassade bilder som är säkrade kan behöva portar öppnas explicit. Vanliga Linux OS-lager `iptables`brandväggar inkluderar , `ufw`, eller `firewalld`.

1. På servernoden ändrar du till katalogen där iperf3.exe extraheras. Kör sedan iPerf i serverläge och ställ in den på att lyssna på port 5001 som följande kommandon:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 kan anpassas för att ta hänsyn till särskilda brandväggsbegränsningar i din miljö.

1. På klientnoden ändrar du till katalogen där iperf-verktyget extraheras och kör sedan följande kommando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Klienten dirigerar trettio sekunders trafik på port 5001, till servern. Flaggan '-P' anger att vi gör 32 samtidiga anslutningar till servernoden.

   Följande skärm visar utdata från det här exemplet:

   ![Resultat](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (VALFRITT) Om du vill bevara testresultaten kör du det här kommandot:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. När du har slutfört föregående steg, kör samma steg med rollerna omvända, så att servernoden nu blir klientnoden och vice versa.

> [!Note]
> Iperf är inte det enda verktyget. [NTTTCP är en alternativ lösning för testning](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing).

## <a name="test-vms-running-windows"></a>Testa virtuella datorer som kör Windows

### <a name="load-latteexe-onto-the-vms"></a>Ladda Latte.exe på de virtuella datorerna

Ladda ner den senaste versionen av [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Överväg att placera Latte.exe i separat mapp, till exempel`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Tillåt Latte.exe genom Windows-brandväggen

Skapa en tillåt-regel i Windows-brandväggen på mottagaren så att trafiken Latte.exe kan komma fram. Det är enklast att tillåta hela Latte.exe-programmet vid namn i stället för att tillåta specifika TCP-portar inkommande.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Tillåt Latte.exe genom Windows-brandväggen så här

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Om du till exempel kopierade latte.exe till mappen "c:\tools" är det kommandot

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Kör svarstidstester

Starta latte.exe på RECEIVER (kör från CMD, inte från PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Runt 65k iterationer är tillräckligt länge för att returnera representativa resultat.

Alla tillgängliga portnummer är bra.

Om den virtuella datorn har en IP-adress på 10.0.0.4, skulle det se ut så här

`latte -c -a 10.0.0.4:5005 -i 65100`

Starta latte.exe på AVSÄNDAREN (körs från CMD, inte från PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Det resulterande kommandot är detsamma som på mottagaren förutom med tillägget av "-c" för att indikera att detta är "klienten" eller avsändaren

`latte -c -a 10.0.0.4:5005 -i 65100`

Vänta på resultaten. Beroende på hur långt ifrån varandra de virtuella datorerna är det kan det ta några minuter att slutföra. Överväg att börja med färre iterationer för att testa för framgång innan du kör längre tester.

## <a name="test-vms-running-linux"></a>Testa virtuella datorer som kör Linux

Använd [SockPerf](https://github.com/mellanox/sockperf) för att testa virtuella datorer.

### <a name="install-sockperf-on-the-vms"></a>Installera SockPerf på de virtuella datorerna

På linux-datorer (både SENDER och RECEIVER) kör du dessa kommandon för att förbereda SockPerf på dina virtuella datorer:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Installera GIT och andra användbara verktyg

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Installera GIT och andra användbara verktyg

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - alla

Från bash kommandorad (förutsätter git är installerat)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Gör är långsammare, kan ta flera minuter

`make`

Gör installationen går snabbt

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Kör SockPerf på de virtuella datorerna

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Exempelkommandon efter installationen. Server/mottagare - förutsätter att serverns IP är 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Klient - förutsätter serverns IP är 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Kontrollera att det inte finns några mellanliggande hopp (t.ex. virtuell installation) under dataflödestestningen mellan den virtuella datorn och gatewayen.
> Om det finns dåliga resultat (i termer av övergripande genomströmning) som kommer från iPERF/ NTTTCP-testerna ovan, se följande artikel för att förstå de viktigaste faktorerna bakom de möjliga bakomliggande orsakerna till problemet:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

I synnerhet analys av paket fånga spår (Wireshark / Network Monitor) samlas in parallellt från klient och server under dessa tester kommer att bidra till bedömningar av dåliga resultat. Dessa spår kan omfatta paketförlust, hög latens, MTU-storlek. fragmentering, TCP 0-fönster, Ur funktion-fragment och så vidare.

## <a name="address-slow-file-copy-issues"></a>Åtgärda problem med långsam filkopiering

Även om det totala dataflödet som bedömts med föregående steg (iPERF/NTTTCP/etc..) var bra, kan det uppstå långsam filstektiv när du antingen använder Utforskaren eller drar och släpper igenom en RDP-session. Detta problem beror normalt på en eller båda av följande faktorer:

* Filkopieringsprogram, till exempel Utforskaren och RDP, använder inte flera trådar när du kopierar filer. För bättre prestanda, använd ett flertrådat filkopieringsprogram som [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) för att kopiera filer med 16 eller 32 trådar. Om du vill ändra trådnumret för filkopiering i Richcopy klickar du på**Alternativ för Åtgärdskopia** >  **Action** > **Filkopia**.

   ![Problem med långsam filkopiering](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Inte alla program fungerar samma, och inte alla program / process använder alla trådar. Om du kör testet kan du se vissa trådar vara tomma och kommer inte att ge korrekta dataflödesresultat.
   > För att kontrollera din program filöverföring prestanda, använd flera trådar genom att öka # av tråd i följd eller minska för att hitta den optimala dataflödet av programmet eller filöverföring.

* Otillräcklig läs-/skrivhastighet för VM-disken. Mer information finns i [Felsökning av Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Lokalt externt gränssnitt för externt vänt gränssnitt

Nämnde undernäten i lokala intervall som du vill att Azure ska nå via VPN på Lokal nätverksgateway. Samtidigt definierar du VNET-adressutrymmet i Azure till den lokala enheten.

* **Ruttbaserad gateway**: Principen eller trafikväljaren för ruttbaserade VPN är konfigurerade som valfria (eller jokertecken).

* **Principbaserad gateway:** Principbaserade VPN krypterar och dirigerar paket via IPsec-tunnlar baserat på kombinationer av adressprefix mellan det lokala nätverket och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

* **UsePolicyBasedTrafficSelector-anslutningar:** ("UsePolicyBasedTrafficSelectors" för att $True på en anslutning konfigurerar Azure VPN-gatewayen för att ansluta till principbaserad VPN-brandvägg lokalt. Om du aktiverar PolicyBasedTrafficSelectors måste du se till att din VPN-enhet har de matchande trafikväljare som definierats med alla kombinationer av dina lokala nätverksprefix (lokal nätverksgateway) till och från Azures virtuella nätverksprefix, i stället för valfria.

Olämplig konfiguration kan leda till frekventa frånkopplingar i tunneln, paketdroppar, dåligt dataflöde och svarstid.

## <a name="check-latency"></a>Kontrollera svarstid

Du kan kontrollera svarstiden med hjälp av följande verktyg:

* WinMTR (på andra sätt)
* Tcptraceroute
* `ping`och `psping` (Dessa verktyg kan ge en bra uppskattning av RTT, men de kan inte användas i alla fall.)

![Kontrollera svarstid](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Om du märker en hög latens spik på någon av hopp innan du anger MS Network stamnät, kanske du vill fortsätta med ytterligare undersökningar med din Internet-leverantör.

Om en stor, ovanlig latens spik märks från humle inom "msn.net", kontakta MS support för ytterligare undersökningar.

## <a name="next-steps"></a>Nästa steg

Mer information eller hjälp finns på följande länk:

* [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
