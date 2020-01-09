---
title: Förstå hälso tillståndet för dina virtuella Azure-datorer | Microsoft Docs
description: Den här artikeln beskriver hur du förstår hälsan för virtuella datorer och underliggande operativ system med Azure Monitor for VMs.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: e01223783130ea6b276db26bab709e2b51a8f76d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399792"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Förstå hälso tillståndet för dina virtuella Azure-datorer

Azure innehåller tjänster för vissa roller eller uppgifter i övervaknings utrymmet, men det ger inte djupgående hälso perspektiv för operativ system (OSs) som finns på Azure Virtual Machines (VM). Även om du kan använda Azure Monitor för olika villkor är det inte utformat för att modellera och representera hälsan hos kärn komponenter eller den övergripande hälsan för virtuella datorer.

Genom att använda Azure Monitor for VMs hälsa kan du aktivt övervaka tillgänglighet och prestanda för ett Windows-eller Linux gäst operativ system. Hälso funktionen använder en modell som representerar viktiga komponenter och deras relationer, innehåller villkor som anger hur komponent hälsan ska mätas och skickar en avisering när ett fel upptäcks.

Att visa det övergripande hälso tillståndet för en virtuell Azure-dator och det underliggande operativ systemet kan observeras från två perspektiv: direkt från en virtuell dator eller över alla virtuella datorer i en resurs grupp från Azure Monitor.

Den här artikeln visar hur du snabbt kan utvärdera, undersöka och lösa hälso problem när de upptäcks av Azure Monitor for VMs Health-funktionen.

Information om hur du konfigurerar Azure Monitor for VMs finns i [aktivera Azure Monitor for VMS](vminsights-enable-overview.md).

>[!NOTE]
>Vi har nyligen [lanserat ändringar](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) som vi gör i hälso funktionen baserat på feedback vi har fått från våra kunder med offentlig för hands version. Med tanke på antalet ändringar vi kommer att göra, kommer vi att sluta erbjuda hälso funktionen för nya kunder. Befintliga kunder kan fortsätta att använda hälso funktionen. Mer information finns i [vanliga frågor och svar om allmän tillgänglighet](vminsights-ga-release-faq.md). 

## <a name="monitoring-configuration-details"></a>Konfigurations information för övervakning

Det här avsnittet beskriver standard hälso villkoren för att övervaka virtuella datorer i Azure Windows och Linux. Alla hälso kriterier är förkonfigurerade för att skicka en avisering när de identifierar ett ohälsosamt tillstånd.

| Övervakarens namn | Frekvens (min) | Lookback varaktighet (min) | Operator | Tröskelvärde | Avisering om tillstånd | Allvarsgrad | Arbets belastnings kategori | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Logisk disk online | 5 | 15 | <> | 1 (sant) | Kritiskt | Sev1 | Linux | 
| Ledigt utrymme för logisk disk | 5 | 15 | < | 200 MB (varning)<br> 100 MB (kritiskt) | Varning | Sev1<br> Allv.grad2 | Linux | 
| Lediga I-noder i procent för logisk disk | 5 | 15 | < | 5 % | Kritiskt | Sev1 | Linux | 
| Ledigt utrymme i procent för logisk disk | 5 | 15 | < | 5 % | Kritiskt | Sev1 | Linux | 
| Status för nätverkskort | 5 | 15 | <> | 1 (sant) | Varning | Allv.grad2 | Linux | 
| Tillgängligt minne i megabyte för operativsystem | 5 | 10 | < | 2,5 MB | Kritiskt | Sev1 | Linux | 
| Disk medel s/disk läsning | 5 | 25 | > | 0,05 s | Kritiskt | Sev1 | Linux | 
| Disk medel s/disk överföring | 5 | 25 | > | 0,05 s | Kritiskt | Sev1 | Linux | 
| Disk medel s/disk skrivning | 5 | 25 | > | 0,05 s | Kritiskt | Sev1 | Linux | 
| Disk status | 5 | 25 | <> | 1 (sant) | Kritiskt | Sev1 | Linux | 
| Total processortid i procent för operativsystem | 5 | 10 | >= | 95% | Kritiskt | Sev1 | Linux | 
| Total processor användning i procent | 5 | 10 | >= | 95% | Kritiskt | Sev1 | Windows | 
| Fel eller fel i fil systemet | 60 | 60 | <> | 4 | Kritiskt | Sev1 | Windows | 
| Genomsnittligt antal sekunder per läsning för logiska diskar | 1 | 15 | > | 0.04 s | Varning | Allv.grad2 | Windows | 
| Genomsnittligt antal sekunder per överföring för logiska diskar | 1 | 15 | > | 0.04 s | Varning | Allv.grad2 | Windows | 
| Genomsnittligt antal sekunder per skrivning (logisk disk) för logiska diskar | 1 | 15 | > | 0.04 s | Varning | Allv.grad2 | Windows | 
| Aktuell diskkölängd (logisk disk) | 5 | 60 | >= | 32 | Varning | Allv.grad2 | Windows | 
| Ledigt utrymme på logisk disk (MB) | 15 | 60 | > | varning om 500 MB<br> 300 MB kritiskt | Kritiskt | Sev1<br> Allv.grad2 | Windows | 
| Ledigt utrymme på logisk disk (%) | 15 | 60 | > | 10% varning<br> 5% kritiskt | Kritiskt | Sev1<br> Allv.grad2 | Windows |
| Ledig tid i procent för logisk disk | 15 | 360 | <= | 20 % | Varning | Allv.grad2 | Windows | 
| Procent läst Använd bandbredd | 5 | 60 | >= | 60 % | Varning | Allv.grad2 | Windows | 
| Total mängd bandbredd som används | 5 | 60 | >= | 75 % | Varning | Allv.grad2 | Windows | 
| Procent Använd bandbredds skrivning | 5 | 60 | >= | 60 % | Varning | Allv.grad2 | Windows | 
| DHCP-klient Service Health | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| DNS-klient Service Health | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| Windows händelse logg Service Health | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| Service Health för Windows-brandväggen | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| RPC-Service Health | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| Server Service Health | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| Windows Remote Management-Service Health | 5 | 12 | <> | 4 (körs) | Kritiskt | Sev1 | Windows | 
| Ledigt minne i MB | 5 | 10 | < | 100 MB | Kritiskt | Sev1 | Windows | 
| Kostnads fria system sid tabell poster | 5 | 10 | <= | 5000 | Kritiskt | Sev1 | Windows | 
| Minnes sidor per sekund | 5 | 10 | >= | 5000/s | Varning | Sev1 | Windows | 
| Procent andelen allokerat minne som används | 5 | 10 | > | 80 % | Kritiskt | Sev1 | Windows | 
| Genomsnittligt antal sekunder per överföring | 1 | 15 | > | 0.04 s | Varning | Allv.grad2 | Windows | 
| Genomsnittligt antal sekunder per skrivning | 1 | 15 | > | 0.04 s | Varning | Allv.grad2 | Windows | 
| Aktuell diskkölängd | 5 | 60 | >= | 32 | Varning | Allv.grad2 | Windows | 
| Ledig tid i procent för disk | 5 | 60 | >= | 20 % | Varning | Allv.grad2 | Windows | 

>[!NOTE]
>Lookback varaktighet anger hur ofta fönstret gå tillbaka kontrollerar värdena, till exempel under de senaste fem minuterna.  

>[!NOTE]
>Frekvensen anger hur ofta mått aviseringen ska kontrol lera om villkoren är uppfyllda, till exempel en gång i minuten.  Det är den hastighet som hälso kriteriet körs, och lookback är den tid under vilken hälso kriteriet utvärderas. Hälso kriteriet kan till exempel utvärderas om villkoret **CPU-användning** är större än 95 procent med en frekvens på 5 minuter och förblir större än 95% i 15 minuter (3 av varandra följande utvärderings serier), så uppdateras tillståndet till kritisk allvarlighets grad om det inte redan var det.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in genom att gå till [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introduktion till Azure Monitor for VMs hälsa

Innan du använder funktionen för hälso tillstånd för en enskild virtuell dator eller grupp med virtuella datorer är det viktigt att förstå hur informationen presenteras och vilka visualiseringar som representerar.

### <a name="view-health-directly-from-a-vm"></a>Visa hälso tillståndet direkt från en virtuell dator

Om du vill visa hälso tillståndet för en virtuell Azure-dator väljer du **insikter (för hands version)** i den vänstra rutan i den virtuella datorn. Fliken **hälsa** är öppen som standard på sidan VM Insights och visar hälso tillståndet för den virtuella datorn.

![Översikt över Azure Monitor for VMs hälsa för en vald virtuell Azure-dator](./media/vminsights-health/vminsights-directvm-health-01.png)

I avsnittet **gäst VM-hälsa** visar tabellen hälso sammanslagningen av prestanda komponenter som övervakas av hälso kriterierna för den virtuella datorn och det totala antalet aviseringar om virtuella datorer som har Aktiver ATS av felaktiga komponenter. Dessa komponenter omfattar **processor**, **minne**, **disk**och **nätverk**. Expandera-ikonen bredvid hälsa för gäst-VM för att Visa hälso tillståndet för dess komponenter.

![Azure Monitor for VMs komponent hälso status för en vald virtuell Azure-dator](./media/vminsights-health/vminsights-directvm-health-02.png)

Om du väljer tillståndet bredvid komponenten öppnas hälso Diagnoss upplevelsen i den valda komponentens kontext. Den visar sammansättningen av tillståndet för komponenten, som beskriver vilka hälso kriterier som används för att beräkna dess hälsa. Mer information finns i [hälsodiagnostik och arbeta med hälso kriterier](#health-diagnostics). Mer information om aviseringar finns i [aviseringar](#alerts).

De hälso tillstånd som definierats för en virtuell dator beskrivs i följande tabell:

|Ikon |Hälso tillstånd |Betydelse |
|-----|-------------|---------------|
| |Felfri |Den virtuella datorn ligger inom de definierade hälso villkoren. Det här läget anger att inga problem har identifierats och att den virtuella datorn fungerar normalt. Med en överordnad sammanslagnings Övervakare samlar hälso tillståndet upp och återspeglar det bästa fallet eller det värsta fallet för det underordnade.|
| |Kritiskt |Tillståndet ligger inte inom det definierade hälso tillståndet, vilket tyder på att ett eller flera kritiska problem har identifierats. De här problemen måste åtgärdas för att återställa normala funktioner. Med en överordnad sammanslagnings Övervakare samlar hälso tillståndet upp och återspeglar det bästa fallet eller sämsta fall tillståndet för det underordnade.|
| |Varning |Tillståndet är mellan två tröskelvärden för det definierade hälso tillståndet, där en indikerar ett varnings tillstånd och det andra indikerar ett kritiskt tillstånd (tre hälso tillstånds trösklar kan konfigureras) eller när ett icke-kritiskt problem kan orsaka allvarliga problem om olösta. Om en eller flera underordnade är i ett varnings tillstånd med en överordnad sammanslagnings övervakare, visas ett varnings tillstånd i den överordnade. Om ett underordnat objekt är i kritiskt tillstånd och ett annat underordnat objekt i varnings tillstånd visas hälso tillståndet som kritisk i den överordnade sammanslagningen.|
| |Okänt |Det går inte att beräkna tillstånd av flera orsaker. Följande avsnitt innehåller ytterligare information och möjliga lösningar. |

Ett okänt hälso tillstånd kan orsakas av följande problem:

- Agenten konfigurerades om och rapporterades inte längre till arbets ytan som angavs när Azure Monitor for VMs aktiverades. Om du vill konfigurera agenten att rapportera till arbets ytan ser du [lägga till eller ta bort en arbets yta](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Den virtuella datorn har tagits bort.
- Arbets ytan som är kopplad till Azure Monitor for VMs har tagits bort. Du kan återställa arbets ytan om du har Premier Support-förmåner. Gå till [Premier](https://premier.microsoft.com/) och öppna en support förfrågan.
- Lösningens beroenden har tagits bort. Om du vill återaktivera ServiceMap-och InfrastructureInsights-lösningarna på arbets ytan Log Analytics installerar du om ServiceMap-lösningen med hjälp av [Azure Resource Manager-mallen](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution). Om du vill installera om InfastructureInsights-lösningen, e-vminsights@microsoft.com. 
- Den virtuella datorn stängdes av.
- Tjänsten Azure VM är inte tillgänglig eller underhåll utförs.
- Dagliga data för arbets ytan [eller gränsen för kvarhållning](../platform/manage-cost-storage.md) uppfylldes.

Välj **Visa hälsodiagnostik** för att öppna en sida som visar alla komponenter i en virtuell dator, associerade hälso kriterier, tillstånds ändringar och andra problem som identifieras av övervaknings komponenter som är relaterade till den virtuella datorn.

Mer information finns i [Health Diagnostics](#health-diagnostics).

I avsnittet **hälsa** visar en tabell hälso insamlingen av prestanda komponenter som övervakas av hälso kriterier. Dessa komponenter omfattar **processor**, **minne**, **disk**och **nätverk**. Om du väljer en komponent öppnas en sida som visar alla övervaknings villkor och hälso tillståndet för den komponenten.

När du får åtkomst till hälsan från en virtuell Azure-dator som kör Windows, visas hälso tillståndet för de fem översta Windows-tjänsterna under **Core Services Health**. Om du väljer någon av tjänsterna öppnas en sida med en lista med hälso tillstånds övervakning för den komponenten tillsammans med hälso tillståndet.

Om du väljer namnet på hälso kriteriet öppnas egenskaps fönstret. I det här fönstret kan du granska konfigurations informationen, inklusive om hälso villkoren har en motsvarande Azure Monitor avisering.

Mer information finns i [hälsodiagnostik och arbeta med hälso kriterier](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Sammanställd VM-perspektiv

Om du vill visa hälso samlingen för alla virtuella datorer i en resurs grupp väljer du **Azure Monitor** i navigerings listan i portalen och väljer **Virtual Machines (för hands version)** .

![Vyn VM Insights-övervakning från Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

I list rutorna **prenumeration** och **resurs grupp** väljer du lämplig resurs grupp som innehåller de virtuella datorer som är relaterade till gruppen för att visa deras rapporterade hälso tillstånd. Ditt val gäller endast för hälso tillstånds funktionen och överför inte till **prestanda** -eller **kart** flikar.

Fliken **hälsa** innehåller följande information:

* Hur många virtuella datorer som är i ett kritiskt eller ohälsosamt tillstånd, jämfört med hur många som är felfria eller inte skickar data (kallas okänt tillstånd).
* Vilka och hur många virtuella datorer av OS som rapporterar ett ohälsosamt tillstånd.
* Hur många virtuella datorer som är ohälsosama på grund av ett problem med en processor, disk, minne eller nätverkskort, kategoriserade efter hälso tillstånd.
* Hur många virtuella datorer som är ohälsosama på grund av ett problem med en kärn-OS-tjänst, kategoriserade efter hälso tillstånd.

På fliken **hälsa** kan du identifiera de kritiska problem som identifieras av hälso kriterierna som övervakar den virtuella datorn och granska aviserings information och tillhör ande kunskaps artiklar. Dessa artiklar kan hjälpa till med diagnosen och reparationen av problem. Välj någon av allvarlighets graderna för att öppna sidan [alla aviseringar](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) som filtrerats efter allvarlighets grad.

I listan **VM-distribution per operativ system** visas de virtuella datorer som anges av Windows-versionen eller Linux-distribution, tillsammans med deras version. I varje operativ system kategori delas de virtuella datorerna ned ytterligare baserat på den virtuella datorns hälso tillstånd.

![Distributions perspektiv för virtuell dator för VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Välj valfri kolumn, inklusive **antal virtuella datorer**, **kritisk**, **Varning**, **felfri**eller **okänd**. Visa listan över filtrerade resultat på **Virtual Machines** sidan som matchar den valda kolumnen.

Om du till exempel vill granska alla virtuella datorer som kör Red Hat Enterprise Linux version 7,5 väljer du värdet antal virtuella datorer för det operativ systemet och visar en lista över de virtuella **datorerna** som matchar filtret och deras aktuella hälso tillstånd.

![Exempel på sammanslagning av virtuella Red Hat Linux-datorer](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Du klickar på **Visa hälso** kryss rutan och hälso tillståndet returneras för de filtrerade resultaten i tabellen.  

![Exempel på hälso tillstånd för virtuella Red Hat Linux-datorer](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

För något av objekten i listan kan du klicka på motsvarande hälso tillstånd för att starta hälsodiagnostik, som visar hur hälso tillståndet utvärderas för den valda virtuella datorn. 

Om du väljer namnet på en virtuell dator under kolumnen **VM-namn**på sidan **Virtual Machines** dirigeras du till sidan **VM-instans** . Den här sidan innehåller mer information om de aviseringar och hälso villkors problem som påverkar den valda virtuella datorn. Filtrera hälso tillstånds informationen genom att välja **hälso tillstånds** ikonen i det övre vänstra hörnet på sidan för att se vilka komponenter som inte är felfria. Du kan också Visa hälso aviseringar för virtuella datorer som har Aktiver ATS av en komponent som inte är felfri Kategoriserad efter allvarlighets grad för avisering.

Från vyn **VM-lista** väljer du namnet på en virtuell dator för att öppna sidan **hälso tillstånd** för den virtuella datorn, på samma sätt som om du har valt **insikter (för hands version)** från den virtuella datorn direkt.

![VM Insights för en vald virtuell Azure-dator](./media/vminsights-health/vminsights-directvm-health.png)

**Virtual Machines (för hands version) på Azure Monitor** sidan visar en samlad hälso status för VM och aviseringar. Den här hälso statusen kategoriseras efter allvarlighets grad, som representerar de virtuella datorernas hälso aviseringar som aktive ras när hälso tillståndet ändras från felfritt till dåligt, baserat på kriterier. Om du väljer **virtuella datorer i kritiskt tillstånd** öppnas en sida med en lista över en eller flera virtuella datorer i ett kritiskt hälso tillstånd.

Om du väljer hälso status för en av de virtuella datorerna visas **hälso diagnosen** för den virtuella datorn. I den här vyn kan du avgöra vilka hälso kriterier som visar ett hälso tillstånds problem. När sidan **hälsodiagnostik** öppnas visas alla VM-komponenter och deras associerade hälso kriterier med det aktuella hälso tillståndet.

Mer information finns i [Health Diagnostics](#health-diagnostics).

Om du väljer **Visa alla hälso kriterier** öppnas en sida som visar en lista över alla hälso kriterier som är tillgängliga för den här funktionen. Informationen kan filtreras ytterligare utifrån följande alternativ:

* **Skriv**. Det finns tre typer av hälso kriterier för att utvärdera villkor och samla in det övergripande hälso tillståndet för en övervakad virtuell dator:
    - **Enhet**. Mäter lite aspekt av en virtuell dator. Den här hälso kriterie typen kan kontrol lera en prestanda räknare för att fastställa komponentens prestanda, köra ett skript för att utföra en syntetisk transaktion eller titta på en händelse som anger ett fel. Filtret ställs in på enhet som standard.
    - **Beroende**. Tillhandahåller en hälso sammanslagning mellan olika entiteter. Med det här hälso tillståndet kan en entitets hälso tillstånd bero på hälsan för en annan typ av enhet som den förlitar sig på för lyckad åtgärd.
    - **Aggregation**. Ger ett kombinerat hälso tillstånd med liknande hälso kriterier. Hälso villkor för enhet och beroende är vanligt vis konfigurerade under ett samlat hälso kriterium. Förutom att tillhandahålla en bättre allmän organisation av de många olika hälso kriterier som är riktade till en enhet, ger ett samlat hälso tillstånd ett unikt hälso tillstånd för olika kategorier av entiteterna.

* **Kategori**. Typ av hälso kriterier som används för att gruppera likartade kriterier för rapporterings syfte. Dessa kategorier är **tillgänglighet** och **prestanda**.

Om du vill se vilka instanser som inte är felfria väljer du ett värde under kolumnen **felaktig komponent** . På den här sidan visar en tabell de komponenter som är i ett kritiskt hälso tillstånd.

## <a name="health-diagnostics"></a>Hälso diagnos

På sidan **hälsodiagnostik** kan du visualisera hälso modellen för en virtuell dator. Den här sidan listar alla VM-komponenter, associerade hälso kriterier, tillstånds ändringar och andra viktiga problem som identifieras av övervakade komponenter som är relaterade till den virtuella datorn.

![Exempel på sidan hälso diagnos för en virtuell dator](./media/vminsights-health/health-diagnostics-page-01.png)

Starta hälso diagnosen med följande metoder:

* Efter samlat hälso tillstånd för alla virtuella datorer från den sammanställda VM-perspektivet i Azure Monitor:

    1. På sidan **hälsa** väljer du ikonen för **kritiskt**, **varnings**, **felfri**eller **Okänt** hälso tillstånd under avsnittet **gäst hälsa för virtuella datorer**.
    2. Gå till sidan med en lista över alla virtuella datorer som matchar den filtrerade kategorin.
    3. Välj värdet i kolumnen **hälso tillstånd** för att öppna hälsodiagnostiken som är begränsad till den virtuella datorn.

* Av OS från den sammanställda VM-perspektivet i Azure Monitor. Under **VM-distribution**öppnar du ett av kolumn värdena för att öppna **Virtual Machines** sidan och returnerar en lista i tabellen som matchar den filtrerade kategorin. Om du väljer värdet under **hälso tillstånds** kolumnen öppnas hälsodiagnostik för den valda virtuella datorn.
 
* Från den virtuella gäst datorn på fliken Azure Monitor for VMs **hälsa** genom att välja **Visa hälso diagnos**.

Hälso diagnosen organiserar hälso information i två kategorier: tillgänglighet och prestanda.
 
Alla hälso kriterier som har definierats för en komponent, till exempel logisk disk, CPU och så vidare, kan visas utan att filtrera på de två kategorierna. De här vyerna kan vara i en vy med alla kriterier, eller genom att filtrera resultaten efter valfri kategori när du väljer **tillgänglighet** eller **prestanda**.

Villkors kategorin kan också visas bredvid kolumnen **hälso villkor** . Om villkoret inte matchar den valda kategorin visas ett meddelande om att **inga hälso kriterier som är tillgängliga för den valda kategorin** visas i kolumnen **hälso kriterier** .

Tillståndet för ett hälso villkor definieras av en av fyra typer: **kritisk**, **Varning**, **felfri**och **okänd**. De första tre kan konfigureras, vilket innebär att du kan ändra tröskelvärdena för övervakarna direkt i konfigurations fönstret för **hälso kriterier** . Detta är också möjligt med hjälp av åtgärden Azure Monitor [övervakare](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)för REST API uppdatering. **Okänd** kan inte konfigureras och är reserverat för vissa scenarier.

Sidan **hälsodiagnostik** har tre huvud avsnitt:

* Komponent modell
* Hälsovillkor
* Tillståndsändringar

![Avsnitt på sidan hälso diagnos](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Komponent modell

Kolumnen längst till vänster på sidan **hälsodiagnostik** är **komponent modell**. Alla komponenter som är associerade med den virtuella datorn visas i den här kolumnen tillsammans med deras aktuella hälso tillstånd.

I följande exempel är de identifierade komponenterna **disk**, **logisk disk**, **processor**, **minne**och **operativ system**. Flera instanser av dessa komponenter identifieras och visas i den här kolumnen.

Följande bild visar till exempel att den virtuella datorn har två instanser av logiska diskar, **C:** och **D:** , som är i felfritt tillstånd:

![Exempel på komponent modell som presenteras i hälsodiagnostik](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Hälso kriterier

Kolumnen Center på sidan hälsodiagnostik är **hälso kriterier**. Den hälso modell som definierats för den virtuella datorn visas i ett hierarkiskt träd. Hälso modellen för en virtuell dator består av enhets-och mängd hälso kriterier.

![Exempel på hälso kriterier som presenteras i hälsodiagnostik](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Ett hälso kriterium mäter hälsan hos en övervakad instans, som kan vara ett tröskelvärde, en enhets tillstånd och så vidare. Ett hälso kriterium har antingen två eller tre konfigurerbara tröskelvärden för hälso tillstånd, enligt beskrivningen ovan. Hälso kriteriet kan när som helst finnas i endast ett av de potentiella staterna.

Hälso modellen definierar kriterier som avgör hälsan för målets övergripande mål och komponenter. Hierarkin av villkor visas i avsnittet **hälso kriterier** på sidan **hälsodiagnostik** .

Hälso insamlings principen är en del av konfigurationen av de samlade hälso kriterierna (Standardinställningen är inställd på **sämsta**). Du kan hitta en standard uppsättning hälso kriterier som körs som en del av den här funktionen i avsnittet [övervaknings konfigurations information](#monitoring-configuration-details) i den här artikeln.

Du kan också använda listan Azure Monitor REST API [övervaka instanser per resurs](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) för att hämta en lista över alla hälso kriterier. Det här villkoret innehåller konfigurations information som körs mot Azure VM-resursen.

Den här typen av **enhets** hälso kriterier kan ändra konfigurationen genom att välja ellips-länken till höger sida. Välj **Visa information** för att öppna konfigurations fönstret.

![Konfigurera ett hälso villkors exempel](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Om du använder exemplet **Genomsnittligt antal sekunder per skrivning**i konfigurations fönstret för det valda hälso villkoret, kan tröskelvärdet konfigureras med ett annat numeriskt värde. Det är en övervakare med två tillstånd, vilket innebär att den endast kan ändras från **felfria** till **varningar**.

Andra hälso kriterier använder ibland tre tillstånd, där du kan konfigurera värdet för varning och kritisk hälso tillstånds tröskel. Du kan också ändra ett tröskelvärde genom att använda Azure Monitor REST API [övervaka konfiguration](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>När du tillämpar ändringar av hälso villkors konfigurationen på en instans tillämpas de på alla övervakade instanser. Om du till exempel väljer **disk-1 D:** och sedan ändrar tröskelvärdet för **Genomsnittligt antal sekunder per skrivning** , gäller ändringen alla instanser som identifieras och övervakas på den virtuella datorn.


![Konfigurera ett hälso villkor för ett exempel på en enhets övervakare](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Om du vill veta mer om hälso kriterier har vi inkluderat kunskaps artiklar som hjälper dig att identifiera problem, orsaker och lösningar. Välj **Visa information** på sidan om du vill se den relaterade kunskaps artikeln.

Information om hur du granskar alla kunskaps artiklar som ingår i Azure Monitor for VMs Health finns i [dokumentationen om Azure Monitor Health knowledgeation](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Tillståndsändringar

Kolumnen längst till höger på sidan **hälso diagnostik** är **status ändringar**. I den här kolumnen visas alla tillstånds ändringar som är associerade med de hälso kriterier som valts i avsnittet **hälso kriterier** eller den virtuella datorns tillstånds ändring om en virtuell dator har valts i kolumnen **komponent modell** eller **hälso kriterier** i tabellen.

![Exempel på status ändringar som presenteras i hälsodiagnostik](./media/vminsights-health/health-diagnostics-page-statechanges.png)

I följande avsnitt visas hälso villkors tillståndet och den associerade tiden. Den här informationen visar det senaste läget överst i kolumnen.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Association av kolumnerna komponent modell, hälso kriterier och status ändringar

De tre kolumnerna är sammanlänkade med varandra. När du väljer en instans i kolumnen **komponent modell** filtreras kolumnen **hälso tillstånd** till den vyn komponent. På motsvarande sätt uppdateras kolumnen **status ändringar** utifrån de valda hälso kriterierna.

![Exempel på att välja övervakad instans och resultat](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Om du till exempel väljer *disk-1 D:* från listan under **komponent modell** **filter filter** till *disk-1d:* och **tillstånds ändringar** , visar tillstånds ändringen baserat på tillgängligheten för *disk-1 D:* .

Om du vill se ett uppdaterat hälso tillstånd kan du uppdatera sidan hälso diagnos genom att välja länken **Uppdatera** . Om det finns en uppdatering av hälso kriteriets hälso tillstånd baserat på det fördefinierade avsöknings intervallet kan du undvika att vänta och återspegla det senaste hälso tillståndet. **Tillståndet för hälso** tillstånd är ett filter som gör att du kan begränsa resultaten baserat på det valda hälso tillståndet: felfri, varning, kritisk, okänd och alla. **Senast uppdaterad** tid i det övre högra hörnet motsvarar den senaste gången sidan för hälsodiagnostik uppdaterades.

## <a name="alerts"></a>Aviseringar

Azure Monitor for VMs hälsa integreras med [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md). Den genererar en avisering när fördefinierade villkor, vid identifiering, ändras från ett felfritt tillstånd till ett ohälsosamt tillstånd. Aviseringar kategoriseras efter allvarlighets grad, från allvarlighets grad 0 till allvarlighets grad 4, med allvarlighets grad 0 som den högsta nivån.

Aviseringar är inte associerade med en åtgärds grupp för att meddela dig när aviseringen har utlösts. En användare med ägar rollen i prenumerations omfånget måste konfigurera meddelanden genom att följa stegen i avsnittet [Konfigurera aviseringar](#configure-alerts) .

Det totala antalet aviseringar om virtuella hälso tillstånd kategoriserade efter allvarlighets grad finns på **hälso** instrument panelen i avsnittet **aviseringar** . När du väljer antingen det totala antalet aviseringar eller numret som motsvarar allvarlighets graden öppnas sidan **aviseringar** och alla aviseringar som matchar ditt val visas.

Om du till exempel väljer raden som motsvarar allvarlighets grad- **nivå 1**visas följande vy:

![Exempel på alla aviseringar på allvarlighets grad 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Sidan **alla aviseringar** är inte begränsad till att endast visa aviseringar som matchar ditt val. Den filtreras också efter **resurs typ** för att endast Visa hälso aviseringar som Aktiver ATS av en VM-resurs. Det här formatet visas i aviserings listan under kolumnen **mål resurs**, där det visar den genererade virtuella Azure-datorn när ett dåligt hälso tillstånd uppfylldes.

Aviseringar från andra resurs typer eller tjänster är inte avsedda att tas med i den här vyn. Dessa aviseringar innehåller logg aviseringar som baseras på logg frågor eller mått aviseringar som du normalt skulle visa från standard Azure Monitor [alla aviserings](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) sidor.

Du kan filtrera den här vyn genom att välja värden i de nedrullningsbara menyerna längst upp på sidan.

|Kolumn |Beskrivning |
|-------|------------|
|Prenumeration |Välj en Azure-prenumeration. Endast aviseringar i den valda prenumerationen ingår i vyn. |
|Resursgrupp |Välj en enskild resurs grupp. Endast aviseringar med mål i den valda resurs gruppen ingår i vyn. |
|Resurstyp |Välj en eller flera resurs typer. Som standard är endast aviseringar för **virtuella mål datorer** markerade och ingår i den här vyn. Den här kolumnen är bara tillgänglig efter att en resurs grupp har angetts. |
|Resurs |Välj en resurs. Endast aviseringar med den resursen som mål ingår i vyn. Den här kolumnen är bara tillgänglig efter att en resurs typ har angetts. |
|Allvarsgrad |Välj en allvarlighets grad för aviseringar eller Välj **alla** om du vill inkludera aviseringar för alla allvarlighets grader. |
|Övervaknings villkor |Välj ett övervaknings villkor för att filtrera aviseringar om de har Aktiver ATS eller lösts av systemet om villkoret inte längre är aktivt. Eller Välj **alla** om du vill inkludera varningar om alla villkor. |
|Aviseringstillstånd |Välj ett aviserings tillstånd, **nytt**, **Bekräfta**, **stängt**eller **alla** för att inkludera aviseringar för alla tillstånd. |
|Övervaka tjänst |Välj en tjänst eller Välj **alla** om du vill inkludera alla tjänster. Endast aviseringar från VM Insights stöds för den här funktionen.|
|Tidsintervall| Endast aviseringar som har utlösts inom den valda tids perioden tas med i vyn. De värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

När du väljer en avisering visas sidan **aviserings information** . Den här sidan innehåller information om aviseringen och gör att du kan ändra dess tillstånd.

Mer information om hur du hanterar aviseringar finns i [skapa, Visa och hantera aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Det finns för närvarande inte stöd för att skapa nya aviseringar baserat på hälso kriterier eller ändra befintliga hälso aviserings regler i Azure Monitor från portalen.


![Aviserings informations fönstret för en vald avisering](./media/vminsights-health/alert-details-pane-01.png)

Du kan ändra ett aviserings tillstånd för en eller flera aviseringar genom att markera dem och sedan välja **ändra tillstånd** på sidan **alla aviseringar** i det övre vänstra hörnet. Välj ett av tillstånden i fönstret **ändra aviserings tillstånd** , Lägg till en beskrivning av ändringen i fältet **kommentar** och välj sedan **OK** för att spara ändringarna. När informationen har verifierats och ändringarna tillämpas spårar du förloppet under **meddelanden** på menyn.

### <a name="configure-alerts"></a>Konfigurera varningar
Du kan inte hantera vissa aviserings hanterings uppgifter från Azure Portal. De här uppgifterna måste utföras med hjälp av [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Närmare bestämt:

- Aktivera eller inaktivera en avisering för hälso villkor
- Konfigurera aviseringar för aviseringar om hälso villkor

I varje exempel används [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. Om du inte är bekant med den här metoden kan du läsa [använda ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Aktivera eller inaktivera en varnings regel

Om du vill aktivera eller inaktivera en avisering för vissa hälso kriterier måste egenskapen **alertGeneration** ändras med värdet **inaktiverat** eller **aktiverat**.

Följande exempel visar hur du frågar efter värdet för villkoret **LogicalDisk\Avg disk sekunder per överföring**för att identifiera *monitorId* för specifika hälso kriterier:

1. Skriv **armclient. exe-inloggning**i ett terminalfönster. Om du gör det blir du ombedd att logga in på Azure.

2. Ange följande kommando för att hämta alla hälso kriterier som är aktiva på en speciell virtuell dator och identifiera värdet för egenskapen *monitorId* :

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    I följande exempel visas utdata från kommandot *ARMCLIENT get* . Anteckna värdet för *MonitorId*. Det här värdet krävs för nästa steg, där vi måste ange ID för hälso kriterierna och ändra dess egenskap för att skapa en avisering.

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

3. Ange följande kommando för att ändra egenskapen *alertGeneration* :

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Ange kommandot GET som används i steg 2 för att kontrol lera att egenskap svärdet är **inaktiverat**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associera en åtgärds grupp med hälso villkor

Azure Monitor for VMs hälsa stöder SMS-och e-postaviseringar när aviseringar genereras från hälso kriterier som inte är hälso tillstånd. Om du vill konfigurera meddelanden noterar du namnet på den konfigurerade åtgärds gruppen för att skicka SMS-eller e-postaviseringar.

>[!NOTE]
>Den här åtgärden måste utföras mot varje övervakad virtuell dator som du vill ta emot ett meddelande för. Den gäller inte för alla virtuella datorer i en resurs grupp.

1. I terminalfönstret anger du *armclient. exe login*. Om du gör det blir du ombedd att logga in på Azure.

2. Ange följande kommando för att associera en åtgärds grupp med varnings regler:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. För att kontrol lera att värdet för egenskapen **actionGroupResourceIds** har uppdaterats, anger du följande kommando:

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

- Information om hur du identifierar begränsningar och övergripande prestanda för virtuella datorer finns i [Visa prestanda](vminsights-performance.md)för virtuella Azure-datorer.

- Information om identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).
