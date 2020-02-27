---
title: Felsöka Azure HPC cache NFS-lagrings mål
description: Tips för att undvika och åtgärda konfigurations fel och andra problem som kan orsaka fel när du skapar ett NFS-lagrings mål
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652092"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Felsök problem med NAS-konfiguration och NFS-lagring

Den här artikeln innehåller lösningar på några vanliga konfigurations fel och andra problem som kan förhindra att Azure HPC cache lägger till ett NFS-lagringssystem som ett lagrings mål.

Den här artikeln innehåller information om hur du kontrollerar portar och hur du aktiverar rot åtkomst till ett NAS-system. Den innehåller också detaljerad information om mindre vanliga problem som kan orsaka att NFS-lagrings mål inte kan skapas.

> [!TIP]
> Innan du använder den här guiden måste du läsa [kraven för NFS-lagrings mål](hpc-cache-prereqs.md#nfs-storage-requirements).

Om lösningen på problemet inte ingår här kan du [öppna ett support ärende](hpc-cache-support-ticket.md) så att Microsoft-tjänsten och supporten kan arbeta tillsammans med dig för att undersöka och lösa problemet.

## <a name="check-port-settings"></a>Kontrol lera port inställningar

Azure HPC-cachen behöver Läs-/skriv åtkomst till flera UDP/TCP-portar på backend-serverns NAS-lagringssystem. Se till att dessa portar är tillgängliga i NAS-systemet och även att trafiken tillåts till dessa portar genom brand väggar mellan lagrings systemet och cache-undernätet. Du kan behöva arbeta med brand Väggs-och nätverks administratörer för ditt data Center för att verifiera konfigurationen.

Portarna skiljer sig från lagrings system från olika leverantörer, så kontrol lera systemets krav när du konfigurerar ett lagrings mål.

I allmänhet behöver cacheminnet åtkomst till dessa portar:

| Protocol | Port  | Tjänst  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | monterad   |
| TCP/UDP  | 4047  | status   |

Om du vill veta vilka portar som behövs för systemet använder du följande ``rpcinfo``-kommando. Det här kommandot nedan visar portarna och formaterar relevanta resultat i en tabell. (Använd systemets IP-adress i stället för *< storage_IP >* term.)

Du kan skicka det här kommandot från valfri Linux-klient som har NFS-infrastruktur installerad. Om du använder en klient i klustrets undernät kan du också kontrol lera anslutningen mellan under nätet och lagrings systemet.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Kontrol lera att alla portar som returneras av ``rpcinfo``s frågan tillåter obegränsad trafik från Azure HPC-cachens undernät.

Kontrol lera de här inställningarna både i själva NAS: en och på eventuella brand väggar mellan lagrings systemet och cache-undernätet.

## <a name="check-root-access"></a>Kontrol lera rot åtkomst

Azure HPC-cachen behöver åtkomst till lagrings systemets export för att skapa lagrings målet. Mer specifikt monteras exporten som användar-ID 0.

Olika lagrings system använder olika metoder för att aktivera den här åtkomsten:

* Linux-servrar lägger vanligt vis till ``no_root_squash`` till den exporterade sökvägen i ``/etc/exports``.
* NetApp och EMC-system styr vanligt vis åtkomst med export regler som är kopplade till vissa IP-adresser eller nätverk.

Om du använder export regler måste du komma ihåg att cachen kan använda flera olika IP-adresser från cache-undernätet. Tillåt åtkomst från alla möjliga IP-adresser för undernät.

Arbeta med din NAS-lagringsenhet för att aktivera rätt åtkomst nivå för cacheminnet.

### <a name="allow-root-access-on-directory-paths"></a>Tillåt rot åtkomst på katalog Sök vägar
<!-- linked in prereqs article -->

För NAS-system som exporterar hierarkiska kataloger behöver Azure HPC-cache rot åtkomst till varje export nivå.

Ett system kan till exempel Visa tre exporter som följande:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Export ``/ifs/accounting/payroll`` är underordnad ``/ifs/accounting``och ``/ifs/accounting`` är i själva fall underordnad ``/ifs``.

Om du lägger till ``payroll`` exportera som ett HPC cache-lagrings mål monterar cachen faktiskt ``/ifs/`` och får åtkomst till löne katalogen därifrån. Så att Azure HPC-cachen behöver rot åtkomst till ``/ifs`` för att få åtkomst till ``/ifs/accounting/payroll`` export.

Detta krav är relaterat till hur cacheminnet indexerar filer och undviker fil kollisioner med hjälp av fil referenser som lagrings systemet tillhandahåller.

Ett NAS-system med hierarkiska exporter kan ge olika fil referenser för samma fil om filen hämtas från olika exporter. En klient kan till exempel montera ``/ifs/accounting`` och komma åt filen ``payroll/2011.txt``. En annan klient monterar ``/ifs/accounting/payroll`` och kommer åt filen ``2011.txt``. Beroende på hur lagrings systemet tilldelar fil referenser, kan dessa två klienter få samma fil med olika fil referenser (en för ``<mount2>/payroll/2011.txt`` och en för ``<mount3>/2011.txt``).

Server dels lagrings systemet behåller interna alias för fil referenser, men Azure HPC-cachen kan inte avgöra vilka fil referenser i index referensen till samma objekt. Det är därför möjligt att cachen kan ha olika skrivningar i cacheminnet för samma fil och tillämpa ändringarna på fel sätt eftersom det inte vet att de är samma fil.

För att undvika den här möjliga filkollisionen för filer i flera exporter monterar Azure HPC cache automatiskt den mest tillgängliga exporten i sökvägen (``/ifs`` i exemplet) och använder fil referensen som har angetts från exporten. Om flera exporter använder samma bas Sök väg behöver Azure HPC-cache rot åtkomst till den sökvägen.

## <a name="enable-export-listing"></a>Aktivera export av lista
<!-- link in prereqs article -->

NAS: en måste ange sin export när Azure HPC-cachen efterfrågar den.

På de flesta NFS-lagrings system kan du testa detta genom att skicka följande fråga från en Linux-klient: ``showmount -e <storage IP address>``

Använd en Linux-klient från samma virtuella nätverk som din cache, om möjligt.

Om kommandot inte visar en lista över exporten, har cachen problem med att ansluta till lagrings systemet. Arbeta med din NAS-leverantör för att aktivera export av listor.

## <a name="adjust-vpn-packet-size-restrictions"></a>Ändra storleks begränsningar för VPN-paket
<!-- link in prereqs article -->

Om du har en VPN-anslutning mellan cachen och NAS-enheten kan VPN-nätverket blockera byte i full storlek 1500 byte. Du kan ha det här problemet om stora byten mellan NAS och Azure HPC-instansen inte slutförs, men mindre uppdateringar fungerar som förväntat.

Det finns inget enkelt sätt att avgöra om systemet har det här problemet om du inte känner till informationen om VPN-konfigurationen. Här följer några metoder som kan hjälpa dig att söka efter det här problemet.

* Använd paket-sniffer på båda sidor av VPN för att identifiera vilka paket som överförs.
* Om ditt VPN tillåter ping-kommandon kan du testa att skicka ett paket med full storlek.

  Kör ett ping-kommando via VPN till NAS med dessa alternativ. (Använd lagrings systemets IP-adress i stället för *< storage_IP >* värde.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Detta är alternativen i kommandot:

  * ``-M do``-Fragmentera inte
  * ``-c 1`` – Skicka bara ett paket
  * ``-s 1472`` – ange nytto lastens storlek till 1472 byte. Detta är den maximala nytto lasten för ett 1500 byte-paket efter det att Ethernet-belastningen har ändrats.

  Ett lyckat svar ut så här:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Om pingen Miss lyckas med 1472 byte kan du behöva konfigurera MSS-ihopfogning på VPN för att fjärrdatorn ska kunna identifiera den maximala ram storleken. Läs mer i [dokumentationen för VPN gateway IPSec/IKE-parametrarna](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) .

## <a name="check-for-acl-security-style"></a>Kontrol lera formatet ACL-säkerhet

Vissa NAS-system använder en hybrid säkerhets stil som kombinerar åtkomst kontrol listor (ACL: er) med traditionell POSIX-eller UNIX-säkerhet.

Om systemet rapporterar sin säkerhets stil som UNIX eller POSIX utan att inkludera förkortningen "ACL", påverkar det här problemet inte.

För system som använder ACL: er måste Azure HPC-cachen spåra ytterligare användarspecifika värden för att kontrol lera fil åtkomsten. Detta görs genom att aktivera cache för åtkomst. Det finns ingen användar kontroll för att aktivera åtkomst-cachen, men du kan öppna ett support ärende om du vill begära att den aktive ras för de lagrings mål som påverkas i ditt cache-system.

## <a name="next-steps"></a>Nästa steg

Om du har problem som inte har åtgärd ATS i den här artikeln [öppnar du ett support ärende](hpc-cache-support-ticket.md) för att få expert hjälp.
