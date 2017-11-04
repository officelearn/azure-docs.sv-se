---
title: "Systemkrav för StorSimple 8000-serien | Microsoft Docs"
description: "Beskriver program, nätverk, och hög tillgänglighet krav och bästa praxis för en Microsoft Azure StorSimple-lösning."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 485320e500d71bd85b69cfcd9628e133ad1c417c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000-serien programvara, hög tillgänglighet och nätverkskrav

## <a name="overview"></a>Översikt

Välkommen till Microsoft Azure StorSimple. Den här artikeln beskriver viktiga krav och bästa praxis för din StorSimple-enhet och för lagring-klienter åtkomst till enheten. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din StorSimple-system och sedan refererar tillbaka till den vid behov under distributionen och efterföljande igen.

Systemkraven är:

* **Programvarukrav för lagring klienter** -beskriver operativsystem som stöds och de ytterligare krav för dessa operativsystem.
* **Nätverkskrav för StorSimple-enhet** -ger information om vilka portar som måste vara öppna i brandväggen för att tillåta för iSCSI-, molnet eller hantering av trafik.
* **Krav för hög tillgänglighet för StorSimple** – beskriver krav för hög tillgänglighet och bästa praxis för din StorSimple-enheten och värden dator.

## <a name="software-requirements-for-storage-clients"></a>Programvarukrav för klienter för lagring

Följande programvarukrav finns för storage-klienter som har åtkomst till din StorSimple-enhet.

| Operativsystem som stöds | Version som krävs | Ytterligare krav/anteckningar |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-volymer stöds för användning på bara Windows disk följande:<ul><li>Enkel volym på en grundläggande disk</li><li>Enkel och speglad volym på en dynamisk disk</li></ul>Endast programvara iSCSI-initierarna finns inbyggt i operativsystemet stöds. Maskinvara iSCSI-initierarna stöds inte.<br></br>Windows Server 2012 och 2016 tunn allokering och ODX funktioner stöds om du använder en iSCSI-StorSimple-volym.<br><br>StorSimple skapa tunt allokerade och helt allokerade volymer. Det går inte att skapa delvis allokerade volymer.<br><br>Formatera om en tunt etablerad volym kan det ta lång tid. Vi rekommenderar att volymen och sedan skapa en ny i stället för att formatera om. Om du fortfarande vill att formatera en volym:<ul><li>Kör följande kommando innan den formatera om för att undvika förseningar för frigöring av utrymme: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>När formateringen är klar använder du följande kommando återaktivera utrymmesåtertagning:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Installera Windows Server 2012 snabbkorrigeringen som beskrivs i [KB 2878635](https://support.microsoft.com/kb/2870270) till Windows Server-datorn.</li></ul></li></ul></ul> Om du konfigurerar StorSimple Snapshot Manager eller en StorSimple-kortet för SharePoint går du till [programvarukrav för valfria komponenter](#software-requirements-for-optional-components). |
| VMware ESX |5.5 och 6.0 |Stöds med VMware vSphere som iSCSI-klient. VAAI-block stöds med VMware vSphere på StorSimple-enheter. |
| Linux RHEL/CentOS |5, 6 och 7 |Stöd för Linux iSCSI-klienter med öppna iSCSI-initieraren versioner 5, 6 och 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX stöds för närvarande inte med StorSimple.


## <a name="software-requirements-for-optional-components"></a>Programvarukrav för valfria komponenter

Följande programvarukrav finns för de valfria StorSimple-komponenterna (StorSimple Snapshot Manager och StorSimple-kortet för SharePoint).

| Komponent | Plattform | Ytterligare krav/anteckningar |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Användning av StorSimple Snapshot Manager på Windows Server krävs för säkerhetskopiering/återställning av speglade dynamiska diskar och programkonsekventa säkerhetskopior.<br> StorSimple Snapshot Manager stöds endast på Windows Server 2008 R2 SP1 (64-bitars), Windows Server 2012 R2 och Windows Server 2012.<ul><li>Om du använder Windows Server 2012 måste du installera .NET 3.5 – 4.5 innan du installerar StorSimple Snapshot Manager.</li><li>Om du använder Windows Server 2008 R2 SP1, måste du installera Windows Management Framework 3.0 innan du installerar StorSimple Snapshot Manager.</li></ul> |
| StorSimple-adapter för SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple-kortet för SharePoint stöds bara på SharePoint 2010 och SharePoint 2013.</li><li>RBS kräver SQL Server Enterprise Edition version 2008 R2 eller 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Nätverkskrav för din StorSimple-enhet

Din StorSimple-enhet är en låst enhet. Portar som måste öppnas i brandväggen för att tillåta för iSCSI-, moln och hantering av trafik. I följande tabell visas de portar som måste öppnas i brandväggen. I den här tabellen *i* eller *inkommande* refererar till den riktning som inkommande klientbegäranden åtkomst till din enhet. *Ut* eller *utgående* refererar till din StorSimple-enhet skickar data externt, utöver distributionen riktning: till exempel utgående till Internet.

| Nej. port<sup>1,2</sup> | In eller ut | Port omfång | Krävs | Anteckningar |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Ut |WAN |Nej |<ul><li>Utgående port används för åtkomst till Internet för att hämta uppdateringar.</li><li>Utgående webbproxy kan konfigureras av användaren.</li><li>Om du vill tillåta uppdateringar måste den här porten också vara öppen för de styrenhets-fästa IP-adresser.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Ut |WAN |Ja |<ul><li>Utgående port används för att komma åt data i molnet.</li><li>Utgående webbproxy kan konfigureras av användaren.</li><li>Om du vill tillåta uppdateringar måste den här porten också vara öppen för de styrenhets-fästa IP-adresser.</li><li>Den här porten används också på båda domänkontrollanterna för skräpinsamling.</li></ul> |
| UDP 53 (DNS) |Ut |WAN |I vissa fall. Du hittar information. |Den här porten krävs bara om du använder en Internet-baserad DNS-server. |
| UDP 123 (NTP) |Ut |WAN |I vissa fall. Du hittar information. |Den här porten krävs bara om du använder en Internetbaserad NTP-server. |
| TCP 9354 |Ut |WAN |Ja |Den utgående porten används av StorSimple-enheten för att kommunicera med StorSimple enheten Manager-tjänsten. |
| 3260 (iSCSI) |i |LAN |Nej |Den här porten används för att komma åt data via iSCSI. |
| 5985 |i |LAN |Nej |Inkommande porten används av StorSimple Snapshot Manager kan kommunicera med StorSimple-enhet.<br>Den här porten används också när du fjärransluta till Windows PowerShell för StorSimple via HTTP. |
| 5986 |i |LAN |Nej |Den här porten används när du fjärransluta till Windows PowerShell för StorSimple via HTTPS. |

<sup>1</sup> några inkommande portar måste öppnas på Internet.

<sup>2</sup> om flera portar har en gateway-konfiguration, utgående trafik i routade-ordning bestäms baserat på port ordningen beskrivs i [Port routning](#routing-metric), nedan.

<sup>3</sup> de styrenhets-fästa IP-adresser på StorSimple-enheten måste vara dirigerbara och kunna ansluta till Internet, direkt eller via den konfigurerade webbproxyn. De fasta IP-adresserna som används för att underhålla uppdateringarna till enheten och skräpinsamling. Om styrenheterna inte kan ansluta till Internet via fasta IP-adresser kan du inte uppdatera din StorSimple-enhet och skräpinsamling fungerar inte korrekt.

> [!IMPORTANT]
> Se till att brandväggen inte ändrar eller dekryptera alla SSL-trafik mellan StorSimple-enhet och Azure.


### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera de inkommande och utgående trafik. Din StorSimple-enhet och StorSimple enheten Manager-tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory-åtkomstkontroll, storage-konton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. Detta kräver i sin tur nätverksadministratören kan övervaka och uppdatera brandväggsregler för din StorSimple som och vid behov.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på StorSimple fasta IP-adresser, liberally i de flesta fall. Du kan dock använda informationen nedan för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> Enheten (källa) IP-adresser ska alltid vara satt till alla aktiverade nätverksgränssnitt. Mål-IP-adresser ska anges till [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-mönster för Azure-portalen

| URL-mönster | Komponenten/funktioner | IP-adresser för enhet |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple enheten Manager-tjänsten<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänst |Moln-aktiverat nätverksgränssnitt |
| `https://*.backup.windowsazure.com` |Enhetsregistrering |DATA 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Återkallade certifikat |Moln-aktiverat nätverksgränssnitt |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-konton och övervakning |Moln-aktiverat nätverksgränssnitt |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |Styrenhets-fästa IP-adresser endast |
| `http://*.deploy.akamaitechnologies.com` |CDN med Akamai |Styrenhets-fästa IP-adresser endast |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Support-paket |Moln-aktiverat nätverksgränssnitt |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-mönster för Azure Government-portalen

| URL-mönster | Komponenten/funktioner | IP-adresser för enhet |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login-us.microsoftonline.com` |StorSimple enheten Manager-tjänsten<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänst |Moln-aktiverat nätverksgränssnitt |
| `https://*.backup.windowsazure.us` |Enhetsregistrering |DATA 0 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Återkallade certifikat |Moln-aktiverat nätverksgränssnitt |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-konton och övervakning |Moln-aktiverat nätverksgränssnitt |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |Styrenhets-fästa IP-adresser endast |
| `http://*.deploy.akamaitechnologies.com` |CDN med Akamai |Styrenhets-fästa IP-adresser endast |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Support-paket |Moln-aktiverat nätverksgränssnitt |

### <a name="routing-metric"></a>Routning mått

Ett routning mått är associerad med gränssnitten och den gateway som kan skicka data till de angivna nätverk. Routning måttet används av routing protocol för att beräkna bästa sökvägen till ett givet mål om lär sig det finns flera sökvägar till samma mål. Ju lägre routning måttet, desto högre prioritet.

I samband med StorSimple, om flera nätverksgränssnitt och gateway har konfigurerats för kanal-trafik kommer routning mått till spela upp för att fastställa den relativa ordning som gränssnitt som kommer få användas. Routning mått ändras inte av användaren. Du kan dock använda den `Get-HcsRoutingTable` cmdlet för att skriva ut routning tabellen (och mått) på din StorSimple-enhet. Mer information om cmdlet Get-HcsRoutingTable i [felsökning StorSimple distribution](storsimple-troubleshoot-deployment.md).

Routning mått algoritmen som används för uppdatering 2 och senare versioner kan beskrivas på följande sätt.

* En uppsättning förinställda värden har tilldelats nätverksgränssnitt.
* Överväg att en exempeltabell nedan med värden som hör till olika nätverksgränssnitt när de är moln-aktiverat eller molnet inaktiverad, men med en konfigurerad gateway. Observera värdena som angetts är endast exempelvärden.

    | Nätverksgränssnitt | Moln aktiverat- | Moln-inaktiverad med gateway |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Den ordning som molntjänster trafik dirigeras via nätverksgränssnitten är:
  
    *Data 0 > Data 1 > datum 2 > Data 3 > Data 4 > Data 5*
  
    Detta kan förklaras med följande exempel.
  
    Överväg att en StorSimple-enhet med två moln-aktiverat nätverksgränssnitt, Data 0 och 5 Data. Data 1 till 4 Data är moln-inaktiverad men har en konfigurerad gateway. Den ordning i vilken trafik vidarebefordras för den här enheten kommer att:
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Siffrorna inom parentes anger respektive Routning mått.*
  
    Om Data 0 misslyckas hämta molntrafiken dirigeras till Data 5. Med hänsyn till att en gateway har konfigurerats på alla andra nätverk, om både Data 0 och 5 Data skulle misslyckas, går molnet trafik igenom Data 1.
* Om ett moln-aktiverat nätverksgränssnitt misslyckas, kan du 3 försök med en 30 sekunder innan du ansluter till gränssnittet. Om alla försök misslyckas, dirigeras trafiken till nästa tillgängliga moln-aktiverat gränssnitt som bestäms av routningstabellen. Om alla moln-aktiverat nätverk gränssnitt misslyckas, sedan växlar enheten över till den andra styrenheten (ingen omstart i detta fall).
* Om det finns ett VIP-fel för en iSCSI-aktiverade nätverksgränssnitt måste finnas det 3 försök med en fördröjning på 2 sekunder. Det här beteendet svaret har inte samma från tidigare versioner. Om alla nätverksgränssnitt för iSCSI-misslyckas, kommer en domänkontrollant växling vid fel inträffa (åtföljs av en omstart).
* En avisering utlöses också på din StorSimple-enhet när det finns en VIP-fel. Mer information finns på [Varna Snabbreferens](storsimple-8000-manage-alerts.md).
* Vad gäller omförsök företräde iSCSI framför molnet.
  
    Exempel: en StorSimple-enhet har två nätverksgränssnitt som är aktiverad, Data 0 och 1 för Data. Data 0 är moln-aktiverat Data 1 är både molntjänster och iSCSI-aktiverade. Inga andra nätverksgränssnitt på den här enheten har aktiverats för molntjänster eller iSCSI.
  
    Om Data 1 misslyckas, får den senaste iSCSI nätverksgränssnittet resulterar detta i en domänkontrollant redundans till Data 1 på en annan domänkontrollant.

### <a name="networking-best-practices"></a>Metodtips för nätverk

Förutom ovanstående nätverkskrav för optimala prestanda av din StorSimple-lösning, följer du till följande metoder:

* Kontrollera att din StorSimple-enhet har en dedikerad 40 Mbit/s bandbredd (eller mer) vid alla tidpunkter. Den här bandbredden inte ska delas (eller allokering bör garanteras genom att använda QoS-principer) med andra program.
* Kontrollera nätverksanslutningen till Internet är tillgänglig vid alla tidpunkter. Sporadiska eller otillförlitliga Internet-anslutningar till enheter, inklusive utan Internet-anslutning som helst, resulterar i en konfiguration som inte stöds.
* Isolera iSCSI och molntrafik genom att ha dedikerade nätverksgränssnitt på enheten för iSCSI- och åtkomst. Mer information finns i så här [ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces) på StorSimple-enheten.
* Använd inte en Link Aggregation Control Protocol (LACP) konfiguration för nätverksgränssnitten. Det här är en konfiguration som inte stöds.

## <a name="high-availability-requirements-for-storsimple"></a>Krav för hög tillgänglighet för StorSimple

Maskinvaruplattform som ingår i StorSimple-lösningen har funktioner för tillgänglighet och tillförlitlighet som utgör en grund för hög tillgänglighet, feltolerant lagringsinfrastruktur i ditt datacenter. Det finns dock krav och bästa praxis som du bör följa för att säkerställa tillgängligheten för din StorSimple-lösning. Innan du distribuerar StorSimple noggrant gå igenom följande krav och bästa praxis för StorSimple-enheten och anslutna värddatorer.

Mer information om övervakning och underhåll maskinvarukomponenter för din StorSimple-enhet går du till [använda tjänsten StorSimple Device Manager för att övervaka maskinvarukomponenter och status](storsimple-8000-monitor-hardware-status.md) och [StorSimple ersättning av maskinvara komponenten](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Krav för hög tillgänglighet och procedurer för din StorSimple-enhet

Granska följande information noggrant för att garantera hög tillgänglighet för din StorSimple-enhet.

#### <a name="pcms"></a>PCMs

StorSimple-enheter är redundant kan bytas ström och kylning moduler (PCMs). Varje PCM har tillräckligt med kapacitet för att tillhandahålla tjänsten för hela chassi. För att säkerställa hög tillgänglighet, måste båda PCMs installeras.

* Anslut din PCMs till olika strömkällor att tillhandahålla tillgänglighet om ett eluttag misslyckas.
* Om en PCM misslyckas kan du begära en ersättning omedelbart.
* Ta bort en misslyckad PCM när du har ersätter och är redo att installera den.
* Ta inte bort båda PCMs samtidigt. PCM-modulen innehåller batterimodulen säkerhetskopiering. Ta bort både PCMs resulterar i en avstängning utan batteri skydd och sparas inte enhetens tillstånd. Mer information om batteriet, gå till [Underhåll batterimodulen säkerhetskopiering](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Domänkontrollanten moduler

StorSimple-enheter är redundant kan bytas controller moduler. Modulerna som domänkontrollanten fungerar i ett aktivt-passivt. Vid en given tidpunkt en domänkontrollant modul är aktiv och betjänar, medan andra domänkontrollanter modulen är passiv. Modulen passiva domänkontrollant är påslagen och aktiveras om den aktiva styrenhet-modulen misslyckas eller tas bort. Varje domänkontrollant-modul har tillräckligt med kapacitet för att tillhandahålla tjänsten för hela chassi. Båda controller moduler måste installeras för att säkerställa hög tillgänglighet.

* Kontrollera att båda controller moduler installeras alltid.
* Om en domänkontrollant modul inte begära en ersättning omedelbart.
* Ta bort en felaktig Styrenhetsmodul när du har ersätter och är redo att installera den. Ta bort en modul för långa perioder påverkar luftflödet och därför kylningen av systemet.
* Se till att Nätverksanslutningar till båda controller moduler är identiska och anslutna nätverksgränssnitt har en identisk nätverkskonfigurationen.
* Om en domänkontrollant modul misslyckas eller behöver bytas ut, kontrollera att modulen andra domänkontrollanter är aktiv innan du ersätter modulen felaktig styrenhet. Kontrollera att en domänkontrollant är aktiv, gå till [identifiera den aktiva styrenheten på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Ta inte bort båda controller moduler på samma gång. Om en domänkontrollant växling pågår inte stänga av modulen vänteläge domänkontrollant eller ta bort den från chassit.
* Vänta minst fem minuter innan du tar bort antingen domänkontrollant modulen efter en domänkontrollant växling.

#### <a name="network-interfaces"></a>Nätverksgränssnitt

StorSimple-styrenhet moduler varje har fyra 1 Gigabit och två 10 Gigabit Ethernet-nätverkskort.

* Kontrollera att Nätverksanslutningar till båda controller moduler är identiska och nätverksgränssnitt att domänkontrollanten modulgränssnitt är anslutna till har en identisk nätverkskonfigurationen.
* När det är möjligt ska du distribuera nätverksanslutningar i olika växlar för att kontrollera tjänsttillgänglighet vid ett nätverksfel för enheten.
* När du kopplar från den enda eller det sista återstående iSCSI-gränssnittet (med IP-adresser tilldelas) inaktivera gränssnittet först och sedan koppla från kablarna. Om gränssnittet är inte ansluten först, kommer den aktiva styrenheten att växla över till den passiva styrenheten. Om passiva domänkontrollant har även dess motsvarande gränssnitt kopplas från, kommer båda domänkontrollanterna startas om flera gånger innan reglera på en domänkontrollant.
* Ansluta minst två gränssnitt för DATA i nätverket från varje domänkontrollant modul.
* Om du har aktiverat två 10 GbE-gränssnitt, distribuera dessa över olika växlar.
* När det är möjligt använda MPIO på servrar för att säkerställa att servrarna kan tolerera länken, nätverk och fel.

Mer information om nätverk enheten för hög tillgänglighet och prestanda, gå till [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD och hårddiskar

StorSimple-enheter med transistoriserade diskar (SSD) och hårddiskar (hårddiskar) som skyddas med hjälp av speglade lagringsutrymmen. Användning av speglade lagringsutrymmen gör att enheten kan tolerera fel på en eller flera SSD eller hårddiskar.

* Kontrollera att alla SSD och HDD-moduler är installerade.
* Om en SSD och HDD misslyckas kan begära en ersättning omedelbart.
* Om en SSD och HDD misslyckas eller behöver bytas ut, se till att du tar bort endast SSD och HDD som behöver bytas ut.
* Ta inte bort mer än en SSD och HDD från systemet när som helst i tid.
  Ett fel på 2 eller flera diskar för en viss typ (Hårddisk, SSD) eller på varandra följande fel inom en kort tidsperiod kan resultera i system-fel och potentiella dataförluster. Om detta inträffar [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md) om du behöver hjälp.
* Under ersättning, övervaka den **delade komponenter** i den **maskinvara hälsa** bladet för i SSD och HDD-enheterna. En grön bock status anger att diskarna är felfri eller OK, medan ett rött utropstecken peka anger en misslyckad SSD eller Hårddisk.
* Vi rekommenderar att du konfigurerar molnögonblicksbilder för alla volymer som du behöver skydda vid ett systemfel.

#### <a name="ebod-enclosure"></a>EBOD hölje

StorSimple enhetsmodell 8600 innehåller en utökad Bunch av diskar (EBOD) bilaga utöver primära höljet. En EBOD innehåller EBOD domänkontrollanterna och hårddiskar (hårddiskar) som skyddas med hjälp av speglade lagringsutrymmen. Användning av speglade lagringsutrymmen gör att enheten kan tolerera fel på en eller flera hårddiskar. EBOD höljet är ansluten till det primära höljet via redundanta SAS-kablar.

* Kontrollera att båda EBOD hölje controller moduler, både SAS-kablar och alla hårddiskar installeras alltid.
* Om en EBOD hölje controller modul inte begära en ersättning omedelbart.
* Om en EBOD hölje controller modul misslyckas, kontrollera att modulen andra domänkontrollanter är aktiv innan du ersätter modulen misslyckades. Kontrollera att en domänkontrollant är aktiv, gå till [identifiera den aktiva styrenheten på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Under en EBOD styrenhet modulen ersättning kontinuerligt övervaka status för komponenten i StorSimple enheten Manager-tjänsten genom att öppna **övervakaren** > **maskinvara hälsa**.
* Om en SAS-misslyckas eller behöver bytas ut (Microsoft-supporten involveras för att göra sådant fastställande), se till att du tar bort endast SAS kabeln som behöver bytas ut.
* Ta inte samtidigt bort båda SAS-kablar från systemet när som helst i tid.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Rekommendationer för hög tillgänglighet för din värddatorer

Granska noggrant följande rekommenderade metoder för att säkerställa hög tillgänglighet för värdar som är anslutna till din StorSimple-enhet.

* Konfigurera StorSimple med [tvånods-filen server-klusterkonfigurationer][1]. Tar bort enskilda felpunkter på felet och bygga i redundans på värddatorns sida, blir hela lösningen hög tillgänglighet.
* Använda ständigt tillgängliga (CA) resurser som är tillgängliga med Windows Server 2012 (SMB 3.0) för hög tillgänglighet under växling vid fel i lagringsstyrenheter. För ytterligare information för att konfigurera filserverkluster och ständigt tillgängliga resurser med Windows Server 2012, referera till den här [videodemonstration](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om StorSimple system gränser](storsimple-8000-limits.md).
* [Lär dig att distribuera StorSimple-lösningen](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
