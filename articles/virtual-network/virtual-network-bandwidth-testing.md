---
title: Testa Azure Virtuella nätverkets dataflöde
titlesuffix: Azure Virtual Network
description: Lär dig hur du testar nätverksdataflöde för virtuella Azure-datorn.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481408"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Bandbredd/dataflöde testning (NTTTCP)

När du testar nätverkets dataflödesprestanda i Azure, är det bäst att använda ett verktyg som riktar sig mot nätverket för testning och minimerar användningen av andra resurser som kan påverka prestanda. NTTTCP rekommenderas.

Kopiera verktyget till två virtuella Azure-datorer av samma storlek. En virtuell dator fungerar som avsändare och andra som mottagare.

#### <a name="deploying-vms-for-testing"></a>Distribuera virtuella datorer för testning
För det här testet, ska de två virtuella datorerna vara i samma molntjänst eller i samma Tillgänglighetsuppsättning så att vi kan använda sina interna IP-adresser och undanta Belastningsutjämnarna från testet. Det är möjligt att testa med VIP-Adressen men den här typen av testning ligger utanför omfånget för det här dokumentet.

Anteckna MOTTAGARENS IP-adress. Vi kan kalla den IP ”a.b.c.r”

Anteckna antalet kärnor på den virtuella datorn. Vi kallar detta ”\#num\_kärnor”

Kör testet NTTTCP för 300 sekunder (eller 5 minuter) på VM-avsändaren och mottagaren VM.

Tips: När du konfigurerar det här testet för första gången, kan du försöka en kortare period test för att få feedback tidigare. När verktyget fungerar som förväntat, kan du utöka testperioden på 300 sekunder för bästa resultat.

> [!NOTE]
> Avsändaren **och** mottagare måste ange **samma** testa varaktighet parameter (-t).

Så här testar du en enda TCP-ström för 10 sekunder:

Mottagare parametrar: ntttcp - r -t 10 - P-1

Avsändaren parametrar: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> I föregående exempel bör endast användas för att bekräfta din konfiguration. Giltiga exempel på testning beskrivs senare i det här dokumentet.

## <a name="testing-vms-running-windows"></a>Testa virtuella datorer som kör WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Hämta NTTTCP till de virtuella datorerna.

Hämta den senaste versionen: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Eller Sök efter det om flyttas: <https://www.bing.com/search?q=ntttcp+download> \< --bör först träffar

Överväg att placera NTTTCP i en separat mapp, t.ex. c:\\verktyg

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Tillåt NTTTCP via Windows-brandväggen
Skapa en Tillåt-regel i Windows-brandväggen så att den NTTTCP trafiken som tas emot på mottagaren. Det är enklast att se till att hela NTTTCP programmet efter namn i stället för att för att tillåta inkommande specifika TCP-portar.

Tillåt ntttcp via Windows-brandväggen så här:

netsh advfirewall firewall Lägg till regel för programmet =\<sökväg\>\\ntttcp.exe namn = ”ntttcp”-protokollet = alla dir = in action = Tillåt aktivera = yes profile = ANY

Exempel: Om du kopierade ntttcp.exe till den ”c:\\verktyg” mappen detta skulle vara kommandot: 

netsh advfirewall firewall lägger du till programmet för regel = c:\\verktyg\\ntttcp.exe namn = ”ntttcp”-protokollet = alla dir = in action = Tillåt enable = yes profile = ANY

#### <a name="running-ntttcp-tests"></a>Aktiva NTTTCP test

Starta NTTTCP på mottagaren (**kör från CMD**, inte från PowerShell):

ntttcp - r – m [2\*\#num\_kärnor],\*, a.b.c.r -t 300

Om den virtuella datorn har fyra kärnor och IP-adressen 10.0.0.4, skulle det se ut så här:

ntttcp -r –m 8,\*,10.0.0.4 -t 300


Starta NTTTCP på AVSÄNDAREN (**kör från CMD**, inte från PowerShell):

ntttcp -s – m 8,\*, 10.0.0.4 -t 300 

Vänta tills resultatet.


## <a name="testing-vms-running-linux"></a>Testa virtuella datorer som kör LINUX:

Använda nttcp för linux. Den är tillgänglig från <https://github.com/Microsoft/ntttcp-for-linux>

Kör dessa kommandon för att förbereda ntttcp för linux på dina virtuella datorer på Linux-datorer (både AVSÄNDAREN och mottagaren):

CentOS - installera Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Install Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Kontrollera och installera på både:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Vi antar Linux MOTTAGARENS IP-Adressen är 10.0.0.4 som Windows-exempel

Starta NTTTCP för Linux på mottagaren:

``` bash
ntttcp -r -t 300
```

Och på AVSÄNDAREN, kör:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Testa längd standardvärdet är 60 sekunder om ingen tid-parameter anges

## <a name="testing-between-vms-running-windows-and-linux"></a>Testa mellan virtuella datorer som kör Windows och LINUX:

På den här scenarier bör vi aktivera no sync-läge så att testet kan köras. Detta görs med hjälp av den **-N flaggan** för Linux och **-ns flaggan** för Windows.

#### <a name="from-linux-to-windows"></a>Från Linux och Windows:

Mottagaren \<Windows >:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Avsändaren \<Linux >:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Från Windows till Linux:

Mottagaren \<Linux >:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Avsändaren \<Windows >:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testa Cloud Service-instanser:
Du måste lägga till följande avsnitt i din ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Nästa steg
* Beroende på resultatet, det kan finnas utrymme att [optimera dataflödet nätverksdatorer](virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Läs om hur [bandbredden som allokeras till virtuella datorer](virtual-machine-network-throughput.md)
* Lär dig mer med [Azure Virtual Network, vanliga frågor (och svar FAQ)](virtual-networks-faq.md)
