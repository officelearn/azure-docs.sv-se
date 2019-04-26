---
title: Övervaka hälsa för virtuell dator med Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Den här artikeln beskrivs hur du förstå hälsotillståndet för den virtuella datorn och det underliggande operativsystemet med Azure Monitor för virtuella datorer.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2019
ms.author: magoedte
ms.openlocfilehash: f2a0d64da5a88e82c0ae1fd893af52f2070268f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402249"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Förstå hälsotillståndet för virtuella datorer i Azure

Azure innehåller flera tjänster som individuellt utför en viss roll eller en uppgift i övervakningsutrymmet, men inte har angett en djupgående hälsotillstånd perspektiv av operativsystemet finns på Azure virtual machines. Medan du kan övervaka för olika villkor med Azure Monitor, var inte det utformat för att modellera och representerar hälsan för kärnkomponenter eller övergripande hälsa för den virtuella datorn. Med Azure Monitor för virtuella datorer hälsotillstånd funktion kan övervakar det proaktivt tillgänglighet och prestanda för Windows eller Linux gästoperativsystemet med en modell som representerar viktiga komponenter och deras relationer villkor som anger hur hälsotillståndet för de komponenter, och varnar dig när ett feltillstånd har identifierats.  

Visa det övergripande hälsotillståndet för Azure VM och underliggande operativsystemet kan observeras ur två perspektiv med Azure Monitor för virtuella datorer hälsotillstånd, direkt från den virtuella datorn eller i alla virtuella datorer i en resursgrupp från Azure Monitor.

Den här artikeln hjälper dig att snabbt utvärdera, undersöka och lösa problem med hälsotillstånd har identifierats.

Information om hur du konfigurerar Azure Monitor för virtuella datorer finns i [aktivera Azure Monitor för virtuella datorer](vminsights-onboard.md).

## <a name="monitoring-configuration-details"></a>Information om övervakning

Det här avsnittet beskrivs standardkriteriet för hälsotillstånd som definierats för att övervaka Azure Windows och Linux-datorer. Alla health-villkor är förinställd på att aviseringen när det felaktiga villkoret uppfylls. 

### <a name="windows-vms"></a>Virtuella Windows-datorer

- Ledigt minne i MB 
- Genomsnittlig sekunder Per skrivning (logisk Disk)
- Genomsnittlig sekunder Per skrivning (Disk)
- Genomsnittlig logisk Disk sekunder Per läsning
- Genomsnittlig logisk Disk sekunder Per överföring
- Genomsnittlig sekunder Per läsning
- Genomsnittlig sekunder Per överföring
- Aktuell diskkölängd (logisk Disk)
- Aktuell diskkölängd (Disk)
- Disk procent inaktivitetstid
- Filsystemfel eller skadade data
- Ledigt utrymme för logisk Disk (%) Låg
- Ledigt utrymme (MB) med låg för logisk Disk
- Logisk Disk procent inaktivitetstid
- Minnessidor Per sekund
- Procent bandbredd används Läs
- Procent bandbredd används totalt
- Procent bandbredd används skrivning
- Procentandelen allokerat minne som används
- Disk procent inaktivitetstid
- Tjänstehälsa för DHCP-klient
- DNS Client Service Health
- RPC Service Health
- Server Service Health
- Total CPU-utnyttjandeprocent
- Tjänstehälsa för Windows-händelseloggen
- Tjänstehälsa för Windows-brandväggen
- Windows Remote Management Service Health

### <a name="linux-vms"></a>Virtuella Linux-datorer
- Disk genomsn. S/disköverföring 
- Disk genomsn. S/diskläsning 
- Disk genomsn. S/diskskrivning 
- Disk Health
- Ledigt utrymme för logisk Disk
- Logisk Disk % ledigt utrymme
- Logisk Disk % Ledigai
- Nätverkskortets hälsa
- Total processortid i procent
- Operativsystemet ledigt minne i MB

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Introduktion till hälsotillstånd upplevelse

Innan du börjar använda funktionen hälsotillståndet för en enskild virtuell dator eller en grupp med virtuella datorer, är det viktigt att vi ger en kort introduktion så att du förstår hur informationen visas och visualiseringar representerar.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Visa hälsa direkt från en virtuell dator 

Om du vill visa hälsotillståndet för en Azure-dator, Välj **Insights (förhandsversion)** från det vänstra fönstret för den virtuella datorn. På sidan VM insights **hälsotillstånd** visas som standard och visar vyn hälsotillståndet för den virtuella datorn.  

![Azure Monitor för översikt av hälsotillstånd för virtuella datorer för en vald Azure-dator](./media/vminsights-health/vminsights-directvm-health.png)

På den **hälsotillstånd** fliken, under avsnittet **gäst VM-hälsa**, visas det aktuella hälsotillståndet för den virtuella datorn och det totala antalet VM-hälsa aviseringar som aktiverats av en defekt komponent. Mer information finns i [aviseringar](#alerts) för ytterligare information om aviseringar uppstår.  

Hälsotillstånden som definierats för en virtuell dator beskrivs i följande tabell: 

|Ikon |Hälsotillstånd |Betydelse |
|-----|-------------|------------|
| |Felfri |Hälsotillståndet är felfri om det är inom de definierade hälsovillkoren, som anger inga problem hittades för den virtuella datorn och den fungerar som krävs. Med en överordnad Övervakare för insamling visar hälsotillstånd samlar in och du det bästa eller sämsta tillståndet för underordnat.|
| |Kritisk |Hälsotillståndet är kritiskt om den inte är inom definierad hälsostatus, som anger att en eller flera kritiska problem har identifierats som behöver åtgärdas om du vill återställa normal drift. Med en överordnad Övervakare för insamling visar hälsotillstånd samlar in och du det bästa eller sämsta tillståndet för underordnat.|
| |Varning |Hälsotillståndet är en varning om det är mellan två tröskelvärden för definierade hälsostatus, där en anger en *varning* tillstånd och den andra anger en *kritisk* tillstånd (tre hälsotillstånd tillstånd tröskelvärden kan kan konfigureras), eller när ett icke-kritiska problem har identifierats som kan orsaka problem om inte lösas. Med en överordnad samlad Övervakare, om en eller flera av de underordnade objekten är i varningstillstånd så kommer att användas i överordnat *varning* tillstånd. Om det finns en underordnad som tillhör en *kritisk* och en annan underordnade i en *varning* tillstånd, den överordnade samlade visar ett hälsotillstånd *kritisk*.|
| |Inte tillgängligt |Hälsotillståndet är i ett *okänd* tillstånd när hälsotillståndet inte kan beräknas av flera skäl, till exempel inte kunna samla in data, tjänsten som ej initierad, osv. Den här hälsotillstånd kan inte konfigureras.| 

Att välja **visa hälsotillstånd diagnostik** öppnas en sida som visar alla komponenter i den virtuella datorn, tillhörande hälsostatus kriterier, tillståndsändringar och andra problem som uppstod genom att övervaka komponenter som hör till den virtuella datorn. Mer information finns i [hälsotillstånd diagnostik](#health-diagnostics). 

Under den **komponenthälsa** avsnittet visas en samlad hälsostatus primära prestanda-kategorier som övervakas av health-villkor för dessa områden mer specifikt **CPU**,  **Minne**, **Disk**, och **nätverk**.  Att välja någon av komponenterna öppnas en sida där alla enskilda hälsotillstånd villkoret övervakning aspekter av komponenten och respektive hälsotillståndet för var och en.  

Vid åtkomst till hälsotillstånd från en Azure virtuell dator som kör Windows-operativsystem, hälsotillståndet för överst fem viktiga Windows services visas under avsnittet **Core services health**.  Om du väljer någon av tjänsterna öppnas en sida där health-villkor som övervakning av komponenten och dess hälsotillstånd.  När du klickar på namnet på health-villkor öppnas egenskapsrutan och härifrån kan du granska konfigurationsinformationen, inklusive om health-villkor har en motsvarande Azure Monitor-avisering som definierats. Mer information finns i [hälsotillstånd diagnostik- och arbeta med Hälsokriterier](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Sammanställd VM perspektiv

Om du vill visa insamling av hälsotillstånd för alla dina virtuella datorer i en resursgrupp från listan över navigeringsfönstret i portalen, Välj **Azure Monitor** och välj sedan **virtuella datorer (förhandsversion)**.  

![VM-Insights övervakningsvyn från Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Från den **prenumeration** och **resursgrupp** listrutor, Välj lämplig resursgruppen som innehåller de virtuella datorerna relaterade till gruppen för att visa deras rapporterade hälsotillstånd.  Ditt val har endast gäller för funktionen hälsotillstånd och inget prestanda eller kartan.

På den **hälsotillstånd** fliken du kan lära dig följande:

* Hur många virtuella datorer är i ett kritiskt eller dåligt tillstånd, jämfört med hur många är felfri eller inte skickar data (kallas ett okänt tillstånd)?
* Vilka virtuella datorer efter operativsystem (OS) rapporterar ett feltillstånd och hur många?
* Hur många virtuella datorer är skadad på grund av ett problem har identifierats med en processor, disk, minne eller nätverkskort, kategoriserade efter hälsotillstånd?  
* Hur många virtuella datorer är skadad på grund av ett problem har identifierats med en grundläggande operativsystemtjänst, kategoriserade efter hälsotillstånd?

Här du snabbt kan identifiera de övre kritiska problem som identifieras av health-villkor som proaktivt övervaka den virtuella datorn och granska varningsinformationen för VM-hälsa och associerade kunskapsartikel som är avsedd att hjälpa diagnos och reparation av problemet.  Välj någon av allvarlighetsgrader att öppna den [alla aviseringar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sidan filtreras efter den allvarlighetsgraden.

Den **VM-distribution av operativsystem** i listan visas virtuella datorer som anges av version av Windows eller Linux-distribution, tillsammans med deras version. I varje kategori för operativsystemet, de virtuella datorerna är uppdelade ytterligare baserat på hälsotillståndet för den virtuella datorn. 

![VM-Insights VM distribution perspektiv](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Du kan klicka på valfri kolumn-artikel - **antal virtuella datorer**, **kritisk**, **varning**, **felfri** eller **okänd** till detaljnivån i **virtuella datorer** sidan finns i en lista över filtrerade resultat som matchar den markerade kolumnen. Om vi vill granska alla virtuella datorer som kör till exempel **Red Hat Enterprise Linux-version 7.5**, klicka på den **antal virtuella datorer** värdet för det OS och det öppnas sidan därefter visa en lista över de virtuella datorerna som matchar filtret och deras kända hälsotillstånd.  

![Exempelsammanslagning för Red Hat Linux-datorer](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
På den **virtuella datorer** om du väljer namnet på en virtuell dator under kolumnen **namn på virtuell dator**, dirigeras du till sidan VM-instans med mer information om aviseringar och hälsoproblem för villkor har identifierat som är påverkar den valda virtuella datorn.  Härifrån kan du filtrera tillståndsinformation hälsa genom att klicka på **hälsotillstånd** ikonen i det övre vänstra hörnet av sidan för att se vilka komponenter som är felaktiga eller du kan visa VM-hälsa aviseringar som genererats av en defekt komponent kategoriserade efter allvarlighetsgrad.    

När du klickar på namnet på en virtuell dator öppnas från VM-listvyn i **hälsotillstånd** för vald virtuell dator, på samma sätt som om du har valt **Insights (förhandsversion)** från den virtuella datorn direkt.

![VM-insikter från en valda Azure-dator](./media/vminsights-health/vminsights-directvm-health.png)

Här visas en sammanfattning **hälsostatus** för den virtuella datorn och **aviseringar**, kategoriserade efter allvarlighetsgrad som motsvarar den VM-hälsa aviseringar aktiveras när hälsotillståndet ändras från hälsotillstånd till feltillstånd för en Health-villkor.  Att välja **virtuella datorer i kritiska tillståndet** öppnas en sida med en lista över en eller flera virtuella datorer som tillhör ett kritiskt hälsotillstånd.  Om du klickar på hälsotillståndet för någon av de virtuella datorerna i listan visas de **hälsotillstånd diagnostik** vy av den virtuella datorn.  Här kan du se vilka health-villkor återger ett hälsoproblem tillstånd. När den **hälsotillstånd diagnostik** öppnas, visas alla komponenter i den virtuella datorn och deras tillhörande hälsostatus kriterier, och det aktuella hälsotillstånd. Mer information finns i [hälsotillstånd diagnostiska](#health-diagnostics).  

Att välja **visa alla Hälsokriterier** öppnas en sida som visar en lista över alla health-villkor som medföljer den här funktionen.  Informationen kan filtreras ytterligare baserat på följande alternativ:

* **Typ** – det finns tre typer av hälsotillstånd kriterier typer att utvärdera villkor och samlar in övergripande hälsotillståndet hos den övervakade virtuella datorn.  
    a. **Enhet** – mäter en viss aspekt av den virtuella datorn. Denna Kriterietyp för hälsotillstånd kan handla om att kontrollera en prestandaräknare för att avgöra prestanda för komponenten, köra ett skript för att utföra en syntetisk transaktion eller bevaka en händelse som påvisar ett fel.  Filtret är som standard till enheten.  
    b. **Beroende** -ger en hälsosammanslagning mellan olika enheter. Den här health-villkor kan hälsotillståndet för en entitet förlita sig på hälsotillståndet för en annan typ av enhet som den förlitar sig på för åtgärden lyckas.  
    c. **Sammanställd** -ger ett kombinerat hälsotillstånd för liknande health-villkor. Enhets- och beroendeövervakare hälsotillstånd kriterium konfigureras vanligtvis under ett kriterium sammanställd hälsotillstånd. Förutom att tillhandahålla bättre överblick av många olika Hälsokriterier riktad mot en entitet, ger sammanställda hälsotillstånd kriterium ett unikt hälsotillstånd för olika kategorier av entiteterna.

* **Kategori** -typ av health-villkor som används för att gruppen kriterier av liknande typ för rapportering.  De är antingen **tillgänglighet** eller **prestanda**.

Du kan gå längre ned om du vill se vilka instanser som inte är felfria genom att klicka på ett värde under den **komponenter som innehåller fel** kolumn.  På sidan med en tabell de komponenter som finns i ett kritiskt hälsotillstånd.    

## <a name="health-diagnostics"></a>Diagnostik för hälsa

Thge **hälsotillstånd diagnostik** sidan kan du visualisera Hälsomodell för en virtuell dator, visa en lista över alla komponenter i den virtuella datorn, som är associerade health-villkor, tillståndsändringar, och andra problem som identifieras av övervakas relaterade komponenter till den virtuella datorn.

![Exempel på sidan hälsa diagnostik för en virtuell dator](./media/vminsights-health/health-diagnostics-page-01.png)

Du kan starta hälsotillstånd diagnostik på följande sätt.

* Efter insamling av hälsotillstånd för alla virtuella datorer ur sammanställd VM i Azure Monitor.  På den **hälsotillstånd** klickar du på ikonen för **kritisk**, **varning**, **felfri**, eller **okänd** hälsotillstånd under avsnittet **gäst VM-hälsa** och öka detaljnivån ned till den sida som visar en lista över alla virtuella datorer som matchar den filtrera kategorin.  Klicka på värdet i den **hälsotillstånd** kolumn öppnas hälsotillstånd diagnostik tillhöra den viss virtuell datorn.      

* Efter operativsystem ur sammanställd VM i Azure Monitor. Under **VM-distribution**, att välja någon av kolumnvärdena öppnas den **virtuella datorer** sidan och returnerar en lista i tabellen som matchar den filtrerade kategorin.  När du klickar på värde under **hälsotillstånd** kolumn öppnas diagnostik för hälsa för den valda virtuella datorn.    
 
* Från den Virtuella Azure-Övervakaren för virtuella datorer gästdatorn **hälsotillstånd** fliken genom att välja **visa hälsotillstånd diagnostik** 

Diagnostik för hälsa ordnar hälsoinformation i följande kategorier: 

* Tillgänglighet
* Prestanda
 
Alla health-villkor som definierats för en viss komponent, till exempel logisk disk, CPU, etc. kan ses utan filtrering på två kategorier (det vill säga en fulla vy över alla villkor) eller filtrera resultaten genom att antingen kategori när du väljer **tillgänglighet**  eller **prestanda** alternativen på sidan. Dessutom kategorin för villkoren visas bredvid den i den **Hälsokriterier** kolumn. Om villkoren inte matchar den valda kategorin, visas meddelandet **inga health-villkor som är tillgängliga för den valda kategorin** i den **Hälsokriterier** kolumn.  

Tillståndet för en health-villkor definieras av något av fyra tillstånd – *kritisk*, *varning*, *felfri*, och *okänd*. De första tre kan konfigureras, vilket innebär att du kan ändra tröskelvärdena för Övervakare direkt från fönstret Hälsokriterier konfiguration eller med hjälp av REST-API i Azure Monitor [uppdateringsåtgärden övervakaren](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Okänd* inte är konfigurerbar och reserverade för specifika scenarier.  

Sidan för Health-diagnostik har tre huvudområden:

* Komponentmodell 
* Hälsovillkor
* Tillståndsändringar 

![Delar av sidan hälsa diagnostik](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Komponentmodellen

Kolumnen längst till vänster på sidan hälsa diagnostik är komponentmodellen. Alla komponenter, som är associerade med den virtuella datorn, visas i den här kolumnen tillsammans med deras aktuella hälsotillstånd. 

I exemplet nedan är de identifierade komponenterna disk, logisk disk, processor, minne och operativsystem. Flera instanser av dessa komponenter identifieras och visas i den här kolumnen. Bilden nedan visar exempelvis den virtuella datorn har två instanser av logiska diskar – C: och D:, som inte är i felfritt tillstånd.  

![Komponenten exempelmodellen visas i hälsotillstånd diagnostik](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Health-villkor

Den mellersta kolumnen på sidan hälsa diagnostik är den **Hälsokriterier** kolumn. Hälsomodell som definierats för den virtuella datorn visas i en hierarkisk trädstruktur. Hälsomodell för en virtuell dator består av enhet och sammanställda health-villkor.  

![Exempel health-villkor visas i hälsotillstånd diagnostik](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Ett villkor för health mäter hälsotillståndet för övervakade instansen med vissa villkor, vilket kan vara ett tröskelvärde läget för en entitet, osv. Ett villkor för hälsotillstånd har antingen två eller tre konfigurerbara hälsotillstånd tillstånd tröskelvärden enligt beskrivningen ovan. Hälsotillstånd villkoret kan vara i endast en av sina möjliga tillstånd vid en given tidpunkt. 

Den övergripande hälsan för ett mål bestäms av hälsotillståndet för var och en av dess health-villkor som definierats i hälsomodellen. Det är en kombination av Hälsokriterier riktade direkt mot målet, health-villkor som är riktad mot komponenter som samlas in till målet via en sammanställd health-villkor. Den här hierarkin illustreras i den **Hälsokriterier** delen av sidan diagnostik för hälsa. Principen för hälsosammanslagning är en del av konfigurationen av sammanställda health-villkor (standard är inställt på *sämsta av*). Du hittar en lista över standarduppsättning health-villkor som körs som en del av den här funktionen under avsnittet [övervakning konfigurationsinformation](#monitoring-configuration-details), och du kan använda Azure Monitor REST API [övervaka instanser - lista efter resurs åtgärden](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) att hämta en lista över alla health-villkor och dess detaljerad konfiguration som körs mot den Virtuella Azure-resursen.  

**Enhet** hälsotillstånd Kriterietyp kan ha sin konfiguration ändras genom att klicka på ellipsen länken till längst till höger och markera **Visa detaljer** att öppna fönstret konfiguration. 

![Konfigurera ett hälsotillstånd kriterier-exempel](./media/vminsights-health/health-diagnostics-vm-example-02.png)

I fönstret konfiguration för valda health-villkor med hjälp av exempel **genomsnittliga Disk sekunder Per skriva**, dess tröskeln kan konfigureras med ett annat numeriskt värde. Det är en Övervakare med två tillstånd, vilket innebär att det endast ändringar från felfri till varning. Andra health-villkor kan vara tre lägen, där du kan konfigurera värdet för tillståndströskeln varning och kritiskt hälsotillstånd. Du kan också ändra tröskelvärdet med hjälp av REST-API i Azure Monitor [uppdateringsåtgärden övervakaren](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Tillämpa hälsotillstånd kriterier konfigurationsändringar på en instans tillämpas på alla övervakade instanser.  Exempel: Om du väljer **Disk-1 D:** och ändra den **genomsnittliga Disk sekunder Per skriva** tröskelvärde för det gäller inte för endast den instansen, men om alla andra diskinstanser identifieras och övervakas på den virtuella datorn.
>

![Konfigurera en health-villkor för till exempel övervaka en enhet](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Om du vill veta mer om indikatorn för hälsotillstånd ingår artiklar för att identifiera problem, orsaker och lösningar. Klicka på den **visa information** länken på sidan och öppnar en ny flik i webbläsaren som visar den specifika kunskapsartikeln. När som helst kan du granska alla hälsotillstånd kriterium kunskapsartiklar ingår i Azure Monitor för virtuella datorer hälsotillstånd funktionen [här](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Tillståndsändringar

Höger kolumn i sidan diagnostik för hälsa är **tillståndsändringar**. Den visar en lista över alla tillståndsändringar som är associerade med Hälsokriterier som valts i den **Health-villkor** avsnittet eller tillståndsändring för den virtuella datorn om du valde en virtuell dator från den **Komponentmodellen** eller **Hälsokriterier** kolumnen i tabellen. 

![Exempel tillståndsändringar som presenteras i hälsotillstånd diagnostik](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Det här avsnittet består av kriterier hälsotillstånd och den associera tid sorterade efter det senaste tillståndet längst upp.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Associering av Komponentmodellen, Health-villkor och tillstånd ändra kolumner 

De tre kolumnerna är sammankopplade med varandra. När du väljer en identifierade instans i den **Komponentmodellen** avsnittet den **Hälsokriterier** avsnittet filtreras till denna Komponentvy och motsvarande den **tillståndsändring**uppdateras baserat på valda health-villkor. 

![Exempel på att välja övervakade instans och resultat](./media/vminsights-health/health-diagnostics-vm-example-01.png)

I exemplet ovan, när du väljer **Disk – 1 D:**, Health-villkor trädet är filtrerats till **Disk - 1 D:**. Den **tillståndsändring** kolumnen visar tillståndsändringen baserat på tillgängligheten för **Disk – 1 D:**. 

Om du vill se det uppdaterade hälsotillståndet, kan du uppdatera sidan diagnostik för hälsa genom att klicka på den **uppdatera** länk.  Om det finns en uppdatering till villkoret hälsotillstånd hälsotillstånd baserat på fördefinierade avsökningsintervallet, kan du undvika att vänta på den här uppgiften och återspeglar det senaste hälsotillståndet.  Den **kriterier hälsotillstånd** är ett filter så att du kan begränsa resultatet baserat på valda hälsotillståndet - *felfri*, *varning*, *kritisk*, *Okänd*, och *alla*.  Den **senaste uppdaterade** tid i det övre högra hörnet representerar den senaste gången när sidan diagnostik för hälsotillstånd har uppdaterats.  

## <a name="alerts"></a>Aviseringar

Azure Monitor för virtuella datorer Health-funktionen kan integreras med [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) och aktiverar en avisering när fördefinierade health-villkor ändras från felfri till feltillstånd när villkoret har identifierats. Aviseringar kategoriserade efter allvarlighetsgrad - allvarlighetsgrad 0 och 4, med allvarlighetsgrad 0 som representerar den högsta allvarlighetsgraden. 

Aviseringar är inte associerad med en åtgärdsgrupp att meddela dig när aviseringen har aktiverats. Prenumerationens ägare måste konfigurera meddelanden att följa [senare i det här avsnittet](#configure-alerts).   

Totalt antal VM-hälsa aviseringar kategoriserade efter allvarlighetsgrad är tillgänglig på den **hälsotillstånd** instrumentpanelen under avsnittet **aviseringar**. När du väljer antingen det totala antalet aviseringar eller talet som motsvarar en allvarlighetsgrad den **aviseringar** sidan öppnas och visar en lista över alla aviseringar som matchar ditt val.  Exempel: Om du har valt den raden motsvarar **allvarlighetsgrad nivå 1**, kan du se följande vy:

![Exempel på alla allvarlighetsgrad 1-aviseringar](./media/vminsights-health/vminsights-sev1-alerts-01.png)

På den **aviseringar** kan den inte omfattar endast om du vill visa aviseringar som matchar ditt val, men också filtreras efter **resurstyp** till att bara visa health-aviseringar som aktiverats av den virtuella datorresursen.  Den visas i listan över aviseringar under kolumnen **målresursen**, där det visar Azure-VM som varningen skapades för när de specifika Hälsokriterier feltillstånd uppfylldes.  

Aviseringar från andra typer av resurser eller tjänster är inte avsedda att inkluderas i den här vyn, som aviseringar baserat på loggfrågor eller mått aviseringar som du normalt skulle visa från standard Azure Monitor [alla aviseringar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sidan. 

Du kan filtrera den här vyn genom att välja värden i listrutorna överst på sidan.

|Kolumn |Beskrivning | 
|-------|------------| 
|Prenumeration |Välj en Azure-prenumeration. Bara aviseringar i den valda prenumerationen ingår i vyn. | 
|Resursgrupp |Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen ingår i vyn. | 
|Resurstyp |Välj en eller flera resurstyper. Som standard bara de aviseringar som mål **virtuella datorer** har valts och ingår i den här vyn. Den här kolumnen är endast tillgänglig när en resursgrupp har angetts. | 
|Resurs |Välj en resurs. Endast aviseringar med den här resursen som ett mål som ingår i vyn. Den här kolumnen är endast tillgänglig när du har angett en resurstyp. | 
|Severity |välja en allvarlighetsgrad för avisering eller välj *alla* att inkludera aviseringar för alla allvarlighetsgrader. | 
|Övervakningsvillkor |Välj ett övervakningsvillkor att filtrera aviseringar om de har *Fired* av systemet eller *löst* av systemet om villkoret är inte längre aktiv. Eller välj *alla* att inkludera aviseringar av alla villkor. | 
|Aviseringstillstånd |Välj en aviseringstillståndet *New*, *Bekräfta*, *stängd*, eller välj *alla* att inkludera aviseringar av alla tillstånd. | 
|Övervaka tjänsten |Välj en tjänst, eller välj *alla* att inkludera alla tjänster. Endast varningar från *VM insikter* stöds för den här funktionen.| 
|Tidsintervall| Bara de aviseringar som utlösts inom det valda tidsfönstret ingår i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. | 

Den **Avisera detalj** visas när du väljer en avisering genom att tillhandahålla information om aviseringen och så att du kan ändra tillståndet. Mer information om hur du hanterar aviseringar finns [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>På den här tiden, skapa nya aviseringar baserat på kriterier för hälsotillstånd eller ändra befintliga hälsotillstånd aviseringsregler i Azure Monitor från portalen stöds inte.  
>

![Information om fönstret om markerad avisering](./media/vminsights-health/alert-details-pane-01.png)

Avisera tillstånd kan också ändras för en eller flera aviseringar genom att markera dem och sedan välja **ändra tillståndet** från den **alla aviseringar** sidan klickar du på det övre vänstra hörnet. På den **ändra aviseringtillståndet** fönster du välja något av tillstånd, lägga till en beskrivning av ändringen i den **kommentar** fält och klicka på **Ok** att genomföra ändringarna. Även om informationen har verifierats och ändringarna tillämpas, du kan spåra förloppet under **meddelanden** på menyn.  

### <a name="configure-alerts"></a>Konfigurera varningar
Vissa Avisera management uppgifter inte kan hanteras från Azure-portalen och måste utföras med hjälp av den [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Närmare bestämt:

- Aktivera eller inaktivera en avisering för health-villkor 
- Konfigurera aviseringar för kriterier för hälsovarningar 

Med hjälp av den metod som används i varje exempel [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. Om du inte är bekant med den här metoden kan du läsa [med ARMClient](../platform/rest-api-walkthrough.md#use-armclient).  

#### <a name="enable-or-disable-alert-rule"></a>Aktivera eller inaktivera varningsregel

Aktivera eller inaktivera en avisering för ett visst hälsotillstånd villkor, egenskapen hälsotillstånd kriterier *alertGeneration* ändras med antingen värdet **inaktiverad** eller **aktiverad**. Att identifiera den *monitorId* för ett visst hälsotillstånd villkor i följande exempel visar hur du frågar efter värdet för villkoret **LogicalDisk\Avg Disk sekunder Per överföra**.

1. Skriv i ett terminalfönster **armclient.exe inloggning**. Då uppmanas du att logga in på Azure.

2. Skriv följande kommando för att hämta alla health-villkoret som aktiv på en specifik virtuell dator och identifiera värdet för *monitorId* egenskapen. 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    I följande exempel visar utdata från kommandot. Anteckna värdet för *MonitorId*. Det här värdet måste anges för nästa steg när vi behöver ange ID för health-villkor och ändra dess egenskap för att skapa en avisering.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Skriv följande kommando för att ändra den *alertGeneration* egenskapen.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Skriv kommandot GET används i steg 2 för att kontrollera värdet för egenskapen har angetts till **inaktiverad**.  

#### <a name="associate-action-group-with-health-criteria"></a>Koppla åtgärdsgrupp health-villkor

Azure Monitor för virtuella datorer Health stöder SMS eller e-postmeddelanden när aviseringar genereras när Hälsokriterier blir ohälsosamt. Om du vill konfigurera meddelanden, måste du notera namnet på åtgärdsgrupp som är konfigurerad för att skicka SMS eller e-meddelanden. 

>[!NOTE]
>Den här åtgärden måste utföras mot varje virtuell dator övervakas som du vill få ett meddelande för.

1. Skriv i ett terminalfönster **armclient.exe inloggning**. Då uppmanas du att logga in på Azure.

2. Skriv följande kommando för att associera en åtgärdsgrupp med Varningsregler.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Kontrollera värdet för egenskapen **actionGroupResourceIds** har uppdaterats, skriver du följande kommando.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Utdata bör likna följande:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Nästa steg

För att identifiera flaskhalsar och totala användningen med din prestanda för virtuella datorer, se [visa Azure VM prestanda](vminsights-performance.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). 
