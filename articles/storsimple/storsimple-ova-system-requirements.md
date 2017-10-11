---
title: "Systemkrav för Microsoft Azure StorSimple virtuell matris | Microsoft Docs"
description: "Lär dig mer om program- och nätverkskrav för din virtuella StorSimple-matris"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.openlocfilehash: 4a2c86cee40dbd1dc2c37d58e69bdf35b0046c4d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systemkrav för StorSimple Virtual Array
## <a name="overview"></a>Översikt
Den här artikeln beskriver viktiga systemkraven för Microsoft Azure StorSimple virtuell matrisen och för lagring-klienter åtkomst till matrisen. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din StorSimple-system och sedan refererar tillbaka till den vid behov under distributionen och efterföljande igen.

Systemkraven är:

* **Programvarukrav för lagring klienter** -beskriver stöds virtualiseringsplattformar, webbläsare, iSCSI-initierare, SMB-klienter, krav på minsta virtuella enheten och eventuella ytterligare krav för dessa operativsystem.
* **Nätverkskrav för StorSimple-enhet** -ger information om vilka portar som måste vara öppna i brandväggen för att tillåta för iSCSI-, molnet eller hantering av trafik.

StorSimple-system kravinformation publicerade i den här artikeln gäller bara för virtuella StorSimple-matriser.

* För enheter i 8000-serien, gå till [systemkrav för enheten StorSimple 8000-serien](storsimple-system-requirements.md).
* 7000-serien enheter, gå till [systemkrav för enheten StorSimple 5000 7000-serien](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Programvarukrav
Programvarukraven innehålla information om webbläsare som stöds, SMB-versioner, virtualiseringsplattformar och krav för minsta virtuella enheten.

### <a name="supported-virtualization-platforms"></a>Virtualiseringsplattformar som stöds
| **Hypervisor-programmet** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 och senare |
| VMware ESXi |5.5 och senare |

### <a name="virtual-device-requirements"></a>Krav för virtuell enhet
| **Komponent** | **Krav** |
| --- | --- |
| Minsta antal virtuella processorer (kärnor) |4 |
| Minsta RAM-minne |8 GB <br> För en filserver, 8 GB för mindre än 2 miljoner filer och 16 GB för 2-4 miljoner filer|
| Diskutrymme<sup>1</sup> |OS-disk - 80 GB <br></br>Datadisk - 500 GB till 8 TB |
| Minsta antalet nätverksgränssnitt |1 |
| Minsta bandbredd för Internet<sup>2</sup> |5 Mbit/s |

<sup>1</sup> - tunn etablerad

<sup>2</sup> -nätverkskrav kan variera beroende på den dagliga förändringstakten för data. Till exempel om en enhet behöver säkerhetskopiera 10 GB eller mer ändringar under en dag, kan sedan daglig säkerhetskopiering via en 5 Mbit/s-anslutning ta upp till 4,25 timmar (om data inte kan komprimeras eller dedupliceras).

### <a name="supported-web-browsers"></a>Webbläsare som stöds
| **Komponent** | **Version** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Microsoft Edge |senaste versionen | |
| Internet Explorer |senaste versionen |Testats med Internet Explorer 11 |
| Google Chrome |senaste versionen |Testats med Chrome 46 |

### <a name="supported-storage-clients"></a>Klienter som stöds
Följande programvarukrav finns för iSCSI-initierare som har åtkomst till din virtuella StorSimple-matris (konfigurerat som en iSCSI-server).

| **Operativsystem som stöds** | **Version som krävs** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012 R2 |StorSimple skapa tunt allokerade och helt allokerade volymer. Det går inte att skapa delvis allokerade volymer. StorSimple iSCSI-volymer stöds endast för: <ul><li>Enkla volymer på Windows standarddiskar.</li><li>Windows NTFS för att formatera en volym.</li> |

Följande programvarukrav finns för de SMB-klienter som har åtkomst till din virtuella StorSimple-matris (konfigurerad som en filserver).

| **SMB-Version** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Kopiera inte eller lagra filer som skyddas av Windows Krypterande filsystem (EFS) till virtuella StorSimple-matris filservern; Detta resulterar i en konfiguration som inte stöds. 
> 

### <a name="supported-storage-format"></a>Lagringsformat som stöds
Endast Azure blockera blob-lagring stöds. Sidblobbar stöds inte. Mer information [om blockblobbar och sidblobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Nätverkskrav
I följande tabell visas de portar som måste öppnas i brandväggen för att möjliggöra iSCSI, SMB, molnet eller hantering av trafik. I den här tabellen *i* eller *inkommande* refererar till den riktning som inkommande klientbegäranden åtkomst till din enhet. *Ut* eller *utgående* refererar till din StorSimple-enhet skickar data externt, utöver distributionen riktning: till exempel utgående till Internet.

| **Nej. port<sup>1</sup>** | **In eller ut** | **Port omfång** | **Krävs** | **Anteckningar** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Ut |WAN |Nej |Utgående port används för åtkomst till Internet för att hämta uppdateringar. <br></br>Utgående webbproxy kan konfigureras av användaren. |
| TCP 443 (HTTPS) |Ut |WAN |Ja |Utgående port används för att komma åt data i molnet. <br></br>Utgående webbproxy kan konfigureras av användaren. |
| UDP 53 (DNS) |Ut |WAN |I vissa fall. Du hittar information. |Den här porten krävs bara om du använder en Internet-baserad DNS-server. <br></br> Observera att om du distribuerar en filserver, bör du använda lokala DNS-servern. |
| UDP 123 (NTP) |Ut |WAN |I vissa fall. Du hittar information. |Den här porten krävs bara om du använder en Internetbaserad NTP-server.<br></br> Observera att om distribuerar en filserver, rekommenderar vi att synkronisera tiden med Active Directory-domänkontrollanter. |
| TCP 80 (HTTP) |i |LAN |Ja |Det här är den inkommande porten för lokala Användargränssnittet på StorSimple-enhet för lokal hantering. <br></br> Observera att åtkomst till lokala Gränssnittet via HTTP omdirigeras automatiskt till HTTPS. |
| TCP 443 (HTTPS) |i |LAN |Ja |Det här är den inkommande porten för lokala Användargränssnittet på StorSimple-enhet för lokal hantering. |
| TCP-3260 (iSCSI) |i |LAN |Nej |Den här porten används för att komma åt data via iSCSI. |

<sup>1</sup> några inkommande portar måste öppnas på Internet.

> [!IMPORTANT]
> Se till att brandväggen inte ändrar eller dekryptera alla SSL-trafik mellan StorSimple-enhet och Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler
Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera de inkommande och utgående trafik. Virtuella matrisen och StorSimple enheten Manager-tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory-åtkomstkontroll, storage-konton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. Detta kräver i sin tur nätverksadministratören kan övervaka och uppdatera brandväggsregler för din StorSimple som och vid behov. 

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på StorSimple fasta IP-adresser, liberally i de flesta fall. Du kan dock använda informationen nedan för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> 
> * Enheten (källa) IP-adresser ska alltid vara satt till alla moln-aktiverat nätverksgränssnitt. 
> * Mål-IP-adresser ska anges till [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).
> 
> 

| URL-mönster | Komponenten/funktioner |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` |StorSimple enheten Manager-tjänsten<br>Access Control Service<br>Azure Service Bus |
| `http://*.backup.windowsazure.com` |Enhetsregistrering |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Återkallade certifikat |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-konton och övervakning |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN med Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Support-paket |
| `http://*.data.microsoft.com ` |Telemetri service i Windows kan se den [uppdatering för customer experience och diagnostik telemetri](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-step"></a>Nästa steg
* [Förbereda portalen för att distribuera din virtuella StorSimple-matris](storsimple-virtual-array-deploy1-portal-prep.md)

