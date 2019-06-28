---
title: Förstå hälsotillståndet för virtuella datorer i Azure | Microsoft Docs
description: Den här artikeln beskriver hur du förstå hälsotillståndet för virtuella datorer och underliggande operativsystem med Azure Monitor för virtuella datorer.
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
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340148"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Förstå hälsotillståndet för virtuella datorer i Azure

Azure innehåller tjänster för specifika roller eller uppgifter i övervakningsutrymmet, men det ger inte djupgående hälsotillstånd perspektiv av operativsystem (OSs) på virtuella Azure-datorer (VM). Men du kan använda Azure Monitor för olika villkor, utformad inte för att modellera och representerar hälsan för kärnkomponenter eller den övergripande hälsan för virtuella datorer.

Med Azure Monitor för hälsotillståndet för virtuella datorer kan övervaka du tillgänglighet och prestanda för en Windows- eller Linux gäst-OS aktivt. Funktionen hälsotillstånd använder en modell som representerar viktiga komponenter och deras relationer, innehåller villkor som anger hur att mäta komponentens hälsostatus och skickar en avisering när den identifierar ett feltillstånd.

Visa det övergripande hälsotillståndet för en Azure-dator och det underliggande Operativsystemet kan observeras ur två perspektiv: direkt från en virtuell dator eller i alla virtuella datorer i en resursgrupp från Azure Monitor.

Den här artikeln visar hur du snabbt utvärdera, undersöka och lösa problem med hälsotillstånd när de identifieras av Azure-Övervakare för virtuella datorer hälsotillstånd funktion.

Information om hur du konfigurerar Azure Monitor för virtuella datorer finns i [aktivera Azure Monitor för virtuella datorer](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Information om övervakning

Det här avsnittet beskrivs standardkriteriet hälsotillstånd för att övervaka Azure Windows och Linux-datorer. Alla health-villkor är förinställd på att skicka en avisering när de identifierar ett feltillstånd.

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

Logga in genom att gå till den [Azure-portalen](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introduktion till Azure Monitor för hälsotillståndet för virtuella datorer

Innan du använder funktionen hälsotillståndet för en enskild virtuell dator eller grupp med virtuella datorer, är det viktigt att förstå hur informationen visas och visualiseringar representerar.

### <a name="view-health-directly-from-a-vm"></a>Visa hälsa direkt från en virtuell dator

Om du vill visa hälsotillståndet för en Azure-dator, Välj **Insights (förhandsversion)** i det vänstra fönstret för den virtuella datorn. På sidan VM insikter i **hälsotillstånd** fliken visas som standard och visar vyn hälsotillståndet för den virtuella datorn.

![Azure Monitor för översikt av hälsotillstånd för virtuella datorer för en vald Azure-dator](./media/vminsights-health/vminsights-directvm-health.png)

I den **hälsotillstånd** fliken, under **gäst VM-hälsa**, tabellen visar hälsotillståndet för den virtuella datorn och det totala antalet hälsovarningar för virtuell dator som skapats av en defekt komponent.

Mer information finns i [aviseringar](#alerts).

Hälsotillstånden som definierats för en virtuell dator beskrivs i följande tabell:

|Ikon |Hälsotillstånd |Betydelse |
|-----|-------------|---------------|
| |Felfri |Den virtuella datorn ligger inom de definierade health-villkor. Det här tillståndet anger det inga problem hittas och den virtuella datorn fungerar normalt. Med en Övervakare för insamling av överordnade, hälsa samlas in och visar bästa eller sämsta tillståndet för underordnat.|
| |Kritiskt |Tillstånd som inte är inom definierad hälsostatus, som anger att en eller flera kritiska problem har identifierats. Dessa problem måste åtgärdas för att återställa normal drift. Med en Övervakare för insamling av överordnade, hälsotillståndet samlas in och visar bästa eller sämsta tillståndet för underordnat.|
| |Varning |Tillståndet är mellan två tröskelvärden för definierade hälsostatus, där en anger Varningsläge och den andra anger kritiskt tillstånd (tre hälsotillstånd tillstånd tröskelvärden kan konfigureras) eller när ett icke-kritiska problem kan orsaka problem om olöst. Med en Övervakare för insamling av överordnade, om en eller flera underordnade är i tillståndet varning visas överordnad ett varningstillstånd. Om en underordnad är i kritiskt tillstånd och en annan underordnad med tillståndet varning, visar den överordnade samlade hälsotillståndet som kritiskt.|
| |Okänt |Tillståndet kan inte beräknas av flera skäl. Följande avsnitt innehåller ytterligare information och möjliga lösningar. |

Ett okänt hälsotillstånd kan orsakas av följande problem:

- Agenten har konfigurerats och inte längre rapporter till arbetsytan anges när Azure Monitor för virtuella datorer har aktiverats. Konfigurera agent att rapportera till arbetsytan finns i [att lägga till eller ta bort en arbetsyta](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Den virtuella datorn har tagits bort.
- Arbetsytan som är associerade med Azure Monitor för virtuella datorer har tagits bort. Du kan återställa arbetsytan om du har Premier support-förmåner. Gå till [Premier](https://premier.microsoft.com/) och öppna en supportbegäran.
- Lösningsberoenden togs bort. Om du vill återaktivera ServiceMap och InfrastructureInsights-lösningar i Log Analytics-arbetsytan, installerar du om dessa lösningar med hjälp av den [Azure Resource Manager-mall](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Du kan också använda alternativet konfigurera arbetsytan finns i Kom igång-fliken.
- Den virtuella datorn stängdes av.
- Virtuella Azure-tjänsten är inte tillgänglig eller underhåll utförs.
- Arbetsytan [dagliga data eller kvarhållning gränsen](../platform/manage-cost-storage.md) uppfylldes.

Välj **visa hälsotillstånd diagnostik** att öppna en sida som visar alla komponenter i en virtuell dator som är associerade health-villkor, tillståndsändringar och andra problem som identifieras genom att övervaka komponenter som hör till den virtuella datorn.

Mer information finns i [hälsotillstånd diagnostik](#health-diagnostics).

I den **hälsotillstånd** avsnittet, visas en tabell hälsoinsamling av prestanda-komponenter som övervakas av health-villkor. Dessa komponenter omfattar **CPU**, **minne**, **Disk**, och **nätverk**. Om du väljer en komponent öppnas en sida som innehåller alla övervakning villkoret och hälsotillståndet för den komponenten.

När du har åtkomst till hälsotillstånd från en Azure virtuell dator som kör Windows, visas hälsotillståndet för de fem viktigaste Windows-tjänsterna **Core services health**. Om du väljer någon av tjänsterna öppnas en sida som visar en lista över health-villkor övervakning för respektive komponent tillsammans med dess hälsotillstånd.

Om du väljer namnet på health-villkor öppnas egenskapsrutan. I det här fönstret kan granska du konfigurationsinformationen, inklusive om health-villkor har en motsvarande Azure Monitor-avisering.

Mer information finns i [hälsotillstånd diagnostik- och arbeta med Hälsokriterier](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Sammanställd VM-perspektiv

Om du vill visa samlingen hälsotillståndet för dina virtuella datorer i en resursgrupp, Välj **Azure Monitor** från navigeringslistan i portalen och välj sedan **virtuella datorer (förhandsversion)** .

![VM-Insights övervakningsvyn från Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

I den **prenumeration** och **resursgrupp** listrutor, Välj lämplig resursgruppen som innehåller de virtuella datorerna relaterade till gruppen för att visa deras rapporterade hälsotillstånd. Ditt val som endast gäller för funktionen hälsotillstånd och inte sprids till **prestanda** eller **kartan** flikar.

Den **hälsotillstånd** fliken innehåller följande information:

* Hur många virtuella datorer är i ett kritiskt eller dåligt tillstånd, jämfört med hur många är felfria eller skickar inte data (kallas ett okänt tillstånd).
* Vilka och hur många virtuella datorer efter operativsystem rapporterar ett feltillstånd.
* Hur många virtuella datorer är skadad på grund av ett problem har identifierats med en processor, disk, minne eller nätverkskort, kategoriserade efter hälsotillstånd.
* Hur många virtuella datorer är skadad på grund av ett problem har identifierats med en core OS-tjänst efter hälsotillstånd.

På den **hälsotillstånd** fliken kan du identifiera viktiga problem som upptäckts hälsotillstånd kriterierna för övervakning av den virtuella datorn och granska varningsinformationen och tillhörande kunskapsartiklar. De här artiklarna kan bidra till diagnos och reparation av problem. Välj någon av allvarlighetsgrader att öppna den [alla aviseringar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sidan filtreras efter den allvarlighetsgraden.

Den **VM-distribution av operativsystem** i listan visas virtuella datorer som anges av version av Windows eller Linux-distribution, tillsammans med deras version. I varje kategori OS, de virtuella datorerna är uppdelade ytterligare baserat på hälsotillståndet för den virtuella datorn.

![VM-Insights VM distribution perspektiv](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Välj valfri kolumn, inklusive **antal virtuella datorer**, **kritisk**, **varning**, **felfri**, eller **okänd**. Visa listan över filtrerade resultat i den **virtuella datorer** sidan som matchar den markerade kolumnen.

Till exempel för att granska alla virtuella datorer som kör Red Hat Enterprise Linux-version 7.5, välja den **antal virtuella datorer** värdet för det OS och visar en lista över de virtuella datorerna som matchar filtret och deras aktuella hälsotillstånd.

![Exempelsammanslagning för Red Hat Linux-datorer](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

I den **virtuella datorer** om du väljer namnet på en virtuell dator under kolumnen **namn på virtuell dator**, du är skickas till den **VM-instans** sidan. Den här sidan innehåller mer information om aviseringar och hälsoproblem för kriterier som påverkar den valda virtuella datorn. Filtrera tillståndsinformation hälsa genom att välja **hälsotillstånd** ikonen i det övre vänstra hörnet på sidan för att se vilka komponenter som är felaktiga. Du kan också visa VM-hälsa aviseringar som genererats av en defekt komponent efter allvarlighetsgrad.

Från den **VM-lista** väljer du namnet på en virtuell dator för att öppna den **hälsotillstånd** sidan för den virtuella datorn på samma sätt som om du har valt **Insights (förhandsversion)** från den virtuella datorn direkt.

![VM-insikter från en valda Azure-dator](./media/vminsights-health/vminsights-directvm-health.png)

Den **Insights (förhandsversion)** visar en insamling av hälsostatus för den virtuella datorn och aviseringar. Den här hälsostatus kategoriseras efter allvarlighetsgrad som representerar VM hälsovarningar aktiveras när hälsotillståndet ändras från felfri till feltillstånd, baserat på kriterier. Att välja **virtuella datorer i kritiska tillståndet** öppnas en sida med en lista över en eller flera virtuella datorer i ett kritiskt hälsotillstånd.

Att välja hälsostatus för ett av programmen för virtuella datorer i **hälsotillstånd diagnostik** vy av den virtuella datorn. I den här vyn kan bestämma du vilka health-villkor återger ett hälsotillstånd problem. När den **hälsotillstånd diagnostik** öppnas, den visar alla VM-komponenter och deras tillhörande hälsostatus kriterier, och det aktuella hälsotillståndet.

Mer information finns i [hälsotillstånd diagnostik](#health-diagnostics).

Att välja **visa alla Hälsokriterier** öppnas en sida som visar en lista över alla health-villkor som medföljer den här funktionen. Informationen kan filtreras ytterligare baserat på följande alternativ:

* **typ**. Det finns tre typer av health-villkor att utvärdera villkor och samlar in det övergripande hälsotillståndet för en övervakad virtuell dator:
    - **Enhet**. Mäter en viss aspekt av en virtuell dator. Denna Kriterietyp för hälsotillstånd kan kontrollerar en prestandaräknare för att avgöra prestanda för komponenten, köra ett skript för att utföra en syntetisk transaktion eller tittar på för en händelse som påvisar ett fel. Filtret anges till enheten som standard.
    - **Beroende**. Ger en hälsosammanslagning mellan olika enheter. Den här health-villkor kan hälsotillståndet för en entitet förlita sig på hälsotillståndet för en annan typ av enhet som den förlitar sig på för åtgärden lyckas.
    - **Sammanställd**. Ger ett kombinerat hälsotillstånd för liknande health-villkor. Enhets- och beroendeövervakare hälsotillstånd kriterium konfigureras normalt under ett kriterium sammanställd hälsotillstånd. Förutom att tillhandahålla bättre överblick av många olika Hälsokriterier riktad mot en entitet, ger sammanställda hälsotillstånd kriterium ett unikt hälsotillstånd för olika kategorier av entiteterna.

* **Kategori**. Typ av health-villkor som används för att gruppera liknande kriterier för rapportering. Dessa kategorier är **tillgänglighet** och **prestanda**.

Om du vill se vilka instanser är skadade, väljer du ett värde under den **komponenter som innehåller fel** kolumn. I den här sidan kan med en tabell de komponenter som är i ett kritiskt hälsotillstånd.

## <a name="health-diagnostics"></a>Diagnostik för hälsa

Den **hälsotillstånd diagnostik** sidan kan du visualisera hälsomodell för en virtuell dator. Den här sidan listar alla VM-komponenter, tillhörande hälsostatus kriterier, tillståndsändringar och andra problem som identifieras av övervakade komponenter som hör till den virtuella datorn.

![Exempel på sidan hälsa diagnostik för en virtuell dator](./media/vminsights-health/health-diagnostics-page-01.png)

Starta diagnostik för hälsa med hjälp av följande metoder:

* Efter insamling av hälsotillstånd för alla virtuella datorer ur sammanställd VM i Azure Monitor:

    1. På den **hälsotillstånd** väljer du ikonen för **kritisk**, **varning**, **felfri**, eller **okänd** hälsotillstånd under avsnittet **gäst VM-hälsa**.
    2. Gå till sidan som visar alla datorer som matchar den filtrera kategorin.
    3. Välj värde i den **hälsotillstånd** kolumnen för att öppna health diagnostiken för den virtuella datorn.

* Av Operativsystemet ur sammanställd VM i Azure Monitor. Under **VM-distribution**, att välja någon av kolumnvärdena öppnas den **virtuella datorer** sidan och returnerar en lista i tabellen som matchar den filtrerade kategorin. Att välja värdet under **hälsotillstånd** kolumn öppnas diagnostik för hälsa för den valda virtuella datorn.
 
* Från den Virtuella Azure-Övervakaren för virtuella datorer gästdatorn **hälsotillstånd** fliken genom att välja **visa hälsotillstånd diagnostik**.

Diagnostik för hälsa ordnar hälsoinformation i två kategorier: tillgänglighet och prestanda.
 
Alla health-villkor som definierats för en komponent, till exempel logisk disk-, processor- och så vidare kan visas utan filtrering på två typer. Vyerna kan finnas i en vy för alla upp av kriterier eller via filtrering av resultaten efter antingen kategori när du väljer **tillgänglighet** eller **prestanda**.

Dessutom kategorin kriterier kan ses bredvid den **Hälsokriterier** kolumn. Om villkoren inte matchar den valda kategorin ett meddelande om **inga health-villkor som är tillgängliga för den valda kategorin** visas i den **Hälsokriterier** kolumn.

Tillståndet för en health-villkor definieras av en av fyra typer: **Kritiska**, **varning**, **felfri**, och **okänd**. De första tre kan konfigureras, vilket innebär att du kan ändra tröskelvärdena för Övervakare direkt i den **Hälsokriterier** konfigurationsruta. Detta är också möjligt med hjälp av REST-API i Azure Monitor [uppdateringsåtgärden övervakaren](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Okänd** inte kan konfigureras och är reserverat för specifika scenarier.

Den **hälsotillstånd diagnostik** sidan har tre huvudområden:

* Komponentmodellen
* Hälsovillkor
* Tillståndsändringar

![Delar av sidan hälsa diagnostik](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Komponentmodellen

Kolumnen längst till vänster i den **hälsotillstånd diagnostik** är **Komponentmodellen**. Alla komponenter som är associerade med den virtuella datorn, visas i den här kolumnen tillsammans med deras aktuella hälsotillstånd.

I följande exempel visas de identifierade komponenterna är **Disk**, **logisk Disk**, **Processor**, **minne**, och  **Operativsystemet**. Flera instanser av dessa komponenter identifieras och visas i den här kolumnen.

Följande bild visar till exempel att den virtuella datorn har två instanser av logiska diskar **C:** och **D:** , som är i felfritt tillstånd:

![Komponenten exempelmodellen visas i hälsotillstånd diagnostik](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Health-villkor

Den mellersta kolumnen på sidan hälsa diagnostik är **Hälsokriterier**. Hälsomodell som definierats för den virtuella datorn visas i en hierarkisk trädstruktur. Hälsomodell för en virtuell dator består av enhet och sammanställda health-villkor.

![Exempel health-villkor visas i hälsotillstånd diagnostik](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Ett villkor för health mäter hälsotillståndet för en övervakad instansen, som kan vara ett tröskelvärde läget för en entitet och så vidare. Ett villkor för hälsotillstånd har antingen två eller tre konfigurerbara hälsotillstånd tillstånd tröskelvärden, enligt beskrivningen ovan. Hälsotillstånd villkoret kan vara i endast en av sina möjliga tillstånd när som helst.

Hälsomodell definierar kriterier som avgör hälsotillståndet för den övergripande mål och komponenter i målet. Hierarkin för villkor visas i den **Hälsokriterier** avsnittet på den **hälsotillstånd diagnostik** sidan.

Princip för insamling av hälsotillstånd är en del av konfigurationen av sammanställda health-villkor (standard är inställd på **sämsta av**). Du hittar en standarduppsättning health-villkor som körs som en del av den här funktionen i den [övervakning konfigurationsinformation](#monitoring-configuration-details) i den här artikeln.

Du kan också använda REST-API i Azure Monitor [övervaka instanser lista efter resurs](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) att hämta en lista över alla health-villkor. Det här villkoret med konfigurationsinformation som körs mot den Virtuella Azure-resursen.

Den **enhet** hälsotillstånd Kriterietyp kan ha dess konfiguration ändras genom att välja ellipsen länken till höger. Välj **Visa detaljer** att öppna fönstret konfiguration.

![Konfigurera ett hälsotillstånd kriterier-exempel](./media/vminsights-health/health-diagnostics-vm-example-02.png)

I fönstret konfiguration för valda health-villkor, om du använder exemplet **genomsnittliga Disk sekunder Per skriva**, tröskelvärdet kan konfigureras med ett annat numeriskt värde. Det är en Övervakare med två tillstånd, vilket innebär att den kan ändras endast från **felfri** till **varning**.

Andra health-villkor använder ibland tre lägen, där du kan konfigurera värdet för varningar och kritiska tröskelvärden för hälsotillstånd för. Du kan också ändra ett tröskelvärde med hjälp av Azure Monitor REST API [övervakarkonfiguration](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Tillämpa hälsotillstånd kriterier konfigurationsändringar på en instans tillämpar dem på alla övervakade instanser. Exempel: Om du väljer **Disk-1 D:** och ändrar sedan den **genomsnittliga Disk sekunder Per skriva** tröskelvärdet ändringen gäller för alla instanser identifieras och övervakas på den virtuella datorn.


![Konfigurera en health-villkor för till exempel övervaka en enhet](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Om du vill veta mer om health-villkor finns artiklar som hjälper dig identifiera problem, orsaker och lösningar. Välj **visa information** på sidan om du vill se relaterade kunskapsartikeln.

Alla kunskapsartiklar som ingår i Azure Monitor för hälsotillståndet för virtuella datorer finns i [dokumentation för Azure Monitor hälsotillstånd knowledge](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Tillståndsändringar

Kolumnen längst till höger i den **hälsotillstånd diagnostik** är **tillståndsändringar**. Den här kolumnen visas de tillståndsändringar som som är associerade med health villkoren i den **Hälsokriterier** avsnitt eller tillståndsändring för den virtuella datorn om du valde en virtuell dator från den **Komponentmodellen** eller  **Hälsokriterier** kolumnen i tabellen.

![Exempel tillståndsändringar som presenteras i hälsotillstånd diagnostik](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Följande avsnitt visar tillståndet health-villkor och den associerade tid. Den här informationen visar det senaste tillståndet överst i kolumnen.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associationen mellan Komponentmodellen Health-villkor och tillståndsändringar kolumner

De tre kolumnerna är sammankopplade med varandra. När du väljer en instans i den **Komponentmodellen** kolumnen den **Hälsokriterier** kolumn filtreras till denna Komponentvy. På motsvarande sätt den **tillståndsändringar** kolumn uppdateras baserat på valda health-villkor.

![Exempel på att välja övervakade instans och resultat](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Exempel: Om du väljer *Disk – 1 D:* i listan under **Komponentmodellen**, **Hälsokriterier** filter på *Disk - 1 D:* , och  **Tillstånd ändringar** visar tillståndsändringen baserat på tillgängligheten för *Disk – 1 D:* .

Om du vill se det uppdaterade hälsotillståndet, kan du uppdatera sidan diagnostik för hälsa genom att välja den **uppdatera** länk. Om det finns en uppdatering till villkoret hälsotillstånd hälsotillstånd baserat på fördefinierade avsökningsintervallet, kan du undvika att vänta på den här uppgiften och återspeglar det senaste hälsotillståndet. Den **kriterier hälsotillstånd** är ett filter som låter dig begränsa resultatet baserat på det valda hälsotillståndet: Felfri, varning, kritiskt, Okänd, och alla. Den **senaste uppdaterade** tid i det övre högra hörnet representerar den senaste gången sidan diagnostik för hälsotillstånd har uppdaterats.

## <a name="alerts"></a>Aviseringar

Azure Monitor för virtuella datorer health kan integreras med [Azure Alerts](../../azure-monitor/platform/alerts-overview.md). Den genererar en avisering när fördefinierade villkor, när upptäckts kan ändras från felfri till feltillstånd. Aviseringar kategoriserade efter allvarlighetsgrad, från allvarlighetsgrad 0 till verksamheten 4 med allvarlighetsgrad 0 som den högsta nivån.

Aviseringar är inte associerad med en åtgärdsgrupp att meddela dig när aviseringen har aktiverats. Prenumerationens ägare måste konfigurera meddelanden genom att följa stegen i den [konfigurera aviseringar](#configure-alerts) avsnittet.

Det totala antalet VM-hälsa aviseringar kategoriserade efter allvarlighetsgrad är tillgänglig på den **hälsotillstånd** instrumentpanelen under den **aviseringar** avsnittet. När du väljer antingen det totala antalet aviseringar eller talet som motsvarar en allvarlighetsgrad den **aviseringar** sidan öppnas och visar en lista över alla aviseringar som matchar ditt val.

Exempel: Om du väljer den raden motsvarar **allvarlighetsgrad nivå 1**, visas följande vy:

![Exempel på alla allvarlighetsgrad 1-aviseringar](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Den **alla aviseringar** sidan är inte avsett för att visa endast aviseringar som matchar ditt val. Den också filtreras efter **resurstyp** att visa endast hälsotillstånd aviseringar som genererats av en VM-resurs. Det här formatet visas i listan aviseringar under kolumnen **målresursen**, där det visar sig Azure VM upphöjt aviseringen när ett feltillstånd uppfylldes.

Aviseringar från andra typer av resurser eller tjänster är inte avsedda som ska ingå i den här vyn. Dessa aviseringar innehåller loggvarningar som baseras på loggfrågor eller måttaviseringar som du normalt skulle visa från standard Azure Monitor [alla aviseringar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sidan.

Du kan filtrera den här vyn genom att välja värden i listmenyerna överst på sidan.

|Kolumn |Beskrivning |
|-------|------------|
|Prenumeration |Välj en Azure-prenumeration. Bara aviseringar i den valda prenumerationen ingår i vyn. |
|Resursgrupp |Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen ingår i vyn. |
|Resurstyp |Välj en eller flera resurstyper. Som standard bara de aviseringar som mål **virtuella datorer** har valts och ingår i den här vyn. Den här kolumnen är endast tillgänglig när en resursgrupp har angetts. |
|Resurs |Välj en resurs. Endast aviseringar med den här resursen som ett mål som ingår i vyn. Den här kolumnen visas bara när en resurstyp har angetts. |
|Severity |Välj en allvarlighetsgrad för avisering eller **alla** att inkludera aviseringar för alla allvarlighetsgrader. |
|Övervakningsvillkor |Välj ett övervakningsvillkor filter aviseringar om de har utlöst eller löst av systemet om villkoret är inte längre aktiv. Eller välj **alla** att inkludera aviseringar av alla villkor. |
|Aviseringstillstånd |Välj en aviseringstillståndet **New**, **Bekräfta**, **stängd**, eller **alla** att inkludera aviseringar av alla tillstånd. |
|Övervaka tjänsten |Välj en tjänst eller **alla** att inkludera alla tjänster. Bara aviseringar från VM-Insights har stöd för den här funktionen.|
|Tidsintervall| Bara de aviseringar som utlösts inom det valda tidsfönstret ingår i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

När du väljer en avisering i **Avisera detalj** visas. Den här sidan innehåller information om aviseringen och kan du ändra dess tillstånd.

Mer information om hur du hanterar aviseringar finns [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Skapa nya aviseringar baserat på kriterier för hälsotillstånd eller ändra befintliga hälsotillstånd aviseringsregler i Azure Monitor från portalen stöds inte för närvarande.


![Information om fönstret om markerad avisering](./media/vminsights-health/alert-details-pane-01.png)

Du kan ändra en aviseringstillståndet för en eller flera aviseringar genom att markera dem och sedan välja **ändra tillståndet** från den **alla aviseringar** sida i det övre vänstra hörnet. Väljer du något av tillstånden på den **ändra aviseringtillståndet** fönstret Lägg till en beskrivning av ändringen i den **kommentar** och välj sedan **Ok** att genomföra ändringarna. När informationen har verifierats och ändringarna tillämpas kan följa förloppet under **meddelanden** på menyn.

### <a name="configure-alerts"></a>Konfigurera varningar
Du kan inte hantera vissa avisering hanteringsuppgifter från Azure-portalen. Dessa uppgifter måste utföras med hjälp av den [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Närmare bestämt:

- Aktivera eller inaktivera en avisering för health-villkor
- Ställa in meddelanden för kriterier för hälsovarningar

Varje exempel används [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. Om du inte är bekant med den här metoden kan du läsa [med ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Aktivera eller inaktivera en varningsregel

Aktivera eller inaktivera en avisering för specifika Hälsokriterier, egenskapen **alertGeneration** måste ändras med antingen värdet **inaktiverad** eller **aktiverad**.

Att identifiera den *monitorId* för specifika Hälsokriterier i följande exempel visas hur du frågar efter värdet för villkoret **LogicalDisk\Avg Disk sekunder Per överföra**:

1. Skriv i ett terminalfönster **armclient.exe inloggning**. Då uppmanas du att logga in på Azure.

2. Ange följande kommando för att hämta alla health-villkoret som aktiv på en specifik virtuell dator och identifiera värdet för *monitorId* egenskapen:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    I följande exempel visar utdata från den *armclient GET* kommando. Anteckna värdet för *MonitorId*. Det här värdet krävs för nästa steg, där vi måste ange ID för health-villkor och ändra dess egenskap för att skapa en avisering.

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

3. Ange följande kommando för att ändra den *alertGeneration* egenskapen:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Ange GET-kommando som används i steg 2 för att kontrollera att egenskapens värde har angetts till **inaktiverad**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Koppla en åtgärdsgrupp health-villkor

Azure Monitor för virtuella datorer health stöder SMS eller e-postmeddelanden när aviseringar genereras från feltillstånd health-villkor. Notera namnet på den konfigurerade åtgärdsgruppen att skicka SMS eller e-post-meddelanden för att konfigurera meddelanden.

>[!NOTE]
>Den här åtgärden måste utföras mot varje övervakade virtuell dator som du vill få ett meddelande för. Det gäller inte för alla virtuella datorer i en resursgrupp.

1. Ange i ett terminalfönster *armclient.exe inloggning*. Då uppmanas du att logga in på Azure.

2. Ange följande kommando för att koppla en åtgärdsgrupp Varningsregler:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Kontrollera att värdet för egenskapen **actionGroupResourceIds** har uppdaterats, anger du följande kommando:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Utdata bör se ut som följande kriterier:
    
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

- För att identifiera begränsningar och prestandan för virtuell dator, se [visa Azure VM prestanda](vminsights-performance.md).
- Mer information om identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
