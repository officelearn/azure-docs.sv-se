---
title: Diagnostikverktyget för att felsöka enhet i StorSimple 8000 | Microsoft Docs
description: Beskrivs lägen för StorSimple-enheten och hur du använder Windows PowerShell för StorSimple för att ändra Enhetsläge.
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
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576194"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Använda StorSimple diagnostikverktyget för att felsöka problem med 8000 series-enhet

## <a name="overview"></a>Översikt

StorSimple diagnostikverktyget diagnostisera problem som rör system-, prestanda-, nätverks- och maskinvara komponentens hälsostatus för en StorSimple-enhet. Diagnostikverktyget som kan användas i olika scenarier. Dessa scenarier är arbetsbelastningen planera, distribuera en StorSimple-enhet, utvärdera nätverksmiljön och bestämma prestandan hos en operational enhet. Den här artikeln innehåller en översikt över diagnostikverktyget och beskriver hur verktyget kan användas med en StorSimple-enhet.

Diagnostikverktyget är främst avsett för StorSimple 8000-serien lokala enheter på (8100 och 8600).

## <a name="run-diagnostics-tool"></a>Kör diagnostikverktyget

Det här verktyget kan köras via Windows PowerShell-gränssnittet för StorSimple-enheten. Det finns två sätt att få åtkomst till det lokala gränssnittet för enheten:

* [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Fjärråtkomst till verktyget via Windows PowerShell för StorSimple](storsimple-8000-remote-connect.md).

I den här artikeln förutsätter vi att du har anslutit till enhetens seriekonsol via PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Köra verktyg för diagnostik

När du har anslutit till Windows PowerShell-gränssnittet på enheten, utför du följande steg för att köra cmdlet: en.
1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Ange följande kommando:

    `Invoke-HcsDiagnostics`

    Om omfångets parameter har angetts, kör cmdleten alla diagnostiktest. De här testerna innehåller system, komponenthälsa för maskinvara, nätverk och prestanda. 
    
    Kör endast ett specifikt test genom att ange parametern omfång. Exempelvis kan endast nätverkstest ange

    `Invoke-HcsDiagnostics -Scope Network`

3. Markera och kopiera utdata från PuTTY-fönstret i en textfil för vidare analys.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenarier för att använda diagnostikverktyget för

Använda diagnostikverktyget för att felsöka nätverk, prestanda, system och maskinvara hälsotillståndet för systemet. Här följer några möjliga scenarier:

* **Enhet i offlineläge** -enhet för din StorSimple 8000-serien är offline. Från Windows PowerShell-gränssnittet verkar det dock att båda styrenheterna är igång och körs.
    * Du kan använda det här verktyget för att avgöra tillståndet för nätverket.
         
         > [!NOTE]
         > Använd inte det här verktyget för att utvärdera prestanda- och inställningar på en enhet innan registreringen (eller konfiguration via installationsguiden). En giltig IP-adress tilldelas till enheten under installationsguiden och registrering. Du kan köra denna cmdlet på en enhet som inte är registrerad, för hälsotillstånd för maskinvara och system. Använda omfattningsparametern, till exempel:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Beständiga enhetsproblem** -du upplever problem med enheter som verkar kvar. Exempelvis kan misslyckas registreringen. Du kan också problem med enheten när enheten är registrerad och har fungerar på ett tag.
    * I så fall använda verktyget för preliminär felsökning innan du loggar en tjänstbegäran med Microsoft Support. Vi rekommenderar att du kör det här verktyget och samla in utdata från det här verktyget. Du kan sedan ge stöd för att underlätta felsökning av dessa utdata.
    * Om det finns några maskinvarufel för komponent eller ett kluster, bör du logga in en supportbegäran.

* **Låg Enhetsprestanda** -din StorSimple-enhet är långsam.
    * I det här fallet kör denna cmdlet med omfattningsparametern inställd på prestanda. Analysera utdata. Du får molnet Läs-och svarstider. Använder de rapporterade svarstiderna som högsta möjliga mål, ta hänsyn till vissa kostnader för interna databearbetning och distribuera arbetsbelastningar på systemet. Mer information går du till [använda nätverket testet för att felsöka Enhetsprestanda](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostik test och sample utdata

### <a name="hardware-test"></a>Maskinvarutest

Det här testet status för maskinvarukomponenter, USM-firmware och den inbyggda programvaran för disken som körs på datorn.

* De maskinvarukomponenter som rapporteras är de komponenter som misslyckats testet eller finns inte i systemet.
* Versioner för USM inbyggd programvara och disk av inbyggd programvara rapporteras för kontrollenhet 0, kontrollenhet 1 och delade komponenter i systemet. En fullständig lista över de komponenter finns:

    * [Komponenter i primära hölje](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenter i EBOD hölje](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Om testet maskinvara rapporterar misslyckade komponenter, [logga in en tjänstbegäran med Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Exempel på utdata från maskinvarutest som körs på en 8100-enhet

Här är ett exempel på utdata från en StorSimple 8100-enhet. I den modell 8100-enheten finns inte EBOD-höljet. EBOD-komponenter kontrollenhet rapporteras därför inte.

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

Det här testet rapporterar systeminformationen, tillgängliga uppdateringar, klusterinformationen och tjänstinformation för din enhet.

* Information om innehåller modellen, enhetens serienummer, tidszon, status för domänkontrollanten och den detaljerade programvaruversionen som körs på systemet. För att förstå de olika systemparametrar som rapporteras som utdata, gå till [tolka Systeminformation](#appendix-interpreting-system-information).

* Uppdatera tillgängligheten rapporterar om lägena ordinarie och underhåll är tillgängliga och deras associerade paketnamn. Om `RegularUpdates` och `MaintenanceModeUpdates` är `false`, detta anger att uppdateringar inte är tillgängliga. Enheten är uppdaterad.
* Klusterinformationen innehåller information om olika logiska komponenter av alla HCS klustergrupper och deras respektive status. Om du ser ett offline klustergrupp i det här avsnittet i rapporten, [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Information om tjänstens innehåller namn och status för alla HCS och CiS tjänster som körs på din enhet. Den här informationen är användbart för Microsoft Support-felsökning av problemet i enheten.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Exempel på utdata från system-test som körs på en 8100-enhet

Här är ett exempel på utdata för testkörning för system på en 8100-enhet.

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

Det här testet kontrollerar status för de nätverksgränssnitt, portar, DNS och NTP anslutning, SSL certifikat, autentiseringsuppgifter för lagringskonto, anslutning till Update-servrar och proxy webbanslutningen på StorSimple-enheten.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Exempel på utdata från nätverket testa när endast DATA0 är aktiverat

Här är ett exempel på utdata från 8100-enheten. Du ser i utdata som:
* Endast DATA 0 och DATA 1 nätverksgränssnitt är aktiverade och konfigurerade.
* DATA 2-5 har inte aktiverats i portalen.
* DNS-server-konfigurationsfilen är giltig och enheten kan ansluta via DNS-servern.
* NTP-server-anslutning är också bra.
* Portarna 80 och 443 är öppna. Dock är port 9354 blockerad. Utifrån den [system nätverkskrav](storsimple-system-requirements.md), måste du öppna den här porten för service bus-kommunikation.
* SSL-certifikat är giltig.
* Enheten kan ansluta till lagringskontot: _myss8000storageacct_.
* Anslutningen till Update-servrar är giltig.
* Webbproxy har inte konfigurerats på den här enheten.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Exempel på utdata från nätverkstest när DATA0 och fil1 är aktiverade

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

Det här testet rapporterar molnet prestanda via molnet Läs-och svarstider för din enhet. Det här verktyget kan användas för att upprätta en baslinje för prestanda för molnet som du kan uppnå med StorSimple. Verktyget rapporterar ut maximala prestanda (bästa användningsfall för Läs-och svarstider) som du kan få för anslutningen.

Som verktyget rapporterar den högsta prestandan, kan vi använda de rapporterade svarstiderna för Läs-och som mål när du distribuerar arbetsbelastningar.

Testet simulerar blob-storlekar som är associerade med olika volymtyper på enheten. Vanliga nivåer och säkerhetskopior av lokalt fixerade volymer använder en blobstorlek på 64 KB. Nivåindelade volymer med Arkiv-alternativet är markerat använder 512 KB storleken för blob-data. Om enheten har konfigurerad nivåindelade och lokalt fixerade volymer, endast testet motsvarar 64 KB blob datastorlek körs.

Om du vill använda det här verktyget, utför du följande steg:

1.  Skapa först en blandning av nivåindelade volymer och nivåindelade volymer med arkiverade alternativet är markerat. Den här åtgärden säkerställer att verktyget kör testerna för både 64 KB och 512 KB blob storlekar.

2. Kör cmdlet när du har skapat och konfigurerat volymerna. Ange:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anteckna Läs-och-fördröjning som rapporterats av verktyget. Det här testet kan ta flera minuter innan den rapporterar resultaten.

4. Om anslutningen befinner sig allt under det förväntade intervallet fördröjningar som rapporterats av verktyget kan användas som mål för högsta möjliga när du distribuerar arbetsbelastningar. Ta hänsyn till vissa kostnader för interna databearbetning.

    Om de Läs-och svarstider som rapporteras av diagnostikverktyget är hög:

    1. Konfigurerar Lagringsanalys för blob-tjänster och analysera resultatet för att förstå fördröjning för Azure storage-kontot. Detaljerade anvisningar finns i [aktivera och konfigurera Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Om dessa svarstider är också hög och jämförbar talen som du har fått från StorSimple diagnostikverktyget, måste du logga en tjänstbegäran med Azure storage.

    2. Om storage-konto svarstiderna är låg, kontakta nätverksadministratören för att undersöka några svarstidsproblem i nätverket.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Exempel på utdata från prestandatest som körs på en 8100-enhet

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

Här är en tabell som beskriver vilka olika Windows PowerShell parametrarna i systemet information mappas till. 

| PowerShell Parameter    | Beskrivning  |
|-------------------------|------------------|
| Instans-ID             | Varje styrenhet har en unik identifierare eller ett GUID som är associerade med den.|
| Namn                    | Det egna namnet på enheten som konfigurerats via Azure portal under distributionen av enheten. Det egna namnet för standard är enhetens serienummer. |
| Modell                   | Modell för din enhet i StorSimple 8000-serien. Modellen kan vara 8100 eller 8600.|
| serialNumber            | Enhetens serienummer tilldelas på fabriken och 15 tecken. Exempelvis kan anger 8600-SHX0991003G44HT:<br> 8600 – är den nuvarande enhetsmodellen.<br>SHX – är anläggning.<br> 0991003 - är en specifik produkt. <br> G44HT--de 5 senaste siffrorna ökas för att skapa unika serienummer. Det kanske inte är en sekventiell uppsättning.|
| TimeZone                | Enhetens tidszon som konfigurerats i Azure-portalen under distributionen av enheten.|
| CurrentController       | Den styrenhet som du är ansluten till via Windows PowerShell-gränssnittet för StorSimple-enheten.|
| ActiveController        | Den domänkontrollant som är aktiv på enheten och kontrollerar alla nätverks- och diskkonfiguration åtgärder. Detta kan vara kontrollenhet 0 eller kontrollenhet 1.  |
| Controller0Status       | Status för kontrollenhet 0 på din enhet. Status för domänkontrollanten kan vara normala i återställningsläge eller inte kan nås.|
| Controller1Status       | Status för kontrollenhet 1 på din enhet.  Status för domänkontrollanten kan vara normala i återställningsläge eller inte kan nås.|
| SystemMode              | Övergripande status för din StorSimple-enhet. Enhetens status kan vara normal, underhåll, eller inaktiverade (motsvarar inaktiveras i Azure portal).|
| FriendlySoftwareVersion | Eget strängen som motsvarar enhetens programvaruversion. För ett system som kör uppdatering 4 är eget programvaruversionen StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | Programvaruversion HCS som körs på din enhet. Exempelvis är HCS programvaruversionen för StorSimple 8000 Series Update 4.0 6.3.9600.17820. |
| ApiVersion              | Programvaruversion av Windows PowerShell-API för HCS-enheten.|
| VhdVersion              | Programvaruversion av fabriksavbildning som enheten levererades med. Om du återställer enheten till fabriksinställningarna, körs den här programvaruversionen.|
| OSVersion               | Programvaruversion av Windows Server-operativsystemet som körs på enheten. StorSimple-enheten bygger på Windows Server 2012 R2 som motsvarar 6.3.9600.|
| CisAgentVersion         | Versionen för din Cis-agenten som körs på din StorSimple-enhet. Den här agenten kan kommunicera med StorSimple Manager-tjänsten som körs i Azure.|
| MdsAgentVersion         | Versionen motsvarar Mds-agenten som körs på StorSimple-enheten. Den här agenten flyttar data till övervakning och diagnostik Service (MDS).|
| Lsisas2Version          | Versionen motsvarar LSI-drivrutiner på StorSimple-enheten.|
| Kapacitet                | Den totala kapaciteten för enheten i byte.|
| RemoteManagementMode    | Anger om enheten kan fjärrhanteras via dess Windows PowerShell-gränssnittet. |
| FipsMode                | Anger om USA FIPS Federal Information Processing Standard ()-läge har aktiverats på din enhet. Standarden FIPS 140 definierar kryptografiska algoritmer som godkänts för användning av amerikanska federala myndigheter government datorsystem för skydd av känsliga data. FIPS-läge är aktiverat som standard för enheter som kör uppdatering 4 eller senare. |

## <a name="next-steps"></a>Nästa steg

* Läs den [syntaxen för cmdleten Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Mer information om hur du [felsöka distributionsproblem](storsimple-troubleshoot-deployment.md) på StorSimple-enheten.
