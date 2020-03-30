---
title: Tidssynkronisering för virtuella Windows-datorer i Azure
description: Tidssynkronisering för virtuella Windows-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: dd2ae2159c43da6a049d67cae739f111eba682c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74534453"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Tidssynkronisering för virtuella Windows-datorer i Azure

Tidssynkronisering är viktigt för säkerhet och händelsekorrelation. Ibland används den för implementering av distribuerade transaktioner. Tidsnoggrannheten mellan flera datorsystem uppnås genom synkronisering. Synkroniseringen kan påverkas av flera saker, inklusive omstarter och nätverkstrafik mellan tidskällan och datorn som hämtar tiden. 

Azure backas nu upp av infrastruktur som kör Windows Server 2016. Windows Server 2016 har förbättrat algoritmer som används för att korrigera tid och villkor den lokala klockan för att synkronisera med UTC.  Windows Server 2016 förbättrade också TJÄNSTEN VMICTimeSync som styr hur virtuella datorer synkroniseras med värden under korrekt tid. Förbättringar inkluderar mer exakt inledande tid på VM start eller VM-återställning och avbrott svarstid korrigering för prover som tillhandahålls till Windows Time (W32time). 


>[!NOTE]
>En snabb översikt över Windows Tidstjänst kan du titta på den här [översiktsvideon](https://aka.ms/WS2016TimeVideo)på hög nivå .
>
> Mer information finns i [Exakt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Översikt

Noggrannheten för en datorklocka mäts på hur nära datorklockan är till tidsstandarden Coordinated Universal Time (UTC). UTC definieras av ett multinationellt urval av exakta atomklockor som bara kan vara avstängd med en sekund på 300 år. Men att läsa UTC kräver direkt specialiserad maskinvara. I stället synkroniseras tidsservrar till UTC och nås från andra datorer för att ge skalbarhet och robusthet. Varje dator har tid synkronisering tjänst körs som vet vilken tid servrar att använda och regelbundet kontrollerar om datorns klocka behöver korrigeras och justerar tiden om det behövs. 

Azure-värdar synkroniseras med interna Microsoft-tidsservrar som tar sin tid från Microsoft-ägda Stratum 1-enheter, med GPS-antenner. Virtuella datorer i Azure kan antingen vara beroende av att värden skickar den korrekta tiden *(värdtid)* till den virtuella datorn eller så kan den virtuella datorn direkt få tid från en tidsserver eller en kombination av båda. 

Interaktioner med den virtuella datorn med värden kan också påverka klockan. Under [minnesbevarande underhåll](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)pausas virtuella datorer i upp till 30 sekunder. Innan underhållet påbörjas visar till exempel vm-klockan 10:00:00 och varar 28 sekunder. När den virtuella datorn har återupptagits visas klockan på den virtuella datorn fortfarande 10:00:00, vilket skulle vara 28 sekunder bort. För att korrigera detta övervakar VMICTimeSync-tjänsten vad som händer på värden och uppmanar till ändringar som ska ske på de virtuella datorerna för att kompensera.

TJÄNSTEN VMICTimeSync fungerar antingen i exempel- eller synkroniseringsläge och påverkar bara klockan framåt. I exempelläge, som kräver att W32time körs, avsökningar vmictimesync-tjänsten värden var 5:e sekund och ger tidsexempel till W32time. Ungefär var 30:e sekund tar W32time-tjänsten det senaste tidsprovet och använder det för att påverka gästens klocka. Synkroniseringsläget aktiveras om en gäst har återupptagits eller om en gästs klocka driver mer än 5 sekunder efter värdens klocka. I de fall då W32time-tjänsten körs korrekt bör det senare fallet aldrig hända.

Utan tidssynkronisering fungerar skulle klockan på den virtuella datorn ackumulera fel. När det bara finns en virtuell dator kanske effekten inte är signifikant om inte arbetsbelastningen kräver mycket exakt tidtagning. Men i de flesta fall har vi flera, sammankopplade virtuella datorer som använder tid för att spåra transaktioner och tiden måste vara konsekvent under hela distributionen. När tiden mellan virtuella datorer är annorlunda kan du se följande effekter:

- Autentiseringen misslyckas. Säkerhetsprotokoll som Kerberos eller certifikatberoende teknik är beroende av att tiden är konsekvent i alla system. 
- Det är mycket svårt att räkna ut vad som har hänt i ett system om loggar (eller andra data) inte kommer överens om i tid. Samma händelse skulle se ut som det inträffade vid olika tidpunkter, vilket gör korrelation svårt.
- Om klockan är avstängd kan faktureringen beräknas felaktigt.

De bästa resultaten för Windows-distributioner uppnås genom att använda Windows Server 2016 som gästoperativsystem, vilket garanterar att du kan använda de senaste förbättringarna i tidssynkronisering.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns tre alternativ för att konfigurera tidssynkronisering för dina virtuella Windows-datorer i Azure:

- Värdtid och time.windows.com. Det här är standardkonfigurationen som används i Azure Marketplace-avbildningar.
- Endast värd.
- Använd en annan extern tidsserver med eller utan att använda värdtid.


### <a name="use-the-default"></a>Använd standardvärdet

Som standard windows os VM-avbildningar är konfigurerade för w32time att synkronisera från två källor: 

- NtpClient-leverantören, som hämtar information från time.windows.com.
- TJÄNSTEN VMICTimeSync, som används för att kommunicera värdtiden till de virtuella datorerna och göra korrigeringar efter att den virtuella datorn har pausats för underhåll. Azure-värdar använder Microsoft-ägda Stratum 1-enheter för att hålla korrekt tid.

w32time skulle föredra tidsleverantören i följande prioritetsordning: stratumnivå, rotfördröjning, rotspridning, tidsförskjutning. I de flesta fall skulle w32time föredra time.windows.com värd eftersom time.windows.com rapporterar lägre stratum. 

För domänanslutna datorer upprättar domänen själv tidssynkroniseringshierarki, men skogsroten måste fortfarande ta tid från någonstans och följande överväganden håller fortfarande sant.


### <a name="host-only"></a>Endast värd 

Eftersom time.windows.com är en offentlig NTP-server kräver synkroniseringstiden med den att skicka trafik via internet, kan varierande paketfördröjningar påverka kvaliteten på tidssynkroniseringen negativt. Om du tar bort time.windows.com genom att byta till synkronisering endast för värd kan ibland förbättra dina tidssynkroniseringsresultat.

Det är logiskt att växla till tidssynkronisering endast för värdprogram om du upplever problem med tidssynkronisering med standardkonfigurationen. Prova synkroniseringen endast värd för att se om det skulle förbättra tidssynkroniseringen på den virtuella datorn. 

### <a name="external-time-server"></a>Extern tidsserver

Om du har specifika krav på tidssynkronisering finns det också ett alternativ för att använda externa tidsservrar. Externa tidsservrar kan ge specifik tid, vilket kan vara användbart för testscenarier, vilket säkerställer tidsenhetlighet med datorer som finns i datacenter som inte kommer från Microsoft eller hanterar skottsekunder på ett speciellt sätt.

Du kan kombinera externa servrar med TJÄNSTEN VMICTimeSync och VMICTimeProvider för att ge resultat som liknar standardkonfigurationen. 

## <a name="check-your-configuration"></a>Kontrollera konfigurationen


Kontrollera om NtpClient-tidsprovidern är konfigurerad för att använda explicita NTP-servrar (NTP) eller domäntidssynkronisering (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Om den virtuella datorn använder NTP visas följande utdata:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

För att se vilken tidsserver ntpClient-tidsleverantören använder, vid en upphöjd kommandotolkstyp:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Om den virtuella datorn använder standardinställningen ser utdata ut så här:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


För att se vilken tidsleverantör som används för närvarande.

```
w32tm /query /source
```


Här är produktionen du kunde se och vad det skulle innebära:
    
- **time.windows.com** - i standardkonfigurationen skulle w32time få tid från time.windows.com. Tidssynkroniseringskvaliteten beror på internetanslutning till den och påverkas av paketfördröjningar. Detta är den vanliga utdata från standardinställningen.
- **VM IC Tidssynkroniseringsprovider** - den virtuella datorn synkroniserar tid från värden. Detta är vanligtvis resultatet om du väljer att synkronisera värdtid eller om NtpServer inte är tillgänglig för tillfället. 
- *Domänservern* – den aktuella datorn finns i en domän och domänen definierar tidssynkroniseringshierarkin.
- *Någon annan server* - w32time har uttryckligen konfigurerats för att få tid från den en annan server. Kvaliteten på tidssynkronisering beror på den här tidsserverns kvalitet.
- **Lokal CMOS-klocka** - klockan är osynkroniserad. Du kan få den här utdata om w32time inte har haft tillräckligt med tid att starta efter en omstart eller när alla konfigurerade tidskällor inte är tillgängliga.


## <a name="opt-in-for-host-only-time-sync"></a>Opt-in för värd-bara tidssynkronisering

Azure arbetar ständigt med att förbättra tidssynkronisering på värdar och kan garantera att all tidssynkroniseringsinfrastruktur samlokaleras i Microsoft-ägda datacenter. Om du har problem med tidssynkronisering med standardinställningarna som föredrar att använda time.windows.com som primär tidskälla kan du använda följande kommandon för att välja att vara värd-bara tidssynkronisering.

Markera VMIC-providern som aktiverad. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Markera NTPClient-providern som inaktiverad.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Starta om w32time-tjänsten.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Virtuella datorer med Windows Server 2012 och R2 

Windows Server 2012 och Windows Server 2012 R2 har olika standardinställningar för tidssynkronisering. W32time som standard är konfigurerad på ett sätt som föredrar låg overhead av tjänsten över till exakt tid. 

Om du vill flytta distributionerna Windows Server 2012 och 2012 R2 för att använda de nyare standardvärden som föredrar exakt tid kan du använda följande inställningar.

Uppdatera avsöknings- och uppdateringsintervallen för w32time så att de matchar inställningarna för Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

För w32time för att kunna använda de nya avsökningsintervallen markeras NtpServers som med hjälp av dem. Om servrar är kommenterade med 0x1 bitflag mask, som skulle åsidosätta denna mekanism och w32time skulle använda SpecialPollInterval istället. Kontrollera att angivna NTP-servrar antingen använder 0x8-flagga eller ingen flagga alls:

Kontrollera vilka flaggor som används för de använda NTP-servrarna.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Nästa steg

Nedan finns länkar till mer information om tidssynkronisering:

- [Verktyg och inställningar för Windows tidstjänst](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Förbättringar av Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Exakt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Stödgräns för att konfigurera Windows Tidstjänst för miljöer med hög noggrannhet](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


