---
title: Systemkrav för Microsoft Azure StorSimple Virtual Array | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.openlocfilehash: 7e5cf79613bdbd62427e99a0d1f2aa29ed8f85be
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245199"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systemkrav för StorSimple Virtual Array
## <a name="overview"></a>Översikt
Den här artikeln beskriver viktiga systemkraven för Microsoft Azure StorSimple Virtual Array och för lagringsklienter matrisen. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din StorSimple-systemet och referera tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkraven är:

* **Programvarukrav för storage-klienter** -beskriver stöds virtualiseringsplattformar, webbläsare, iSCSI-initierare, SMB-klienter, krav på minsta virtuella enheten och ytterligare krav för dessa operativsystem.
* **Nätverkskrav för StorSimple-enheten** – innehåller information om vilka portar som måste vara öppna i brandväggen för att tillåta för iSCSI, i molnet eller hantering av trafik.

StorSimple-system kravinformation publiceras i den här artikeln gäller bara för StorSimple Virtual Array.

* För enheter i 8000-serien, går du till [systemkrav för din enhet i StorSimple 8000-serien](storsimple-system-requirements.md).
* För enheter för 7000-serien, går du till [systemkrav för StorSimple 5000-7000-serien enheten](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Programvarukrav
Programvarukrav innehålla information om vilka webbläsare, SMB-versioner, virtualiseringsplattformar och krav för minsta virtuella enheter.

### <a name="supported-virtualization-platforms"></a>Stöds virtualiseringsplattformar
| **Hypervisor-programmet** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 och senare |
| VMware ESXi |5.0, 5.5, 6.0 och 6.5. |

> [!IMPORTANT]
> Installera inte VMware-verktyg på StorSimple Virtual Array; Detta resulterar i en konfiguration som inte stöds.

### <a name="virtual-device-requirements"></a>Krav för virtuella enheter
| **Komponent** | **Krav** |
| --- | --- |
| Minsta antal virtuella processorer (kärnor) |4 |
| Lägsta minne (RAM) |8 GB <br> För en filserver, 8 GB för mindre än 2 miljoner filer och 16 GB för 2-4 miljoner filer|
| Diskutrymme<sup>1</sup> |OS-disk – 80 GB <br></br>Datadisk - 500 GB till 8 TB |
| Minsta antal nätverksgränssnitt |1 |
| Internetbandbredd<sup>2</sup> |Minsta bandbredd som krävs: 5 Mbit/s <br> Rekommenderade bandbredd: 100 Mbit/s <br> Hur snabbt data transfer skalas med Internet-bandbredd. 100 GB data tar till exempel 2 dagar att överföra med 5 Mbit/s som kan leda till Säkerhetskopieringsfel eftersom inte skulle att slutföra dagliga säkerhetskopior under en dag. 100 GB data kan överföras i 2,5 timmar med en bandbredd på 100 Mbit/s.   |

<sup>1</sup> – tunt etablerats

<sup>2</sup> -nätverkskrav kan variera beroende på den dagliga förändringstakten för data. Till exempel om en enhet behöver säkerhetskopiera 10 GB eller fler ändringar under en dag, kan sedan daglig säkerhetskopiering över en 5 Mbit/s-anslutning ta upp till 4,25 timmar (om data inte kan komprimeras eller deduplicerad).

### <a name="supported-web-browsers"></a>Webbläsare som stöds
| **Komponent** | **Version** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Microsoft Edge |Senaste versionen | |
| Internet Explorer |Senaste versionen |Testats med Internet Explorer 11 |
| Google Chrome |Senaste versionen |Testats med Chrome 46 |

### <a name="supported-storage-clients"></a>Stöds storage-klienter
Följande programvarukrav finns för iSCSI-initierare som har åtkomst till din StorSimple Virtual Array (konfigurerat som en iSCSI-server).

| **Operativsystem som stöds** | **Version som krävs** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012 R2 |StorSimple kan skapa tunt allokerade och helt allokerade volymer. Det går inte att skapa delvis allokerade volymer. StorSimple iSCSI-volymer stöds endast för: <ul><li>Enkla volymer på Windows-standarddiskar.</li><li>Windows NTFS för att formatera en volym.</li> |

Följande krav är för SMB-klienter som har åtkomst till din StorSimple Virtual Array (konfigureras som en filserver).

| **SMB-Version** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Kopiera inte eller lagra filer som skyddas av Windows Krypterande filsystem (EFS) till filservern StorSimple Virtual Array; Detta resulterar i en konfiguration som inte stöds.


### <a name="supported-storage-format"></a>Storage-format som stöds
Endast Azure block blob-lagring stöds. Sidblobar stöds inte. Mer information [om blockblobar och sidblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Nätverkskrav
I följande tabell visas de portar som måste öppnas i brandväggen för att tillåta för iSCSI, SMB, i molnet eller hanteringstrafik. I den här tabellen *i* eller *inkommande* refererar till den riktning som inkommande klientbegäranden åtkomst till din enhet. *Ut* eller *utgående* refererar till den riktning som din StorSimple-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| **Nej. port<sup>1</sup>** | **In eller ut** | **Port omfång** | **Krävs** | **Anteckningar** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Utdata |WAN |Nej |Utgående port används för åtkomst till Internet för att hämta uppdateringar. <br></br>Utgående webbproxy kan konfigureras av användaren. |
| TCP 443 (HTTPS) |Utdata |WAN |Ja |Utgående port används för att komma åt data i molnet. <br></br>Utgående webbproxy kan konfigureras av användaren. |
| UDP 53 (DNS) |Utdata |WAN |I vissa fall. Se information. |Den här porten krävs endast om du använder en Internet-baserad DNS-server. <br></br> Observera att om du distribuerar en filserver, bör du använda lokala DNS-servern. |
| UDP 123 (NTP) |Utdata |WAN |I vissa fall. Se information. |Den här porten krävs endast om du använder en Internetbaserad NTP-server.<br></br> Observera att om du distribuerar en filserver, rekommenderar vi att synkronisera tiden med Active Directory-domänkontrollanter. |
| TCP 80 (HTTP) |I |LAN |Ja |Det här är den inkommande porten för lokala användargränssnitt på StorSimple-enheten för lokal hantering. <br></br> Observera att komma åt lokala Användargränssnittet via HTTP omdirigeras automatiskt till HTTPS. |
| TCP 443 (HTTPS) |I |LAN |Ja |Det här är den inkommande porten för lokala användargränssnitt på StorSimple-enheten för lokal hantering. |
| TCP 3260 (iSCSI) |I |LAN |Nej |Den här porten används för att ansluta till data via iSCSI. |

<sup>1</sup> inga ingående portar måste du öppna på Internet.

> [!IMPORTANT]
> Se till att brandväggen inte ändrar eller dekryptera SSL trafik mellan StorSimple-enhet och Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler
Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera det inkommande och utgående trafik. Den virtuella matrisen och StorSimple Device Manager-tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. Detta kräver i sin tur att nätverksadministratören kan övervaka och uppdatera brandväggsregler för StorSimple som och när det behövs. 

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på StorSimple fasta IP-adresser, liberally i de flesta fall. Du kan dock använda den här informationen för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> 
> * Enheten (källa) IP-adresser ska alltid vara inställd till alla moln-aktiverat nätverksgränssnitt. 
> * Målets IP-adresser ska vara inställd på [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| URL-mönster | Komponenten/funktioner |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple Device Manager-tjänst<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänsten|
| `http://*.backup.windowsazure.com` |Enhetsregistrering |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Återkallade certifikat |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-konton och övervakning |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Supportpaket |
| `http://*.data.microsoft.com ` |Telemetritjänsten i Windows, finns i den [uppdatering för customer experience och diagnostiktelemetri](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Nästa steg
* [Förbereda portalen för att distribuera StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
