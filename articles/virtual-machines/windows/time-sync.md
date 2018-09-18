---
title: Tid synkronisering för Windows-datorer i Azure | Microsoft Docs
description: Tidssynkronisering för Windows-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/017/2018
ms.author: cynthn
ms.openlocfilehash: 7fadf4a8bcf545229dd604829780e9837ad5a94a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986366"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Tidssynkronisering för Windows-datorer i Azure

Tidsynkroniseringen är viktigt för säkerhet och händelsekorrelation. Den används ibland för implementering av distribuerade transaktioner. Tid noggrannhet mellan flera datorsystem uppnås via synkronisering. Synkronisering kan påverkas av flera saker, inklusive omstarter och nätverkstrafik mellan tidskällan och den dator som hämtar tiden. 

Azure är nu kompletterat med infrastruktur som kör Windows Server 2016. Windows Server 2016 har förbättrade algoritmer som används för att korrekt tid och ange villkor för den lokala klockan att synkronisera med UTC.  Windows Server 2016 har också förbättrat VMICTimeSync tjänsten som styr hur virtuella datorer ska synkronisera med värden för rätt tid. Förbättringarna innefattar mer exakta första gången på VM start eller återställning av virtuella datorer och interrupt svarstid korrigering för exemplen till Windows-tid (W32time). 


>[!NOTE]
>Ta en titt på detta för en snabb översikt över Windows tidstjänst [översikt på hög nivå video](https://aka.ms/WS2016TimeVideo).
>
> Mer information finns i [korrekt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Översikt

Precision för en datorklocka gauged på hur nära datorklockan är tid-standarden Coordinated Universal Time (UTC). UTC definieras av ett multinationella exempel på exakt atomiska åtgärden klockor kan bara vara av av en sekund 300 åren. Men läser UTC direkt kräver specialiserad maskinvara. I stället tidsservrar synkroniseras till UTC och kan nås från andra datorer för att tillhandahålla skalbarhet och stabilitet. Varje dator har tid kör som-synkroniseringstjänsten vet vilken tidsservrar som ska användas och regelbundet kontrollerar om datorklocka måste åtgärdas och justerar tid om det behövs. 

Azure-värdar som synkroniseras till interna Microsoft-tidsservrar att ta sina tid från Microsoft-ägda Stratum 1-enheter med GPS antenner. Virtuella datorer i Azure kan antingen bero på deras värden för att skicka rätt tid (*värd tid*) in på den virtuella datorn eller den virtuella datorn direkt får tid från en time-server eller en kombination av båda. 

VM-interaktioner med-värden kan också påverka klockan. Under [minne bevarande Underhåll](maintenance-and-updates.md#memory-preserving-maintenance), virtuella datorer har pausats för upp till 30 sekunder. Till exempel innan du påbörjar Underhåll VM klockan visar 10:00:00 AM och varar 28 sekunder. När den virtuella datorn återupptas klockan på den virtuella datorn fortfarande visas 10:00:00 AM, som är 28 sekunder av. Att korrekt för detta, VMICTimeSync tjänsten övervakar vad som händer på värden och frågar efter ändringar på de virtuella datorerna att kompensera.

Klockan på den virtuella datorn skulle ackumuleras fel utan att tid synkronisering fungerar. När det finns bara en virtuell dator, kanske inte effekten är betydande om arbetsbelastningen kräver mycket exakta tidsangivelserna. Men i de flesta fall, vi har flera, sammankopplade virtuella datorer som använder tid för att spåra transaktioner och de tid måste vara konsekvent på hela distributionen. När tiden mellan virtuella datorer skiljer sig, kan du få se följande effekter:

- Misslyckas autentiseringen. Säkerhetsprotokoll som Kerberos eller certifikat-beroende teknik som förlitar sig på tid som konsekvent över systemen. 
- Det är mycket svårt att ta reda på vad hände i ett system om loggar (eller andra data) inte godkänner villkoren i tid. Samma händelse skulle se ut som om det händer vid olika tidpunkter, och korrelation svårt.
- Om klockan inte är aktiv kan faktureringen beräknas felaktigt.

Bästa resultat för Windows-distributioner uppnås med hjälp av Windows Server 2016 som gästoperativsystem, vilket garanterar att du kan använda de senaste förbättringarna i tidssynkronisering.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns tre alternativ för att konfigurera tidssynkronisering för dina Windows virtuella datorer i Azure:

- Tiden på värddatorn och time.windows.com. Det här är standardkonfigurationen som används i Azure Marketplace-avbildningar.
- Värden endast.
- Använd en annan, externa tidsserver med eller utan att använda tiden på värddatorn.


### <a name="use-the-default"></a>Använd standard

Som standard konfigureras Windows OS VM-avbildningar för w32time ska synkroniseras från två källor: 

- NtpClient-providern som hämtar information från time.windows.com.
- Tjänsten VMICTimeSync används för att kommunicera värden tid till de virtuella datorerna och gör ändringar när den virtuella datorn har pausats för underhåll. Azure värdar använder enheter som ägs av Microsoft Stratum 1 för att hålla rätt tid.

W32Time föredrar Tidsprovidern i följande prioritetsordning: stratum nivå, rotfördröjning, rot spridning, förskjutning. I de flesta fall föredrar w32time time.windows.com till värden eftersom time.windows.com rapporterar lägre stratum. 

För domänanslutna datorer upprättar själva domänen tidshierarki för synkronisering, men skogens rot måste fortfarande ta tid från en plats och följande överväganden skulle fortfarande uppfylls.


### <a name="host-only"></a>Endast värden 

Eftersom time.windows.com är en offentlig NTP-server kan synkroniseras med den måste du skicka trafik via internet, olika paket fördröjningar kan påverka kvaliteten på synkroniseringen. Ta bort time.windows.com genom att växla till endast värden synkronisering kan förbättra din tid ibland synkronisera resultat.

Växla till värden endast tid synkronisering gör uppfattning om det uppstår tidssynkronisering utfärdar använder standardkonfigurationen. Prova att använda endast värden sync att se om det skulle förbättra tidssynkronisering på den virtuella datorn. 

### <a name="external-time-server"></a>Extern tidsservern

Om du har viss tidskrav för katalogsynkronisering, finns det också ett alternativ för att använda extern tidsservrar. Extern tidsservrar kan tillhandahålla viss tid, vilket kan vara användbart för testning, säkerställer tid enhetlighet med datorer som finns i icke-Microsoft-datacenter eller hantering av skottsekunder på ett speciellt sätt.

Du kan kombinera externa servrar med VMICTimeSync tjänsten och VMICTimeProvider att visa resultat som liknar standardkonfigurationen. 

## <a name="check-your-configuration"></a>Kontrollera konfigurationen


Kontrollera om NtpClient Tidsprovidern har konfigurerats för att använda explicita NTP-servrar (NTP) eller domän-tidssynkronisering (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Om Virtuellt datorn använder NTP, visas följande utdata:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Om du vill se vilka tidsserver NtpClient tid leverantören använder, vid en upphöjd kommandotolk skriver:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Om den virtuella datorn använder standardvärdet, ser utdata ut så här:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


För att se vilken tidsprovider används för närvarande.

```
w32tm /query /source
```


Här är utdata kan du få se och vad det skulle innebära:
    
- **Time.Windows.com** -konfiguration, w32time får tid från time.windows.com. Tid synkronisering kvaliteten beror på internet-anslutning till den och påverkas av fördröjningar i paketet. Detta är vanligt utdata från standardinställningen.
- **VM IC tid synkronisering providern** -den virtuella datorn synkroniserar tiden från värden. Detta är vanligtvis resultatet om du Anmäl dig till endast värden tidssynkronisering eller NtpServer är inte tillgänglig för tillfället. 
- *Din domänserver* – den aktuella datorn tillhör en domän och domänen definierar tidshierarki för synkronisering.
- *Någon annan server* -w32time uttryckligen har konfigurerats för att hämta tiden från att en annan server. Tid synkronisering kvalitet är beroende av denna tid servern kvalitet.
- **Lokala CMOS klockan** -klocka är inte synkroniserad. Du kan hämta dessa utdata om w32time inte har fått tillräckligt med tid att starta efter en omstart eller när alla konfigurerade tidskällor inte är tillgängliga.


## <a name="opt-in-for-host-only-time-sync"></a>Anmäl dig till endast värden tidssynkronisering

Azure fungerar ständigt förbättra tidssynkronisering på värdar och kan garantera att all infrastruktur för time-synkronisering är samordnad i ägs av Microsoft-datacenter. Du kan använda följande kommandon för att anmäla sig till endast värden tidssynkronisering om du har tid att synkronisera problem med standardinställningen som föredrar att använda time.windows.com som primär.

Markera VMIC providern som aktiverats. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Markera NTPClient providern som inaktiverade.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Starta om w32time-tjänsten.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 och R2 virtuella datorer 

Windows Server 2012 och Windows Server 2012 R2 har olika standardinställningarna för synkronisering. W32time som standard konfigureras på ett sätt som föredrar låg belastning av tjänsten via till exakt tid. 

Om du vill flytta dina Windows Server 2012 och 2012 R2-distributioner som ska användas senare standardvärdena som föredrar exakt tid kan du använda följande inställningar.

Uppdatering av w32time omröstningen och intervall för att matcha inställningar med Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

För w32time för att kunna använda de nya avsökningsintervall i NtpServers markeras som använder dem. Om servrarna är kommenterade med 0x1 bitflag mask som skulle åsidosätter den här mekanismen och w32time ska använda SpecialPollInterval i stället. Kontrollera att angiven NTP-servrar som antingen använder 0x8 flagga eller utan flaggan alls:

Kontrollera vilka flaggor som används för NTP-servrar som används.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Nästa steg

Nedan finns länkar till mer information om synkroniseringen:

- [Windows Time serviceverktyg och inställningar](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Förbättringar i Windows Server 2016 ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Korrekt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Gränsen för support som konfigurerar Windows tidstjänst för miljöer med hög precision](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


