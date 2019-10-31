---
title: Testa nätverks fördröjning i Azure Virtual Machine i ett Azure-Virtual Network | Microsoft Docs
titleSuffix: Azure Virtual Network latency
description: Lär dig hur du testar nätverks fördröjningen mellan virtuella Azure-datorer i ett virtuellt nätverk
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: d5efc1b802246597b4ee545b4d805e4f0d10ebb5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166569"
---
# <a name="testing-network-latency"></a>Testa nätverks fördröjning

Att mäta nätverks fördröjningen med ett verktyg som är utformat för aktiviteten ger de mest exakta resultaten. Allmänt tillgängliga verktyg som SockPerf (för Linux) och latte (för Windows) är exempel på verktyg som kan isolera och mäta nätverks svars tider och utesluta andra typer av svars tider, t. ex. program svars tid. Dessa verktyg fokuserar på den typ av nätverks trafik som påverkar program prestanda, nämligen TCP och UDP. Andra vanliga anslutnings verktyg, t. ex. ping, kan ibland användas för att mäta latens, men dessa resultat kanske inte är representativa för nätverks trafik som används i verkliga arbets belastningar. Det&#39;innebär att de flesta av dessa verktyg använder ICMP-protokollet, som kan behandlas på ett annat sätt än program trafik, och resultatet kanske inte gäller för arbets belastningar som använder TCP och UDP. För korrekt testning av nätverks fördröjning av protokoll som används av de flesta program, ger SockPerf (för Linux) och latte (för Windows) de mest relevanta resultaten. Det här dokumentet omfattar båda dessa verktyg.

## <a name="overview"></a>Översikt

Med hjälp av två virtuella datorer, en som avsändare och en som mottagare, skapas en tvåvägs kommunikations kanal för att skicka och ta emot paket i båda riktningarna för att mäta tur och retur-tiden.

Dessa steg kan användas för att mäta nätverks fördröjningen mellan två Virtual Machines (VM) eller till och med mellan två fysiska datorer. Tids fördröjnings mått kan vara användbara i följande scenarier:

- Upprätta ett riktmärke för nätverks fördröjning mellan de distribuerade virtuella datorerna
- Jämför effekterna av ändringar i nätverks fördröjningen efter att relaterade ändringar har gjorts i:
  - OS-eller nätverks stack-programvara, inklusive konfigurations ändringar
  - Distributions metod för virtuella datorer, till exempel distribution till en zon eller PPG
  - VM-egenskaper, t. ex. accelererat nätverk eller storleks ändringar
  - Virtuellt nätverk, t. ex. routning eller filtrering av ändringar

### <a name="tools-for-testing"></a>Verktyg för testning
För att mäta svars tiden har vi två olika alternativ, ett för Windows-baserade system och ett för Linux-baserade system

För Windows: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

För Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

Med dessa verktyg kan du se till att endast leverans tider för TCP-eller UDP-nyttolasten mäts och inte ICMP (ping) eller andra paket typer som inte används av program och inte påverkar deras prestanda.

### <a name="tips-for-an-optimal-vm-configuration"></a>Tips för en optimal VM-konfiguration:

- Använd den senaste versionen av Windows eller Linux
- Aktivera accelererat nätverk för bästa resultat
- Distribuera virtuella datorer med [Azure närhets placerings grupp](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Större virtuella datorer utför vanligt vis bättre än mindre virtuella datorer

### <a name="tips-for-analysis"></a>Tips för analys

- Upprätta en bas linje tidigt så snart distributionen, konfigurationen och Optimeringarna har slutförts
- Jämför alltid nya resultat med en bas linje eller på annat sätt från ett test till ett annat med kontrollerade ändringar
- Upprepa tester när ändringar observeras eller planeras


## <a name="testing-vms-running-windows"></a>Testa virtuella datorer som kör Windows:

## <a name="get-latteexe-onto-the-vms"></a>Hämta latte. exe på de virtuella datorerna

Ladda ned den senaste versionen: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Överväg att lägga till latte. exe i en separat mapp, t. ex. c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Tillåt latte. exe via Windows-brandväggen

På mottagaren skapar du en Tillåt-regel i Windows-brandväggen så att latte. exe-trafiken kan komma. Det är enklast att tillåta hela latte. exe-programmet efter namn i stället för att tillåta vissa TCP-portar inkommande.

Tillåt latte. exe via Windows-brandväggen så här:

netsh advfirewall Firewall Add Rule program =\<sökväg\>\\latte. exe Name =&quot;latte&quot; Protocol = any dir = in Action = Allow Enable = Ja Profile = ANY


Om du till exempel har kopierat latte. exe till mappen &quot;c:\\tools&quot;, är detta kommandot:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Kör latens-tester

Starta latte. exe på mottagaren (kör från CMD, inte från PowerShell):

latte – en IP-adress för &lt;mottagare&gt;:&lt;port&gt;-i &lt;iterationer&gt;

Cirka 65 k-iterationer är tillräckligt långt för att returnera representativa resultat.

Alla tillgängliga port nummer är fina.

Om den virtuella datorn har en IP-adress för 10.0.0.4 skulle den se ut så här:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Starta latte. exe på avsändaren (kör från CMD, inte från PowerShell):

latte-c-a \<mottagare IP-adress\>:\<port\>-i \<iterationer\>


Det resulterande kommandot är detsamma som på mottagaren, förutom vid tillägg av &quot;-c&quot; för att indikera att detta är &quot;klient&quot; eller avsändare:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Vänta på resultaten. Beroende på hur långt isär de virtuella datorerna är, kan det ta några minuter att slutföra. Överväg att börja med färre iterationer för att testa framgång innan du kör längre tester.



## <a name="testing-vms-running-linux"></a>Testa virtuella datorer som kör Linux

Använd SockPerf. Den är tillgänglig från [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>Installera SockPerf på de virtuella datorerna

Kör de här kommandona på de virtuella Linux-datorerna (både sändare och mottagare) för att förbereda SockPerf på dina virtuella datorer. Det finns kommandon för det större distributioner.

#### <a name="for-rhel--centos-follow-these-steps"></a>Följ dessa steg för RHEL/CentOS:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>För Ubuntu följer du de här stegen:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>För alla distributioner, kopiera, kompilera och installera SockPerf enligt följande steg:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Kör SockPerf på de virtuella datorerna

När SockPerf-installationen är klar är de virtuella datorerna redo att köra svars tids testen. 

Starta först SockPerf på mottagaren.

Alla tillgängliga port nummer är fina. I det här exemplet använder vi port 12345:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Nu när servern lyssnar kan klienten börja skicka paket till servern på den port där den lyssnar, 12345 i det här fallet.

Cirka 100 sekunder är tillräckligt lång för att returnera representativa resultat som visas i följande exempel:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Vänta på resultaten. Beroende på hur långt isär de virtuella datorerna är, varierar antalet iterationer. Överväg att börja med kortare tester på ungefär 5 sekunder för att testa framgång innan du kör längre tester.

I det här SockPerf-exemplet används 350 byte-meddelande storlek eftersom det är ett typiskt genomsnitts storleks paket. Meddelande storleken kan justeras högre eller lägre för att få resultat som bättre representerar arbets belastningen som körs på de virtuella datorerna.


## <a name="next-steps"></a>Nästa steg
* Förbättra svars tiden med [Azure närhets placerings grupp](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Lär dig mer om hur du [optimerar nätverk för virtuella datorer](../virtual-network/virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Läs om hur [bandbredd allokeras till virtuella datorer](../virtual-network/virtual-machine-network-throughput.md)
* Lär dig mer med [Azure Virtual Network vanliga frågor och svar](../virtual-network/virtual-networks-faq.md)
