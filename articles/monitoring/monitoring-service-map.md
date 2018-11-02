---
title: Med lösningen Tjänstkarta i Azure | Microsoft Docs
description: Tjänstkarta är en lösning i Azure som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln innehåller information för att distribuera Service Map i din miljö och använder den på en mängd olika scenarier.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2018
ms.author: magoedte
ms.openlocfilehash: ad696168674e0c04c457c06a81d4e723981eee47
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914761"
---
# <a name="using-service-map-solution-in-azure"></a>Med lösningen Tjänstkarta i Azure
Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Med Service Map, du kan visa dina servrar på det sätt som du tänker på dem: sammankopplat system som levererar viktiga tjänster. Service Map ser du anslutningarna mellan servrar, processer, svarstid för inkommande och utgående anslutningar, och portar i alla TCP-anslutna arkitekturer utan konfiguration måste installera en agent.

Den här artikeln innehåller information om onboarding och med hjälp av Tjänstkarta. Information om hur du konfigurerar Service Map och publicering av agenter finns i [konfigurerar Service Map lösning i Azure]( monitoring-service-map-configure.md).

>[!NOTE]
>Om du redan har distribuerat Service Map kan du nu också se dina kartor i Azure Monitor för virtuella datorer, som innehåller ytterligare funktioner för att övervaka VM-hälsa och prestanda. Mer information finns i [Azure Monitor för virtuella datorer översikt](monitoring-vminsights-overview.md).


## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Aktivera Tjänstkarta
1. I Azure-portalen klickar du på **+ skapa en resurs**.
2. I sökfältet skriver **Tjänstkarta** och tryck på **RETUR**.
3. Välj i marketplace-sökresultatsidan **Tjänstkarta** i listan.<br><br> ![Välj lösningen Tjänstkarta från Azure Marketplace-sökresultat](./media/monitoring-service-map/marketplace-search-results.png)<br>
4. På den **Tjänstkarta** översiktsfönstret, granska informationen om lösningen och klicka sedan på **skapa** att påbörja onboarding-processen till Log Analytics-arbetsytan.<br><br> ![Integrera lösningen Tjänstkarta](./media/monitoring-service-map/service-map-onboard.png).
5. I den **konfigurera en lösning** fönstret, Välj en befintlig eller skapa en ny Log Analytics-arbetsyta.  Mer information om hur du skapar en ny arbetsyta finns [skapa en Log Analytics-arbetsyta i Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md). När du har angett informationen som krävs, klickar du på **skapa**.  

Även om informationen har verifierats och lösningen har distribuerats, kan du spåra förloppet under **meddelanden** på menyn. 

Du kommer åt Service Map i Azure portal från Log Analytics-arbetsytan och välja alternativet **lösningar** i den vänstra rutan.<br><br> ![Välj alternativet för lösningar i arbetsytan](./media/monitoring-service-map/select-solution-from-workspace.png).<br> I listan med lösningar, Välj **ServiceMap(workspaceName)** och i Tjänstkarta-lösningen översikt över sidan klickar du på på panelen Tjänstkarta sammanfattning.<br><br> ![Tjänstkarta sammanfattningspanel](./media/monitoring-service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Användningsfall: gör IT bearbetar beroende medveten

### <a name="discovery"></a>Identifiering
Tjänstkarta skapar automatiskt en gemensam referens karta över beroenden mellan dina servrar, processer och tjänster från tredje part. Den identifierar och mappar alla TCP-beroenden, identifiera överraskning anslutningar, fjärrsystem du lita på från tredje part och beroenden till traditionella mörk delar av nätverket, till exempel Active Directory. Service Map identifierar misslyckade anslutningar som de hanterade systemen försöker göra, vilket hjälper dig att identifiera potentiella server felaktig konfiguration, avbrott i tjänsten och nätverksproblem.

### <a name="incident-management"></a>incidenthantering
Tjänstkarta hjälper till att eliminera felsökning som visar hur datorerna är anslutna, vilket påverkar varandra. Förutom att identifiera misslyckade anslutningar, kan den identifiera felkonfigurerad belastningsutjämnare, så konstigt eller hög belastning på kritiska tjänster och obehöriga klienter, till exempel datorer för utvecklare som pratar på produktionssystemen. Genom att använda integrerad arbetsflöden med ändringsspårning kan du kan också se om en Ändringshändelse på en backend-dator eller tjänst beskriver de grundläggande orsakerna till en incident.

### <a name="migration-assurance"></a>Migrering assurance
Med Service Map kan du effektivt planera, snabbare och verifiera Azure-migreringar, vilket hjälper till att säkerställa att ingenting kvar och förvåning avbrott sker inte. Du kan identifiera alla beroende av varandra system som behöver för att migrera tillsammans, utvärdera systemkonfiguration och kapacitet och identifiera om ett system som körs fortfarande fungerar som värd för användare eller är en kandidat för inaktivering av i stället för migrering. När övergången är klar kan du kontrollera klientbelastningen och identitet för att kontrollera att testsystem och kunder ansluter. Om dina subnätsdefinitioner för planering och brandväggen har problem kan peka misslyckade anslutningar i Service Map maps du till de system som behöver ansluta.

### <a name="business-continuity"></a>Verksamhetskontinuitet
Om du använder Azure Site Recovery och behöver hjälp med att definiera recovery sekvensen för din Programmiljö, Tjänstkarta kan automatiskt visa dig hur System beroende av varandra för att se till att är din plan tillförlitlig. Genom att välja en kritisk server eller en grupp och visa dess klienter, kan du identifiera vilka frontend system återställa när servern har återställts och är tillgängliga. Däremot genom att titta på kritiska servrar backend-beroenden, kan du identifiera vilka system för återställning innan dina fokus system har återställts.

### <a name="patch-management"></a>Uppdateringshantering
Tjänstkarta förbättrar din användning av den Systemuppdateringsbedömning genom att visa som andra team och servrar är beroende av din tjänst, så meddela dem i förväg innan du tar ned dina system för uppdatering. Tjänstkarta förbättrar också uppdateringshantering genom att visa oavsett om dina tjänster är tillgänglig och korrekt ansluten när de är korrigerade och startas om.

## <a name="mapping-overview"></a>Översikt över mappning
Service Map agenter samla in information om alla TCP-anslutna processer på servern där de är installerade och information om inkommande och utgående anslutningar för varje process.

Från listan i den vänstra rutan, kan du välja datorer eller grupper med Service Map agenter att visualisera beroenden via ett angivet tidsintervall. Datorn beroende mappas fokusera på en specifik dator, och de visar alla datorer som är direkt TCP-klienter eller servrar för denna dator.  Datorgruppen maps visar uppsättningar av servrar och deras beroenden.

![Översikt över Service Map](media/monitoring-service-map/service-map-overview.png)

Datorer kan expanderas i kartan för att visa löpande bearbeta grupper och processer med aktiva nätverksanslutningar under det valda tidsintervallet. När en fjärrdator med en tjänstkarteagent utökas för att visa information om process, visas de processer som kommunicerar med fokus-datorn. Antalet utan Agent frontend-datorer som ansluter till fokus datorn visas på vänster sida av de processer som de ansluter till. Om datorn fokus är att upprätta en anslutning till en backend-Server-dator som har ingen agent, med backend-server i en grupp för Server-Port, tillsammans med andra anslutningar till samma portnummer.

Som standard visar Service Map maps de senaste 30 minuterna av beroendeinformation. Du kan fråga maps för historiska tidsintervall på upp till en timme att visa hur beroenden tittat tidigare (exempelvis under en incident eller innan en ändring inträffat) med kontroller i det övre vänstra hörnet. Tjänstkarta data lagras i 30 dagar i betalda arbetsytor och i 7 dagar i kostnadsfria arbetsytor.

## <a name="status-badges-and-border-coloring"></a>Status för märken och kantlinje färgsättning
Längst ned på varje server i kartan kan vara en lista över status Aktivitetsikoner förmedla statusinformation om servern. Aktivitetsikonerna betyda att det finns vissa relevant information för servern från en av lösningen integreringar. Klicka på en symbol kommer du direkt till information om statusen i den högra rutan. För närvarande tillgänglig status aktivitetsikonerna omfattar aviseringar, Service Desk, ändringar, säkerhet och uppdateringar.

Beroende på allvarlighetsgrad för aktivitetsikonerna status datorn noden kantlinjer vara färgade röd (kritisk), gult (varning) eller blått (endast i informationssyfte). Färgen representerar allvarligaste status för alla för aktivitetsikonerna status. En grå kantlinje anger en nod som har inga indikatorer.

![Status Aktivitetsikoner](media/monitoring-service-map/status-badges.png)

## <a name="process-groups"></a>Processgrupper
Processgrupper kombinera processer som är associerade med en gemensam produkt eller tjänst i en processgrupp.  När en datornod expanderas visas fristående processer tillsammans med processgrupper.  Om alla inkommande och utgående anslutningar till en process i en processgrupp har misslyckats sedan anslutningen visas som misslyckades för gruppen hela processen.

## <a name="machine-groups"></a>Datorgrupper
Datorgrupper kan du se maps inriktade på en uppsättning servrar, inte bara en så att du kan se alla medlemmar i ett flerskiktat program eller server-kluster på en karta.

Användare välja vilka servrar som hör ihop i en grupp och välj ett namn för gruppen.  Du kan sedan välja att visa gruppen med alla dess processer och anslutningar eller visa den med endast processer och anslutningar som är direkt relaterade till andra medlemmar i gruppen.

![Datorgrupp](media/monitoring-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Skapa en datorgrupp
Om du vill skapa en grupp, Välj dator eller datorer som du vill på datorerna i listan och klicka på **Lägg till i grupp**.

![Skapa grupp](media/monitoring-service-map/machine-groups-create.png)

Där kan du välja **Skapa nytt** och ge gruppen ett namn.

![Namn på grupp](media/monitoring-service-map/machine-groups-name.png)

>[!NOTE]
>Datorgrupper är begränsade till 10 servrar.

### <a name="viewing-a-group"></a>Visa en grupp
När du har skapat vissa grupper, kan du visa dem genom att välja fliken grupper.

![Fliken grupper](media/monitoring-service-map/machine-groups-tab.png)

Välj sedan gruppnamn för att visa kartan för den datorn-gruppen.
![Datorgruppen](media/monitoring-service-map/machine-group.png) datorer som tillhör gruppen beskrivs i vitt på kartan.

Expandera gruppen visas en lista över de datorer som utgör den datorgruppen.

![Datorn gruppera datorer](media/monitoring-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrera efter processer
Du kan växla kartvyn mellan som visar alla processer och anslutningar i gruppen och bara de filer som är direkt relaterade till den datorgruppen.  Standardvyn är att visa alla processer.  Du kan ändra vyn genom att klicka på filter-ikonen ovanför kartan.

![Filtergrupp](media/monitoring-service-map/machine-groups-filter.png)

När **alla processer** är valt, kartan innehåller alla processer och anslutningar på varje dator i gruppen.

![Bearbetar alla datorgruppen](media/monitoring-service-map/machine-groups-all.png)

Om du ändrar vyn till att endast visa **gruppanslutna processer**, kartan ska begränsas till de processer och anslutningar som är anslutna direkt till andra datorer i gruppen, skapa en förenklad vy.

![Datorgruppen filtrerade processer](media/monitoring-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Att lägga till datorer i en grupp
Om du vill lägga till datorer i en befintlig grupp, markerar du kryssrutorna bredvid de datorer du vill ha och klicka sedan på **Lägg till i grupp**.  Välj den grupp som du vill lägga till datorer till.
 
### <a name="removing-machines-from-a-group"></a>Tar bort datorer från en grupp
Expandera namnet på listan över datorer i den datorgruppen i listan över grupper.  Klicka på menyn ellipsen bredvid den dator du vill ta bort och välj **ta bort**.

![Ta bort datorn från grupp](media/monitoring-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Ta bort eller byta namn på en grupp
Klicka på menyn ellipsen bredvid gruppnamnet på i listan.

![Datorns Gruppmeny](media/monitoring-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Rollikoner
Vissa processer fungerar specifika roller på datorer: servrar, programservrar, databas och så vidare. Service Map annoterar process- och rutor med Rollikoner för att identifiera i korthet roll en process eller server spelas upp.

| Rollikon | Beskrivning |
|:--|:--|
| ![Webbserver](media/monitoring-service-map/role-web-server.png) | Webbserver |
| ![App-servern](media/monitoring-service-map/role-application-server.png) | Programserver |
| ![Databasserver](media/monitoring-service-map/role-database.png) | Databasserver |
| ![LDAP-servern](media/monitoring-service-map/role-ldap.png) | LDAP-servern |
| ![SMB-servern](media/monitoring-service-map/role-smb.png) | SMB-servern |

![Rollikoner](media/monitoring-service-map/role-icons.png)


## <a name="failed-connections"></a>Misslyckade anslutningar
Misslyckade anslutningar visas i Service Map maps för processer och -datorer med en streckad röd linje vilket betyder att ett klientsystem kan inte nå en process eller port. Misslyckade anslutningar rapporteras från alla system med en distribuerad tjänstkarteagent om systemet är försöker den felande anslutningen. Service Map mäter den här processen genom att följa TCP uttag som det gick inte att upprätta en anslutning. Felet kan bero på en brandvägg kan en felaktig konfiguration i klienten eller servern eller en fjärrtjänst som som är otillgänglig.

![Misslyckade anslutningar](media/monitoring-service-map/failed-connections.png)

Förstå misslyckade anslutningar kan hjälpa till med felsökning, verifiera migreringen, säkerhetsanalys och övergripande arkitekturen förståelse. Misslyckade anslutningar är ibland ofarliga, men de pekar ofta direkt på ett problem, till exempel en redundans miljö plötsligt blir kan inte nås eller två nivåer av programmet som inte kommunicera med när du har en molnmigrering.

## <a name="client-groups"></a>Klientgrupper
Klientgrupper är rutorna på kartan som representerar klientdatorer som inte har Beroendeagenter. En enda klientgrupp representerar klienter för en enskild process eller dator.

![Klientgrupper](media/monitoring-service-map/client-groups.png)

Välj gruppen om du vill se IP-adresserna för servrar i en klientgrupp. Innehållet i gruppen visas i den **klienten gruppegenskaper** fönstret.

![Egenskaper för klient](media/monitoring-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Serverport grupper
Serverport grupper är rutorna som representerar server-portar på servrar som inte har Beroendeagenter. Rutan innehåller server-port och en uppräkning av antalet servrar med anslutningar till den porten. Expandera rutan om du vill visa enskilda servrar och anslutningar. Om det finns endast en server i rutan, visas namn eller IP-adress.

![Serverport grupper](media/monitoring-service-map/server-port-groups.png)

## <a name="context-menu"></a>Snabbmeny
Klicka på ellipsen (...) längst upp höger för en server visas snabbmenyn för den här servern.

![Misslyckade anslutningar](media/monitoring-service-map/context-menu.png)

### <a name="load-server-map"></a>Läs in Servermappning
Klicka på **Läs in Servermappning** tar dig till en ny karta med den valda servern som den nya datorn i fokus.

### <a name="show-self-links"></a>Visa länkar egna
Klicka på **visa Self-Links** omritningar servernoden, inklusive alla egna länkar som är TCP-anslutningar som börjar och slutar med processer i servern. Om självlänkar visas, menykommandona till **Dölj Self-Links**, så att du kan inaktivera dem.

## <a name="computer-summary"></a>Sammanfattning av dator
Den **datorn sammanfattning** innehåller en översikt över serverns operativsystem beroende antal och data från andra lösningar. Dessa data inkluderar prestandamått, supportavdelningen tjänstbiljetter, för att spåra ändringar, säkerhet och uppdateringar.

![Datorn sammanfattningsfönstret](media/monitoring-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Egenskaper för datorn och processen
När du navigerar en Tjänstkarta-karta väljer du datorer och processer för att få ytterligare kontext om deras egenskaper. Datorer tillhandahåller information om DNS-namn, IPv4-adresser, CPU och minne kapacitet, VM-typ, operativsystem och version, senaste omstart tid och ID: N för agenterna OMS och Tjänstkarta.

![Egenskapsrutan för datorn](media/monitoring-service-map/machine-properties.png)

Du kan samla in information från operativsystemet metadata om processer, inklusive processnamn, beskrivningen, användarnamn och domän (på Windows), företagets namn, produktnamn, produktversion, arbetskatalog, kommandoraden och processen som körs Starttid.

![Egenskapsrutan för process](media/monitoring-service-map/process-properties.png)

Den **Processammanfattning** tillhandahåller ytterligare information om processens anslutningar, inklusive dess bundna portar, inkommande och utgående anslutningar och misslyckade anslutningar.

![Processen sammanfattningsfönstret](media/monitoring-service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrering av aviseringar
Tjänstkarta kan integreras med Azure-aviseringar att visa aktiverade aviseringar för den valda servern i det valda tidsintervallet. Servern visas en ikon om det finns aktuella aviseringar och **datorn aviseringar** rutan visas aviseringarna.

![Aviseringspanelen för datorn](media/monitoring-service-map/machine-alerts.png)

Om du vill aktivera Tjänstkarta att visa relevanta aviseringar, skapar du en varningsregel som utlöses för en specifik dator. Skapa rätt aviseringar:
- Innehåller en instruktion i gruppen per dator (till exempel **datorn intervall 1 minut**).
- Välj varningar baserat på metriska måttenheter.

## <a name="log-events-integration"></a>Händelser-loggintegrering
Tjänstkarta är integrerat med Log Search för att visa en uppräkning av alla tillgängliga logghändelser för den valda servern under det valda tidsintervallet. Du kan klicka på alla rader i listan över händelseantal går du vidare till Loggsökning och se enskilda logghändelser.

![Datorn logghändelser fönstret](media/monitoring-service-map/log-events.png)

## <a name="service-desk-integration"></a>Service Desk-integrering
Tjänstkarta-integrering med IT Service Management Connector sker automatiskt när båda lösningarna är aktiverade och konfigurerade i Log Analytics-arbetsytan. Integrering i Tjänstkarta är märkt ”Service Desk”. Mer information finns i [centralt hantera ITSM-arbetsuppgifter med IT Service Management Connector](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

Den **datorn Service Desk** fönstret visar en lista över alla IT Service Management-händelser för den valda servern i det valda tidsintervallet. Servern visas en ikon om det finns aktuella objekt och datorn Service Desk-fönstret visas de.

![Datorn Service Desk-fönstret](media/monitoring-service-map/service-desk.png)

Klicka för att öppna objektet i dina anslutna ITSM-lösning **visa arbetsobjekt**.

Om du vill visa information om objektet i Loggsökning, klickar du på **visa i Loggsökning**.
Anslutningsmått skrivs till två nya tabeller i Log Analytics 

## <a name="change-tracking-integration"></a>Ändra – integrering
Tjänstkarta-integrering med ändringsspårning sker automatiskt när båda lösningarna är aktiverade och konfigurerade i Log Analytics-arbetsytan.

Den **datorn Change Tracking** rutan visar alla ändringar, med det senaste först, tillsammans med en länk för att öka detaljnivån till Loggsökning för ytterligare information.

![Datorn ändringsspårning fönstret](media/monitoring-service-map/change-tracking.png)

Följande bild är en detaljerad vy av en ConfigurationChange händelse som kan uppstå när du har valt **visa i Log Analytics**.

![ConfigurationChange händelse](media/monitoring-service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Prestanda-integrering
Den **prestanda datorer** fönstret visar standard prestandamått för den valda servern. Måtten inkludera CPU-användning, minnesanvändning, nätverks-byte skickas och tas emot och en lista över de främsta processerna med nätverks-byte skickas och tas emot.

![Fönstret för dator-prestanda](media/monitoring-service-map/machine-performance.png)

Om du vill visa prestandadata, du kan behöva [aktivera lämpliga Log Analytics-prestandaräknarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Räknarna som du vill aktivera:

Windows:
- Processor(*)\\% processortid
- Minne\\% allokerade byte som används
- Network Adapter(*)\\skickade byte/sek
- Network Adapter(*)\\mottagna byte/sek

Linux:
- Processor(*)\\% processortid
- Memory(*)\\använt minne i procent
- Network Adapter(*)\\skickade byte/sek
- Network Adapter(*)\\mottagna byte/sek

Om du vill hämta prestandadata för nätverk, måste du också ha aktiverat Wire Data 2.0-lösningen i din arbetsyta.
 
## <a name="security-integration"></a>Security-integrering
Tjänstkarta-integrering med säkerhet och granskning sker automatiskt när båda lösningarna är aktiverade och konfigurerade i Log Analytics-arbetsytan.

Den **säkerhet datorer** visar data från lösningen för säkerhet och granskning för den valda servern. Fönstret visar en sammanfattning av alla utestående säkerhetsproblem för servern under det valda tidsintervallet. Att klicka på någon security problem test ned till en Loggsökning för ytterligare information om dem.

![Fönstret för dator-säkerhet](media/monitoring-service-map/machine-security.png)

## <a name="updates-integration"></a>Integrering av uppdateringar
Tjänstkarta-integrering med hantering av uppdateringar sker automatiskt när båda lösningarna är aktiverat och konfigurerat i loggen Anlaytics-arbetsytan.

Den **uppdateringar för virtuella datorer** fönstret visar data från lösningen för uppdateringshantering för den valda servern. Fönstret visar en sammanfattning av uppdateringar som saknas för servern under det valda tidsintervallet.

![Datorn ändringsspårning fönstret](media/monitoring-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-poster
Tjänstkarta-datorn och processen inventeringsdata är tillgänglig för [search](../log-analytics/log-analytics-log-search.md) i Log Analytics. Du kan använda dessa data för scenarier som omfattar planering av migreringsaktiviteter, kapacitetsanalys, identifiering och prestandafelsökning för på begäran.

En post skapas per timme för varje unika datornamn och processen, förutom de poster som genereras när en process eller datorn startas inte eller är publicerat till Tjänstkarta. Dessa poster har egenskaper i följande tabeller. Fält och värden i ServiceMapComputer_CL händelserna mappar till fält i resursen för datorer i Azure Resource Manager-API för ServiceMap. Fält och värden i ServiceMapProcess_CL händelserna mappar till fälten för Process-resurs i ServiceMap Azure Resource Manager API. Fältet ResourceName_s matchar namnfältet i motsvarande Resource Manager-resurs. 

>[!NOTE]
>Eftersom Service Map funktioner växer, kan de här fälten ändras.

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använda *ResourceId* eller *ResourceName_s* att unikt identifiera en dator inom en Log Analytics-arbetsyta.
- Förlopp: Använda *ResourceId* att unikt identifiera en process i Log Analytics-arbetsytan. *ResourceName_s* är unikt inom kontexten för datorn där processen körs (MachineResourceName_s) 

Eftersom flera poster kan finnas för en angiven process och datorer i ett specifikt tidsintervall kan frågor returnera fler än en post för samma dator eller process. Om du vill inkludera endast den senaste posten, lägger du till ”| dedupliceringen ResourceId ”i frågan.

### <a name="connections"></a>Anslutningar
Anslutningsmått skrivs till en ny tabell i Log Analytics - VMConnection. Den här tabellen innehåller information om anslutningar för en virtuell dator (inkommande och utgående). Anslutningsmått blir också tillgängliga med API: er som tillhandahåller metoder för att hämta ett specifikt mått under ett tidsintervall.  TCP-anslutningar som härrör från ”*acceptera*- ing för en lyssnande socket är inkommande, samtidigt som de som används av *ansluta*- ing till en given IP och port är utgående. Riktning för en anslutning som representeras av egenskapen riktning, som kan vara inställd på antingen **inkommande** eller **utgående**. 

Poster i tabellerna genereras från data som rapporteras av beroendeagenten. Varje post representerar en områdes under ett tidsintervall för en minut. Egenskapen TimeGenerated anger början av tidsintervallet. Varje post innehåller information för att identifiera entiteten respektive, det vill säga, anslutning eller port samt mått som är associerade med denna entitet. För närvarande rapporteras endast nätverksaktivitet som sker med TCP över IPv4.

För att hantera kostnaden och komplexiteten, utgör anslutningen poster inte enskilda fysiska nätverksanslutningar. Flera fysiska nätverksanslutningar är grupperade i en logisk anslutning, som sedan visas i respektive tabell.  Betydelse, registrerar i *VMConnection* tabell representerar en logisk gruppering och inte de enskilda fysiska anslutningar som är som observeras. Fysiska nätverksanslutningen som delar samma värde för följande attribut under ett visst minuts intervall, slås ihop till en enskild logisk post i *VMConnection*. 

| Egenskap  | Beskrivning |
|:--|:--|
|Riktning |Riktning för anslutningen värdet är *inkommande* eller *utgående* |
|Dator |Datorn FQDN |
|Process |Identiteten för processen eller grupper av processer, initierar/godkänna anslutningen |
|SourceIp |IP-adressen för källan |
|DestinationIp |IP-adressen för målet |
|DestinationPort |Portnumret för mål |
|Protokoll |Protokoll som används för anslutningen.  Värden är *tcp*. |

Information om antalet grupperade fysiska anslutningar finns i följande egenskaper för posten för att redovisa effekten av gruppering:

| Egenskap  | Beskrivning |
|:--|:--|
|LinksEstablished |Antal fysiska nätverksanslutningar som har upprättats under tidsperioden för rapportering |
|LinksTerminated |Antal fysiska nätverksanslutningar som har avslutats under tidsperioden för rapportering |
|LinksFailed |Antal fysiska nätverksanslutningar som har misslyckats under tidsperioden för rapportering. Den här informationen är endast tillgänglig för utgående anslutningar. |
|LinksLive |Antal fysiska nätverksanslutningar som var öppna längst ned i tidsfönstret|

#### <a name="metrics"></a>Mått

Förutom antalet anslutningsmått, information om mängden data som skickas och tas emot på en viss logisk anslutning eller nätverksport ingår även i följande egenskaper för posten:

| Egenskap  | Beskrivning |
|:--|:--|
|BytesSent |Sammanlagt antal byte som har skickats under tidsperioden för rapportering |
|BytesReceived |Sammanlagt antal byte som tagits emot under tidsperioden för rapportering |
|Svar |Antal svar som observerats under tidsperioden för rapportering. 
|ResponseTimeMax |Den största svarstid (millisekunder) observerats under tidsperioden för rapportering.  Egenskapen är tomt om inget värde.|
|ResponseTimeMin |Den minsta svarstid (millisekunder) observerats under tidsperioden för rapportering.  Egenskapen är tomt om inget värde.|
|ResponseTimeSum |Summan av alla svarstider (millisekunder) som observerats under tidsperioden för rapportering.  Om inget värde är egenskapen tomt|

Den tredje typ av data som rapporteras svarstid – hur länge en anropare ägna åt att vänta på en begäran som skickas via en anslutning som ska bearbetas och besvarats av fjärrslutpunkten. Svarstiden som rapporteras är en uppskattning av SANT svarstiden för det underliggande protokollet. Det beräknas med hjälp av heuristik baserat på observationer av flödet av data mellan käll- och slutet av en fysisk anslutning. Den övergripande är skillnaden mellan den tid som den sista byten av en begäran lämnar avsändaren och tid när den sista byten av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att ge en bild av händelser som begäranden och svar på en viss fysisk anslutning. Skillnaden mellan dem representerar svarstiden för en enskild begäran. 

I den första versionen av den här funktionen är vår algoritmen ett approximativt värde som kan fungera med varierande framgång beroende på typ av App-protokollet som används för en viss nätverksanslutning. Exempelvis kan den aktuella metoden fungerar bra för begäranden och svar-baserade protokoll som HTTP (S), men inte fungerar med enkelriktad och message queue-baserade protokoll.

Här följer några viktiga saker att tänka på:

1. Om en process som tar emot anslutningar på samma IP-adress men över flera nätverksgränssnitt, rapporteras en separat post för varje gränssnitt. 
2. Poster med jokertecken IP innehåller ingen aktivitet. De ingår som representerar det faktum att en port på datorn är öppna för inkommande trafik.
3. För att minska detaljnivå och datavolym, utelämnas poster med jokertecken IP när det finns en matchande post (för samma process, port och protokoll) med en specifik IP-adress. När ett jokertecken IP-adressposten utelämnas, ställs egenskapen IsWildcardBind poster med specifika IP-adress ”true” som anger att porten som exponeras över varje gränssnitt för den reporting datorn.
4. Portar som är bundna endast på ett visst gränssnitt har IsWildcardBind inställd på ”False”.

#### <a name="naming-and-classification"></a>Namngivning och klassificering
För att underlätta för som IP-adressen för den fjärranslutna datorn för en anslutning ingår i egenskapen RemoteIp. För inkommande anslutningar RemoteIp är samma som SourceIp, och för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar de kanoniska DNS-namn som rapporterats av den för RemoteIp. Egenskaperna RemoteDnsQuestions och RemoteClassification är reserverade för framtida användning. 

#### <a name="geolocation"></a>Geoplats
*VMConnection* innehåller även geoplats information för den fjärranslutna datorn för varje post för anslutning av följande egenskaper för posten: 

| Egenskap  | Beskrivning |
|:--|:--|
|RemoteCountry |Namnet på det land som är värd för RemoteIp.  Till exempel *USA* |
|RemoteLatitude |Geoplats latitud.  Till exempel *47.68* |
|RemoteLongitude |Geoplats longitud.  Till exempel *-122.12* |

#### <a name="malicious-ip"></a>Skadlig IP
Varje RemoteIp-egenskapen i *VMConnection* tabell kontrolleras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadlig följande egenskaper är ifyllda (de är tom, när den IP-Adressen inte anses vara skadlig) i följande egenskaper för posten:

| Egenskap  | Beskrivning |
|:--|:--|
|MaliciousIp |RemoteIp-adress |
|IndicatorThreadType |Threat indikatorn har identifierats är något av följande värden *Botnät*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *skadlig kod*, *nätfiske*, *Proxy*, *oönskade program*, *Visningslista*.   |
|Beskrivning |Beskrivning av observerade hotet. |
|TLPLevel |Trafikljus Protocol (TLP) är en av de definierade värdena *White*, *grönt*, *gul*, *Red*. |
|Konfidensbedömning |Värden är *0 – 100*. |
|Severity |Värden är *0 – 5*, där *5* är den mest allvarliga och *0* inte är allvarligt alls. Standardvärdet är *3*.  |
|FirstReportedDateTime |Första gången providern rapporterade indikatorn. |
|LastReportedDateTime |Senast indikatorn har setts av Interflow. |
|IsActive |Anger indikatorer inaktiveras med *SANT* eller *FALSKT* värde. |
|ReportReferenceLink |Länkar till rapporter som rör en viss övervakas. |
|AdditionalInformation |Tillhandahåller ytterligare information om det är tillämpligt, om observerade hotet. |

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL poster
Poster med en typ av *ServiceMapComputer_CL* har inventeringsdata för servrar med Service Map agenter. Dessa poster har egenskaper i följande tabell:

| Egenskap  | Beskrivning |
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
| Bitness_s | Bitar som inte för datorn (32-bitars eller 64-bitars)  |
| PhysicalMemory_d | Fysiskt minne i MB |
| Cpus_d | Antal processorer |
| CpuSpeed_d | CPU-hastighet i MHz|
| VirtualizationState_s | *Okänd*, *fysiska*, *virtuella*, *hypervisor-programmet* |
| VirtualMachineType_s | *Hyper-v*, *vmware*och så vidare |
| VirtualMachineNativeMachineId_g | VM-ID som tilldelats av dess hypervisor-programmet |
| VirtualMachineName_s | Namnet på den virtuella datorn |
| BootTime_t | Starttiden |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL poster
Poster med en typ av *ServiceMapProcess_CL* ha inventeringsdata för TCP-anslutna processer på servrar med Service Map agenter. Dessa poster har egenskaper i följande tabell:

| Egenskap  | Beskrivning |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| Resurs-ID | Den unika identifieraren för en process i arbetsytan |
| ResourceName_s | Den unika identifieraren för en process på datorn där den körs|
| MachineResourceName_s | Resursnamnet för datorn |
| ExecutableName_s | Namnet på processprogramfil |
| StartTime_t | Starttid för process-pool |
| FirstPid_d | Första PID i programprocess-poolen |
| Description_s | Beskrivning av process |
| CompanyName_s | Namnet på företaget |
| InternalName_s | Det interna namnet |
| ProductName_s | Namnet på produkten |
| ProductVersion_s | Produktversion |
| FileVersion_s | Filversionen |
| CommandLine_s | Från kommandoraden |
| ExecutablePath _Vä | Sökvägen till den körbara filen |
| WorkingDirectory_s | Arbetskatalogen |
| Användarnamn | Det konto som processen körs |
| UserDomain | Den domän där processen körs |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

### <a name="list-all-known-machines"></a>Lista över alla kända datorer
ServiceMapComputer_CL | Sammanfatta arg_max(TimeGenerated, *) av resurs-ID

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista över kapacitet för fysiskt minne för alla hanterade datorer.
ServiceMapComputer_CL | Sammanfatta arg_max(TimeGenerated, *) av ResourceId | projektet PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Lista datornamn, DNS, IP- och OS.
ServiceMapComputer_CL | Sammanfatta arg_max(TimeGenerated, *) av ResourceId | projektet ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med ”sql” på kommandoraden
ServiceMapProcess_CL | där CommandLine_s contains_cs ”sql” | Sammanfatta arg_max(TimeGenerated, *) av resurs-ID

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste post) efter resursnamn
Sök i (ServiceMapComputer_CL) ”m-4b9c93f9-bc37-46df-b43c-899ba829e07b” | Sammanfatta arg_max(TimeGenerated, *) av resurs-ID

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste post) genom att IP-adress
Sök i (ServiceMapComputer_CL) ”10.229.243.232” | Sammanfatta arg_max(TimeGenerated, *) av resurs-ID

### <a name="list-all-known-processes-on-a-specified-machine"></a>Lista över alla kända processer på en angiven dator
ServiceMapProcess_CL | där MachineResourceName_s == ”m-559dbcd8-3130-454d-8d1d-f624e57961bc” | Sammanfatta arg_max(TimeGenerated, *) av resurs-ID

### <a name="list-all-computers-running-sql"></a>Lista över alla datorer som kör SQL
ServiceMapComputer_CL | där ResourceName_s i ((Sök i (ServiceMapProcess_CL) ”\*sql\*” | distinkta MachineResourceName_s)) | distinkta ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista över alla unika produktversioner av curl i mitt datacenter
ServiceMapProcess_CL | där ExecutableName_s == ”curl” | distinkta ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en datorgrupp för alla datorer som kör CentOS
ServiceMapComputer_CL | där OperatingSystemFullName_s contains_cs ”CentOS” | distinkta ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Sammanfatta utgående anslutningar från en grupp datorer
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST-API
Alla data för server, process och beroenden i Tjänstkarta är tillgängligt via den [REST-API för Service Map](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata
Microsoft samlar automatiskt in användnings- och data till din användning av tjänsten Tjänstkarta. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten för tjänsten Tjänstkarta. Data innehåller information om konfigurationen av din programvara, till exempel operativsystem och version, IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Nästa steg
Läs mer om [loggsökningar](../log-analytics/log-analytics-log-search.md) i Log Analytics för att hämta data som samlas in av Tjänstkarta.


## <a name="troubleshooting"></a>Felsökning
Se den [felsökning i Konfigurera Tjänstkarta-dokumentet]( monitoring-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Feedback
Har du feedback för oss om Tjänstkarta eller den här dokumentationen?  Besök vår [User Voice-sidan](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), där du kan föreslå funktioner eller rösta in befintliga förslag.
