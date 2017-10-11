---
title: "Testa Azure VM dataflödet i nätverket | Microsoft Docs"
description: "Lär dig mer om att testa virtuell dator i Azure dataflödet i nätverket."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: ccebc722386a19014674d7a59757a3685bd50793
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Bandbredd/dataflöde testning (NTTTCP)

När du testar nätverksprestanda genomflöde i Azure, är det bäst att använda ett verktyg som riktar sig till nätverket för testning och minimerar användningen av andra resurser som kan påverka prestanda. NTTTCP rekommenderas.

Kopiera verktyget till två virtuella Azure-datorer med samma storlek. En virtuell dator fungerar som avsändare och andra som mottagare.

#### <a name="deploying-vms-for-testing"></a>Distribuera virtuella datorer för testning
Enligt det här testet ska två virtuella datorer i samma molntjänst eller i samma Tillgänglighetsuppsättning, så att vi kan använda sina interna IP-adresser och undanta belastningsutjämnare från testet. Det är möjligt att testa med VIP-Adressen men den här typen av testning ligger utanför omfånget för det här dokumentet.
 
Anteckna MOTTAGARENS IP-adress. Vi ringer som IP ”a.b.c.r”

Notera antal kärnor på den virtuella datorn. Vi ska anropa detta ”\#num\_kärnor”
 
Kör testet NTTTCP för 300 sekunder (eller 5 minuter) på VM-avsändare och mottagare VM.

Tips: När du ställer in det här testet för första gången du försöka med en kortare period test om du vill ha feedback snabbare. När verktyget fungerar som förväntat, utöka testperioden med 300 sekunder för de mest korrekta resultat.

> [!NOTE]
> Avsändaren **och** mottagare måste ange **samma** testa varaktighet parameter (-t).

Så här testar du en enda TCP-ström för 10 sekunder:

Mottagaren parametrar: ntttcp - r -t 10 - P 1

Avsändaren parametrar: ntttcp-s10.27.33.7 -t 10 - n 1 P - 1

> [!NOTE]
> Föregående exempel ska bara användas för att bekräfta din konfiguration. Giltiga exempel tester beskrivs senare i det här dokumentet.

## <a name="testing-vms-running-windows"></a>Testa virtuella datorer som kör WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Hämta NTTTCP till de virtuella datorerna.

Hämta den senaste versionen: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Eller söka efter den om flyttas: <https://www.bing.com/search?q=ntttcp+download> \< --bör först träffar

Du kan lägga NTTTCP i separat mapp som c:\\verktyg

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Tillåt NTTTCP via Windows-brandväggen
Skapa en Tillåt-regel i Windows-brandväggen så att den NTTTCP trafiken som tas emot på mottagaren.. Det enklast att hela NTTTCP programmet efter namn snarare än för att tillåta inkommande specifika TCP-portar.

Tillåt ntttcp via Windows-brandväggen så här:

netsh advfirewall firewall Lägg till regel för programmet =\<sökväg\>\\ntttcp.exe namn = ”ntttcp”-protokollet = alla dir = i praktiken = Tillåt aktivera = yes profil = alla

Om du kopierade ntttcp.exe till exempelvis den ”c:\\verktyg” mapp, skulle detta kommandot: 

netsh advfirewall firewall Lägg till regel för programmet = c:\\verktyg\\ntttcp.exe namn = ”ntttcp”-protokollet = alla dir = i praktiken = Tillåt aktivera = yes profil = alla

#### <a name="running-ntttcp-tests"></a>Kör NTTTCP

Starta NTTTCP på mottagaren (**kör från CMD**, inte från PowerShell):

ntttcp - r-m [2\*\#num\_kärnor],\*, a.b.c.r -t 300

Om den virtuella datorn har fyra kärnor och IP-adressen 10.0.0.4, skulle det se ut så här:

ntttcp - r-m 8,\*, 10.0.0.4 -t 300


Starta NTTTCP på AVSÄNDAREN (**kör från CMD**, inte från PowerShell):

ntttcp -s-m 8,\*, 10.0.0.4 -t 300 

Vänta tills resultaten.


## <a name="testing-vms-running-linux"></a>Testa virtuella datorer som kör LINUX:

Använd nttcp för linux. Den är tillgänglig från <https://github.com/Microsoft/ntttcp-for-linux>

Kör följande kommandon för att förbereda ntttcp för linux på dina virtuella datorer på Linux virtuella datorer (både AVSÄNDAREN och mottagaren):

CentOS - Install Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Install Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Kontrollera och installera både:
``` bash
 git clone <https://github.com/Microsoft/ntttcp-for-linux>
 cd ntttcp-for-linux/src
 make && make install
```

Som i Windows-exemplet förutsätter vi att mottagaren Linux IP är 10.0.0.4

Starta NTTTCP för Linux mottagaren:

``` bash
ntttcp -r -t 300
```

Och på AVSÄNDAREN, kör:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Test längd standardvärdet är 60 sekunder om ingen tid parameter anges

## <a name="testing-between-vms-running-windows-and-linux"></a>Testa mellan virtuella datorer som kör Windows och LINUX:

På den här scenarier bör vi aktivera läget no sync testet kan köras. Detta görs med hjälp av den **-N flaggan** för Linux och **-ns flaggan** för Windows.

#### <a name="from-linux-to-windows"></a>Från Linux i Windows:

Mottagaren <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Avsändaren <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Från Windows till Linux:

Mottagaren <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Avsändaren <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```

## <a name="next-steps"></a>Nästa steg
* Beroende på resultatet, det kan finnas utrymme att [optimera genomflödet datorer](virtual-network-optimize-network-bandwidth.md) för ditt scenario.
* Lär dig mer i [Azure Virtual Network vanliga frågor (FAQ)](virtual-networks-faq.md)
