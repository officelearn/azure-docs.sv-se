---
title: Felsöka Azure HPC Cache NFS-lagringsmål
description: Tips för att undvika och åtgärda konfigurationsfel och andra problem som kan orsaka fel när du skapar ett NFS-lagringsmål
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77652092"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Felsöka problem med NAS-konfiguration och NFS-lagringsmål

Den här artikeln innehåller lösningar för några vanliga konfigurationsfel och andra problem som kan förhindra att Azure HPC-cachen lägger till ett NFS-lagringssystem som ett lagringsmål.

Den här artikeln innehåller information om hur du kontrollerar portar och hur du aktiverar root-åtkomst till ett NAS-system. Den innehåller också detaljerad information om mindre vanliga problem som kan orsaka NFS lagring mål skapas misslyckas.

> [!TIP]
> Innan du använder den här guiden bör du läsa [förutsättningarna för NFS-lagringsmål](hpc-cache-prereqs.md#nfs-storage-requirements).

Om lösningen på ditt problem inte ingår här öppnar du [en supportbiljett](hpc-cache-support-ticket.md) så att Microsoft Service and Support kan arbeta med dig för att undersöka och lösa problemet.

## <a name="check-port-settings"></a>Kontrollera portinställningar

Azure HPC Cache behöver läs-/skrivåtkomst till flera UDP/TCP-portar i det bakre NAS-lagringssystemet. Se till att dessa portar är tillgängliga på NAS-systemet och att trafik tillåts till dessa portar genom brandväggar mellan lagringssystemet och cacheundernätet. Du kan behöva arbeta med brandväggar och nätverksadministratörer för att datacentret ska kunna verifiera den här konfigurationen.

Portarna är olika för lagringssystem från olika leverantörer, så kontrollera systemets krav när du ställer in ett lagringsmål.

I allmänhet behöver cachen åtkomst till dessa portar:

| Protokoll | Port  | Tjänst  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind (rpcbind)  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr (nlockmgr) |
| TCP/UDP  | 4046  | monterad   |
| TCP/UDP  | 4047  | status   |

Om du vill veta de specifika portar som behövs för ditt system använder du följande ``rpcinfo`` kommando. Det här kommandot nedan visar portarna och formaterar relevanta resultat i en tabell. (Använd systemets IP-adress i stället för *<storage_IP>* sikt.)

Du kan utfärda det här kommandot från alla Linux-klienter som har NFS-infrastruktur installerad. Om du använder en klient i klustrets undernät kan den också hjälpa till att verifiera anslutningen mellan undernätet och lagringssystemet.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Kontrollera att alla portar som ``rpcinfo`` returneras av frågan tillåter obegränsad trafik från Azure HPC-cachens undernät.

Kontrollera dessa inställningar både på själva NAS:en och på eventuella brandväggar mellan lagringssystemet och cacheundernätet.

## <a name="check-root-access"></a>Kontrollera root-åtkomst

Azure HPC Cache behöver åtkomst till ditt lagringssystems export för att skapa lagringsmålet. Närmare bestämt monteras exporten som användar-ID 0.

Olika lagringssystem använder olika metoder för att möjliggöra den här åtkomsten:

* Linux-servrar ``no_root_squash`` lägger vanligtvis till ``/etc/exports``den exporterade sökvägen i .
* NetApp- och EMC-system styr vanligtvis åtkomst med exportregler som är kopplade till specifika IP-adresser eller nätverk.

Om du använder exportregler bör du komma ihåg att cachen kan använda flera olika IP-adresser från cacheundernätet. Tillåt åtkomst från hela skalan av möjliga IP-adresser i undernätet.

Arbeta med din NAS-lagringsleverantör för att aktivera rätt åtkomstnivå för cachen.

### <a name="allow-root-access-on-directory-paths"></a>Tillåt rotåtkomst på katalogsökvägar
<!-- linked in prereqs article -->

För NAS-system som exporterar hierarkiska kataloger behöver Azure HPC-cache rotåtkomst till varje exportnivå.

Ett system kan till exempel visa tre exporter som dessa:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Exporten ``/ifs/accounting/payroll`` är ett ``/ifs/accounting``barn ``/ifs/accounting`` till , ``/ifs``och är i sig ett barn till .

Om du ``payroll`` lägger till exporten som ett HPC-cachelagringsmål monteras ``/ifs/`` och kommer cachen faktiskt åt lönekatalogen därifrån. Så Azure HPC Cache ``/ifs`` behöver root-åtkomst ``/ifs/accounting/payroll`` till för att komma åt exporten.

Det här kravet är relaterat till hur cachen indexerar filer och undviker filkollisioner med hjälp av filreferenser som lagringssystemet tillhandahåller.

Ett NAS-system med hierarkisk export kan ge olika filreferenser för samma fil om filen hämtas från olika exporter. En klient kan till ``/ifs/accounting`` exempel montera ``payroll/2011.txt``och komma åt filen . En annan ``/ifs/accounting/payroll`` klient monterar ``2011.txt``och kommer åt filen . Beroende på hur lagringssystemet tilldelar filhandtag kan dessa två klienter ta emot samma ``<mount2>/payroll/2011.txt`` fil ``<mount3>/2011.txt``med olika filhandtag (en för och en för ).

Backend-lagringssystemet behåller interna alias för filreferenser, men Azure HPC-cachen kan inte avgöra vilken fil som hanterar i indexreferensen för samma objekt. Så det är möjligt att cachen kan ha olika skrivningar cachelagrade för samma fil, och tillämpa ändringarna felaktigt eftersom det inte vet att de är samma fil.

För att undvika denna möjliga filkollision för filer i flera exporter monterar Azure``/ifs`` HPC-cache automatiskt den ytligaste tillgängliga exporten i sökvägen (i exemplet) och använder filreferensen som ges från den exporten. Om flera exporter använder samma bassökväg behöver Azure HPC-cache rotåtkomst till sökvägen.

## <a name="enable-export-listing"></a>Aktivera exportlista
<!-- link in prereqs article -->

NAS måste lista sin export när Azure HPC Cache frågar den.

På de flesta NFS-lagringssystem kan du testa detta genom att skicka följande fråga från en Linux-klient:``showmount -e <storage IP address>``

Använd om möjligt en Linux-klient från samma virtuella nätverk som cachen.

Om det kommandot inte visar exporten har cachen problem med att ansluta till ditt lagringssystem. Arbeta med DIN NAS-leverantör för att aktivera exportnotering.

## <a name="adjust-vpn-packet-size-restrictions"></a>Justera begränsningar för VPN-paketstorlek
<!-- link in prereqs article -->

Om du har ett VPN mellan cachen och NAS-enheten kan VPN blockera Ethernet-paket med full storlek. Du kan ha det här problemet om stora utbyten mellan NAS och Azure HPC Cache-instansen inte slutförs, men mindre uppdateringar fungerar som förväntat.

Det finns inte ett enkelt sätt att avgöra om ditt system har detta problem om du inte vet detaljerna i din VPN-konfiguration. Här är några metoder som kan hjälpa dig att söka efter det här problemet.

* Använd paketsniffare på båda sidor av VPN för att identifiera vilka paket som överförs.
* Om din VPN tillåter ping-kommandon kan du testa att skicka ett fullstort paket.

  Kör ett ping-kommando över VPN till NAS med dessa alternativ. (Använd ditt ip-adress för lagringssystemet i stället för *<storage_IP>* värde.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Dessa är alternativen i kommandot:

  * ``-M do``- Fragmentera inte
  * ``-c 1``- Skicka bara ett paket
  * ``-s 1472``- Ställ in storleken på nyttolasten till 1472 byte. Det här är den maximala storleken för ett paket med 1 500 byte efter att ha redovisat Omkostnader för Ethernet.

  Ett lyckat svar ut så här:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Om ping misslyckas med 1472 byte kan du behöva konfigurera MSS-fastspänning på VPN för att fjärrsystemet ska upptäcka den maximala bildrutestorleken. Läs [dokumentationen för VPN Gateway IPsec/IKE-parametrar](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) om du vill veta mer.

## <a name="check-for-acl-security-style"></a>Sök efter ACL-säkerhetsformat

Vissa NAS-system använder en hybridsäkerhetsstil som kombinerar åtkomstkontrollistor (ACL) med traditionell POSIX- eller UNIX-säkerhet.

Om systemet rapporterar sin säkerhetsstil som UNIX eller POSIX utan att inkludera förkortningen "ACL" påverkar det här problemet inte dig.

För system som använder ACL:er måste Azure HPC-cachen spåra ytterligare användarspecifika värden för att styra filåtkomsten. Detta görs genom att aktivera en åtkomstcache. Det finns ingen användarinriktad kontroll för att aktivera åtkomstcachen, men du kan öppna en supportbiljett för att begära att den aktiveras för de berörda lagringsmålen i cachesystemet.

## <a name="next-steps"></a>Nästa steg

Om du har ett problem som inte åtgärdades i den här artikeln [öppnar du en supportbiljett](hpc-cache-support-ticket.md) för att få experthjälp.
