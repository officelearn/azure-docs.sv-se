---
title: Använda Service Map-lösning i Azure | Microsoft-dokument
description: Tjänstkarta är en lösning i Azure som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln innehåller information om hur du distribuerar Service Map i din miljö och använder den i en mängd olika scenarier.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: f2f3e84462307f43ffe432fe878476d979f489f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480920"
---
# <a name="using-service-map-solution-in-azure"></a>Använda lösningen Tjänstkarta i Azure

Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Med Tjänstkarta kan du se dina servrar på samma sätt som du tänker på dem, dvs. som sammankopplade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer, inkommande och utgående anslutningssvarstid, samt portar i valfri TCP-ansluten arkitektur, utan att det krävs någon konfiguration förutom installationen av en agent.

I den här artikeln beskrivs information om introduktion och användning av Service Map. Information om hur du konfigurerar förutsättningarna för den här lösningen finns i [Aktivera översikten över Azure Monitor för virtuella datorer](vminsights-enable-overview.md#prerequisites). Sammanfattningsvis behöver du följande:

* En Log Analytics-arbetsyta för att aktivera den här lösningen.

* Log Analytics-agenten som är installerad på Windows-datorn eller Linux-servern som konfigurerats för att rapportera samma arbetsyta som du aktiverade lösningen med.

* Beroendeagenten som är installerad på Windows-datorn eller Linux-servern.

>[!NOTE]
>Om du redan har distribuerat Service Map kan du nu även visa dina kartor i Azure Monitor för virtuella datorer, som innehåller ytterligare funktioner för att övervaka vm-hälsa och prestanda. Mer information finns i [översikt över Azure Monitor för virtuella datorer](../../azure-monitor/insights/vminsights-overview.md). Mer information om skillnaderna mellan servicemappningslösningen och Azure Monitor för virtuella datorer kartfunktionen finns i följande [vanliga frågor](../faq.md#azure-monitor-for-vms)och svar .

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Aktivera tjänstöversikt

1. Aktivera servicemappningslösningen från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) eller genom att använda den process som beskrivs i [Lägg till övervakningslösningar från lösningsgalleriet](solutions.md).
1. [Installera beroendeagenten i Windows](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) eller [Installera beroendeagenten på Linux](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) på varje dator där du vill hämta data. Beroendeagenten kan övervaka anslutningar till omedelbara grannar, så du behöver kanske inte ha en agent på varje dator.

Du öppnar Service Map i Azure-portalen från din Log Analytics-arbetsyta och väljer alternativet **Lösningar** i den vänstra rutan.<br><br> ![Välj lösningsalternativ på](./media/service-map/select-solution-from-workspace.png)arbetsytan .<br> Välj **ServiceMap(workspaceName)** i listan över lösningar och klicka på panelen Sammanfattning av Service Map på panelen Sammanfattning av Service Map.<br><br> ![Sammanfattningspanel](./media/service-map/service-map-summary-tile.png)för Service Map .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Användningsfall: Gör dina IT-processer beroende

### <a name="discovery"></a>Identifiering

Service Map skapar automatiskt en gemensam referenskarta över beroenden över dina servrar, processer och tjänster från tredje part. Den upptäcker och mappar alla TCP-beroenden, identifierar överraskningsanslutningar, fjärrsystem från tredje part som du är beroende av och beroenden till traditionella mörka områden i nätverket, till exempel Active Directory. Service Map identifierar misslyckade nätverksanslutningar som dina hanterade system försöker skapa, vilket hjälper dig att identifiera potentiella felkonfigurationer av servrar, avbrott i tjänsten och nätverksproblem.

### <a name="incident-management"></a>Incidenthantering

Service Map hjälper till att eliminera gissningar av problemisolering genom att visa hur system är anslutna och påverkar varandra. Förutom att identifiera misslyckade anslutningar hjälper det till att identifiera felkonfigurerade belastningsutjämnare, överraskande eller överdriven belastning på kritiska tjänster och oseriösa klienter, till exempel utvecklardatorer som pratar med produktionssystem. Genom att använda integrerade arbetsflöden med ändringsspårning kan du också se om en ändringshändelse på en backend-dator eller tjänst förklarar orsaken till en incident.

### <a name="migration-assurance"></a>Migrationsgaranti

Genom att använda Service Map kan du effektivt planera, påskynda och validera Azure-migreringar, vilket hjälper till att säkerställa att ingenting lämnas kvar och att överrasknings avbrott inte inträffar. Du kan identifiera alla system som är beroende av varandra och som behöver migrera tillsammans, bedöma systemkonfiguration och kapacitet och identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för att inaktivera i stället för migrering. När flytten är klar kan du kontrollera klientbelastning och identitet för att kontrollera att testsystem och kunder ansluter. Om planeringen och brandväggsdefinitionerna för undernätet har problem pekar misslyckade anslutningar i Service Map-kartor dig till de system som behöver anslutas.

### <a name="business-continuity"></a>Verksamhetskontinuitet

Om du använder Azure Site Recovery och behöver hjälp med att definiera återställningssekvensen för din programmiljö kan Service Map automatiskt visa hur system förlitar sig på varandra för att säkerställa att återställningsplanen är tillförlitlig. Genom att välja en kritisk server eller grupp och visa dess klienter kan du identifiera vilka frontend-system som ska återställas när servern har återställts och är tillgänglig. Genom att titta på kritiska servrars backend-beroenden kan du däremot identifiera vilka system som ska återställas innan fokussystemen återställs.

### <a name="patch-management"></a>Uppdateringshantering

Service Map förbättrar din användning av systemuppdateringsutvärderingen genom att visa vilka andra team och servrar som är beroende av din tjänst, så att du kan meddela dem i förväg innan du tar ner dina system för korrigering. Service Map förbättrar också korrigeringshanteringen genom att visa om dina tjänster är tillgängliga och korrekt anslutna efter att de har korrigerats och startats om.

## <a name="mapping-overview"></a>Översikt över mappning

Service Map-agenter samlar in information om alla TCP-anslutna processer på servern där de är installerade och information om inkommande och utgående anslutningar för varje process.

I listan i den vänstra rutan kan du välja datorer eller grupper som har Service Map-agenter för att visualisera sina beroenden över ett angivet tidsintervall. Maskinberoendekartor fokuserar på en viss dator och de visar alla datorer som är direkta TCP-klienter eller servrar på den datorn.  Machine Group-kartor visar uppsättningar av servrar och deras beroenden.

![Översikt över servicekarta](media/service-map/service-map-overview.png)

Maskiner kan expanderas på kartan för att visa de processgrupper och processer som körs med aktiva nätverksanslutningar under det valda tidsintervallet. När en fjärrdator med en Service Map-agent utökas för att visa processinformation visas endast de processer som kommunicerar med fokusdatorn. Antalet agentlösa frontend-maskiner som ansluter till fokusmaskinen anges till vänster om de processer de ansluter till. Om fokusdatorn gör en anslutning till en backend-dator som inte har någon agent, ingår backend-servern i en serverportgrupp, tillsammans med andra anslutningar till samma portnummer.

Som standard visar Service Map-kartor de senaste 30 minuternas beroendeinformation. Genom att använda tidskontrollerna längst upp till vänster kan du fråga kartor efter historiska tidsintervall på upp till en timme för att visa hur beroenden såg ut tidigare (till exempel under en incident eller innan en ändring inträffade). Service Map-data lagras i 30 dagar i betalda arbetsytor och i 7 dagar på lediga arbetsytor.

## <a name="status-badges-and-border-coloring"></a>Statusmärken och kantfärg

Längst ned på varje server på kartan kan vara en lista över statusmärken som förmedlar statusinformation om servern. Märkena visar att det finns viss relevant information för servern från en av lösningsintegrationerna. Om du klickar på ett märke går du direkt till informationen om statusen i den högra rutan. De statusmärken som för närvarande är tillgängliga är aviseringar, servicedesk, ändringar, säkerhet och uppdateringar.

Beroende på statusmärkenas allvarlighetsgrad kan maskinnodens kantlinjer färgas röda (kritiska), gula (varning) eller blå (informativa). Färgen representerar den allvarligaste statusen för någon av statusmärkena. En grå kantlinje anger en nod som inte har några statusindikatorer.

![Statusmärken](media/service-map/status-badges.png)

## <a name="process-groups"></a>Processgrupper

Processgrupper kombinerar processer som är associerade med en gemensam produkt eller tjänst till en processgrupp.  När en maskinnod expanderas visas fristående processer tillsammans med processgrupper.  Om några inkommande och utgående anslutningar till en process inom en processgrupp har misslyckats visas anslutningen som misslyckad för hela processgruppen.

## <a name="machine-groups"></a>Datorgrupper

Med maskingrupper kan du se kartor centrerade kring en uppsättning servrar, inte bara en så att du kan se alla medlemmar i ett program- eller serverkluster på flera nivåer på en karta.

Användarna väljer vilka servrar som hör hemma i en grupp tillsammans och väljer ett namn för gruppen.  Du kan sedan välja att visa gruppen med alla dess processer och anslutningar, eller visa den med endast de processer och anslutningar som direkt relaterar till de andra medlemmarna i gruppen.

![Maskingrupp](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Skapa en maskingrupp

Om du vill skapa en grupp markerar du den eller de datorer du vill använda i listan Datorer och klickar på **Lägg till i gruppen**.

![Skapa grupp](media/service-map/machine-groups-create.png)

Där kan du välja **Skapa nytt** och ge gruppen ett namn.

![Namngrupp](media/service-map/machine-groups-name.png)

>[!NOTE]
>Maskingrupper är begränsade till 10 servrar.

### <a name="viewing-a-group"></a>Visa en grupp

När du har skapat vissa grupper kan du visa dem genom att välja fliken Grupper.

![Fliken Grupper](media/service-map/machine-groups-tab.png)

Välj sedan gruppnamnet om du vill visa kartan för den maskingruppen.
![Maskingrupp](media/service-map/machine-group.png) De maskiner som tillhör gruppen beskrivs i vitt på kartan.

Om du expanderar koncernen listas de maskiner som utgör maskingruppen.

![Maskiner i maskingrupp](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrera efter processer

Du kan växla kartvyn mellan att visa alla processer och anslutningar i gruppen och bara de som direkt relaterar till maskingruppen.  Standardvyn är att visa alla processer.  Du kan ändra vyn genom att klicka på filterikonen ovanför kartan.

![Filtergrupp](media/service-map/machine-groups-filter.png)

När **alla processer** är valda innehåller kartan alla processer och anslutningar på var och en av datorerna i gruppen.

![Maskingrupp alla processer](media/service-map/machine-groups-all.png)

Om du ändrar vyn för att bara visa **gruppanslutna processer**begränsas kartan till endast de processer och anslutningar som är direkt kopplade till andra datorer i gruppen, vilket skapar en förenklad vy.

![Maskingrupp filtrerade processer](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Lägga till maskiner i en grupp

Om du vill lägga till datorer i en befintlig grupp markerar du rutorna bredvid de datorer du vill använda och klickar sedan på **Lägg till i grupp**.  Välj sedan den grupp som du vill lägga till datorerna i.
 
### <a name="removing-machines-from-a-group"></a>Ta bort datorer från en grupp

Expandera gruppnamnet i grupplistan för att lista datorerna i maskingruppen i grupplistan.  Klicka sedan på ellipsmenyn bredvid den maskin du vill ta bort och välj **Ta bort**.

![Ta bort maskin från grupp](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Ta bort eller byta namn på en grupp

Klicka på ellipsmenyn bredvid gruppnamnet i grupplistan.

![Menyn Maskingrupp](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Rollikoner

Vissa processer har särskilda roller på datorer: webbservrar, programservrar, databas och så vidare. Service Map kommenterar process- och maskinrutor med rollikoner för att snabbt identifiera vilken roll en process eller server spelar.

| Ikon för roll | Beskrivning |
|:--|:--|
| ![Webbserver](media/service-map/role-web-server.png) | Webbserver |
| ![Appserver](media/service-map/role-application-server.png) | Programserver |
| ![Databasserver](media/service-map/role-database.png) | Databasserver |
| ![LDAP-server](media/service-map/role-ldap.png) | LDAP-server |
| ![SMB-server](media/service-map/role-smb.png) | SMB-server |

![Rollikoner](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Misslyckade anslutningar

Misslyckade anslutningar visas i Service Map-kartor för processer och datorer, med en streckad röd linje som anger att ett klientsystem inte når en process eller port. Misslyckade anslutningar rapporteras från alla system med en distribuerad Service Map-agent om det systemet är det som försöker ansluta den misslyckade anslutningen. Service Map mäter den här processen genom att observera TCP-socketar som inte kan upprätta en anslutning. Det här felet kan bero på att en brandvägg, en felkonfiguration i klienten eller servern eller en fjärrtjänst inte är tillgänglig.

![Misslyckade anslutningar](media/service-map/failed-connections.png)

Att förstå misslyckade anslutningar kan hjälpa till med felsökning, migreringsvalidering, säkerhetsanalys och övergripande arkitektonisk förståelse. Misslyckade anslutningar är ibland ofarliga, men de pekar ofta direkt på ett problem, till exempel att en redundansmiljö plötsligt blir onåbar eller två programnivåer som inte kan prata efter en molnmigrering.

## <a name="client-groups"></a>Klientgrupper

Klientgrupper är rutor på kartan som representerar klientdatorer som inte har beroendeagenter. En enskild kundgrupp representerar klienterna för en enskild process eller dator.

![Klientgrupper](media/service-map/client-groups.png)

Om du vill visa IP-adresserna för servrarna i en klientgrupp väljer du gruppen. Innehållet i gruppen visas i fönstret **Egenskaper för klientgrupp.**

![Egenskaper för klientgrupp](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Serverportgrupper

Serverportgrupper är rutor som representerar serverportar på servrar som inte har beroendeagenter. Rutan innehåller serverporten och antalet servrar med anslutningar till den porten. Expandera rutan för att se de enskilda servrarna och anslutningarna. Om det bara finns en server i rutan visas namnet eller IP-adressen.

![Serverportgrupper](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Snabbmeny

Om du klickar på ellipsen (...) längst upp till höger på en server visas snabbmenyn för den servern.

![Misslyckade anslutningar](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Ladda serverkarta

Om du klickar **på Läs in serverkartan** kommer du till en ny karta med den valda servern som ny fokusdator.

### <a name="show-self-links"></a>Visa självlänkar

Om du klickar på **Visa självlänkar** du servernoden, inklusive eventuella självlänkar, som är TCP-anslutningar som startar och slutar på processer på servern. Om självlänkar visas ändras menykommandot till **Dölj självlänkar**så att du kan stänga av dem.

## <a name="computer-summary"></a>Sammanfattning av datorn

Fönstret **Sammanfattning av datorer** innehåller en översikt över en servers operativsystem, beroendeantal och data från andra lösningar. Sådana data omfattar prestandamått, servicedesk-biljetter, ändringsspårning, säkerhet och uppdateringar.

![Fönstret Sammanfattning av datorer](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Dator- och processegenskaper

När du navigerar på en tjänstkarta kan du välja datorer och processer för att få ytterligare kontext om deras egenskaper. Datorer ger information om DNS-namn, IPv4-adresser, CPU och minneskapacitet, VM-typ, operativsystem och version, senaste omstartstid och ID:n för deras OMS- och Service Map-agenter.

![Fönstret Datoregenskaper](media/service-map/machine-properties.png)

Du kan samla in processinformation från operativsystemmetadata om processer som körs, inklusive processnamn, processbeskrivning, användarnamn och domän (i Windows), företagsnamn, produktnamn, produktversion, arbetskatalog, kommandorad och process starttid.

![Fönstret Bearbeta egenskaper](media/service-map/process-properties.png)

Fönstret **Processsammanfattning** innehåller ytterligare information om processens anslutning, inklusive dess bundna portar, inkommande och utgående anslutningar och misslyckade anslutningar.

![Fönstret Sammanfattning av processen](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integrering av aviseringar

Service Map integreras med Azure-aviseringar för att visa avfyrade aviseringar för den valda servern i det valda tidsintervallet. Servern visar en ikon om det finns aktuella aviseringar och fönstret **Maskinvarningar** visar aviseringarna.

![Fönstret Maskinvarningar](media/service-map/machine-alerts.png)

Om du vill att Service Map ska kunna visa relevanta aviseringar skapar du en varningsregel som utlöses för en viss dator. Så här skapar du lämpliga aviseringar:
- Inkludera en sats som ska grupperas efter dator (t.ex. **efter datorintervall 1 minut**).
- Välj att avisera baserat på måttmätning.

## <a name="log-events-integration"></a>Integrering av logghändelser

Service Map integreras med Loggsökning för att visa antalet tillgängliga logghändelser för den valda servern under det valda tidsintervallet. Du kan klicka på valfri rad i listan över händelseantal för att hoppa till Loggsökning och se de enskilda logghändelserna.

![Fönstret Händelser för maskinlogg](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integrering av servicedesk

Service Map-integrering med IT Service Management Connector sker automatiskt när båda lösningarna är aktiverade och konfigurerade på logganalysarbetsytan. Integreringen i Service Map är märkt "Service Desk". Mer information finns i [Centralt hantera ITSM-arbetsobjekt med HJÄLP av IT Service Management Connector](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

I fönstret **Maskinservicedesk** visas alla IT Service Management-händelser för den valda servern inom det valda tidsintervallet. Servern visar en ikon om det finns aktuella objekt och i fönstret Maskinservicedisk visas dem.

![Fönstret Skrivbord för maskinservice](media/service-map/service-desk.png)

Om du vill öppna objektet i den anslutna ITSM-lösningen klickar du på **Visa arbetsobjekt**.

Om du vill visa information om objektet i Loggsökning klickar du på **Visa i Loggsökning**.
Anslutningsmått skrivs till två nya tabeller i Log Analytics 

## <a name="change-tracking-integration"></a>Integrering av ändringsspårning

Service Map-integrering med ändringsspårning sker automatiskt när båda lösningarna är aktiverade och konfigurerade på logganalysarbetsytan.

I fönstret **Spårning av maskinändringar** visas alla ändringar, med den senaste först, tillsammans med en länk för att öka detaljnivån till Loggsökning för ytterligare information.

![Fönstret Spårning av datorändringar](media/service-map/change-tracking.png)

Följande bild är en detaljerad vy av en ConfigurationChange-händelse som du kan se när du har valt **Visa i Logganalys**.

![ConfigurationChange-händelse](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integrering av prestanda

I fönstret **Maskinprestanda** visas standardprestandamått för den valda servern. Måtten omfattar CPU-användning, minnesanvändning, nätverksbyte som skickas och tas emot och en lista över de bästa processerna efter nätverksbyte som skickas och tas emot.

![Fönstret Maskinprestanda](media/service-map/machine-performance.png)

Om du vill visa prestandadata kan du behöva [aktivera lämpliga prestandaräknare för Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  De räknare som du vill aktivera:

Windows:
- Processor(*)\\% processortid
- Minne\\% bekräftade byte som används
- Nätverkskort(*)\\Skickade byte/sek
- Nätverkskort(*)\\mottagna byte/sek

Linux:
- Processor(*)\\% processortid
- Minne(*)\\% använt minne
- Nätverkskort(*)\\Skickade byte/sek
- Nätverkskort(*)\\mottagna byte/sek

För att få nätverksprestandadata måste du också ha aktiverat Wire Data 2.0-lösningen på arbetsytan.
 
## <a name="security-integration"></a>Integrering av säkerhet

Service Map-integrering med säkerhet och granskning sker automatiskt när båda lösningarna är aktiverade och konfigurerade på logganalysarbetsytan.

Fönstret **Datorsäkerhet** visar data från säkerhets- och granskningslösningen för den valda servern. I fönstret visas en sammanfattning av eventuella kvarstående säkerhetsproblem för servern under det valda tidsintervallet. Om du klickar på något av säkerhetsproblemen går du närmare in på en loggsökning efter information om dem.

![Fönstret Maskinsäkerhet](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Uppdaterar integrering

Service Map-integrering med Uppdateringshantering sker automatiskt när båda lösningarna är aktiverade och konfigurerade på logganalysarbetsytan.

I fönstret **Datoruppdateringar** visas data från lösningen för uppdateringshantering för den valda servern. I fönstret visas en sammanfattning av eventuella uppdateringar som saknas för servern under det valda tidsintervallet.

![Fönstret Spårning av datorändringar](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-poster

Service Map-dator- och processlagerdata är tillgängliga för [sökning](../../azure-monitor/log-query/log-query-overview.md) i Logganalys. Du kan använda dessa data på scenarier som omfattar migreringsplanering, kapacitetsanalys, identifiering och felsökning av prestanda på begäran.

En post genereras per timme för varje unik dator och process, utöver de poster som genereras när en process eller dator startar eller är inbyggd i Service Map. Dessa poster har egenskaperna i följande tabeller. Fälten och värdena i ServiceMapComputer_CL händelser mappas till fält för datorresursen i Api:et för ServiceMap Azure Resource Manager. Fälten och värdena i ServiceMapProcess_CL händelser mappas till fälten för processresursen i Api:et för ServiceMap Azure Resource Manager. Fältet ResourceName_s matchar namnfältet i motsvarande Resurshanteraren-resurs. 

>[!NOTE]
>I takt med att servicemappningsfunktionerna växer kan dessa fält ändras.

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använd *ResourceId* eller *ResourceName_s* för att unikt identifiera en dator i en Log Analytics-arbetsyta.
- Process: Använd *ResourceId* för att unikt identifiera en process inom en Log Analytics-arbetsyta. *ResourceName_s* är unik inom ramen för den maskin som processen körs på (MachineResourceName_s) 

Eftersom det kan finnas flera poster för en angiven process och dator i ett angivet tidsintervall, kan frågor returnera mer än en post för samma dator eller process. Om du bara vill ta med den senaste posten lägger du till "| dedup ResourceId" till frågan.

### <a name="connections"></a>Anslutningar

Anslutningsmått skrivs till en ny tabell i Log Analytics - VMConnection. Den här tabellen innehåller information om anslutningarna för en dator (inkommande och utgående). Anslutningsmått visas också med API:er som ger möjlighet att hämta ett visst mått under ett tidsfönster.  TCP-anslutningar som härrör från att acceptera på en lyssningsuttag är inkommande, medan de som skapas genom att ansluta till en viss IP och port är utgående. Riktningen för en anslutning representeras av egenskapen Riktning, som kan ställas in på antingen **inkommande** eller **utgående**. 

Poster i dessa tabeller genereras från data som rapporteras av beroendeagenten. Varje post representerar en observation över en minuts tidsintervall. Egenskapen TimeGenerated anger början av tidsintervallet. Varje post innehåller information för att identifiera respektive entitet, det vill ha, anslutning eller port, samt mått som är associerade med den entiteten. För närvarande rapporteras endast nätverksaktivitet som inträffar med TCP över IPv4.

För att hantera kostnader och komplexitet representerar anslutningsposter inte enskilda fysiska nätverksanslutningar. Flera fysiska nätverksanslutningar grupperas i en logisk anslutning, vilket sedan återspeglas i respektive tabell.  Det betyder att poster i *tabellen VMConnection* representerar en logisk gruppering och inte de enskilda fysiska anslutningar som observeras. Fysisk nätverksanslutning som delar samma värde för följande attribut under ett givet enminutsintervall aggregeras till en enda logisk post i *VMConnection*. 

| Egenskap | Beskrivning |
|:--|:--|
| `Direction` |Anslutningens riktning, värdet är *inkommande* eller *utgående* |
| `Machine` |Datorn FQDN |
| `Process` |Identitet för process eller grupper av processer, initiera/acceptera anslutningen |
| `SourceIp` |Källans IP-adress |
| `DestinationIp` |Målets IP-adress |
| `DestinationPort` |Destinationens portnummer |
| `Protocol` |Protokoll som används för anslutningen.  Värden är *tcp*. |

För att ta hänsyn till effekten av gruppering finns information om antalet grupperade fysiska anslutningar i postens följande egenskaper:

| Egenskap | Beskrivning |
|:--|:--|
| `LinksEstablished` |Antalet fysiska nätverksanslutningar som har upprättats under rapporteringstidsfönstret |
| `LinksTerminated` |Antalet fysiska nätverksanslutningar som har avslutats under rapporteringstidsfönstret |
| `LinksFailed` |Antalet fysiska nätverksanslutningar som har misslyckats under rapporteringstidsfönstret. Den här informationen är för närvarande endast tillgänglig för utgående anslutningar. |
| `LinksLive` |Antalet fysiska nätverksanslutningar som var öppna i slutet av rapporteringstidsfönstret|

#### <a name="metrics"></a>Mått

Förutom mått för antalet anslutningar inkluderas även information om mängden data som skickas och tas emot på en viss logisk anslutnings- eller nätverksport i postens följande egenskaper:

| Egenskap | Beskrivning |
|:--|:--|
| `BytesSent` |Totalt antal byte som har skickats under rapporteringstidsfönstret |
| `BytesReceived` |Totalt antal byte som har tagits emot under rapporteringstidsfönstret |
| `Responses` |Antalet svar som observerats under rapporteringstidsfönstret. 
| `ResponseTimeMax` |Den största svarstiden (millisekunder) som observerats under rapporteringstidsfönstret.  Om inget värde är egenskapen tom.|
| `ResponseTimeMin` |Den minsta svarstiden (millisekunder) som observerats under rapporteringstidsfönstret.  Om inget värde är egenskapen tom.|
| `ResponseTimeSum` |Summan av alla svarstider (millisekunder) som observerats under rapporteringstidsfönstret.  Om inget värde är egenskapen tom|

Den tredje typen av data som rapporteras är svarstid - hur länge spenderar en uppringare på att vänta på att en begäran som skickas över en anslutning ska bearbetas och besvaras av fjärrslutpunkten. Den rapporterade svarstiden är en uppskattning av den verkliga svarstiden för det underliggande programprotokollet. Det beräknas med heuristik baserat på observation av flödet av data mellan källan och målslutet för en fysisk nätverksanslutning. Begreppsmässigt är det skillnaden mellan den tidpunkt då den sista bytet av en begäran lämnar avsändaren och den tidpunkt då den sista bytet av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att avgränsa begärande- och svarshändelser på en viss fysisk anslutning. Skillnaden mellan dem representerar svarstiden för en enskild begäran. 

I den här första versionen av den här funktionen är vår algoritm en approximation som kan fungera med varierande grad av framgång beroende på det faktiska programprotokollet som används för en viss nätverksanslutning. Den aktuella metoden fungerar till exempel bra för protokoll baserade på begäransvar, till exempel HTTP(S), men fungerar inte med enkelriktade eller meddelandeköbaserade protokoll.

Här är några viktiga punkter att tänka på:

1. Om en process accepterar anslutningar på samma IP-adress men via flera nätverksgränssnitt rapporteras en separat post för varje gränssnitt. 
2. Poster med jokertecken-IP innehåller ingen aktivitet. De ingår för att representera det faktum att en port på maskinen är öppen för inkommande trafik.
3. För att minska verbositeten och datavolymen utelämnas poster med jokertecken-IP när det finns en matchande post (för samma process, port och protokoll) med en specifik IP-adress. När en IP-post med jokertecken utelämnas ställs egenskapen IsWildcardBind-post med den specifika IP-adressen in på "True" för att ange att porten exponeras över alla gränssnitt på rapporteringsdatorn.
4. Portar som endast är bundna på ett visst gränssnitt har IsWildcardBind inställt på "False".

#### <a name="naming-and-classification"></a>Namngivning och klassificering

För enkelhetens skull ingår IP-adressen för fjärrslutet av en anslutning i egenskapen RemoteIp. För inkommande anslutningar är RemoteIp samma som SourceIp, medan för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar de DNS-kanoniska namn som rapporteras av datorn för RemoteIp. Egenskaperna RemoteDnsQuestions och RemoteClassification är reserverade för framtida användning. 

#### <a name="geolocation"></a>Geolocation

*VMConnection* innehåller även geolokaliseringsinformation för fjärrslutet av varje anslutningspost i följande egenskaper för posten: 

| Egenskap | Beskrivning |
|:--|:--|
| `RemoteCountry` |Namnet på det land/den region som är värd för RemoteIp.  Till exempel *kan USA* |
| `RemoteLatitude` |Geolocation latitud.  Till exempel *47,68* |
| `RemoteLongitude` |Geolocation longituden.  Till exempel *-122,12* |

#### <a name="malicious-ip"></a>Skadlig IP

Varje RemoteIp-egenskap i *VMConnection-tabellen* kontrolleras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadliga fylls följande egenskaper i (de är tomma, när IP-adressen inte anses vara skadlig) i postens följande egenskaper:

| Egenskap | Beskrivning |
|:--|:--|
| `MaliciousIp` |RemoteIp-adressen |
| `IndicatorThreadType` |Hotindikator som upptäckts är ett av följande värden, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
| `Description` |Beskrivning av det observerade hotet. |
| `TLPLevel` |TLP-nivå (Traffic Light Protocol) är ett av de definierade värdena, *Vit,* *Grön*, *Gul*, *Röd*. |
| `Confidence` |Värdena är *0 – 100*. |
| `Severity` |Värdena är *0 – 5*, där *5* är den allvarligaste och *0* inte är allvarlig alls. Standardvärdet är *3*.  |
| `FirstReportedDateTime` |Första gången leverantören rapporterade indikatorn. |
| `LastReportedDateTime` |Senast indikatorn sågs av Interflow. |
| `IsActive` |Anger att indikatorer inaktiveras med *sant* eller *falskt* värde. |
| `ReportReferenceLink` |Länkar till rapporter relaterade till en viss observerbar. |
| `AdditionalInformation` |Ger ytterligare information, om tillämpligt, om det observerade hotet. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL poster

Poster med en typ av *ServiceMapComputer_CL* har lagerdata för servrar med Service Map-agenter. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Den unika identifieraren för en maskin inom arbetsytan |
| `ResourceName_s` | Den unika identifieraren för en maskin inom arbetsytan |
| `ComputerName_s` | Datorn FQDN |
| `Ipv4Addresses_s` | En lista över serverns IPv4-adresser |
| `Ipv6Addresses_s` | En lista över serverns IPv6-adresser |
| `DnsNames_s` | En matris med DNS-namn |
| `OperatingSystemFamily_s` | Windows eller Linux |
| `OperatingSystemFullName_s` | Operativsystemets fullständiga namn  |
| `Bitness_s` | Maskinens bitighet (32- eller 64-bitars)  |
| `PhysicalMemory_d` | Det fysiska minnet i MB |
| `Cpus_d` | Antalet processorer |
| `CpuSpeed_d` | CPU-hastigheten i MHz|
| `VirtualizationState_s` | *okänd*, *fysisk*, *virtuell*, *hypervisor* |
| `VirtualMachineType_s` | *hyperv*, *vmware*, och så vidare |
| `VirtualMachineNativeMachineId_g` | VM-ID:et som tilldelats av dess hypervisor |
| `VirtualMachineName_s` | Namnet på den virtuella datorn |
| `BootTime_t` | Starttiden |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL typposter

Poster med en typ av *ServiceMapProcess_CL* har lagerdata för TCP-anslutna processer på servrar med Service Map-agenter. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Den unika identifieraren för en process inom arbetsytan |
| `ResourceName_s` | Den unika identifieraren för en process i den maskin som den körs på|
| `MachineResourceName_s` | Maskinens resursnamn |
| `ExecutableName_s` | Namnet på den körbara processen |
| `StartTime_t` | Processpoolens starttid |
| `FirstPid_d` | Det första PID i processpoolen |
| `Description_s` | Processbeskrivningen |
| `CompanyName_s` | Namnet på företaget |
| `InternalName_s` | Det interna namnet |
| `ProductName_s` | Produktens namn |
| `ProductVersion_s` | Produktversionen |
| `FileVersion_s` | Filversionen |
| `CommandLine_s` | Kommandoraden |
| `ExecutablePath _s` | Sökvägen till den körbara filen |
| `WorkingDirectory_s` | Arbetskatalogen |
| `UserName` | Kontot under vilket processen körs |
| `UserDomain` | Den domän under vilken processen körs |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

### <a name="list-all-known-machines"></a>Lista alla kända maskiner

ServiceMapComputer_CL | sammanfatta arg_max(TimeGenerated, *) efter ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista den fysiska minneskapaciteten för alla hanterade datorer.

ServiceMapComputer_CL | sammanfatta arg_max (TimeGenerated, *) av ResourceId | projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Lista datornamn, DNS, IP och OS.

ServiceMapComputer_CL | sammanfatta arg_max (TimeGenerated, *) av ResourceId | projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med "sql" på kommandoraden

ServiceMapProcess_CL | där CommandLine_s contains_cs "sql" | sammanfatta arg_max(TimeGenerated, *) efter ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste posten) efter resursnamn

sök i (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | sammanfatta arg_max(TimeGenerated, *) efter ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste posten) efter IP-adress

sök i (ServiceMapComputer_CL) "10.229.243.232" | sammanfatta arg_max(TimeGenerated, *) efter ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Lista alla kända processer på en angiven dator

ServiceMapProcess_CL | där MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | sammanfatta arg_max(TimeGenerated, *) efter ResourceId

### <a name="list-all-computers-running-sql"></a>Lista alla datorer som kör SQL

ServiceMapComputer_CL | där ResourceName_s i ((sök i (ServiceMapProcess_CL)\*"\*sql " | MachineResourceName_s) | distinkt ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista alla unika produktversioner av curl i mitt datacenter

ServiceMapProcess_CL | där ExecutableName_s == "curl" | distinkt ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en datorgrupp över alla datorer som kör CentOS

ServiceMapComputer_CL | där OperatingSystemFullName_s contains_cs "CentOS" | distinkt ComputerName_s

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

## <a name="rest-api"></a>REST API

Alla server-, process- och beroendedata i Service Map är tillgängliga via [REST-API:et för tjänstmappning](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata

Microsoft samlar automatiskt in användnings- och prestandadata genom din användning av tjänsten Service Map. Microsoft använder dessa data för att tillhandahålla och förbättra servicetjänstens kvalitet, säkerhet och integritet. För att tillhandahålla korrekta och effektiva felsökningsfunktioner innehåller data information om konfigurationen av programvaran, till exempel operativsystem och version, IP-adress, DNS-namn och arbetsstationsnamn. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning av data finns i [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Nästa steg

Läs mer om [loggsökningar](../../azure-monitor/log-query/log-query-overview.md) i Logganalys för att hämta data som samlas in av Service Map.

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att installera eller köra Service Map kan det här avsnittet hjälpa dig. Om du fortfarande inte kan lösa problemet kontaktar du Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Installationsproblem för beroendeagent

#### <a name="installer-prompts-for-a-reboot"></a>Installationsprogrammet uppmanas till en omstart
Beroendeagenten kräver *i allmänhet* ingen omstart vid installation eller borttagning. I vissa sällsynta fall kräver Windows Server dock en omstart för att fortsätta med en installation. Detta händer när ett beroende, vanligtvis Microsoft Visual C ++ Redistributable biblioteket kräver en omstart på grund av en låst fil.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Meddelande "Det gick inte att installera Beroendeagent: Visual Studio Runtime-bibliotek kunde inte installeras (kod = [code_number])" visas

Microsoft Dependency-agenten bygger på Microsoft Visual Studio-körningsbiblioteken. Du får ett meddelande om det finns ett problem under installationen av biblioteken. 

Installationsprogrammet för körningsbibliotek skapar loggar i mappen %LOCALAPPDATA%\temp. Filen är `dd_vcredist_arch_yyyymmddhhmmss.log`, där `x86` `amd64` *bågen* är eller och *yyyymmddhhmmss* är datum och tid (24-timmars klocka) när loggen skapades. Loggen innehåller information om problemet som blockerar installationen.

Det kan vara användbart att installera de [senaste körningsbiblioteken](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) först.

I följande tabell visas kodnummer och föreslagna lösningar.

| Kod | Beskrivning | Lösning |
|:--|:--|:--|
| 0x17 | Biblioteksinstallationsprogrammet kräver en Windows-uppdatering som inte har installerats. | Titta i den senaste biblioteksinstallerarloggen.<br><br>Om en `Windows8.1-KB2999226-x64.msu` referens till följs av en rad `Error 0x80240017: Failed to execute MSU package,` som du inte har förutsättningar att installera KB2999226. Följ instruktionerna i avsnittet krav i [universal C Runtime i](https://support.microsoft.com/kb/2999226) Windows-artikeln. Du kan behöva köra Windows Update och starta om flera gånger för att kunna installera förutsättningarna.<br><br>Kör installationsprogrammet för Microsoft Dependency Agent igen. |

### <a name="post-installation-issues"></a>Problem efter installationen

#### <a name="server-doesnt-appear-in-service-map"></a>Servern visas inte i servicemappning

Om installationen av beroendeagenten lyckades, men du inte ser din dator i servicemappningslösningen:
* Är beroendeagenten installerad? Du kan validera detta genom att kontrollera om tjänsten är installerad och körs.<br><br>
**Windows**: Leta efter tjänsten **Microsoft Dependency Agent**.
**Linux**: Leta efter den pågående processen **microsoft-dependency-agent**.

* Är du på [Log Analytics-kostnadsfria nivån?](https://azure.microsoft.com/pricing/details/monitor/) Den kostnadsfria planen tillåter upp till fem unika servicekartmaskiner. Efterföljande datorer visas inte i Service Map, även om de föregående fem inte längre skickar data.

* Skickar servern logg- och perf-data till Azure Monitor Logs? Gå till Azure Monitor\Loggar och kör följande fråga för datorn: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Fick du en mängd olika händelser i resultaten? Är uppgifterna aktuella? I så fall fungerar log Analytics-agenten korrekt och kommunicerar med arbetsytan. Om inte, kontrollera agenten på din dator: [Log Analytics agent för Windows felsökning](../platform/agent-windows-troubleshoot.md) eller Log Analytics agent för Linux [felsökning](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servern visas i Service Map men har inga processer

Om du ser din dator i Service Map, men den inte har några process- eller anslutningsdata, betyder det att beroendeagenten är installerad och körs, men kärndrivrutinen läses inte in. 

Kontrollera `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) `/var/opt/microsoft/dependency-agent/log/service.log file` eller (Linux). De sista raderna i filen bör ange varför kärnan inte laddas. Kärnan kanske till exempel inte stöds på Linux om du har uppdaterat din kärna.

## <a name="feedback"></a>Feedback

Har du någon feedback till oss om Service Map eller denna dokumentation?  Besök vår [User Voice sida](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), där du kan föreslå funktioner eller rösta upp befintliga förslag.
