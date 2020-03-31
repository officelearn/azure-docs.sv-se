---
title: Testa Azure-nätverkssvarstid för virtuella datorer i ett virtuellt Azure-nätverk | Microsoft-dokument
description: Lär dig hur du testar nätverksfördröjning mellan virtuella Azure-datorer i ett virtuellt nätverk
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896366"
---
# <a name="test-vm-network-latency"></a>Testa svarstid för VM-nätverk

För att uppnå de mest exakta resultaten mäter du din Azure-nätverksfördröjning med ett verktyg som är utformat för uppgiften. Allmänt tillgängliga verktyg som SockPerf (för Linux) och latte.exe (för Windows) kan isolera och mäta nätverksfördröjning samtidigt som andra typer av svarstid utesluts, till exempel programfördröjning. Dessa verktyg fokuserar på den typ av nätverkstrafik som påverkar programmets prestanda (nämligen Transmission Control Protocol [TCP] och User Datagram Protocol [UDP]-trafik). 

Andra vanliga anslutningsverktyg, till exempel Ping, kan mäta svarstid, men deras resultat kanske inte representerar nätverkstrafiken som används i verkliga arbetsbelastningar. Det beror på att de flesta av dessa verktyg använder ICMP (Internet Control Message Protocol), som kan behandlas annorlunda än programtrafik och vars resultat kanske inte gäller för arbetsbelastningar som använder TCP och UDP. 

För korrekt nätverksfördröjning av de protokoll som används av de flesta program ger SockPerf (för Linux) och latte.exe (för Windows) de mest relevanta resultaten. Den här artikeln täcker båda dessa verktyg.

## <a name="overview"></a>Översikt

Genom att använda två virtuella datorer, en som avsändare och en som mottagare, skapar du en tvåvägskommunikationskanal. Med den här metoden kan du skicka och ta emot paket i båda riktningarna och mäta rundresatiden (RTT).

Du kan använda den här metoden för att mäta nätverksfördröjning mellan två virtuella datorer eller till och med mellan två fysiska datorer. Svarstidsmätningar kan vara användbara för följande scenarier:

- Upprätta ett riktmärke för nätverksfördröjning mellan de distribuerade virtuella datorerna.
- Jämför effekterna av ändringar i nätverksfördröjningen efter att relaterade ändringar har gjorts med:
  - Operativsystem (OS) eller nätverksstacksprogram, inklusive konfigurationsändringar.
  - En VM-distributionsmetod, till exempel distribuera till en tillgänglighetszon eller närhetsplaceringsgrupp (PPG).
  - Egenskaper för virtuella datorer, till exempel Accelererade nätverk eller storleksändringar.
  - Ett virtuellt nätverk, till exempel routning eller filtrering, ändras.

### <a name="tools-for-testing"></a>Verktyg för testning
Om du vill mäta svarstiden har du två olika verktygsalternativ:

* För Windows-baserade system: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* För Linux-baserade system: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Genom att använda dessa verktyg kan du se till att endast TCP- eller UDP-leveranstider för nyttolast mäts och inte ICMP (Ping) eller andra pakettyper som inte används av program och inte påverkar deras prestanda.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tips för att skapa en optimal VM-konfiguration

Tänk på följande rekommendationer när du skapar vm-konfigurationen:
- Använd den senaste versionen av Windows eller Linux.
- Aktivera accelererat nätverk för bästa resultat.
- Distribuera virtuella datorer med en [Azure-närhetsplaceringsgrupp](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
- Större virtuella datorer presterar i allmänhet bättre än mindre virtuella datorer.

### <a name="tips-for-analysis"></a>Tips för analys

När du analyserar testresultat bör du tänka på följande rekommendationer:

- Upprätta en baslinje tidigt, så snart distribution, konfiguration och optimeringar är klara.
- Jämför alltid nya resultat med en baslinje eller, på annat sätt, från ett test till ett annat med kontrollerade ändringar.
- Upprepa tester när ändringar observeras eller planeras.


## <a name="test-vms-that-are-running-windows"></a>Testa virtuella datorer som kör Windows

### <a name="get-latteexe-onto-the-vms"></a>Få latte.exe på de virtuella datorerna

Ladda ner den [senaste versionen av latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Överväg att placera latte.exe i separat mapp, till exempel *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Tillåt latte.exe via Windows Defender-brandväggen

Skapa en tillåt-regel i Windows Defender-brandväggen på *mottagaren*så att trafiken latte.exe kan komma fram. Det är enklast att tillåta hela latte.exe-programmet vid namn i stället för att tillåta specifika TCP-portar inkommande.

Tillåt latte.exe via Windows Defender-brandväggen genom att köra följande kommando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Om du till exempel kopierade latte.exe till mappen *c:\tools* är det kommandot:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Kör svarstidstester

* Starta latte.exe (kör det från CMD-fönstret på *mottagaren,* inte från PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Omkring 65 000 iterationer är tillräckligt långa för att returnera representativa resultat.

    Alla tillgängliga portnummer är bra.

    Om den virtuella datorn har en IP-adress på 10.0.0.4 skulle kommandot se ut så här:

    `latte -a 10.0.0.4:5005 -i 65100`

* Starta latte.exe (kör det från CMD-fönstret på *avsändaren),* inte från PowerShell:

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Det resulterande kommandot är detsamma som på&nbsp;mottagaren, förutom med tillägg av *-c* för att indikera att detta är *klienten*eller *avsändaren:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Vänta på resultaten. Beroende på hur långt ifrån varandra de virtuella datorerna är kan testet ta några minuter att slutföra. Överväg att börja med färre iterationer för att testa för framgång innan du kör längre tester.

## <a name="test-vms-that-are-running-linux"></a>Testa virtuella datorer som kör Linux

Om du vill testa virtuella datorer som kör Linux använder [du SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Installera SockPerf på de virtuella datorerna

På linux-datorer, både *avsändare* och *mottagare*, kör du följande kommandon för att förbereda SockPerf på de virtuella datorerna. Kommandon finns för de stora distributionerna.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>För Red Hat Enterprise Linux (RHEL)/CentOS

Kör följande kommandon:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>För Ubuntu

Kör följande kommandon:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>För alla distributioner

Kopiera, kompilera och installera SockPerf enligt följande steg:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
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

När SockPerf-installationen är klar är de virtuella datorerna redo att köra svarstidstesterna. 

Börja med att starta SockPerf på *mottagaren*.

Alla tillgängliga portnummer är bra. I det här exemplet använder vi port 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Nu när servern lyssnar kan klienten börja skicka paket till servern på den port som den lyssnar på (i det här fallet 12345).

Cirka 100 sekunder är tillräckligt lång för att returnera representativa resultat, vilket visas i följande exempel:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Vänta på resultaten. Beroende på hur långt ifrån varandra de virtuella datorerna är varierar antalet iterationer. För att testa för framgång innan du kör längre tester, överväga att börja med kortare tester på ca 5 sekunder.

I det här SockPerf-exemplet används en meddelandestorlek på 350 byte, vilket är typiskt för ett genomsnittligt paket. Du kan justera storleken högre eller lägre för att uppnå resultat som mer exakt representerar den arbetsbelastning som körs på dina virtuella datorer.


## <a name="next-steps"></a>Nästa steg
* Förbättra svarstiden med en [Azure-närhetsplaceringsgrupp](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
* Lär dig hur du [optimerar nätverk för virtuella datorer](../virtual-network/virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Läs om [hur bandbredd allokeras till virtuella datorer](../virtual-network/virtual-machine-network-throughput.md).
* Mer information finns i [Vanliga frågor och svar om Azure Virtual Network](../virtual-network/virtual-networks-faq.md).
