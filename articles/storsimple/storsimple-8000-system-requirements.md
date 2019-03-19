---
title: Systemkrav för StorSimple 8000-serien | Microsoft Docs
description: Beskriver programvara, nätverk, och krav på hög tillgänglighet och bästa praxis för en Microsoft Azure StorSimple-lösning.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013636"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000-serien programvara, hög tillgänglighet och nätverkskrav

## <a name="overview"></a>Översikt

Välkommen till Microsoft Azure StorSimple. Den här artikeln beskriver viktiga krav och bästa praxis för StorSimple-enheten och för lagringsklienter enheten. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din StorSimple-systemet och referera tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkraven är:

* **Programvarukrav för storage-klienter** -beskriver operativsystem som stöds och eventuella ytterligare krav för dessa operativsystem.
* **Nätverkskrav för StorSimple-enheten** – innehåller information om vilka portar som måste vara öppna i brandväggen för att tillåta för iSCSI, i molnet eller hantering av trafik.
* **Krav på hög tillgänglighet för StorSimple** – beskriver kraven på hög tillgänglighet och bästa praxis för din StorSimple-enheten och värden dator.

## <a name="software-requirements-for-storage-clients"></a>Programvarukrav för storage-klienter

Följande programvarukrav finns för storage-klienter som har åtkomst till din StorSimple-enhet.

| Operativsystem som stöds | Version som krävs | Ytterligare krav/anteckningar |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-volymer stöds för användning på endast följande Windows disk objekttyper:<ul><li>Enkel volym på en grundläggande disk</li><li>Enkla och speglade volymen på dynamisk disk</li></ul>Endast software iSCSI-initierare finns inbyggt i operativsystemet stöds. Maskinvara iSCSI-initierarna stöds inte.<br></br>Windows Server 2012 och 2016 tunn allokering och ODX funktioner stöds om du använder en StorSimple iSCSI-volymen.<br><br>StorSimple kan skapa tunt allokerade och helt allokerade volymer. Det går inte att skapa delvis allokerade volymer.<br><br>Formatera om en tunt etablerad volym kan det ta lång tid. Vi rekommenderar att volymen och sedan skapa ett nytt lösenord i stället för att formatera om. Men om du fortfarande vill formatera om en volym:<ul><li>Kör följande kommando innan den formatera om för att undvika förseningar för frigöring av utrymme: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>När formateringen är klar använder du följande kommando för att återaktivera frigöring av utrymme:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Installera Windows Server 2012-snabbkorrigeringen som beskrivs i [KB 2878635](https://support.microsoft.com/kb/2870270) till Windows Server-datorn.</li></ul></li></ul></ul> Om du konfigurerar StorSimple Snapshot Manager eller StorSimple Adapter för SharePoint går du till [programvarukrav för valfria komponenter](#software-requirements-for-optional-components). |
| VMware ESX |5.5 och 6.0 |Stöds med VMware vSphere som iSCSI-klient. VAAI-block-funktionen stöds med VMware vSphere på StorSimple-enheter. |
| Linux RHEL/CentOS |5, 6 och 7 |Stöd för Linux iSCSI-klienter med öppna iSCSI-initierare versioner 5, 6 och 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX stöds för närvarande inte med StorSimple.


## <a name="software-requirements-for-optional-components"></a>Programvarukrav för valfria komponenter

Följande programvarukrav finns för de valfria StorSimple-komponenterna (StorSimple Snapshot Manager och StorSimple Adapter för SharePoint).

| Komponent | Virtualiseringsvärdens plattform | Ytterligare krav/anteckningar |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Användning av StorSimple Snapshot Manager på Windows Server krävs för säkerhetskopiering/återställning av speglade dynamiska diskar och eventuella programkonsekventa säkerhetskopior.<br> StorSimple Snapshot Manager stöds endast på Windows Server 2008 R2 SP1 (64-bitars), Windows Server 2012 R2 och Windows Server 2012.<ul><li>Om du använder Windows Server 2012, måste du installera .NET 3.5 – 4.5 innan du installerar StorSimple Snapshot Manager.</li><li>Om du använder Windows Server 2008 R2 SP1, måste du installera Windows Management Framework 3.0 innan du installerar StorSimple Snapshot Manager.</li></ul> |
| StorSimple-adapter för SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple Adapter för SharePoint stöds bara på SharePoint 2010 och SharePoint 2013.</li><li>RBS kräver SQL Server Enterprise Edition, version 2008 R2 eller 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Nätverkskraven för din StorSimple-enhet

StorSimple-enheten är en låst enhet. Portar som måste öppnas i brandväggen för att tillåta för iSCSI-, moln- och hanteringstrafik. I följande tabell visas de portar som måste öppnas i brandväggen. I den här tabellen *i* eller *inkommande* refererar till den riktning som inkommande klientbegäranden åtkomst till din enhet. *Ut* eller *utgående* refererar till den riktning som din StorSimple-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| Nej. port<sup>1,2</sup> | In eller ut | Port omfång | Krävs | Anteckningar |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Utdata |WAN |Nej |<ul><li>Utgående port används för åtkomst till Internet för att hämta uppdateringar.</li><li>Utgående webbproxy kan konfigureras av användaren.</li><li>Om du vill tillåta systemuppdateringar, måste den här porten också vara öppen för kontrollenheternas fasta IP-adresser.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Utdata |WAN |Ja |<ul><li>Utgående port används för att komma åt data i molnet.</li><li>Utgående webbproxy kan konfigureras av användaren.</li><li>Om du vill tillåta systemuppdateringar, måste den här porten också vara öppen för kontrollenheternas fasta IP-adresser.</li><li>Den här porten används också på båda styrenheterna för skräpinsamling.</li></ul> |
| UDP 53 (DNS) |Utdata |WAN |I vissa fall. Se information. |Den här porten krävs endast om du använder en Internet-baserad DNS-server. |
| UDP 123 (NTP) |Utdata |WAN |I vissa fall. Se information. |Den här porten krävs endast om du använder en Internetbaserad NTP-server. |
| TCP 9354 |Utdata |WAN |Ja |Den utgående porten används av StorSimple-enheten för att kommunicera med StorSimple Device Manager-tjänsten. |
| 3260 (iSCSI) |I |LAN |Nej |Den här porten används för att ansluta till data via iSCSI. |
| 5985 |I |LAN |Nej |Inkommande port används av StorSimple Snapshot Manager för att kommunicera med StorSimple-enheten.<br>Den här porten används också när du fjärransluta till Windows PowerShell för StorSimple via HTTP. |
| 5986 |I |LAN |Nej |Den här porten används när du fjärransluta till Windows PowerShell för StorSimple via HTTPS. |

<sup>1</sup> inga ingående portar måste du öppna på Internet.

<sup>2</sup> om flera portar har en gatewaykonfiguration, utgående routade trafik ordningen bestäms baserat på port ordningen som beskrivs i [Port routning](#routing-metric)nedan.

<sup>3</sup> kontrollenheternas fasta IP-adresser på StorSimple-enheten måste vara dirigerbara och kunna ansluta till Internet, direkt eller via konfigurerade webbproxy. De fasta IP-adresserna används för att hantera uppdateringarna till enheten och för skräpinsamling. Om styrenheterna inte kan ansluta till Internet via den fasta IP-adresser, du kan inte uppdatera din StorSimple-enhet och skräpinsamling fungerar inte korrekt.

> [!IMPORTANT]
> Se till att brandväggen inte ändrar eller dekryptera SSL trafik mellan StorSimple-enhet och Azure.


### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera det inkommande och utgående trafik. Din StorSimple-enhet och StorSimple Device Manager-tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. Detta kräver i sin tur att nätverksadministratören kan övervaka och uppdatera brandväggsregler för StorSimple som och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på StorSimple fasta IP-adresser, liberally i de flesta fall. Du kan dock använda den här informationen för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> Enheten (källa) IP-adresser ska alltid vara inställd på alla aktiverade nätverksgränssnitt. Målets IP-adresser ska vara inställd på [Azure datacenter IP-adressintervall](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-mönster för Azure-portalen

| URL-mönster | Komponenten/funktioner | Enhetens IP-adresser |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple Device Manager-tjänst<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänsten |Moln-aktiverat nätverksgränssnitt |
| `https://*.backup.windowsazure.com` |Enhetsregistrering |DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallade certifikat |Moln-aktiverat nätverksgränssnitt |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-konton och övervakning |Moln-aktiverat nätverksgränssnitt |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |Kontrollenheternas fasta IP-adresser endast |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Kontrollenheternas fasta IP-adresser endast |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Supportpaket |Moln-aktiverat nätverksgränssnitt |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-mönster för Azure Government-portalen

| URL-mönster | Komponenten/funktioner | Enhetens IP-adresser |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple Device Manager-tjänst<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänsten |Moln-aktiverat nätverksgränssnitt |
| `https://*.backup.windowsazure.us` |Enhetsregistrering |DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallade certifikat |Moln-aktiverat nätverksgränssnitt |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure storage-konton och övervakning |Moln-aktiverat nätverksgränssnitt |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |Kontrollenheternas fasta IP-adresser endast |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Kontrollenheternas fasta IP-adresser endast |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Supportpaket |Moln-aktiverat nätverksgränssnitt |

### <a name="routing-metric"></a>Routning mått

Ett mått som routning är associerad med gränssnitten och gateway som dirigerar data till de angivna nätverk. Routning måttet används av routingprotokollet för att beräkna den bästa vägen till ett givet mål, om den lär sig att det finns flera vägar till samma mål. Nedre routning mått, desto högre prioritet.

I samband med StorSimple, om flera nätverksgränssnitt och gateway har konfigurerats på kanal-trafik, kommer routning måtten till play för att fastställa relativ ordning gränssnitt som ska få användas. Routning mått kan inte ändras av användaren. Du kan dock använda den `Get-HcsRoutingTable` cmdlet för att skriva ut routning tabell (och mått) på StorSimple-enheten. Mer information om cmdlet Get-HcsRoutingTable i [felsökning StorSimple distribution](storsimple-troubleshoot-deployment.md).

Routning mått algoritmen som används för uppdatering 2 och senare versioner kan förklaras på följande sätt.

* En uppsättning förinställda värden har tilldelats till nätverksgränssnitt.
* Överväg en exempeltabell som visas nedan med värden som tilldelats till de olika nätverksgränssnitt när de är moln-aktiverat eller molnet inaktiverad men med en konfigurerad gateway. Observera de värden som angetts är endast exempelvärden.

    | Nätverksgränssnitt | Cloud-enabled | Molnet inaktiv med gateway |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* Ordningen som molntrafiken ska dirigeras via nätverksgränssnitt är:
  
    *Data 0 > Data 1 > Date 2 > Data 3 > Data 4 > Data 5*
  
    Detta kan förklaras följande exempel.
  
    Överväg en StorSimple-enhet med två moln-aktiverat nätverksgränssnitt, Data 0 och 5 för Data. Data 1 till 4 Data är molnet inaktiv men har en konfigurerad gateway. Den ordning som trafik dirigeras för den här enheten kommer att:
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Nummer inom parentes visar respektive Routning mått.*
  
    Om det inte går att Data 0, ska cloud trafiken dirigeras till Data 5. Med tanke på att en gateway har konfigurerats på alla andra nätverk, om både Data 0 och 5 Data skulle misslyckas, passerar moln-trafik Data 1.
* Om ett moln-aktiverat nätverksgränssnitt misslyckas, är du 3 nya försök med en 30 sekunder innan du ansluter till gränssnittet. Om alla nya försök misslyckas, dirigeras trafiken till nästa tillgängliga moln-aktiverat gränssnittet systemets routningstabellen. Om alla moln-aktiverat nätverk gränssnitt misslyckas, kommer enheten växlar över till den andra styrenheten (ingen omstart i det här fallet).
* Om det finns en VIP-fel för en iSCSI-aktiverade nätverksgränssnitt, kommer det finnas 3 nya försök med en fördröjning i 2 sekunder. Det här beteendet har har stannat samma från tidigare versioner. Om alla nätverksgränssnitt för iSCSI-misslyckas, kommer en kontrollenhetsredundans inträffar (tillsammans med en omstart).
* En avisering genereras även på StorSimple-enheten när det finns en VIP-fel. Mer information går du till [Avisera Snabbreferens](storsimple-8000-manage-alerts.md).
* När det gäller återförsök företräde iSCSI framför molnet.
  
    Ta följande som exempel: En StorSimple-enhet har två nätverksgränssnitt aktiverat Data 0 och Data 1. Data 0 är moln-aktiverat Data 1 är båda molnet och iSCSI-aktiverat. Inga andra nätverksgränssnitt på den här enheten har aktiverats för molnet eller iSCSI.
  
    Om Data 1 misslyckas, gjort detta är det senaste iSCSI-nätverksgränssnittet, resulterar detta i en kontrollenhetsredundans till 1 av Data på den andra styrenheten.

### <a name="networking-best-practices"></a>Metodtips för nätverk

Förutom ovanstående nätverkskraven för optimala prestanda av din StorSimple-lösning, följer du till följande metoder:

* Kontrollera att din StorSimple-enhet har en dedikerad 40 Mbit/s bandbredd (eller mer) vid alla tidpunkter. Den här bandbredden inte ska delas (eller allokering bör garanteras genom att använda QoS-principer) med andra program.
* Kontrollera nätverksanslutningen till Internet är tillgänglig vid alla tidpunkter. Sporadiska eller instabilt Internet-anslutningar till enheter, inklusive utan Internet-anslutning alls, resulterar i en konfiguration som inte stöds.
* Isolera iSCSI och molntrafik genom att ha dedikerade nätverksgränssnitt på din enhet för iSCSI- och åtkomst. Läs mer om hur du [ändra nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces) på StorSimple-enheten.
* Använd inte en konfiguration för Link Aggregation Control Protocol (LACP) för nätverksgränssnitten. Det här är en konfiguration som inte stöds.

## <a name="high-availability-requirements-for-storsimple"></a>Krav på hög tillgänglighet för StorSimple

Maskinvaruplattform som ingår i StorSimple-lösningen innehåller funktioner för tillgänglighet och tillförlitlighet som ger en grund för en mycket tillgänglig och feltolerant lagringsinfrastruktur i ditt datacenter. Det finns dock krav och bästa praxis som du bör följa för att säkerställa tillgängligheten för din StorSimple-lösning. Innan du distribuerar StorSimple noga igenom följande krav och bästa praxis för StorSimple-enheten och anslutna värddatorer.

Mer information om övervakning och underhåll av maskinvarukomponenter för din StorSimple-enhet går du till [använda StorSimple Device Manager-tjänsten för att övervaka de komponenter och status](storsimple-8000-monitor-hardware-status.md) och [StorSimple komponenten maskinvaruersättning](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Krav på hög tillgänglighet och procedurer för din StorSimple-enhet

Granska följande information noggrant för att garantera hög tillgänglighet för din StorSimple-enhet.

#### <a name="pcms"></a>PCMs

StorSimple-enheter är redundant kan bytas strömförsörjning och kylning moduler (PCMs). Varje PCM har tillräckligt med kapacitet att tillhandahålla tjänst för hela chassin. För att säkerställa hög tillgänglighet, måste båda PCMs installeras.

* Anslut din PCMs till olika strömkällor att ge tillgänglighet om en strömkälla misslyckas.
* Om en PCM misslyckas kan du begära en ersättning omedelbart.
* Ta bort en PCM endast när du har ersättningen och är redo att installera den.
* Ta inte bort båda PCMs samtidigt. PCM-modulen innehåller extrabatteri-modulen. Ta bort både PCMs resulterar i en avstängning utan batteri skydd och enhetens tillstånd sparas inte. Mer information om batteriet går du till [Underhåll modulen extrabatteri](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Controller moduler

StorSimple-enheter är redundant kan bytas controller moduler. Modulerna som domänkontrollant fungera i ett aktivt-passivt. Vid en given tidpunkt, en domänkontrollant-modul är aktiv och betjänar, medan andra domänkontrollanter modulen är passiva. Modulen passiva styrenheten har startats och aktiveras om den aktiva kontrollenhet-modulen misslyckas eller har tagits bort. Varje domänkontrollant-modul har tillräckligt med kapacitet att tillhandahålla tjänst för hela chassin. Båda controller-modulerna måste vara installerat för att säkerställa hög tillgänglighet.

* Se till att båda controller-moduler installeras vid alla tidpunkter.
* Om en domänkontrollant-modulen misslyckas kan du begära en ersättning omedelbart.
* Ta bort en misslyckad controller modul endast när du har ersättningen och är redo att installera den. Ta bort en modul för längre påverkar luftflödet och kylning av systemet.
* Se till att Nätverksanslutningar till båda controller-modulerna är identiska och anslutet nätverksgränssnitt har en identisk nätverkskonfiguration.
* Om en domänkontrollant-modulen misslyckas eller behöver bytas ut, se till att modulen andra domänkontrollanter är i aktivt tillstånd innan du ersätter den misslyckade controller-modulen. Kontrollera att en domänkontrollant är aktiv, gå till [identifiera den aktiva kontrollanten på din enhet](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Ta inte bort båda controller-moduler på samma gång. Om en kontrollenhetsredundans pågår kan inte stänga av modulen kontrollenhet i vänteläge eller ta bort den från chassit.
* Vänta minst fem minuter innan du tar bort antingen controller-modulen när du har en kontrollenhetsredundans.

#### <a name="network-interfaces"></a>Nätverksgränssnitt

StorSimple-enhet controller moduler varje har fyra 1 Gigabit och två 10 Gigabit Ethernet-nätverksgränssnitt.

* Se till att Nätverksanslutningar till båda controller-modulerna är identiska och nätverksgränssnitt att kontrollanten modulgränssnitt är anslutna till har en identisk nätverkskonfiguration.
* Om det är möjligt kan du distribuera nätverksanslutningar i olika växlar till att garantera tjänstens tillgänglighet i händelse av ett fel på nätverket.
* När du kopplar från den enda eller det sista återstående iSCSI-aktiverade gränssnittet (med IP-adresser tilldelade), inaktivera gränssnittet först och sedan koppla från kablarna. Om gränssnittet är inte ansluten först, kommer den aktiva kontrollenheten att växla över till den passiva styrenheten. Om den passiva styrenheten har även dess motsvarande gränssnitt kopplas från, kommer båda styrenheterna startas om flera gånger innan reglera på en domänkontrollant.
* Anslut minst två gränssnitt för DATA till nätverket från varje domänkontrollant-modul.
* Om du har aktiverat två 10 GbE-gränssnitt, distribuera dessa för olika växlar.
* Om det är möjligt använda MPIO på servrar för att säkerställa att servrarna kan tolerera fel på en länk, nätverk eller gränssnitt.

Mer information om nätverk din enhet för hög tillgänglighet och prestanda går du till [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD och hårddiskar

StorSimple-enheter inkluderar solid state-diskar (SSD) och hårddiskar (HDD) som skyddas med hjälp av speglade lagringsutrymmen. Användning av speglingsutrymmen säkerställer att enheten ska kunna tolerera fel på en eller flera SSD eller HDD.

* Se till att alla SSD och HDD-moduler är installerade.
* Om en SSD och HDD misslyckas kan du begära en ersättning omedelbart.
* Om en SSD och HDD misslyckas eller behöver bytas ut, se till att du tar bort endast SSD och HDD som behöver bytas ut.
* Ta inte bort mer än en SSD eller HDD från systemet när som helst i tid.
  Ett fel för 2 eller fler diskar på en viss typ (HDD, SSD) eller på varandra följande fel inom en kort tidsperiod kan resultera i system-fel och potentiella dataförluster. Om detta inträffar [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp.
* Under ersättning, övervaka den **delade komponenter** i den **hälsotillstånd för maskinvara** bladet för i SSD och HDD-enheterna. En grön bock status anger att diskarna är felfri eller OK, medan ett rött utropstecken peka anger en misslyckad SSD eller HDD.
* Vi rekommenderar att du konfigurerar molnögonblicksbilder för att alla volymer som du behöver skydda händelse av ett systemfel.

#### <a name="ebod-enclosure"></a>EBOD hölje

Enhetsmodell för StorSimple 8600 innehåller en utökad Bunch av diskar (EBOD) bilaga utöver primära höljet. En EBOD innehåller EBOD-styrenheter och hårddiskar (HDD) som skyddas med hjälp av speglade lagringsutrymmen. Användning av speglingsutrymmen säkerställer att enheten ska kunna tolerera fel på en eller flera hårddiskar. EBOD-hölje är ansluten till det primära höljet via redundanta SAS-kablar.

* Se till att båda EBOD hölje controller moduler, både SAS-kablar och alla hårddiskar har installerats hela tiden.
* Om en EBOD hölje controller modul misslyckas kan du begära en ersättning omedelbart.
* Om en EBOD hölje controller modul misslyckas, se till att modulen andra domänkontrollanter är aktiv innan du ersätter modulen misslyckades. Kontrollera att en domänkontrollant är aktiv, gå till [identifiera den aktiva kontrollanten på din enhet](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Under en EBOD controller modulen ersättning kontinuerligt övervaka statusen för komponenterna i StorSimple Device Manager-tjänsten genom att gå till **övervakaren** > **hälsotillstånd för maskinvara**.
* Om en SAS-misslyckas eller behöver bytas ut (Microsoft Support bör ingå för att göra sådant fastställande), se till att du tar bort bara den SAS-kabel som behöver bytas ut.
* Ta inte samtidigt bort båda SAS-kablar från systemet när som helst i tid.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Rekommendationer för hög tillgänglighet för dina värddatorer

Noga igenom dessa bästa metoder för att garantera hög tillgänglighet för värdar som är anslutna till din StorSimple-enhet.

* Konfigurera StorSimple med [tvånods filserverkonfigurationer för klustret][1]. Genom att ta bort enskilda felpunkter på fel och att skapa med redundans på värd-sida, blir hela lösningen med hög tillgänglighet.
* Använd ständigt tillgängliga (CA) resurser som är tillgängliga med Windows Server 2012 (SMB 3.0) för hög tillgänglighet under en redundansväxling av lagringsstyrenheter. Ytterligare information för att konfigurera filserverkluster och ständigt tillgängliga resurser med Windows Server 2012 finns i det här [videodemonstration](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Nästa steg

* [Mer information om begränsningar för StorSimple-system](storsimple-8000-limits.md).
* [Lär dig att distribuera din StorSimple-lösning](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
