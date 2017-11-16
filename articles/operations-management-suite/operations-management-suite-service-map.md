---
title: "Använd Tjänstkarta lösningen i Operations Management Suite | Microsoft Docs"
description: "Tjänstkarta är en Operations Management Suite-lösning som automatiskt identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Den här artikeln innehåller information för att distribuera Tjänstkarta i din miljö och använda den i en mängd olika scenarier."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: c07290a5003189b0b773bd9b9c995400b424c7f4
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="use-the-service-map-solution-in-operations-management-suite"></a>Använd Tjänstkarta lösningen i Operations Management Suite
Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Med Tjänstkartan, kan du visa dina servrar på samma sätt som du betrakta dem: som sammanlänkade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar över en TCP-ansluten arkitektur med ingen konfiguration krävs för andra än installation av en agent.

Den här artikeln beskriver hur du använder Tjänstkartan. Information om hur du konfigurerar Tjänstkarta och onboarding agenter finns [konfigurera Tjänstkarta lösning i Operations Management Suite](operations-management-suite-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Användningsfall: Se IT bearbetar beroende medveten

### <a name="discovery"></a>Identifiering
Tjänstkarta skapar automatiskt en gemensam referens karta över beroenden mellan dina servrar, processer och tjänster från tredje part. Den identifieras och mappas alla TCP-beroenden, identifiera oväntat anslutningar, fjärrsystem du beroende från tredje part och beroenden till traditionella mörka delar av nätverket, till exempel Active Directory. Tjänstkarta identifierar misslyckade nätverksanslutningar som de hanterade systemen försöker göra kan hjälpa dig att identifiera potentiella server felkonfiguration och avbrott nätverksproblem.

### <a name="incident-management"></a>Hantering av incidenter
Tjänstkarta hjälper till att eliminera överraskningar av problemet isolering genom att visa hur datorerna är anslutna och påverkar varandra. Förutom identifierar misslyckade anslutningar kan identifiera felkonfigurerad belastningsutjämnare, konstigt eller för stora belastningen på kritiska tjänster och obehöriga klienter som utvecklare datorer kommunicerar med olika system. Du kan också se om en Ändringshändelse på en backend-dator med hjälp av integrerad arbetsflöden med Operations Management Suite ändra spårning eller tjänsten förklarar orsaken till en incident.

### <a name="migration-assurance"></a>Säkerhet för migrering
Med hjälp av en Tjänstkarta kan du effektivt planera, påskynda och validera Azure migreringar som ser till att inget kvar och oväntat avbrott, sker inte. Du kan identifiera alla beroende av varandra system som behöver migreras tillsammans, utvärdera systemkonfiguration och kapacitet och identifiera om ett system som körs fortfarande används av användare eller är en kandidat för avställning av i stället för migrering. När övergången är klar kan kontrollera du på klientbelastningen och identitet för att kontrollera att testsystem och kunder ansluter. Om dina subnätsdefinitioner för planering och brandväggen har problem, peka misslyckade anslutningar i Tjänstkartan maps du vilka datorer som behöver ansluta.

### <a name="business-continuity"></a>Verksamhetskontinuitet
Om du använder Azure Site Recovery och behöver hjälp definierar recovery sekvensen för din miljö för programmet, Tjänstkarta kan automatiskt visa hur system är beroende av varandra för att säkerställa att är din återställningsplan tillförlitlig. Genom att välja en kritisk server eller en grupp och visa dess klienter, kan du identifiera vilka frontend-system kan återskapa servern har återställts och är tillgänglig. Du kan däremot identifiera vilka system för att återställa innan dina fokus system har återställts genom att titta på kritiska servrar backend-beroenden.

### <a name="patch-management"></a>Uppdateringshantering
Tjänstkarta förbättrar din användning av Operations Management Suite System Update Assessment genom att visa andra grupper och servrar beroende på din tjänst så att meddela dem i förväg innan du anteckna dina system för korrigering. Tjänstkarta förbättrar också uppdateringshantering i Operations Management Suite genom att visa om dina tjänster är tillgänglig och korrekt ansluten efter de korrigeras och startas om.


## <a name="mapping-overview"></a>Översikt över mappning
Tjänstkarta agenter samla in information om alla TCP-anslutna processer på servern där de är installerade och information om inkommande och utgående anslutningar för varje process. Du kan välja datorer eller grupper som har Tjänstkarta agenter visualisera beroenden under ett angivet tidsintervall i listan i det vänstra fönstret. Datorn beroende mappar fokuserar på en specifik dator och de visar alla datorer som är direkt TCP-klienter eller servrar för denna dator.  Datorgruppen maps Visa uppsättningar av servrar och deras beroenden.

![Tjänsten: översikt](media/oms-service-map/service-map-overview.png)

Datorer kan expanderas i kartan för att visa processer som körs med aktiva nätverksanslutningar under det valda tidsintervallet. När en fjärrdator med en Tjänstkarta-agent har expanderats för att visa information om visas de processer som kommunicerar med fokus datorn. Antal utan Agent frontend datorer som ansluter till fokus dator visas på vänster sida av de processer som de ansluter till. Om fokus datorn gör en anslutning till en backend-dator som har ingen agent kan med backend-server i en Port servergrupp, tillsammans med andra anslutningar till samma portnummer.

Som standard visar Tjänstkarta maps de senaste 30 minuterna av beroendeinformation. Du kan fråga för historiska tidsintervall för upp till en timme att visa hur beroenden slås tidigare (till exempel vid en incident eller innan en ändring inträffat) med hjälp av kontroller längst upp till vänster. Tjänstkarta data lagras i 30 dagar i betald arbetsytor och 7 dagar i kostnadsfria arbetsytor.

## <a name="status-badges-and-border-coloring"></a>Status märken och kantlinje färgläggning
Längst ned på varje server i kartan kan vara en lista över status Aktivitetsikoner förmedla statusinformation om servern. Aktivitetsikoner betyda att det finns vissa relevant information om servern från en Operations Management Suite-lösningen integreringar. Klicka på en Aktivitetsikon går du direkt till information om statusen i den högra rutan. Tillgängliga status Aktivitetsikoner inkluderar aviseringar, helpdesk, ändringar, säkerhet och uppdateringar.

Beroende på allvarlighetsgrad för status-Aktivitetsikoner datorn nod kantlinjer vara färgade röd (kritisk), gult (varning) eller blå (information). Färgen som representerar de svåraste status för alla status-Aktivitetsikoner. En grå kantlinje anger en nod som har inga indikatorer.

![Status Aktivitetsikoner](media/oms-service-map/status-badges.png)

## <a name="machine-groups"></a>Datorgrupper
Datorgrupper kan du se maps uppbyggd kring en uppsättning servrar, inte bara en så att du kan se alla medlemmar i ett flera nivåer program eller server-kluster i en karta.

Användare välja vilka servrar som tillhör en grupp och välj ett namn för gruppen.  Du kan sedan välja att visa gruppen med alla processer och anslutningar eller visa med bara processer och anslutningar som är direkt relaterade till andra medlemmar i gruppen.

![Datorgruppen](media/oms-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Skapa en grupp datorer
Om du vill skapa en grupp, Välj dator eller datorer som du vill att datorerna i listan och klicka på **Lägg till i grupp**.

![Skapa grupp](media/oms-service-map/machine-groups-create.png)

Där kan du välja **Skapa nytt** och ge gruppen ett namn.

![Gruppnamnet](media/oms-service-map/machine-groups-name.png)

>[!NOTE]
>Datorgrupper som är för tillfället begränsad till 10 servrar, men vi planerar att öka denna gräns snart.

### <a name="viewing-a-group"></a>Visa en grupp
När du har skapat några grupper kan visa du dem genom att välja fliken grupper.

![Grupper](media/oms-service-map/machine-groups-tab.png)

Sedan väljer du gruppnamnet för att visa kartan för att datorgruppen.
![Datorgruppen](media/oms-service-map/machine-group.png) beskrivs de datorer som tillhör gruppen i vitt i kartan.

Expandera gruppen visar en lista över de datorer som ingår i gruppen för datorn.

![Datorer i datorgruppen](media/oms-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrera efter processer
Du kan växla kartvyn mellan visar alla processer och anslutningar i gruppen och bara de som är direkt relaterade till den datorgruppen.  Standardvyn är att visa alla processer.  Du kan ändra vyn genom att klicka på filterikonen ovanför kartan.

![Filtret grupp](media/oms-service-map/machine-groups-filter.png)

När **alla processer** är markerad kartan innehåller alla processer och anslutningar på varje dator i gruppen.

![Bearbetar alla datorgruppen](media/oms-service-map/machine-groups-all.png)

Om du ändrar Visa endast **grupp-anslutna processer**, kartan ska begränsas till dessa processer och anslutningar som är anslutna direkt till andra datorer i gruppen, skapar en förenklad vy.

![Datorgruppen filtrerade processer](media/oms-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Lägga till datorer i en grupp
Om du vill lägga till datorer i en befintlig grupp, markera kryssrutorna bredvid de datorer du vill använda och klicka sedan på **Lägg till i grupp**.  Välj den grupp som du vill lägga till datorer till.
 
### <a name="removing-machines-from-a-group"></a>Om du tar bort datorer från en grupp
I listan grupper expanderar du gruppnamnet att visa en lista med datorer i gruppen datorer.  Klicka på menyn tre punkter bredvid den dator du vill ta bort och välj **ta bort**.

![Ta bort datorn från grupp](media/oms-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Ta bort eller byta namn på en grupp
Klicka på menyn tre punkter bredvid gruppnamnet på i listan.

![Menyn för dator-grupp](media/oms-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Rollikoner
Vissa processer hantera specifika roller på datorer: servrar, programservrar, databas och så vidare. Tjänstkarta annoterar process- och rutor med Rollikoner för att identifiera en överblick över av en process eller server spelar.

| Rollikon | Beskrivning |
|:--|:--|
| ![Webbserver](media/oms-service-map/role-web-server.png) | Webbserver |
| ![App-servern](media/oms-service-map/role-application-server.png) | Programserver |
| ![Databasserver](media/oms-service-map/role-database.png) | Databasserver |
| ![LDAP-servern](media/oms-service-map/role-ldap.png) | LDAP-servern |
| ![SMB-servern](media/oms-service-map/role-smb.png) | SMB-servern |

![Rollikoner](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Misslyckade anslutningar
Misslyckade anslutningar visas i Tjänstkartan maps för processer och datorer, med en röd streckad linje som anger att en klientdator inte kan nå en process eller port. Misslyckade anslutningar rapporteras från alla system med en distribuerad Tjänstkarta agent om systemet är den försöker den felande anslutningen. Tjänstkarta mäter den här processen genom att följa TCP sockets som inte att upprätta en anslutning. Det här felet kan orsaka från en brandvägg, en felaktig konfiguration i klienten eller servern eller en fjärransluten tjänst som inte var tillgänglig.

![Misslyckade anslutningar](media/oms-service-map/failed-connections.png)

Förstå misslyckade anslutningar kan hjälpa dig med felsökning, verifiering av migreringen, säkerhetsanalys och övergripande arkitektur förstå. Misslyckade anslutningar är det ibland ofarligt, men de pekar ofta direkt på ett problem, till exempel en växling vid fel miljö plötsligt blir kan inte nås eller två programnivåerna att du inte kan kommunicera efter en molnmigrering.

## <a name="client-groups"></a>Klientgrupper
Klientgrupper är rutorna på kartan som representerar klientdatorer som inte har beroende agenter. En enda klientgrupp representerar klienter för en enskild process eller datorn.

![Klientgrupper](media/oms-service-map/client-groups.png)

Välj en grupp om du vill visa IP-adresserna för servrar i en klientgrupp. Innehållet i gruppen visas i den **klienten gruppegenskaper** fönstret.

![Egenskaper för klient](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Serverport grupper
Serverport grupper är rutor som representerar server-portarna på servrar som inte har beroende agenter. Rutan innehåller serverporten och en uppräkning av antalet servrar med anslutningar till porten. Expandera rutan att visa enskilda servrar och anslutningar. Om det finns endast en server i rutan, visas namnet eller IP-adressen.

![Serverport grupper](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Snabbmeny
Klicka på ellipsknappen (...) högst upp rätt på servern visas snabbmenyn för servern.

![Misslyckade anslutningar](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Läs in server-karta
Klicka på **belastningen Server kartan** går du till en ny mappning med den valda servern som den nya datorn fokus.

### <a name="show-self-links"></a>Visa självlänkar
Klicka på **visa Self-Links** omritningar servernoden, inklusive sådana självlänkar som TCP-anslutningar som börjar och slutar på processer i servern. Om självlänkar visas, menykommandona till **dölja Self-Links**, så att du kan inaktivera dem.

## <a name="computer-summary"></a>Datorn sammanfattning
Den **datorn sammanfattning** rutan innehåller en översikt över en server-operativsystem, beroende antal och data från andra Operations Management Suite-lösningar. Dessa data inkluderar prestandamått, supportavdelningen tjänstbiljetter, för spårning av ändringar, säkerhet och uppdateringar.

![Datorn sammanfattningsfönstret](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Dator- och egenskaper
Du kan välja datorer och processer för att få ytterligare kontext om deras egenskaper när du navigerar en Tjänstkarta karta. Datorer som innehåller information om DNS-namn, IPv4-adresser, CPU och minne kapacitet, VM-typ, operativsystem och version, senast omstart tid och ID: N för sina Operations Management Suite och Tjänstkarta agenter.

![Egenskapsrutan för datorn](media/oms-service-map/machine-properties.png)

Du kan samla in information från operativsystemet metadata om processer, inklusive processnamn, beskrivningen, användarnamn och domän (på Windows), företagets namn, produktnamn, produktversionen, arbetskatalogen, kommandoraden och process som körs Starttid.

![Egenskapsrutan för processen](media/oms-service-map/process-properties.png)

Den **Processammanfattning** tillhandahåller ytterligare information om processens anslutningen, inklusive dess bundna portar, inkommande och utgående anslutningar och kunde inte anslutningar.

![Processen sammanfattningsfönstret](media/oms-service-map/process-summary.png)

## <a name="operations-management-suite-alerts-integration"></a>Operations Management Suite aviseringar integrering
Tjänstkarta integreras med Operations Management Suite aviseringar att visa Eldad aviseringar för den valda servern i det valda tidsintervallet. Servern visar en ikon om det finns aktuella aviseringar och **datorn aviseringar** fönstret visas aviseringarna.

![Datorn aviseringspanelen](media/oms-service-map/machine-alerts.png)

Skapa en aviseringsregel som utlöses för en specifik dator om du vill aktivera tjänsten för att visa relevanta aviseringarna. För att skapa rätt aviseringar:
- Innehåller en instruktion i gruppen per dator (till exempel **datorn intervall 1 minut**).
- Välj att varna baserat på mått mått.

![Konfiguration av varning](media/oms-service-map/alert-configuration.png)


## <a name="operations-management-suite-log-events-integration"></a>Operations Management Suite logga händelser integrering
Tjänstkarta integreras med Log-sökning för att visa en uppräkning av alla tillgängliga logghändelser för den valda servern under det valda tidsintervallet. Du kan klicka på en rad i listan över händelse antal att hoppa till loggen Sök och se enskilda logghändelser.

![Datorn logghändelser fönstret](media/oms-service-map/log-events.png)

## <a name="operations-management-suite-service-desk-integration"></a>Operations Management Suite-supporten integrering
Tjänstkarta integrering med IT Service Management-anslutningstjänsten sker automatiskt när båda lösningarna är aktiverad och konfigurerad i Operations Management Suite-arbetsyta. Integrering i Tjänstkartan är märkt ”helpdesk”. Mer information finns i [centralt hantera ITSM arbetsobjekt med hjälp av IT Service Management-anslutningstjänsten](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

Den **datorn helpdesk** fönstret innehåller alla händelser som IT-tjänsthantering för den valda servern i det valda tidsintervallet. Servern visas en ikon om det aktuella objekt och datorn helpdesk-fönstret visas de.

![Datorn helpdesk fönstret](media/oms-service-map/service-desk.png)

Klicka för att öppna objektet i din lösning för anslutna ITSM **visa arbetsobjekt**.

Om du vill visa information om objektet i loggen sökning, klickar du på **visas i loggen Sök**.


## <a name="operations-management-suite-change-tracking-integration"></a>Operations Management Suite ändringsspårning integrering
Tjänstkarta integrering med ändringsspårning sker automatiskt när båda lösningarna är aktiverad och konfigurerad i Operations Management Suite-arbetsyta.

Den **datorn ändringsspårning** fönstret visar alla ändringar, med den senaste först, tillsammans med en länk till öka detaljnivån till loggen Sök efter ytterligare information.

![Datorn ändringsspårning fönstret](media/oms-service-map/change-tracking.png)

Följande bild är en detaljerad vy av en ConfigurationChange händelse som kan uppstå när du har valt **visas i logganalys**.

![ConfigurationChange händelse](media/oms-service-map/configuration-change-event.png)


## <a name="operations-management-suite-performance-integration"></a>Operations Management Suite prestanda integrering
Den **prestanda på en dator** fönstret standard prestandastatistik för den valda servern. Mätvärdena inkludera CPU-användning, minnesanvändning, nätverks-byte skickas och tas emot och en lista över de översta processerna med nätverks-byte skickas och tas emot.

![Datorn prestanda fönstret](media/oms-service-map/machine-performance.png)

Om du vill visa prestandadata, du kan behöva [aktivera lämplig logganalys prestandaräknarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  De räknare du vill aktivera:

Windows:
- Processor(*)\\% processortid
- Minne\\% använda dedikerade byte
- Nätverk Adapter(*)\\skickade byte/sek
- Nätverk Adapter(*)\\mottagna byte/sek

Linux:
- Processor(*)\\% processortid
- Memory(*)\\använt minne i procent
- Nätverk Adapter(*)\\skickade byte/sek
- Nätverk Adapter(*)\\mottagna byte/sek

För att få prestandadata som nätverk, måste du också aktiverat överföring Data 2.0 lösningen i Operations Management Suite.
 
## <a name="operations-management-suite-security-integration"></a>Integrering av Operations Management Suite-säkerhet
Tjänstkarta integrering med säkerhet och granska sker automatiskt när båda lösningarna är aktiverad och konfigurerad i Operations Management Suite-arbetsyta.

Den **säkerhet dator** visar data från Operations Management Suite säkerhet och granska lösningen för den valda servern. Fönstret visar en sammanfattning av alla utestående säkerhetsproblem för servern under det valda tidsintervallet. Klicka på någon av säkerhet problem flyttar ned till en logg sökning efter information om dem.

![Datorn säkerhet fönstret](media/oms-service-map/machine-security.png)


## <a name="operations-management-suite-updates-integration"></a>Integrering av Operations Management Suite-uppdateringar
Tjänstkarta integrering med uppdateringshantering sker automatiskt när båda lösningarna är aktiverad och konfigurerad i Operations Management Suite-arbetsyta.

Den **datorn uppdateringar** rutan visar data från Operations Management Suite uppdateringshantering lösningen för den valda servern. Fönstret visar en sammanfattning av uppdateringar som saknas för servern under det valda tidsintervallet.

![Datorn ändringsspårning fönstret](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-poster
Tjänstkarta dator- och lagerdata är tillgänglig för [Sök](../log-analytics/log-analytics-log-searches.md) i logganalys. Du kan använda dessa data för scenarier som inkluderar planering för migrering, kapacitetsanalys, identifiering och felsökning av prestanda på begäran.

En post genereras per timme för varje unikt datornamn och process, förutom de poster som genereras när en process eller datorn startas inte eller är publicerat till Tjänstkarta. Dessa poster har egenskaper i följande tabeller. Fält och värden i ServiceMapComputer_CL händelser mappning till fält av dator-resurs i ServiceMap Azure Resource Manager API. Fält och värden i ServiceMapProcess_CL händelser mappning till fält av processen resurs i ServiceMap Azure Resource Manager API. Fältet ResourceName_s matchar namnfältet i motsvarande Resource Manager-resurs. 

>[!NOTE]
>Eftersom Tjänstkarta funktioner växer, kan dessa fält ändras.

Det finns internt genererade egenskaper som kan användas för att identifiera unika processer och datorer:

- Dator: Använd ResourceId eller ResourceName_s att unikt identifiera en dator inom en Operations Management Suite-arbetsyta.
- Process: Använd ResourceId att unikt identifiera en process i en Operations Management Suite-arbetsyta. ResourceName_s är unikt inom ramen för den dator där processen körs (MachineResourceName_s) 

Eftersom flera poster kan finnas för en angiven process och datorer i ett angivet tidsintervall kan frågor returnera flera poster för samma dator eller process. Lägg till om du vill inkludera endast den senaste posten ”| dedupliceringen ResourceId ”i frågan.

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL poster
Poster med en typ av *ServiceMapComputer_CL* har inventeringsdata för servrar med Tjänstkarta agenter. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| Resurs-ID | Den unika identifieraren för en dator i arbetsytan |
| ResourceName_s | Den unika identifieraren för en dator i arbetsytan |
| ComputerName_s | Datorn FQDN |
| Ipv4Addresses_s | En lista över serverns IPv4-adresser |
| Ipv6Addresses_s | En lista över serverns IPv6-adresser |
| DnsNames_s | En matris med DNS-namn |
| OperatingSystemFamily_s | Windows- eller Linux |
| OperatingSystemFullName_s | Det fullständiga namnet på operativsystemet  |
| Bitness_s | Bitness för datorn (32-bitars eller 64-bitars)  |
| PhysicalMemory_d | Fysiskt minne i MB |
| Cpus_d | Antal processorer |
| CpuSpeed_d | CPU-hastighet i MHz|
| VirtualizationState_s | *Okänd*, *fysiska*, *virtuella*, *hypervisor-programmet* |
| VirtualMachineType_s | *Hyper-v*, *vmware*och så vidare |
| VirtualMachineNativeMachineId_g | VM-ID som tilldelats av dess hypervisor-programmet |
| VirtualMachineName_s | Namnet på den virtuella datorn |
| BootTime_t | Starttiden |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL poster
Poster med en typ av *ServiceMapProcess_CL* har inventeringsdata för TCP-anslutna processer på servrar med Tjänstkarta agenter. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| Resurs-ID | Den unika identifieraren för en process på arbetsytan |
| ResourceName_s | Den unika identifieraren för en process på datorn som kör|
| MachineResourceName_s | Resursnamn för datorn |
| ExecutableName_s | Namnet på den körbara filen process |
| StartTime_t | Starttid för process-pool |
| FirstPid_d | Första PID i processpoolen |
| Description_s | Processbeskrivning |
| CompanyName_s | Namnet på företaget |
| InternalName_s | Det interna namnet |
| ProductName_s | Namnet på produkten |
| ProductVersion_s | Produktversionen |
| FileVersion_s | Filversion |
| CommandLine_s | Kommandoraden |
| ExecutablePath _s | Sökvägen till den körbara filen |
| WorkingDirectory_s | Arbetskatalogen |
| Användarnamn | Det konto som processen körs |
| UserDomain | Domänen som processen körs |


## <a name="sample-log-searches"></a>Exempel på loggsökningar

### <a name="list-all-known-machines"></a>Visa en lista över alla kända datorer
Typ = ServiceMapComputer_CL | dedupliceringen ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Visa en lista med fysiskt minneskapaciteten för alla hanterade datorer.
Typ = ServiceMapComputer_CL | Välj PhysicalMemory_d ComputerName_s | Dedupliceringen ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>Lista över datornamn, DNS, IP- och OS.
Typ = ServiceMapComputer_CL | Välj ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s | dedupliceringen ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med ”sql” i kommandoraden
Typ = ServiceMapProcess_CL CommandLine_s = \*sql\* | dedupliceringen ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste post) efter resursnamn
Typ = ServiceMapComputer_CL ”m-4b9c93f9-bc37-46df-b43c-899ba829e07b” | dedupliceringen ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste post) med IP-adress
Typ = ServiceMapComputer_CL ”10.229.243.232” | dedupliceringen ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Visa en lista över alla kända processer på en angiven dator
Typ = ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b” | dedupliceringen ResourceId

### <a name="list-all-computers-running-sql"></a>Lista med alla datorer som kör SQL
Typ = ServiceMapComputer_CL ResourceName_s IN {typ = ServiceMapProcess_CL \*sql\* | Distinkta MachineResourceName_s} | dedupliceringen ResourceId | Distinkta ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista över alla unika versioner av curl i min datacenter
Typ = ServiceMapProcess_CL ExecutableName_s = curl | Distinkta ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en datorgrupp för alla datorer som kör CentOS
Typ = ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinkta ComputerName_s


## <a name="rest-api"></a>REST API
Alla server, process och beroende data i Tjänstkartan är tillgängliga via den [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>diagnostik och användningsdata
Microsoft samlar automatiskt in användnings- och prestandadata via din användning av tjänsten Tjänstkartan. Microsoft använder informationen för att tillhandahålla och förbättra kvalitet, säkerhet och integritet Tjänstkarta-tjänsten. Data innehåller information om konfigurationen av din programvara, till exempel operativsystem och version, IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i [sekretesspolicy för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Nästa steg
Lär dig mer om [logga sökningar](../log-analytics/log-analytics-log-searches.md) i logganalys att hämta data som samlas in av Tjänstkartan.


## <a name="troubleshooting"></a>Felsökning
Finns det [felsökning i Konfigurera Tjänstkarta dokumentet](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Feedback
Du har feedback för oss om Tjänstkarta eller den här dokumentationen?  Besök vår [User Voice sidan](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), där du kan föreslår funktioner eller rösta in befintliga förslag.
