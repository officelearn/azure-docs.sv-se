---
title: Testa Azure VM-nätverksdataflöde
titlesuffix: Azure Virtual Network
description: Lär dig hur du testar Azure-nätverksdataflöde för virtuella datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60743093"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testning av bandbredd/dataflöde (NTTTCP)

När du testar nätverksdataflödesprestanda i Azure är det bäst att använda ett verktyg som riktar sig till nätverket för testning och minimerar användningen av andra resurser som kan påverka prestanda. NTTTCP rekommenderas.

Kopiera verktyget till två virtuella Azure-datorer av samma storlek. Den ena virtuella datorn fungerar som AVSÄNDARE och den andra som MOTTAGARE.

#### <a name="deploying-vms-for-testing"></a>Distribuera virtuella datorer för testning
I det här testet bör de två virtuella datorerna vara i antingen samma molntjänst eller samma tillgänglighetsuppsättning så att vi kan använda deras interna IPs och utesluta belastningsutjämnare från testet. Det är möjligt att testa med VIP men denna typ av testning är utanför ramen för detta dokument.

Anteckna mottagarens IP-adress. Låt oss kalla det IP "a.b.c.r"

Anteckna antalet kärnor på den virtuella datorn. Låt oss kalla\#detta\_"num kärnor"

Kör NTTTCP-testet i 300 sekunder (eller 5 minuter) på den virtuella avsändaren och mottagarens virtuella dator.

Tips: När du konfigurerar det här testet för första gången kan du prova en kortare testperiod för att få feedback tidigare. När verktyget fungerar som förväntat förlänger du testperioden till 300 sekunder för de mest exakta resultaten.

> [!NOTE]
> Avsändaren **och** mottagaren måste ange samma parametrar **för** testvaraktighet (-t).

Så här testar du en enda TCP-ström i 10 sekunder:

Mottagarparametrar: ntttcp -r -t 10 -P 1

Avsändande parametrar: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> Föregående exempel bör endast användas för att bekräfta din konfiguration. Giltiga exempel på testning behandlas senare i det här dokumentet.

## <a name="testing-vms-running-windows"></a>Testa virtuella datorer som kör WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Få NTTTCP till de virtuella datorerna.

Ladda ner den senaste versionen:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Eller sök efter den <https://www.bing.com/search?q=ntttcp+download> \< om den flyttas: -- bör först drabbas

Överväg att placera NTTTCP i\\separat mapp, till exempel c: verktyg

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Tillåt NTTTCP via Windows-brandväggen
Skapa en tillåt-regel i Windows-brandväggen på MOTTAGAREN så att NTTTCP-trafiken kan komma fram. Det är enklast att tillåta hela NTTTCP-programmet vid namn i stället för att tillåta specifika TCP-portar inkommande.

Tillåt ntttcp via Windows-brandväggen så här:

netsh advfirewall firewall add\<\>\\rule program= PATH ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Om du till exempel kopierade ntttcp.exe\\till mappen "c:tools" är det kommandot: 

netsh advfirewall brandvägg lägga regel\\\\program = c: verktyg ntttcp.exe namn = "ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Köra NTTTCP-tester

Starta NTTTCP på RECEIVER **(körs från CMD**, inte från PowerShell):

ntttcp -r -m\*\#[2 num\_kärnor],\*, a.b.c.r -t 300

Om den virtuella datorn har fyra kärnor och en IP-adress på 10.0.0.4, skulle det se ut så här:

ntttcp -r -m\*8, 10.0.0.4 -t 300


Starta NTTTCP på AVSÄNDAREN (**kör från CMD**, inte från PowerShell):

ntttcp -s -m\*8, 10.0.0.4 -t 300 

Vänta på resultaten.


## <a name="testing-vms-running-linux"></a>Testa virtuella datorer som kör LINUX:

Använd nttcp-for-linux. Den är tillgänglig från<https://github.com/Microsoft/ntttcp-for-linux>

På linux-datorer (både SENDER och RECEIVER) kör du dessa kommandon för att förbereda ntttcp-for-linux på dina virtuella datorer:

CentOS - Installera Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Installera Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Gör och installera på båda:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Som i Windows-exemplet antar vi att Linux-mottagarens IP är 10.0.0.4

Starta NTTTCP-for-Linux på RECEIVER:

``` bash
ntttcp -r -t 300
```

Och på avsändaren, kör:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Testlängden är som standard 60 sekunder om ingen tidsparameter anges

## <a name="testing-between-vms-running-windows-and-linux"></a>Testning mellan virtuella datorer som kör Windows och LINUX:

På detta scenario bör vi aktivera no-sync-läge så att testet kan köras. Detta görs med flaggan **-N** för Linux och **-ns** för Windows.

#### <a name="from-linux-to-windows"></a>Från Linux till Windows:

Mottagare \<Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Avsändare \<Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Från Windows till Linux:

Mottagare \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Avsändare \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testa molntjänstinstanser:
Du måste lägga till följande avsnitt i ditt ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Nästa steg
* Beroende på resultatet kan det finnas utrymme för att [optimera nätverksdataflödesdatorer](virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Läs om hur [bandbredd allokeras till virtuella datorer](virtual-machine-network-throughput.md)
* Läs mer med [Vanliga frågor och svar om Azure Virtual Network (Faq)](virtual-networks-faq.md)
