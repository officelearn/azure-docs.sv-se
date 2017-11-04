---
title: "Diagnostik för att felsöka enheten StorSimple 8000 | Microsoft Docs"
description: "Beskriver lägen för StorSimple-enhet och förklarar hur du använder Windows PowerShell för StorSimple för att ändra läget för enheten."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 8fae7bb357f8e5e8eff249edfe3a2aaafe04283c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Använd verktyget StorSimple diagnostik för felsökning av problem med 8000 series-enheter

## <a name="overview"></a>Översikt

StorSimple-diagnostik diagnostiserar problem relaterade till systemet, prestanda, nätverk och maskinvara komponentens hälsostatus för en StorSimple-enhet. Diagnostikverktyget kan användas i olika scenarier. Dessa scenarier som inkluderar arbetsbelastning planering, distribution av en StorSimple-enhet, utvärdera nätverksmiljön och avgöra prestanda för en operativa enhet. Den här artikeln innehåller en översikt över diagnostikverktyget och beskriver hur verktyget kan användas med en StorSimple-enhet.

Diagnostikverktyget är främst avsett för StorSimple 8000-serien lokala enheter (8100 och 8600).

## <a name="run-diagnostics-tool"></a>Kör diagnostik

Det här verktyget kan köras via Windows PowerShell-gränssnittet för din StorSimple-enhet. Det finns två sätt att få åtkomst till det lokala gränssnittet av enheten:

* [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Fjärransluta till verktyget via Windows PowerShell för StorSimple](storsimple-remote-connect.md).

I den här artikeln förutsätter vi att du har anslutit till enhetens seriekonsol via PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Du kan köra verktyget Nätverksdiagnostik

Utför följande steg för att köra cmdlet när du har anslutit till Windows PowerShell-gränssnittet för enheten.
1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Ange följande kommando:

    `Invoke-HcsDiagnostics`

    Om omfattningsparametern inte anges, kör cmdleten alla diagnostiktest. Dessa tester inkluderar system, komponenthälsa för maskinvara, nätverk och prestanda. 
    
    Ange parametern scope för att köra endast en specifik test. Till exempel för att köra endast nätverkstest Skriv

    `Invoke-HcsDiagnostics -Scope Network`

3. Markera och kopiera utdata från fönstret PuTTY till en textfil för ytterligare analys.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenarier för att använda verktyget Nätverksdiagnostik

Använda diagnostikverktyget för felsökning av nätverk, prestanda, system och maskinvara hälsotillståndet för systemet. Här följer några möjliga scenarier:

* **Enheten offline** -din StorSimple 8000-serien enheten är offline. I Windows PowerShell-gränssnittet verkar det dock att båda domänkontrollanterna är igång.
    * Du kan använda det här verktyget för att avgöra tillståndet nätverk.
         
         > [!NOTE]
         > Använd inte det här verktyget för att bedöma prestanda- och inställningar på en enhet innan registreringen (eller konfigurera via installationsguiden). En giltig IP-adress tilldelas enheten under installationsguiden och registrering. Du kan köra denna cmdlet på en enhet som inte är registrerad, för maskinvara hälso- och system. Använd parametern scope, till exempel:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Beständiga enhetsproblem** -du har enhetsproblem med som verkar kvarstår. Till exempel misslyckas registreringen. Du kan också bero på problem med enheter när enheten är registrerad och har fungerar på ett tag.
    * I detta fall kan använda verktyget för preliminär felsökning innan du loggar en tjänstbegäran med Microsoft-supporten. Vi rekommenderar att du kör det här verktyget och samla in utdata från det här verktyget. Du kan sedan ange dessa utdata till stöd för att underlätta felsökningen.
    * Om det finns några maskinvarufel för komponent eller ett kluster, bör du logga in en supportbegäran.

* **Lite Enhetsprestanda** -din StorSimple-enhet är långsam.
    * I det här fallet köra denna cmdlet med omfattningsparametern inställd på prestanda. Analysera utdata. Du får molnet skrivskyddad svarstider. Använd de rapporterade svarstiderna som maximalt hastigheterna mål, ta hänsyn till vissa kostnader för interna databearbetning och sedan distribuera arbetsbelastningar i systemet. Mer information finns på [använda nätverkstest för att felsöka Enhetsprestanda](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostik för testning och exempel utdata

### <a name="hardware-test"></a>Maskinvarutest

Det här testet status för maskinvarukomponenter, över firmware och den inbyggda programvaran disk som körs på datorn.

* De maskinvarukomponenter som rapporterats är de komponenter som misslyckats testet eller finns inte i systemet.
* De över inbyggd programvara och disk versionerna av inbyggd rapporteras för styrenhet 0, 1 domänkontrollant, och delade komponenter i systemet. En fullständig lista över maskinvarukomponenter finns:

    * [Komponenter i primära hölje](storsimple-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenter i EBOD hölje](storsimple-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Om testet maskinvara rapporterar misslyckade komponenter, [logga in en tjänstbegäran med Microsoft-supporten](storsimple-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Exempel på utdata av maskinvarutest som körs på en 8100-enhet

Här är ett exempel på utdata från en StorSimple 8100-enhet. I 8100-enhet för modellen finns EBOD höljet inte. Därför rapporteras EBOD domänkontrollant komponenter inte.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>System-test

Det här testet rapporterar systeminformationen, tillgängliga uppdateringar, klusterinformationen och tjänstinformation för enheten.

* Systeminformationen omfattar modellen, enhetens serienummer, tidszon, status för domänkontrollanten och detaljerad programvaruversionen körs på systemet. För att förstå de olika systemparametrar som rapporteras som utdata, gå till [tolka Systeminformation](#appendix-interpreting-system-information).

* Uppdatera tillgänglighet rapporter om lägena regular och underhåll är tillgängliga och deras associerade paketnamn. Om `RegularUpdates` och `MaintenanceModeUpdates` är `false`, detta indikerar att uppdateringarna inte är tillgängliga. Enheten är uppdaterad.
* Klusterinformationen innehåller information om olika logiska komponenter till alla HCS klustergrupper och deras respektive status. Om du ser ett offline klustergrupp i det här avsnittet i rapporten, [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).
* Tjänstinformation innehåller namn och status för alla HCS och CiS tjänster som körs på enheten. Den här informationen är användbar för Microsoft-supporten felsökning av problem med enheten.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Exempel på utdata från system-test som körs på en 8100-enhet

Här är ett exempel på utdata för testet system som kör på en 8100-enhet.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_servic         Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Nätverkstest

Det här testet kontrollerar status för den nätverksgränssnitt, portar, DNS och NTP serveranslutning, SSL certifikat, lagringskontouppgifter, anslutning till Update-servrar och proxy webbanslutningen på StorSimple-enheten.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Exempel på utdata för nätverket testa när endast DATA0 är aktiverad

Här är ett exempel på utdata för 8100-enhet. Du kan se i utdata som:
* Endast DATA 0 och DATA 1 nätverksgränssnitt är aktiverad och konfigurerad.
* DATA 2-5 är inte aktiverade i portalen.
* DNS-server-konfigurationsfilen är giltig och enheten kan ansluta via DNS-servern.
* NTP-serveranslutning är också bra.
* Portarna 80 och 443 är öppna. Dock är port 9354 blockerad. Baserat på de [system nätverkskrav](storsimple-system-requirements.md), måste du öppna den här porten för service bus-kommunikation.
* SSL-certifikat är giltigt.
* Enheten kan ansluta till lagringskontot: _myss8000storageacct_.
* Anslutningen till Update-servrar är giltig.
* Webbproxyn är inte konfigurerad på den här enheten.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Utdata från nätverkstest när DATA0 och fil1 har aktiverats

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Prestandatest

Det här testet rapporterar molnet prestanda via molnet skrivskyddad svarstiderna för din enhet. Det här verktyget kan användas för att upprätta en baslinje för prestanda moln som du kan uppnå med StorSimple. Verktyget rapporterar den maximala prestanda (bästa möjliga scenario för skrivskyddad svarstiderna) som du kan hämta för anslutningen.

Allteftersom verktyget rapporterar den maximala prestandan, kan vi använda de rapporterade svarstiderna skrivskyddad som mål när du distribuerar arbetsbelastningar.

Testet simulerar blob-storlekar som är associerade med en annan volymtyper på enheten. Vanliga nivåer och säkerhetskopior av lokalt fästa volymer använder en 64 KB blob-storlek. Nivåindelade volymer med Arkiv alternativet markerat använda 512 KB storleken för blob-data. Om enheten har konfigurerade nivåindelade och lokalt fästa volymer endast testet motsvarar 64 KB blob datastorleken körs.

Utför följande steg om du vill använda det här verktyget:

1.  Skapa först en blandning av nivåindelade volymer och nivåindelade volymer med arkiverade alternativet är markerat. Den här åtgärden säkerställer att verktyget körs testerna för både 64 KB och 512 KB blob-storlekar.

2. Kör cmdlet när du har skapat och konfigurerat volymerna. Ange:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anteckna de skrivskyddad fördröjningar som rapporterats av verktyget. Det här testet kan ta flera minuter innan den rapporterar resultaten.

4. Om anslutningen svarstiderna är alla under det förväntade intervallet fördröjningar som rapporterats av verktyget kan användas som mål för maximal hastigheterna när du distribuerar arbetsbelastningar. Ta hänsyn till vissa kostnader för interna databearbetning.

    Om skrivskyddad rapporteras av diagnostikverktyget befinner sig hög:

    1. Konfigurera Storage Analytics för blob-tjänster och analysera utdata för att förstå svarstiderna för Azure storage-konto. Detaljerade anvisningar finns i [aktivera och konfigurera Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Om dessa svarstider är hög och jämförbar siffror som du har fått från StorSimple-diagnostik, måste logga in på en tjänstbegäran med Azure storage.

    2. Om lagring konto befinner sig låg, kontaktar du nätverksadministratören för att undersöka problemen svarstid i nätverket.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Exempel på utdata av prestandatest som körs på en 8100-enhet

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Bilaga: tolka Systeminformation

Här är en tabell som beskriver vilka olika Windows PowerShell parametrar i kartan system information till. 

| PowerShell-Parameter    | Beskrivning  |
|-------------------------|------------------|
| Instans-ID             | Varje domänkontrollant har en unik identifierare eller ett GUID som är kopplade till den.|
| Namn                    | Det egna namnet på enheten som konfigurerats via Azure portal under distributionen av enheten. Eget standardnamnet är enhetens serienummer. |
| Modellen                   | Modell för enheten StorSimple 8000-serien. Modellen kan vara 8100 eller 8600.|
| Serienummer            | Enhetens serienummer som tilldelas på fabriken och 15 tecken. Exempelvis anger 8600-SHX0991003G44HT:<br> 8600 – är enhetsmodellen.<br>SHX – är platsen manufacturing.<br> 0991003 - är en specifik produkt. <br> G44HT--de 5 senaste siffrorna ökas för att skapa unika serienummer. Det får inte vara en sekventiell uppsättning.|
| Tidszon                | Den enhet tidszonen som konfigurerats i Azure portal under distributionen av enheten.|
| CurrentController       | Den domänkontrollant som du är ansluten till via Windows PowerShell-gränssnittet för din StorSimple-enhet.|
| ActiveController        | Den domänkontrollant som är aktiv på enheten och kontrollerar alla nätverks- och åtgärder. Detta kan vara styrenhet 0 eller 1 för domänkontrollanten.  |
| Controller0Status       | Status för styrenhet 0 på enheten. Status för domänkontrollanten kan vara vanligt i återställningsläge eller kan inte nås.|
| Controller1Status       | Status för styrenheten 1 på enheten.  Status för domänkontrollanten kan vara vanligt i återställningsläge eller kan inte nås.|
| SystemMode              | Övergripande status för din StorSimple-enhet. Enhetens status kan vara normal, underhåll, eller inaktiverade (motsvarar inaktiveras i Azure portal).|
| FriendlySoftwareVersion | Eget strängen som motsvarar programvaruversionen för enheten. För ett system som kör uppdatering 4 blir eget programvaruversionen StorSimple 8000 Series uppdatering 4.0.|
| HcsSoftwareVersion      | Programvaruversion HCS körs på enheten. Exempelvis är programvaruversionen HCS motsvarar StorSimple 8000 Series uppdatering 4.0 6.3.9600.17820. |
| ApiVersion              | Programvaruversion av Windows PowerShell-API för HCS enheten.|
| VhdVersion              | Programvaruversionen av fabriksavbildning som enheten levererades med. Om du återställer enheten till fabriksinställningarna, kör den här programvaruversionen.|
| OSVersion               | Programvaruversion av operativsystemet Windows Server körs på enheten. StorSimple-enhet är baserat på Windows Server 2012 R2 som motsvarar 6.3.9600.|
| CisAgentVersion         | Version för din TIS-agenten som körs på din StorSimple-enhet. Den här agenten kan kommunicera med StorSimple Manager-tjänsten körs i Azure.|
| MdsAgentVersion         | Den version som motsvarar Mds-agenten som körs på din StorSimple-enhet. Den här agenten flyttar data till övervakning och diagnostik Service (MDS).|
| Lsisas2Version          | Den version som motsvarar LSI drivrutiner på din StorSimple-enhet.|
| Kapacitet                | Den totala kapaciteten för enheten i byte.|
| RemoteManagementMode    | Anger om enheten kan fjärrhanteras via dess Windows PowerShell-gränssnittet. |
| FipsMode                | Anger om USA FIPS Federal Information Processing Standard ()-läge är aktiverat på enheten. Standarden FIPS 140 definierar kryptografiska algoritmer som godkänts för användning av oss federala myndigheter datorsystem för att skydda känsliga data. FIPS-läge är aktiverat som standard för enheter som kör uppdatering 4 eller senare. |

## <a name="next-steps"></a>Nästa steg

* Läs den [syntaxen för cmdleten Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Mer information om hur du [felsöka distributionsproblem](storsimple-troubleshoot-deployment.md) på StorSimple-enheten.
