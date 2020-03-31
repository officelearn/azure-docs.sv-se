---
title: Systemkrav för Microsoft Azure StorSimple Virtual Array
description: Lär dig mer om programvaru- och nätverkskraven för din StorSimple Virtual Array
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 020208a8b67d248c02fc659d4dc48fa22d333839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298808"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systemkrav för StorSimple Virtual Array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de viktiga systemkraven för microsoft Azure StorSimple Virtual Array och för lagringsklienter som använder matrisen. Vi rekommenderar att du granskar informationen noggrant innan du distribuerar Ditt StorSimple-system och sedan hänvisar tillbaka till den efter behov under distribution och efterföljande åtgärd.

Systemkraven omfattar:

* **Programvarukrav för lagringsklienter** - beskriver de virtualiseringsplattformar som stöds, webbläsare, iSCSI-initierare, SMB-klienter, lägsta krav på virtuella enheter och eventuella ytterligare krav för dessa operativsystem.
* **Nätverkskrav för StorSimple-enheten** – ger information om de portar som måste vara öppna i brandväggen för att möjliggöra iSCSI-, moln- eller hanteringstrafik.

Den StorSimple-systemkravsinformation som publiceras i den här artikeln gäller endast StorSimple Virtual Arrays.

* För enheter i 8 000-serien går du till [Systemkraven för din StorSimple 8000-serieenhet](storsimple-system-requirements.md).
* För enheter i 7 000-serien går du till [Systemkraven för din StorSimple 5000-7000-serieenhet](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Programvarukrav
Programvarukraven omfattar information om webbläsare som stöds, SMB-versioner, virtualiseringsplattformar och minimikraven för virtuella enheter.

### <a name="supported-virtualization-platforms"></a>Virtualiseringsplattformar som stöds
| **Hypervisor** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 och senare |
| VMware ESXi |5,0, 5,5, 6,0 och 6,5. |

> [!IMPORTANT]
> Installera inte VMware-verktyg på din StorSimple Virtual Array; Detta resulterar i en konfiguration som inte stöds.

### <a name="virtual-device-requirements"></a>Krav på virtuella enheter
| **Komponent** | **Krav** |
| --- | --- |
| Minsta antal virtuella processorer (kärnor) |4 |
| Minsta minne (RAM) |8 GB <br> För en filserver, 8 GB för mindre än 2 miljoner filer och 16 GB för 2 - 4 miljoner filer|
| Diskutrymme<sup>1</sup> |OS-disk - 80 GB <br></br>Datadisk - 500 GB till 8 TB |
| Minsta antal nätverksgränssnitt |1 |
| Internet bandbredd<sup>2</sup> |Minsta bandbredd krävs: 5 Mbit/s <br> Rekommenderad bandbredd: 100 Mbit/s <br> Hastigheten på dataöverföringen skalas med Internetbandbredden. Till exempel tar 100 GB data 2 dagar att överföra på 5 Mbps vilket kan leda till säkerhetskopieringsfel eftersom dagliga säkerhetskopior inte skulle slutföras på en dag. Med en bandbredd på 100 Mbps kan 100 GB data överföras på 2,5 timmar.   |

<sup>1</sup> - Tunn etablerad

<sup>2</sup> - Nätverkskraven kan variera beroende på den dagliga dataförändringshastigheten. Om en enhet till exempel behöver säkerhetskopiera 10 GB eller fler ändringar under en dag kan den dagliga säkerhetskopieringen via en 5 Mbit/s-anslutning ta upp till 4,25 timmar (om data inte kunde komprimeras eller dedupliceras).

### <a name="supported-web-browsers"></a>Webbläsare som stöds
| **Komponent** | **Version** | **Ytterligare krav/anmärkningar** |
| --- | --- | --- |
| Microsoft Edge |Senaste versionen | |
| Internet Explorer |Senaste versionen |Testad med Internet Explorer 11 |
| Google Chrome |Senaste versionen |Testad med Chrome 46 |

### <a name="supported-storage-clients"></a>Lagringsklienter som stöds
Följande programvarukrav gäller för iSCSI-initierare som har åtkomst till den virtuella storsimple-matrisen (konfigurerad som en iSCSI-server).

| **Operativsystem som stöds** | **Version krävs** | **Ytterligare krav/anmärkningar** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple kan skapa tunt etablerade och fullständigt etablerade volymer. Det går inte att skapa delvis etablerade volymer. StorSimple iSCSI-volymer stöds endast för: <ul><li>Enkla volymer på Windows-diskar.</li><li>Windows NTFS för formatering av en volym.</li> |

Följande programvarukrav gäller för SMB-klienter som har åtkomst till den virtuella StorSimple-matrisen (konfigurerad som en filserver).

| **SMB-version** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Kopiera eller lagra inte filer som skyddas av Windows Encrypting File System (EFS) till filservern StorSimple Virtual Array. Detta resulterar i en konfiguration som inte stöds.


### <a name="supported-storage-format"></a>Lagringsformat som stöds
Endast Azure-blockbloloblagring stöds. Sidblobar stöds inte. Mer information [om blockblobar och sidblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Krav på nätverk
I följande tabell visas de portar som måste öppnas i brandväggen för att möjliggöra iSCSI-, SMB-, moln- eller hanteringstrafik. I den här tabellen refererar *i* eller *inkommande* till den riktning från vilken inkommande klient begär åtkomst till enheten. *Utgående* eller *utgående* refererar till i vilken riktning Din StorSimple-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| **Port nr<sup>1</sup>** | **In eller ut** | **Port-scope** | **Obligatoriskt** | **Obs!** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Ut |WAN |Inga |Utgående port används för internetåtkomst för att hämta uppdateringar. <br></br>Den utgående webbproxyn kan konfigureras av användaren. |
| TCP 443 (HTTPS) |Ut |WAN |Ja |Utgående port används för åtkomst till data i molnet. <br></br>Den utgående webbproxyn kan konfigureras av användaren. |
| UDP 53 (DNS) |Ut |WAN |I vissa fall; se anteckningar. |Den här porten krävs bara om du använder en Internetbaserad DNS-server. <br></br> Observera att om du distribuerar en filserver rekommenderar vi att du använder den lokala DNS-servern. |
| UDP 123 (NTP) |Ut |WAN |I vissa fall; se anteckningar. |Den här porten krävs bara om du använder en Internetbaserad NTP-server.<br></br> Observera att om du distribuerar en filserver rekommenderar vi att du synkroniserar tid med Active Directory-domänkontrollanterna. |
| TCP 80 (HTTP) |I |LAN |Ja |Det här är den inkommande porten för lokalt användargränssnitt på StorSimple-enheten för lokal hantering. <br></br> Observera att åtkomst till det lokala användargränssnittet via HTTP automatiskt omdirigeras till HTTPS. |
| TCP 443 (HTTPS) |I |LAN |Ja |Det här är den inkommande porten för lokalt användargränssnitt på StorSimple-enheten för lokal hantering. |
| TCP 3260 (iSCSI) |I |LAN |Inga |Den här porten används för att komma åt data via iSCSI. |

<sup>1</sup> Inga inkommande portar behöver öppnas på det offentliga Internet.

> [!IMPORTANT]
> Kontrollera att brandväggen inte ändrar eller dekrypterar någon TLS-trafik mellan StorSimple-enheten och Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler
Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönstren för att filtrera inkommande och utgående trafik. Din virtuella matris och Tjänsten StorSimple Device Manager är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönstren som är associerade med dessa program kan användas för att konfigurera brandväggsregler. Det är viktigt att förstå att url-mönstren som är associerade med dessa program kan ändras. Detta kräver i sin tur nätverksadministratören att övervaka och uppdatera brandväggsregler för din StorSimple om och när det behövs. 

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på StorSimples fasta IP-adresser, frikostigt i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> 
> * Enhets-IPs-adresser (källa) ska alltid ställas in på alla molnaktiverade nätverksgränssnitt. 
> * Mål-IP-adresser ska ställas in [på AZURE datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| URL-mönster | Komponent/funktionalitet |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple Device Manager-tjänsten<br>Tjänsten Åtkomstkontroll<br>Azure Service Bus<br>Autentiseringstjänst|
| `http://*.backup.windowsazure.com` |Enhetsregistrering |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallelse av certifikat |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-lagringskonton och övervakning |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Supportpaket |
| `https://*.data.microsoft.com` |Telemetritjänst i Windows, se [uppdateringen för kundupplevelse och diagnostiktelemetri](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Nästa steg
* [Förbereda portalen för att distribuera din Virtuella StorSimple-matris](storsimple-virtual-array-deploy1-portal-prep.md)
