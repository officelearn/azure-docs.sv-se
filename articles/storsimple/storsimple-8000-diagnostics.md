---
title: Diagnostikverktyg för felsökning av StorSimple 8000-enhet | Microsoft-dokument
description: Beskriver lägena för StorSimple-enheten och förklarar hur du använder Windows PowerShell för StorSimple för att ändra enhetsläget.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 48bd909eefbaea15cf6ca2427e106ad9bc0ffbb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298745"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Använd StorSimple Diagnostics Tool för att felsöka enhetsproblem i 8000-serien

## <a name="overview"></a>Översikt

StorSimple Diagnostics-verktyget diagnostiserar problem relaterade till system-, prestanda-, nätverks- och maskinvarukomponenthälsa för en StorSimple-enhet. Diagnostikverktyget kan användas i olika scenarier. Dessa scenarier omfattar arbetsbelastningsplanering, distribution av en StorSimple-enhet, bedömning av nätverksmiljön och fastställande av prestanda för en fungerande enhet. Den här artikeln innehåller en översikt över diagnostikverktyget och beskriver hur verktyget kan användas med en StorSimple-enhet.

Diagnostikverktyget är främst avsett för lokala enheter i StorSimple 8000-serien (8100 och 8600).

## <a name="run-diagnostics-tool"></a>Kör diagnostikverktyget

Det här verktyget kan köras via Windows PowerShell-gränssnittet på din StorSimple-enhet. Det finns två sätt att komma åt enhetens lokala gränssnitt:

* [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Fjärråtkomst till verktyget via Windows PowerShell för StorSimple](storsimple-8000-remote-connect.md).

I den här artikeln antar vi att du har anslutit till enhetens seriekonsol via PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Så här kör du diagnostikverktyget

När du har anslutit till enhetens Windows PowerShell-gränssnitt utför du följande steg för att köra cmdleten.
1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Ange följande kommando:

    `Invoke-HcsDiagnostics`

    Om scopeparametern inte har angetts kör cmdleten alla diagnostiska tester. Dessa tester omfattar system, maskinvarukomponenthälsa, nätverk och prestanda. 
    
    Om du bara vill köra ett visst test anger du scopeparametern. Om du till exempel bara vill köra nätverkstestet skriver du

    `Invoke-HcsDiagnostics -Scope Network`

3. Välj och kopiera utdata från PuTTY-fönstret till en textfil för vidare analys.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenarier för användning av diagnostikverktyget

Använd diagnostikverktyget för att felsöka systemets nätverks-, prestanda-, system- och maskinvaruhälsa. Här är några möjliga scenarier:

* **Enheten offline** - Enheten i StorSimple 8000-serien är offline. Men från Windows PowerShell-gränssnittet verkar det som om båda styrenheterna är igång.
    * Du kan använda det här verktyget för att sedan bestämma nätverkstillståndet.
         
         > [!NOTE]
         > Använd inte det här verktyget för att bedöma prestanda- och nätverksinställningar på en enhet före registreringen (eller konfigurera via installationsguiden). En giltig IP tilldelas enheten under installationsguiden och registreringen. Du kan köra den här cmdleten på en enhet som inte är registrerad, för maskinvaruhälsa och system. Använd scopeparametern, till exempel:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Beständiga enhetsproblem** – Du har problem med enheten som verkar finnas kvar. Till exempel misslyckas registreringen. Du kan också ha problem med enheten när enheten har registrerats och tagits i drift ett tag.
    * I det här fallet använder du det här verktyget för preliminär felsökning innan du loggar en servicebegäran med Microsoft Support. Vi rekommenderar att du kör det här verktyget och fångar in utdata för det här verktyget. Du kan sedan tillhandahålla den här utdata till support för att påskynda felsökning.
    * Om det finns maskinvarukomponenter eller klusterfel bör du logga in en supportbegäran.

* **Låg enhetsprestanda** - Din StorSimple-enhet är långsam.
    * I det här fallet kör du den här cmdleten med scopeparametern inställd på prestanda. Analysera utdata. Du får molnet läs-skriva latenser. Använd de rapporterade svarstiderna som maximalt uppnåeligt mål, faktor i vissa omkostnader för intern databearbetning och distribuera sedan arbetsbelastningarna på systemet. Mer information finns i [Använd nätverkstestet för att felsöka enhetens prestanda](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Test- och provutgångar för diagnostik

### <a name="hardware-test"></a>Maskinvarutest

Det här testet avgör statusen för maskinvarukomponenterna, den inbyggda programvaran USM och den fasta disken som körs på datorn.

* Maskinvarukomponenterna som rapporteras är de komponenter som misslyckades med testet eller som inte finns i systemet.
* USM firmware och disk firmware versioner rapporteras för Controller 0, Controller 1 och delade komponenter i ditt system. En fullständig lista över maskinvarukomponenter finns i:

    * [Komponenter i primärhölje](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenter i EBOD-kapsling](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Om maskinvarutestrapporterna misslyckades [loggar du in en tjänstbegäran med Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Exempel på maskinvarutestkörning på en 8100-enhet

Här är ett exempel på utdata från en StorSimple 8100-enhet. I 8100-modellenheten finns inte EBOD-höljet. Därför rapporteras inte EBOD-styrenhetens komponenter.

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

### <a name="system-test"></a>Systemtest

Det här testet rapporterar systeminformation, tillgängliga uppdateringar, klusterinformation och tjänstinformation för enheten.

* Systeminformationen omfattar modell, enhetsserienummer, tidszon, controller-status och den detaljerade programvaruversion som körs på systemet. Om du vill förstå de olika systemparametrar som rapporteras som utdata går du till [Tolka systeminformation](#appendix-interpreting-system-information).

* Uppdateringstillgängligheten rapporterar om de vanliga och underhållslägena är tillgängliga och tillhörande paketnamn. Om `RegularUpdates` `MaintenanceModeUpdates` och `false`är , indikerar detta att uppdateringarna inte är tillgängliga. Enheten är uppdaterad.
* Klusterinformationen innehåller information om olika logiska komponenter i alla HCS-klustergrupper och deras respektive status. Om du ser en offlineklustergrupp i det här avsnittet i rapporten [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Serviceinformationen innehåller namn och status för alla HCS- och CiS-tjänster som körs på din enhet. Den här informationen är användbar för Microsoft Support vid felsökning av enhetsproblemet.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Exempel på utdata från systemtestkörning på en 8100-enhet

Här är ett exempel på utdata från systemtestkörningen på en 8100-enhet.

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
hcs_management_service        Online HCS Cluster Group
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

Det här testet validerar statusen för nätverksgränssnitt, portar, DNS- och NTP-serveranslutning, TLS/SSL-certifikat, autentiseringsuppgifter för lagringskonto, anslutning till uppdateringsservrarna och webbproxyanslutning på Din StorSimple-enhet.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Exempel på utskrift av nätverkstest när endast DATA0 är aktiverat

Här är ett exempel på 8100-enheten. Du kan se i utdata som:
* Endast nätverksgränssnitt för DATA 0 och DATA 1 är aktiverade och konfigurerade.
* DATA 2 - 5 är inte aktiverade i portalen.
* DNS-serverkonfigurationen är giltig och enheten kan ansluta via DNS-servern.
* NTP-serveranslutningen är också bra.
* Portarna 80 och 443 är öppna. Port 9354 är dock blockerad. Baserat på [systemnätverkskraven](storsimple-system-requirements.md)måste du öppna den här porten för servicebusskommunikationen.
* TLS/SSL-certifieringen är giltig.
* Enheten kan ansluta till _lagringskontot: myss8000storageacct_.
* Anslutningen till Uppdateringsservrar är giltig.
* Webbproxyn är inte konfigurerad på den här enheten.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Exempel på utdata från nätverkstest när DATA0 och DATA1 är aktiverade

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

Det här testet rapporterar molnprestanda via molnet läs-skriva latenser för din enhet. Det här verktyget kan användas för att upprätta en baslinje för molnprestanda som du kan uppnå med StorSimple. Verktyget rapporterar det maximala prestanda (bästa scenario för läs-skriv-svarstider) som du kan få för din anslutning.

När verktyget rapporterar maximal uppnåelig prestanda kan vi använda de rapporterade läs-skriv-svaren som mål när du distribuerar arbetsbelastningarna.

Testet simulerar blob-storlekarna som är associerade med de olika volymtyperna på enheten. Regelbundna nivåindelade och säkerhetskopior av lokalt fästa volymer använder en blobstorlek på 64 kB. Nivåindelad volymer med arkivalternativet markerat använd 512 KB blob datastorlek. Om enheten har nivåindelade och lokalt fästa volymer konfigurerade körs endast testet som motsvarar 64 KB blob-datastorlek.

Så här använder du det här verktyget:

1.  Skapa först en blandning av nivåindelade volymer och nivåindelade volymer med arkiverat alternativ markerat. Den här åtgärden säkerställer att verktyget kör testerna för både 64 KB- och 512 KB-blobstorlekar.

2. Kör cmdleten när du har skapat och konfigurerat volymerna. Ange:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anteckna de läs-skriv-svaren som rapporteras av verktyget. Det här testet kan ta flera minuter att köra innan det rapporterar resultaten.

4. Om anslutningsfördynningar är alla under det förväntade intervallet kan de svaren som rapporteras av verktyget användas som maximalt uppnåeligt mål när arbetsbelastningarna distribueras. Faktor i vissa omkostnader för intern databehandling.

    Om de läs-skriv-svaren som rapporteras av diagnostikverktyget är höga:

    1. Konfigurera Storage Analytics för blob-tjänster och analysera utdata för att förstå svarstiderna för Azure-lagringskontot. Detaljerade instruktioner finns i [aktivera och konfigurera Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Om dessa svarstider också är höga och jämförbara med de nummer du fick från StorSimple Diagnostics-verktyget måste du logga en tjänstbegäran med Azure-lagring.

    2. Om svarstidsfördröjningarna för lagringskontot är låga kontaktar du nätverksadministratören för att undersöka eventuella svarstidsproblem i nätverket.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Exempel på prestandatestkörning på en 8100-enhet

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

## <a name="appendix-interpreting-system-information"></a>Tillägg: tolkning av systeminformation

Här är en tabell som beskriver vad de olika Windows PowerShell parametrarna i systeminformationen kartan till. 

| PowerShell-parameter    | Beskrivning  |
|-------------------------|------------------|
| Instans-ID             | Varje styrenhet har en unik identifierare eller ett GUID som är associerat med den.|
| Namn                    | Det egna namnet på enheten som konfigurerats via Azure-portalen under enhetsdistributionen. Standardnamnet är enhetens serienummer. |
| Modell                   | Modellen för din StorSimple 8000-serie enhet. Modellen kan vara 8100 eller 8600.|
| Serienummer            | Enhetens serienummer tilldelas på fabriken och är 15 tecken långt. Till exempel anger 8600-SHX0991003G44HT:<br> 8600 – Är enhetsmodellen.<br>SHX – Är tillverkningsanläggningen.<br> 0991003 - Är en specifik produkt. <br> G44HT- de sista 5 siffrorna ökas för att skapa unika serienummer. Detta kanske inte är en sekventiell uppsättning.|
| TimeZone                | Enhetens tidszon som konfigurerats i Azure-portalen under enhetsdistributionen.|
| CurrentController (Aktuellakontrolller)       | Den handkontroll som du är ansluten till via Windows PowerShell-gränssnittet på Din StorSimple-enhet.|
| ActiveController (aktiv kontroll)        | Styrenheten som är aktiv på enheten och styr alla nätverks- och diskåtgärder. Detta kan vara Controller 0 eller Controller 1.  |
| Controller0Status       | Status för Controller 0 på enheten. Styrenhetens status kan vara normal, i återställningsläge eller inte nås.|
| Controller1Status       | Status för Controller 1 på enheten.  Styrenhetens status kan vara normal, i återställningsläge eller inte nås.|
| SystemMode (systemmode)              | Den övergripande statusen för din StorSimple-enhet. Enhetsstatusen kan vara normal, underhåll eller inaktiverad (motsvarar inaktiverad i Azure-portalen).|
| FriendlySoftwareVersion | Den vänliga strängen som motsvarar enhetens programvaruversion. För ett system som kör Uppdatering 4, skulle den vänliga programvaruversionen vara StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | HCS-programvaruversionen som körs på enheten. Till exempel är HCS-programvaruversionen som motsvarar StorSimple 8000 Series Update 4.0 6.3.9600.17820. |
| ApiVersion (ApiVersion)              | Programvaruversionen av Windows PowerShell-API:et för HCS-enheten.|
| VhdVersion (VhdVersion)              | Programvaruversionen av fabriksavbildningen som enheten levererades med. Om du återställer enheten till fabriksinställningar körs den här programvaruversionen.|
| OSVersion               | Programvaruversionen av operativsystemet Windows Server som körs på enheten. StorSimple-enheten är baserad på Windows Server 2012 R2 som motsvarar 6.3.9600.|
| CisAgentVersion (CisAgentVersion)         | Versionen för din Cis-agent som körs på din StorSimple-enhet. Den här agenten hjälper till att kommunicera med StorSimple Manager-tjänsten som körs i Azure.|
| MdsAgentVersion (MdsAgentVersion)         | Den version som motsvarar Mds-agenten som körs på din StorSimple-enhet. Den här agenten flyttar data till övervaknings- och diagnostiktjänsten (MDS).|
| Lsisas2Version          | Den version som motsvarar LSI-drivrutinerna på StorSimple-enheten.|
| Kapacitet                | Enhetens totala kapacitet i byte.|
| RemoteManagementMode    | Anger om enheten kan fjärrhanteras via sitt Windows PowerShell-gränssnitt. |
| FipsMode (fipsmode)                | Anger om FIPS-läget (Federal Information Processing Standard) är aktiverat på enheten. FIPS 140-standarden definierar kryptografiska algoritmer som godkänts för användning av amerikanska federala myndighetsdatasystem för skydd av känsliga data. För enheter som kör uppdatering 4 eller senare aktiveras FIPS-läget som standard. |

## <a name="next-steps"></a>Nästa steg

* Lär dig [syntaxen i cmdleten Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Läs mer om [felsÃ¶kning av distributionsproblem](storsimple-troubleshoot-deployment.md) på Din StorSimple-enhet.
