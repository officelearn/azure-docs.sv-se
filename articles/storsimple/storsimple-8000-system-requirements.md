---
title: Systemkrav för StorSimple 8000-serien | Microsoft-dokument
description: Beskriver krav på programvara, nätverk och hög tillgänglighet och metodtips för en Microsoft Azure StorSimple-lösning.
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
ms.openlocfilehash: 3032585c6f0a5cc6143eee06b12b6def50cd7cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297709"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>LagringSimple 8000-seriens programvara, hög tillgänglighet och nätverkskrav

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Välkommen till Microsoft Azure StorSimple. I den här artikeln beskrivs viktiga systemkrav och metodtips för Din StorSimple-enhet och för lagringsklienter som använder enheten. Vi rekommenderar att du granskar informationen noggrant innan du distribuerar Ditt StorSimple-system och sedan hänvisar tillbaka till den efter behov under distribution och efterföljande åtgärd.

Systemkraven omfattar:

* **Programvarukrav för lagringsklienter** - beskriver operativsystem som stöds och eventuella ytterligare krav för dessa operativsystem.
* **Nätverkskrav för StorSimple-enheten** – ger information om de portar som måste vara öppna i brandväggen för att möjliggöra iSCSI-, moln- eller hanteringstrafik.
* **Hög tillgänglighetskrav för StorSimple** – beskriver krav på hög tillgänglighet och metodtips för din StorSimple-enhet och värddator.

## <a name="software-requirements-for-storage-clients"></a>Programvarukrav för lagringsklienter

Följande programvarukrav gäller för lagringsklienter som kommer åt din StorSimple-enhet.

| Operativsystem som stöds | Version krävs | Ytterligare krav/anmärkningar |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-volymer stöds endast för användning på följande Windows-disktyper:<ul><li>Enkel volym på enkel disk</li><li>Enkel och speglad volym på dynamisk disk</li></ul>Endast de programvaru-iSCSI-initierare som finns internt i operativsystemet stöds. Maskinvaru-iSCSI-initierare stöds inte.<br></br>Windows Server 2012 och 2016 tunna etablerings- och ODX-funktioner stöds om du använder en StorSimple iSCSI-volym.<br><br>StorSimple kan skapa tunt etablerade och fullständigt etablerade volymer. Det går inte att skapa delvis etablerade volymer.<br><br>Det kan ta lång tid att formatera om en tunt etablerad volym. Vi rekommenderar att du tar bort volymen och sedan skapar en ny i stället för att formatera om. Men om du fortfarande föredrar att formatera om en volym:<ul><li>Kör följande kommando innan formatera om för att undvika fördröjningar av utrymmesåterklamation: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>När formateringen är klar använder du följande kommando för att återaktivera utrymmesåterklamation:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Använd snabbkorrigeringen för Windows Server 2012 enligt beskrivningen i [KB 2878635](https://support.microsoft.com/kb/2870270) på din Windows Server-dator.</li></ul></li></ul></ul> Om du konfigurerar StorSimple Snapshot Manager eller StorSimple-adapter för SharePoint går du till [Programvarukrav för valfria komponenter](#software-requirements-for-optional-components). |
| VMware ESX |5,5 och 6,0 |Stöds med VMware vSphere som iSCSI-klient. VAAI-blockfunktionen stöds med VMware vSphere på StorSimple-enheter. |
| Linux RHEL/CentOS |5, 6 och 7 |Stöd för Linux iSCSI-klienter med open-iSCSI-initierarversioner 5, 6 och 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX stöds för närvarande inte med StorSimple.


## <a name="software-requirements-for-optional-components"></a>Programvarukrav för valfria komponenter

Följande programvarukrav gäller för de valfria StorSimple-komponenterna (StorSimple Snapshot Manager och StorSimple Adapter för SharePoint).

| Komponent | Värdplattform | Ytterligare krav/anmärkningar |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Användning av StorSimple Snapshot Manager på Windows Server krävs för säkerhetskopiering/återställning av speglade dynamiska diskar och för alla programkonsekventa säkerhetskopior.<br> StorSimple Snapshot Manager stöds endast på Windows Server 2008 R2 SP1 (64-bitars), Windows Server 2012 R2 och Windows Server 2012.<ul><li>Om du använder Window Server 2012 måste du installera .NET 3.5–4.5 innan du installerar StorSimple Snapshot Manager.</li><li>Om du använder Windows Server 2008 R2 SP1 måste du installera Windows Management Framework 3.0 innan du installerar StorSimple Snapshot Manager.</li></ul> |
| StorSimple-adapter för SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>StorSimple-kort för SharePoint stöds endast i SharePoint 2010 och SharePoint 2013.</li><li>RBS kräver SQL Server Enterprise Edition, version 2008 R2 eller 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Nätverkskrav för din StorSimple-enhet

Din StorSimple-enhet är en låst enhet. Portar måste dock öppnas i brandväggen för att möjliggöra iSCSI-, moln- och hanteringstrafik. I följande tabell visas de portar som måste öppnas i brandväggen. I den här tabellen refererar *i* eller *inkommande* till den riktning från vilken inkommande klient begär åtkomst till enheten. *Utgående* eller *utgående* refererar till i vilken riktning Din StorSimple-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| Port nr<sup>1,2</sup> | In eller ut | Port-scope | Krävs | Anteckningar |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Ut |WAN |Inga |<ul><li>Utgående port används för internetåtkomst för att hämta uppdateringar.</li><li>Den utgående webbproxyn kan konfigureras av användaren.</li><li>Om du vill tillåta systemuppdateringar måste den här porten också vara öppen för styrenhetens fasta IPs.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Ut |WAN |Ja |<ul><li>Utgående port används för åtkomst till data i molnet.</li><li>Den utgående webbproxyn kan konfigureras av användaren.</li><li>Om du vill tillåta systemuppdateringar måste den här porten också vara öppen för styrenhetens fasta IPs.</li><li>Den här porten används också på båda styrenheterna för skräpinsamling.</li></ul> |
| UDP 53 (DNS) |Ut |WAN |I vissa fall; se anteckningar. |Den här porten krävs bara om du använder en Internetbaserad DNS-server. |
| UDP 123 (NTP) |Ut |WAN |I vissa fall; se anteckningar. |Den här porten krävs bara om du använder en Internetbaserad NTP-server. |
| TCP 9354 |Ut |WAN |Ja |Den utgående porten används av StorSimple-enheten för att kommunicera med StorSimple Device Manager-tjänsten. |
| 3260 (iSCSI) |I |LAN |Inga |Den här porten används för att komma åt data via iSCSI. |
| 5985 |I |LAN |Inga |Inkommande port används av StorSimple Snapshot Manager för att kommunicera med StorSimple-enheten.<br>Den här porten används också när du fjärransluter till Windows PowerShell för StorSimple via HTTP. |
| 5986 |I |LAN |Inga |Den här porten används när du fjärransluter till Windows PowerShell för StorSimple via HTTPS. |

<sup>1</sup> Inga inkommande portar behöver öppnas på det offentliga Internet.

<sup>2</sup> Om flera portar har en gateway-konfiguration bestäms den utgående dirigerade trafikordningen baserat på portroutningsordningen som beskrivs i [portroutning](#routing-metric)nedan.

<sup>3</sup> Den fasta IP-adresserna på StorSimple-enheten måste vara dirigerbara och kunna ansluta till Internet direkt eller via den konfigurerade webbproxyn. De fasta IP-adresserna används för att underhålla uppdateringarna till enheten och för skräpinsamling. Om enhetskontrollanterna inte kan ansluta till Internet via de fasta IPsna kan du inte uppdatera din StorSimple-enhet och skräpinsamlingen fungerar inte korrekt.

> [!IMPORTANT]
> Kontrollera att brandväggen inte ändrar eller dekrypterar någon TLS-trafik mellan StorSimple-enheten och Azure.


### <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönstren för att filtrera inkommande och utgående trafik. Din StorSimple-enhet och Tjänsten StorSimple Device Manager är beroende av andra Microsoft-program som Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönstren som är associerade med dessa program kan användas för att konfigurera brandväggsregler. Det är viktigt att förstå att url-mönstren som är associerade med dessa program kan ändras. Detta kräver i sin tur nätverksadministratören att övervaka och uppdatera brandväggsregler för din StorSimple om och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på StorSimples fasta IP-adresser, frikostigt i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> Enhets-IPs -adresser för enheten (källa) ska alltid ställas in på alla aktiverade nätverksgränssnitt. Mål-IP-adresser ska ställas in [på AZURE datacenter IP-intervall](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-mönster för Azure-portalen

| URL-mönster | Komponent/funktionalitet | Enhets-IPs |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple Device Manager-tjänsten<br>Tjänsten Åtkomstkontroll<br>Azure Service Bus<br>Autentiseringstjänst |Molnaktiverade nätverksgränssnitt |
| `https://*.backup.windowsazure.com` |Enhetsregistrering |ENDAST DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallelse av certifikat |Molnaktiverade nätverksgränssnitt |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-lagringskonton och övervakning |Molnaktiverade nätverksgränssnitt |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |Endast fast IPs-handkontroll |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Endast fast IPs-handkontroll |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Supportpaket |Molnaktiverade nätverksgränssnitt |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-mönster för Azure Government-portalen

| URL-mönster | Komponent/funktionalitet | Enhets-IPs |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple Device Manager-tjänsten<br>Tjänsten Åtkomstkontroll<br>Azure Service Bus<br>Autentiseringstjänst |Molnaktiverade nätverksgränssnitt |
| `https://*.backup.windowsazure.us` |Enhetsregistrering |ENDAST DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Återkallelse av certifikat |Molnaktiverade nätverksgränssnitt |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure-lagringskonton och övervakning |Molnaktiverade nätverksgränssnitt |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servrar<br> |Endast fast IPs-handkontroll |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Endast fast IPs-handkontroll |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Supportpaket |Molnaktiverade nätverksgränssnitt |

### <a name="routing-metric"></a>Mått på routning

Ett routningsmått är associerat med gränssnitten och gatewayen som dirigerar data till de angivna nätverken. Routningsmått används av routningsprotokollet för att beräkna den bästa sökvägen till ett visst mål, om det lär sig att flera sökvägar finns till samma mål. Ju lägre routningsmått, desto högre prioritet.

I samband med StorSimple, om flera nätverksgränssnitt och gateways är konfigurerade för att kanalisera trafik, kommer routningsmåtten att spela in för att bestämma i vilken ordning gränssnitten ska användas. Routningsmåtten kan inte ändras av användaren. Du kan dock `Get-HcsRoutingTable` använda cmdleten för att skriva ut routningstabellen (och måtten) på Din StorSimple-enhet. Mer information om Cmdlet Get-HcsRoutingTable i [Felsökning av StorSimple-distribution](storsimple-troubleshoot-deployment.md).

Routningsmåttalgoritmen som används för uppdatering 2 och senare versioner kan förklaras på följande sätt.

* En uppsättning förutbestämda värden har tilldelats nätverksgränssnitt.
* Tänk dig en exempeltabell som visas nedan med värden som tilldelats de olika nätverksgränssnitten när de är molnaktiverade eller molnaktiverade men med en konfigurerad gateway. Observera att de värden som tilldelas här endast är exempelvärden.

    | Nätverksgränssnitt | Molnaktiverad | Moln inaktiverad med gateway |
    |-----|---------------|---------------------------|
    | Uppgifter 0  | 1            | -                        |
    | Uppgifter 1  | 2            | 20                       |
    | Uppgifter 2  | 3            | 30                       |
    | Uppgifter 3  | 4            | 40                       |
    | Uppgifter 4  | 5            | 50                       |
    | Uppgifter 5  | 6            | 60                       |


* Den ordning i vilken molntrafiken ska dirigeras via nätverksgränssnitten är:
  
    *Data 0 > Data 1 > Datum 2 > Data 3 > Data 4 > Data 5*
  
    Detta kan förklaras med följande exempel.
  
    Överväg en StorSimple-enhet med två molnaktiverade nätverksgränssnitt, Data 0 och Data 5. Data 1 till data 4 är moln inaktiverade men har en konfigurerad gateway. Den ordning i vilken trafiken kommer att dirigeras för den här enheten kommer att vara:
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *Siffrorna inom parentes anger respektive routningsmått.*
  
    Om Data 0 misslyckas dirigeras molntrafiken via data 5. Med tanke på att en gateway har konfigurerats i alla andra nätverk, om både Data 0 och Data 5 skulle misslyckas, kommer molntrafiken att gå igenom data 1.
* Om ett molnaktiverat nätverksgränssnitt misslyckas, är 3 försök med en 30 sekunders fördröjning för att ansluta till gränssnittet. Om alla försök misslyckas dirigeras trafiken till nästa tillgängliga molnaktiverade gränssnitt som bestäms av routningstabellen. Om alla molnaktiverade nätverksgränssnitt misslyckas, kommer enheten att växla över till den andra styrenheten (ingen omstart i det här fallet).
* Om det finns ett VIP-fel för ett iSCSI-aktiverat nätverksgränssnitt kommer det att göras 3 försök igen med 2 sekunders fördröjning. Detta har varit detsamma från tidigare versioner. Om alla iSCSI-nätverksgränssnitt misslyckas sker en rederingsväg för styrenheten (åtföljs av en omstart).
* En avisering höjs också på din StorSimple-enhet när det uppstår ett VIP-fel. Mer information finns i [varningsreferensen](storsimple-8000-manage-alerts.md).
* När det gäller återförsök har iSCSI företräde framför molnet.
  
    Tänk på följande exempel: En StorSimple-enhet har två nätverksgränssnitt aktiverade, Data 0 och Data 1. Data 0 är molnaktiverat medan Data 1 är både molnet och iSCSI-aktiverade. Inga andra nätverksgränssnitt på den här enheten är aktiverade för molnet eller iSCSI.
  
    Om data 1 misslyckas, eftersom det är det sista iSCSI-nätverksgränssnittet, resulterar detta i en kontrollant redundans till data 1 på den andra styrenheten.

### <a name="networking-best-practices"></a>Metodtips för nätverkande

Utöver ovanstående nätverkskrav följer du följande metodtips för att din StorSimple-lösning ska fungera optimalt:

* Se till att din StorSimple-enhet alltid har en dedikerad bandbredd på 40 Mbit/s.. Den här bandbredden bör inte delas (eller allokeringen bör garanteras med hjälp av QoS-principer) med andra program.
* Se till att nätverksanslutningen till Internet alltid är tillgänglig. Sporadiska eller opålitliga Internetanslutningar till enheterna, inklusive ingen internetanslutning alls, resulterar i en konfiguration som inte stöds.
* Isolera iSCSI- och molntrafiken genom att ha dedikerade nätverksgränssnitt på enheten för iSCSI- och molnåtkomst. Mer information finns i hur du [ändrar nätverksgränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces) på StorSimple-enheten.
* Använd inte en LACP-konfiguration (Link Aggregation Control Protocol) för nätverksgränssnitten. Konfigurationen stöds inte.

## <a name="high-availability-requirements-for-storsimple"></a>Höga tillgänglighetskrav för StorSimple

Maskinvaruplattformen som ingår i StorSimple-lösningen har funktioner för tillgänglighet och tillförlitlighet som ger en grund för en högtillgänglig, feltolerant lagringsinfrastruktur i ditt datacenter. Det finns dock krav och metodtips som du bör följa för att säkerställa tillgängligheten för din StorSimple-lösning. Innan du distribuerar StorSimple bör du noggrant granska följande krav och metodtips för StorSimple-enheten och anslutna värddatorer.

Mer information om hur du övervakar och underhåller maskinvarukomponenterna på StorSimple-enheten finns i [Använd Tjänsten StorSimple Device Manager för att övervaka maskinvarukomponenter och status](storsimple-8000-monitor-hardware-status.md) och ersättning för [StorSimple-maskinvarukomponenter](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Krav och procedurer för hög tillgänglighet för din StorSimple-enhet

Granska följande information noggrant för att säkerställa hög tillgänglighet för din StorSimple-enhet.

#### <a name="pcms"></a>PCM-datorer

StorSimple-enheter innehåller redundanta, ström- och kylmoduler som kan bytas under drift (PCM). Varje PCM har tillräcklig kapacitet för att ge service för hela chassit. För att säkerställa hög tillgänglighet måste båda pcm-datorerna installeras.

* Anslut dina PCM till olika strömkällor för att ge tillgänglighet om en strömkälla misslyckas.
* Om en PCM misslyckas, begär en ersättning omedelbart.
* Ta bara bort en misslyckad PCM när du har ersättningen och är redo att installera den.
* Ta inte bort båda PCM samtidigt. PCM-modulen innehåller batterimodulen för säkerhetskopiering. Om du tar bort båda PCM:erna stängs de av utan batteriskydd, och enhetstillståndet sparas inte. Mer information om batteriet finns i [Underhåll batterimodulen](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)för säkerhetskopiering .

#### <a name="controller-modules"></a>Controller-moduler

StorSimple-enheter innehåller redundanta styrenhetsmoduler som kan bytas under drift. Styrenheterna fungerar på ett aktivt/passivt sätt. Vid varje given tidpunkt är en styrenhetsmodul aktiv och tillhandahåller service, medan den andra styrenhetsmodulen är passiv. Den passiva styrenhetsmodulen är påslagen och tas i drift om den aktiva styrenhetsmodulen misslyckas eller tas bort. Varje styrenhetsmodul har tillräcklig kapacitet för att ge service för hela chassit. Båda styrenhetsmodulerna måste installeras för att säkerställa hög tillgänglighet.

* Se till att båda styrenhetsmodulerna alltid är installerade.
* Om en styrenhetsmodul misslyckas, begär en ersättning omedelbart.
* Ta bara bort en misslyckad styrenhetsmodul när du har ersättningen och är redo att installera den. Att ta bort en modul under längre perioder kommer att påverka luftflödet och därmed kylningen av systemet.
* Kontrollera att nätverksanslutningarna till båda styrenhetsmodulerna är identiska och att de anslutna nätverksgränssnitten har en identisk nätverkskonfiguration.
* Om en styrenhetsmodul misslyckas eller behöver bytas ut kontrollerar du att den andra styrenhetsmodulen är i aktivt tillstånd innan du ersätter den misslyckade styrenhetsmodulen. Om du vill kontrollera att en handkontroll är aktiv går du till [Identifiera den aktiva handkontrollen på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Ta inte bort båda styrenhetsmodulerna samtidigt. Om en rederingshandlar för styrenheten pågår ska du inte stänga av standbystyrenheten eller ta bort den från chassit.
* Efter en rederingsav redundans från handkontrollen väntar du minst fem minuter innan du tar bort någon av styrenhetsmodulerna.

#### <a name="network-interfaces"></a>Nätverksgränssnitt

StorSimple enhetsstyrenhet moduler har vardera fyra 1 Gigabit och två 10 Gigabit Ethernet nätverksgränssnitt.

* Kontrollera att nätverksanslutningarna till båda styrenhetsmodulerna är identiska och de nätverksgränssnitt som styrenhetsmodulgränssnitten är anslutna till har en identisk nätverkskonfiguration.
* Distribuera nätverksanslutningar över olika växlar om möjligt för att säkerställa tjänstens tillgänglighet i händelse av fel på en nätverksenhet.
* När du kopplar från det enda eller sista återstående iSCSI-aktiverade gränssnittet (med TILLdelade IPs) inaktiverar du gränssnittet först och kopplar sedan ur kablarna. Om gränssnittet kopplas ur först, då det kommer att orsaka den aktiva styrenheten att växla över till den passiva styrenheten. Om den passiva styrenheten också har sina motsvarande gränssnitt urkopplade, kommer båda styrenheterna att startas om flera gånger innan de sätter sig på en styrenhet.
* Anslut minst två DATA-gränssnitt till nätverket från varje styrenhetsmodul.
* Om du har aktiverat de två 10 GbE-gränssnitten kan du distribuera dem över olika växlar.
* Använd när det är möjligt, använd MPIO på servrar för att säkerställa att servrarna tål ett länk-, nätverks- eller gränssnittsfel.

Mer information om hur du nätverkar enheten för hög tillgänglighet och prestanda finns i [Installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSD och hårddiskar

StorSimple-enheter innehåller SSD-diskar (Solid State) och hårddiskar (HDDs) som är skyddade med speglade utrymmen. Användning av speglade utrymmen säkerställer att enheten kan tolerera fel på en eller flera SSD-enheter eller hårddiskar.

* Kontrollera att alla SSD- och HDD-moduler är installerade.
* Om en SSD eller hårddisk misslyckas, begär en ersättning omedelbart.
* Om en SSD eller hdd misslyckas eller kräver utbyte, se till att du tar bort endast SSD eller hårddisk som kräver utbyte.
* Ta inte bort mer än en SSD eller hdd från systemet vid någon tidpunkt.
  Ett fel på 2 eller fler diskar av viss typ (HDD, SSD) eller på varandra följande fel inom en kort tidsperiod kan resultera i systemfel och potentiell dataförlust. Om detta inträffar [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp.
* Under utbytet övervakar du **de delade komponenterna** i **maskinvaruhälsobladet** för enheterna i SSD och HDDs. En grön kontrollstatus indikerar att diskarna är felfria eller OK, medan ett rött utropstecken indikerar en misslyckad SSD eller hdd.
* Vi rekommenderar att du konfigurerar ögonblicksbilder av molnet för alla volymer som du behöver skydda i händelse av ett systemfel.

#### <a name="ebod-enclosure"></a>EBOD-kapsling

StorSimple enhet modell 8600 innehåller en utökad massa diskar (EBOD) hölje utöver den primära höljet. En EBOD innehåller EBOD-styrenheter och hårddiskar som är skyddade med speglade utrymmen. Användning av speglade utrymmen säkerställer att enheten kan tolerera fel på en eller flera hårddiskar. EBOD-höljet är anslutet till det primära höljet via redundanta SAS-kablar.

* Se till att både EBOD-moduleringsmoduler för kapsling, både SAS-kablar och alla hårddiskar alltid är installerade.
* Om en EBOD-höljestyrenhetsmodul misslyckas, begär en ersättning omedelbart.
* Om en EBOD-höljestyrenhetsmodul misslyckas kontrollerar du att den andra styrenhetsmodulen är aktiv innan du byter ut den felaktiga modulen. Om du vill kontrollera att en handkontroll är aktiv går du till [Identifiera den aktiva handkontrollen på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Under en ersättning för EBOD-styrenhetsmodul övervakar du kontinuerligt komponentens status i Tjänsten StorSimple Device Manager genom att komma åt > **monitormaskinvarans hälsotillstånd**. **Monitor**
* Om en SAS-kabel misslyckas eller kräver utbyte (Microsoft Support bör vara inblandade för att göra en sådan bestämning), se till att du tar bort endast SAS-kabeln som kräver utbyte.
* Ta inte samtidigt bort båda SAS-kablarna från systemet vid någon tidpunkt.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Rekommendationer för hög tillgänglighet för värddatorerna

Läs noggrant igenom de här metodtipsen för att säkerställa hög tillgänglighet för värdar som är anslutna till din StorSimple-enhet.

* Konfigurera StorSimple med [konfigurationer för filserverkluster för tvånoder][1]. Genom att ta bort enskilda felpunkter och bygga i redundans på värdsidan blir hela lösningen mycket tillgänglig.
* Använd kontinuerligt tillgängliga (CA) resurser som är tillgängliga med Windows Server 2012 (SMB 3.0) för hög tillgänglighet under redundans av lagringskontrollanterna. Mer information om hur du konfigurerar filserverkluster och kontinuerligt tillgängliga resurser med Windows Server 2012 finns i den här [videodemonstrationen](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Nästa steg

* [Läs mer om StorSimple-systemgränser](storsimple-8000-limits.md).
* [Lär dig hur du distribuerar din StorSimple-lösning](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
