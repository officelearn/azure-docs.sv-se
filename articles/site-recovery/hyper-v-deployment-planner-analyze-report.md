---
title: "Azure Site Recovery-distribution Planner för Hyper-V-till-Azure | Microsoft Docs"
description: "Den här artikeln beskriver analys av en genererad rapport från Azure Site Recovery-distribution Planner för Hyper-V till Azure-scenariot."
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: 060d51406f67ad8a55cdf61506cd66f5390ebe4c
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analysera Azure Site Recovery-distribution Planner rapporten
Den här artikeln beskriver de blad i Excel-rapport som genererats av Azure Site Recovery-distribution Planner för Hyper-V till Azure-scenariot.

## <a name="on-premises-summary"></a>Lokal sammanfattning
Lokala sammanfattning kalkylbladet innehåller en översikt över profilerad Hyper-V-miljön.

![Lokal sammanfattning](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Startdatum** och **slutdatum**: start- och slutdatum för profileringsdata för skapa rapporter. Som standard är startdatumet det datum då profileringen startades och slutdatumet är det datum när profileringen avslutades. Den här informationen kan vara ”StartDate” och ”EndDate” värden om rapporten genereras med följande parametrar.

**Total number of profiling days** (Totalt antal dagar för profilering): Det totala antalet profileringsdagar mellan start- och slutdatumen som rapporten genererats för.

**Antal kompatibla virtuella datorer**: det totala antalet kompatibla virtuella datorer för vilken krävs nätverkets bandbredd, antal lagringskonton och Azure kärnor beräknas.

**Total number of disks across all compatible virtual machines (Totalt antal diskar för samtliga kompatibla virtuella datorer)**: Det totala antalet diskar för samtliga kompatibla virtuella datorer.

**Average number of disks per compatible virtual machine** (Genomsnittligt antal diskar per kompatibel virtuell dator): Det genomsnittliga antalet diskar beräknat för samtliga kompatibla virtuella datorer.

**Average disk size (GB)** (Genomsnittlig diskstorlek (GB)): Den genomsnittliga diskstorleken beräknad för samtliga kompatibla virtuella datorer.

**Önskad Återställningspunktmål (minuter)**: antingen standard återställningspunkt mål eller värdet som skickades för parametern ”DesiredRPO” vid tidpunkten för rapportgenerering att beräkna bandbredd som krävs.

**Önskad bandbredd (Mbps)**: värdet som du överfört för parametern ”bandbredd” vid tidpunkten för rapporten genereras och beräkna hastigheterna återställningspunktmål (RPO).

**Observed typical data churn per day (GB)** (Observerad normal dataomsättning per dag (GB)) är den genomsnittliga dataomsättning som observerats under alla profileringsdagar.

## <a name="recommendations"></a>Rekommendationer 
Rekommendationsbladet för Hyper-V till Azure-rapporten innehåller följande information enligt vald önskat RPO:

![Rekommendationer för Hyper-V till Azure-rapport](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profildata
![Profildata](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Profiled data period** (Profileringsdataperiod): Den period då profileringen kördes. Som standard innehåller verktyget alla profilerade data i beräkningen. Om du har använt alternativet StartDate och EndDate i rapportgenerering genererar rapporten för den specifika perioden. 

**Antalet Hyper-V-servrar i listan**: antalet Hyper-V-servrar vars VMs rapporten genereras. Välj att visa namnet på Hyper-V-servrar. Lokala lagringsbehov blad öppnas för att visa alla servrar tillsammans med deras lagringskrav. 

**Desired RPO** (Önskat återställningspunktmål): Återställningspunktmålet för din distribution. Som standard beräknas vilken nätverksbandbredd som krävs för RPO-värden på 15, 30 respektive 60 minuter. De aktuella värdena på bladet uppdateras baserat på vad du väljer. Om du har använt parametern DesiredRPOinMin när rapporten genererades visas värdet i önskad Återställningspunktmål resultatet.

### <a name="profiling-overview"></a>Profileringsöversikt
![Profileringsöversikt](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Total Profiled Virtual Machines** (Totalt antal profilerade virtuella datorer): Det totala antalet virtuella datorer som det finns profileringsdata för. Om VMListFile har namnen på virtuella datorer som inte var profileras startas, betraktas inte som i rapportgenereringen dessa virtuella datorer och är undantagna från det totala antalet för profilerad virtuella datorer.

**Compatible Virtual Machines** (Kompatibla virtuella datorer): Det antal virtuella datorer som kan skyddas i Azure med Azure Site Recovery. Det är det totala antalet kompatibla virtuella datorer för vilken krävs nätverkets bandbredd, antal lagringskonton och antal Azure kärnor beräknas. Information om varje kompatibel virtuell dator finns i avsnittet ”Kompatibla virtuella datorer”.

**Incompatible Virtual Machines** (Inkompatibla virtuella datorer): Antalet profilerade virtuella datorer som inte kan skyddas med Site Recovery. Orsaken till inkompatibiliteten beskrivs i avsnittet Inkompatibla virtuella datorer. Om VMListFile har namnen på virtuella datorer som inte var profileras startas, är dessa virtuella datorer undantagna från antalet inkompatibla virtuella datorer. Dessa virtuella datorer visas under Data not found (Inga data hittades) i slutet av avsnittet Incompatible VMs (Inkompatibla virtuella datorer).

**Desired RPO** (Önskat återställningspunktmål): Önskat mål för återställningspunkten (RPO) i minuter. Rapporten genereras för tre värden för återställningspunktmål: 15 (standard), 30 respektive 60 minuter. Rekommendationen bandbredd i rapporten ändras baserat på ditt val i den **önskad Återställningspunktmål** listrutan i övre högra hörnet på bladet. Om du genererade rapporten genom att använda parametern - DesiredRPO med ett anpassat värde anpassade värdet visar som standard i den **önskad Återställningspunktmål** listrutan.

### <a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (Nödvändig nätverksbandbredd (Mbit/s))
![Nödvändig nätverksbandbredd](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Att uppfylla Återställningspunktmål 100% av tiden**: rekommenderade bandbredd i Mbit/s kan tilldelas för att uppfylla dina önskade Återställningspunktmål 100 procent av tiden. Den här mängden bandbredd måste vara reserverad för stabil deltareplikering av samtliga kompatibla virtuella datorer om du helt ska undvika överträdelser av RPO-målet.

**Att uppfylla Återställningspunktmål 90% av tiden**: kanske på grund av bredband priser eller en annan orsak kan du inte ange den bandbredd som behövs för att uppfylla dina önskade Återställningspunktmål 100 procent av tiden. Om så är fallet kan du använda en lägre bandbreddsinställning som uppfyller dina önskade Återställningspunktmål 90 procent av tiden. I rapporten ges även en ”tänk om”-analys av hur många RPO-överträdelser du kan förvänta dig och deras varaktighet, så att du bättre ska förstå vad som kan hända om du tilldelar den här lägre bandbredden.

**Uppnås genomströmning**: genomströmning från den server som du kör kommandot GetThroughput till Azure-regionen där lagringskontot finns. Siffran genomströmning anger den uppskattade nivå som du kan få när du skyddar kompatibla virtuella datorer med hjälp av Site Recovery. Hyper-V server lagring och nätverk egenskaper måste vara samma som på den server där du kör verktyget.

För alla företagsdistributioner av Site Recovery bör du använda [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Nödvändiga lagringskonton
I följande diagram visas hur många lagringskonton (standard och premium) som behövs för att skydda alla kompatibla virtuella datorer. Om du vill veta vilket lagringskonto som ska användas för varje virtuell dator kan du läsa avsnittet ”Placering av VM-lagring”.

![Krävs för Azure storage-konton](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Nödvändigt antal Azure-kärnor
Resultatet är det totala antalet kärnor som ska konfigureras före redundansväxling eller redundanstest av alla kompatibla virtuella datorer. Om det inte finns tillräckligt många kärnor tillgängliga i prenumerationen kan inte Site Recovery skapa virtuella datorer vid redundanstestet eller redundansväxlingen.

![Nödvändigt antal Azure-kärnor](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Ytterligare krav för lokal lagring

Den totala ledigt utrymme som krävs på Hyper-V-servrar för inledande replikering som lyckades och deltareplikering för att säkerställa att alla oönskade driftstopp för dina program i produktion inte VM-replikering. Mer information om krav för varje volym är tillgänglig i [lokala lagringsbehov](#on-premises-storage-requirement). 

För att förstå varför ledigt utrymme som krävs för replikering, se den [lokala lagringsbehov](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) avsnitt.

![Lokal lagring krav och kopiera frekvens](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maximal kopieringsfrekvens
Den rekommenderade maximala kopieringsfrekvensen måste anges för replikeringen för att önskad RPO ska uppnås. Standardvärdet är 5 minuter. Du kan ange kopieringsfrekvensen till 30 sekunder för att få bättre Återställningspunktmål.

### <a name="what-if-analysis"></a>Konsekvensanalys
![Vad händer om analysis](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) analysen beskrivs hur många överträdelser kan uppstå under den profilering tidsperiod när du ställer in en mindre bandbredd för önskad Återställningspunktmålet vara uppfyllda 90 procent av tiden. En eller flera överträdelser av återställningspunktmålen kan inträffa på en viss dag. Grafen visar toppåterställningspunktmålet för dagen. Utifrån den här analysen kan du avgöra om du kan godta antalet RPO-överträdelser och dagens största RPO-överträdelse sett till den lägre bandbredden. Om du accepterar, kan du allokera låg bandbredd för replikering. Om det är acceptabelt att allokera högre bandbredd enligt förslaget att uppfylla önskade Återställningspunktmålet 100 procent av tiden. 

### <a name="recommendation-for-successful-initial-replication"></a>Rekommendation för lyckad initial replikering
Det här avsnittet beskrivs hur många batchar där de virtuella datorerna ska skyddas och den minsta bandbredden som krävs för att slutföra den inledande replikeringen (IR). 

![IR-batchbearbetning](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

Virtuella datorer måste skyddas i ordningen de angivna batch. Varje grupp har en specifik lista med virtuella datorer. Batch 1 virtuella datorer måste skyddas innan Batch 2 virtuella datorer. Batch 2 virtuella datorer måste skyddas innan Batch 3 virtuella datorer och så vidare. Du kan aktivera replikering för Batch 2 virtuella datorer efter första replikeringen av de virtuella datorerna 1 Batch har slutförts. På liknande sätt när inledande replikering av Batch 2 virtuella datorer har slutförts kan kan du aktivera replikering för Batch 3 virtuella datorer, och så vidare. 

Om batch-ordning inte följs kanske tillräckligt med bandbredd för inledande replikering inte tillgängliga för de virtuella datorerna som är skyddade senare. Resultatet är att antingen virtuella datorer aldrig Slutför den inledande replikeringen eller några skyddade virtuella datorerna kan försättas i omsynkronisering läget. IR-batchbearbetning för det valda RPO-arket innehåller detaljerad information om vilka virtuella datorer som ska ingå i varje batch.

Diagrammet här visar bandbreddsfördelningen för inledande replikering och deltareplikering i batchar i den angivna batchordningen. När du skyddar första batch för virtuella datorer är full bandbredd tillgänglig för inledande replikering. När den inledande replikeringen är klar för den första batchen, krävs en del av bandbredden för deltareplikering. Den återstående bandbredden är tillgänglig för inledande replikering av andra batch för virtuella datorer. 

Fältet för batch 2 visar den bandbredd som krävs för deltareplikering för virtuella datorer i batch 1 och den tillgängliga bandbredden för inledande replikering av virtuella datorer i batch 2. På liknande sätt visar Batch 3-fältet den bandbredd som krävs för deltareplikering för föregående batchar (Batch 1 och Batch 2 virtuella datorer) och den tillgängliga bandbredden för inledande replikering för Batch 3 och så vidare. När inledande replikering av alla batchar är klar, visas det senaste fältet bandbredd som krävs för deltareplikering för alla skyddade virtuella datorer.

**Varför behöver jag inledande batchbearbetning av replikering?**
Tidsåtgången för den inledande replikeringen med avseende på den virtuella datorns diskstorlek, förbrukat diskutrymme och tillgängligt dataflöde i nätverket. Informationen finns i IR-batchbearbetning för ett valt RPO-ark.

### <a name="cost-estimation"></a>Kostnadsuppskattning
Diagrammet visar sammanfattning av kostnaden för beräknade totala disaster recovery (DR) till Azure för din valda målregionen och valutor som du angav för rapportgenerering av.

![Sammanfattning av kostnadsuppskattning](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Sammanfattningen hjälper dig att förstå kostnaden som behövs för att betala för lagring, beräkning, nätverk och licensiering när du skyddar alla dina kompatibla virtuella datorer till Azure med Site Recovery. Den beräknade kostnaden för kompatibla virtuella datorer och inte på alla profilerad virtuella datorer. 
 
Du kan visa kostnaden per månad eller per år. Läs mer om [målregioner som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) och [valutor som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Kostnaden med komponenter**: den totala kostnaden för Katastrofåterställning är indelat i fyra komponenter: bearbetning, lagring, nätverk och Site Recovery licens kostnaden. Kostnaden beräknas baserat på förbrukningen som uppstår vid replikering och DR ökad samtidigt. Beräkning, lagring (premium och standard), ExpressRoute/VPN som har konfigurerats mellan lokal plats och Azure Site Recovery-licens används för beräkningar.

**Kostnad utfärda**: Totalt antal disaster recovery kostnaden kategoriseras baserat på två olika lägen: replikering och DR-test. 

**Replikering kostnaden**: kostnaden som uppstår vid replikering. Det täcker kostnaden för lagring, nätverk och Site Recovery-licens. 

**Kostnaden för DR-Återställningsgranskning**: kostnaden som uppstår under redundanstestning. Site Recovery startar virtuella datorer under redundanstestningen. DR ökad kostnaden omfattar körs VMs kostnaden för beräkning och lagring. 

**Azure Storage kostnad per år-månad-**: liggande diagram visar den totala lagringsutrymme kostnad som uppstår för premium och standardlagring för replikering och DR-test. Du kan visa en detaljerad kostnadsanalys per VM på arket [Cost Estimation](hyper-v-deployment-planner-cost-estimation.md) (Kostnadsuppskattning).

### <a name="growth-factor-and-percentile-values-used"></a>Tillväxtfaktor och percentilvärde som används
Det här avsnittet längst ned på bladet visar percentilvärdet används för alla prestandaräknare profilerad virtuella (standard är 95: e percentilen). Den visar även tillväxtfaktor (standardvärdet är 30 procent) som används i alla beräkningar.

![Tillväxtfaktor och percentilvärde som används](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Rekommendationer med tillgänglig bandbredd som indata
![Profileringsöversikt med bandbreddsindata](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Du kan ha en situation där du vet att du inte kan ange en bandbredd på mer än x Mbps för Site Recovery replikering. Du kan använda verktyget för att ange tillgänglig bandbredd (med hjälp av bandbredd parametern - under rapportgenerering) och hämta hastigheterna Återställningspunktmålet i minuter. Med detta är möjligt värde för Återställningspunktsmål, kan du bestämma om du behöver att etablera ytterligare bandbredd eller om du är nöjd med en lösning för katastrofåterställning med den här Återställningspunktmål.

![Möjligt återställningspunktmål (RPO)](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Rekommendation för placering av VM-lagring 
![Placering av VM-lagring](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Disk Storage Type** (Typ av disklagring): Är antingen Standard eller Premium och avser det lagringskonto som ska användas för replikering av motsvarande virtuella datorer i kolumnen **VMs to Place** (Virtuella datorer att placera ut).

**Suggested Prefix** (Föreslaget prefix): Det föreslagna prefixet på tre tecken som du kan använda för att namnge lagringskontot. Du kan använda ditt eget prefix, men verktygets förslag följer [namngivningskonventionen för partitioner av lagringskonton](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Föreslaget kontonamn): Namnet på lagringskontot när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Log Storage Account** (Lagringskonto för loggar): alla replikeringsloggar lagras på ett lagringskonto av standardtyp. För virtuella datorer som replikerar till ett Premium Storage-konto konfigurerar du ytterligare ett Standard Storage-konto för logglagringsutrymme. Flera lagringskonton för premiumreplikering kan använda samma standardkonto för logglagring. Virtuella datorer som replikeras till lagringskonton av standardtyp använder samma lagringskonto för loggarna.

**Suggested Log Account Name** (Föreslaget loggkontonamn): Namnet på lagringsloggkontot när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Placement Summary** (Placeringsöversikt): En översikt över den totala virtuella datorbelastningen på lagringskontot vid replikeringen samt vid redundanstest/redundansväxling. Sammanfattningen innehåller den:

* Totalt antal virtuella datorer som är mappad till lagringskontot. 
* Totalt antal läsning och skrivning IOPS över alla virtuella datorer placeras i det här lagringskontot.
* Totalt antal skrivna (replikering) IOPS.
* Installationsprogrammet för total storlek över alla diskar.
* Totalt antal diskar.

**Virtuella datorer på plats**: en lista över alla virtuella datorer som ska placeras på det angivna lagringskontot för optimala prestanda och användning.

## <a name="compatible-vms"></a>Compatible VMs (Kompatibla virtuella datorer)
Excel-rapport som genererats av Site Recovery-distribution Planner innehåller alla kompatibla virtuella datorer information i ”kompatibla virtuella datorer”-bladet.

![Compatible VMs (Kompatibla virtuella datorer)](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM Name** (Namn på virtuell dator): Den virtuella datorns namn som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VHD:er) som är kopplade till de virtuella datorerna. Namnen inkluderar de Hyper-V-värdnamn där de virtuella datorerna placerades när verktyget upptäckte de under profileringsperioden.

**VM-kompatibilitet**: Värdena är **Ja** och **Ja**\*. **Ja** \* avser instanser där den virtuella datorn är en anpassning för [Azure premium-lagring](https://aka.ms/premium-storage-workload). Här ryms den profilerade höga omsättningen eller IOPS-disken i en högre premiumdiskstorlek än storleken som är mappad till disken. Lagringskontot bestämmer vilka premium storage disktyp att mappa en disk till, baserat på dess storlek: 
* < 128 GB är en P10.
* 128 GB till 256 GB är en P15.
* 256 till 512 GB är en P20.
* 512 GB till 1 024 GB är en P30.
* 1,025 GB till 2 048 GB är en P40.
* 2,049 GB till 4,095 GB är en p 50.

Om exempelvis arbetsbelastningsegenskaperna för en disk placerar den i kategorin P20 eller P30, men storleken mappar den till en lägre Premium Storage-disktyp, markerar verktyget den här virtuella datorn som **Ja**\*. Verktyget rekommenderar också att du antingen ändrar källdiskens storlek så att den passar den rekommenderade Premium Storage-disktypen eller ändrar måldisktypen efter redundansväxling.

**Lagringstyp**: Standard eller premium.

**Suggested Prefix** (Föreslaget prefix): Ett prefix på tre tecken för lagringskontot.

**Lagringskontot**: Namnet med prefixet till det föreslagna lagringskontot.

**Belastning läs-/ skrivåtkomst IOPS (med tillväxtfaktor)**: den belastning arbetsbelastning läsning och skrivningen IOPS på disken (standard är 95: e percentilen) tillsammans med framtida tillväxt faktor (standardvärdet är 30 procent). Totalt antal läsning och skrivning IOPS för en virtuell dator är inte alltid summan av den virtuella datorn individuella diskar läsning och skrivning IOPS. Belastning läsning och skrivning IOPS för den virtuella datorn är belastning av summan av dess enskilda diskar läsning och skrivning IOPS under minuten profilering perioden.

**Belastning Data Omsättningsuppdateringar i MB/s (med tillväxtfaktor)**: Toppvärde omsättningsuppdateringar hastighet på disken (standard är 95: e percentilen) tillsammans med framtida tillväxt faktor (standardvärdet är 30 procent). Den totala dataomsättningen av den virtuella datorn inte alltid är summan av den virtuella datorn individuella diskar dataomsättningen. Dataomsättningen belastning av den virtuella datorn är belastning av summan av dess enskilda diskar omsättning under minuten profilering perioden.

**Azure VM-storlek**: perfekt mappade Azure Cloud Services VM-storlek för det lokala VM. Mappningen baserat på den lokala Virtuella datorns minne, antalet diskar-kärnor-nätverkskort och läsning/skrivning IOPS. Rekommendationen är alltid den lägsta virtuella Azure-datorstorlek som matchar alla lokala virtuella datoregenskaper.

**Number of Disks** (Antal diskar): Det totala antalet virtuella datordiskar (VHD:er) på den virtuella datorn.

**Storlek (GB)-disken**: den totala storleken på alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antalet processorkärnor i den virtuella datorn.

**Minne (MB)**: Den virtuella datorns RAM-minne.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

**Starta typen**: typen Start av den virtuella datorn. Den kan vara BIOS eller EFI.

## <a name="incompatible-vms"></a>Incompatible VMs (Inkompatibla virtuella datorer)
Excel-rapport som genererats av Site Recovery-distribution Planner innehåller alla inkompatibla VMs information i ”inkompatibla VMs”-bladet.

![Incompatible VMs (Inkompatibla virtuella datorer)](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM Name** (Namn på virtuell dator): Den virtuella datorns namn som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VHD:er) som är kopplade till de virtuella datorerna. Namnen inkluderar de Hyper-V-värdnamn där de virtuella datorerna placerades när verktyget upptäckte de under profileringsperioden.

**VM Compatibility** (VM-kompatibilitet): Anger varför den här virtuella datorn inte kan skyddas med Site Recovery. Anledningarna beskrivs för varje inkompatibel disk av den virtuella datorn och kan, baserat på publicerade [lagringsgränser](https://aka.ms/azure-storage-scalbility-performance), vara något av följande:

* Disken är större än 4,095 GB. Azure Storage stöder för närvarande inte data diskstorlekar som är större än 4,095 GB.

* OS-disken är större än 2,047 GB för generation 1 (BIOS starta typen) VM. Site Recovery stöder inte OS-diskens storlek överstiger 2,047 GB för virtuella datorer i generation 1.

* OS-disken är större än 300 GB för generation 2 (EFI-Start typ) VM. Site Recovery stöder inte OS diskens storlek är större än 300 GB för generation 2 virtuella datorer.

* Namn på en virtuell dator stöds inte med något av följande tecken ”:” [] '. Verktyget kan inte hämta profilerad data för virtuella datorer som har något av följande tecken i namnet. 

* En virtuell Hårddisk delas av två eller flera virtuella datorer. Azure stöder inte virtuella datorer med en delad virtuell Hårddisk.

* En virtuell dator med Virtual Fiber Channel stöds inte. Site Recovery stöder inte virtuella datorer med Virtual Fiber Channel.

* Hyper-V-kluster innehåller inte en koordinator för replikering. Site Recovery stöder inte en virtuell dator i Hyper-V-kluster om Hyper-V Replica Broker har inte konfigurerats för klustret.

* En virtuell dator är inte hög tillgänglighet. Site Recovery stöder inte en virtuell dator på en klusternod för Hyper-V vars virtuella hårddiskarna lagras på den lokala disken i stället för på klusterdisken. 

* Total storlek på Virtuellt minne (replikering + testa redundans) överskrider storleksgränsen för stöds premium storage-konto (35 TB). Den här inkompatibiliteten uppstår vanligen när en enskild disk i den virtuella datorn har en prestandaegenskap som överskrider den maxgräns som stöds av Azure- eller Site Recovery-gränserna för standardlagring. Denna instans skickar den virtuella datorn till Premium Storage-zonen. Den maximala storleken som stöds för ett premiumlagringskonto är dock 35 TB. En enda skyddad virtuell dator kan inte skyddas över flera lagringskonton. 

    När ett redundanstest körs på en skyddad virtuell dator och en ohanterad disk är konfigurerad för att testa redundans körs i samma lagringskonto där replikering pågår. I detta fall krävs ytterligare samma mängd lagringsutrymme som för replikering. Den garanterar att replikeringen ska gå vidare och att redundanstest ska lyckas parallellt. När en hanterad disk konfigureras för att testa redundans behöver ingen ytterligare utrymme redovisas med redundanstestningen VM.

* Källan IOPS är längre än stöds IOPS 7,500 per disk.

* Källan IOPS är längre än stöds IOPS 80,000 per virtuell dator.

* Källan VM genomsnittlig omsättning är längre än stöds Site Recovery data omsättning 10 MB/s för genomsnittlig i/o-storlek.

* Källan VM genomsnittlig effektiv skriva IOPS är längre än stöds Site Recovery IOPS 840.

* Beräknat lagringsutrymme för ögonblicksbilder överskrider gränsen på 10 TB.

**Belastning läs-/ skrivåtkomst IOPS (med tillväxtfaktor)**: belastning arbetsbelastningen IOPS på disken (standard är 95: e percentilen) tillsammans med framtida tillväxt faktor (standardvärdet är 30 procent). Totalt antal läsning och skrivning IOPS för den virtuella datorn inte alltid är summan av den virtuella datorn individuella diskar läsning och skrivning IOPS. Belastning läsning och skrivning IOPS för den virtuella datorn är belastning av summan av dess enskilda diskar läsning och skrivning IOPS under minuten profilering perioden.

**Belastning Data Omsättningsuppdateringar (MB/s) (med tillväxtfaktor)**: Toppvärde omsättningsuppdateringar hastighet på disken (standard är 95: e percentilen) tillsammans med framtida tillväxt faktor (standardvärdet är 30 procent). Observera att den totala dataomsättningen av den virtuella datorn inte alltid är summan av den virtuella datorn individuella diskar dataomsättningen. Dataomsättningen belastning av den virtuella datorn är belastning av summan av dess enskilda diskar omsättning under minuten profilering perioden.

**Number of Disks** (Antal diskar): Det totala antalet VHD:er på den virtuella datorn.

**Storlek (GB)-disken**: installationsprogrammet för totala storleken på alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antalet processorkärnor i den virtuella datorn.

**Minne (MB)**: Mängden RAM-minne på den virtuella datorn.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

**Starta typen**: typen Start av den virtuella datorn. Den kan vara BIOS eller EFI.

## <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery
Följande tabell innehåller gränserna som Site Recovery. Gränsvärdena baseras på tester, men de kan omfatta alla möjliga program i/o-kombinationer. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. För bästa resultat bör även efter att planera distribution, utför omfattande programmet testning genom att utfärda ett redundanstest för att hämta bilden true prestanda för programmet.
 
**Replication Storage Target** (Lagringsmål för replikering) | **Källan VM genomsnittlig i/o-storlek** |**Källan VM genomsnittlig omsättning** | **Total dataomsättning per dag för virtuell källdisk**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s per virtuell dator | 168 GB per virtuell dator
Premium Storage | 8 kB  | 5 MB/s per virtuell dator | 421 GB per virtuell dator
Premium Storage | 16 kB eller mer| 10 MB/s per virtuell dator | 842 GB per virtuell dator

Gränserna är genomsnittliga värden baserade på en I/O-överlappning på 30 procent. Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen. Föregående antal antar en typisk eftersläpning på cirka fem minuter. Det vill säga när data överförs den bearbetas och skapa en återställningspunkt inom fem minuter.

## <a name="on-premises-storage-requirement"></a>Krav för lokal lagring

Kalkylbladet tillhandahåller kravet på totalt ledigt diskutrymme för varje volym på Hyper-V-servrarna (där VHD:er finns) för lyckad inledande replikering och deltareplikering. Innan du aktiverar replikering, kan du lägga till nödvändiga lagringsutrymme på volymer att säkerställa att replikeringen inte några oönskade driftstopp för dina program i produktion. 

  Site Recovery-distribution Planner identifierar det optimala lagring kravet på diskutrymme baserat på den virtuella Hårddisken storlek och nätverksbandbredden som används för replikering.

![Krav för lokal lagring](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Varför behöver jag ledigt utrymme på Hyper-V-servern för replikeringen?
* När du aktiverar replikering av en virtuell dator tar en ögonblicksbild av varje virtuell Hårddisk på den virtuella datorn för den inledande replikeringen med Site Recovery. När den inledande replikeringen pågår skrivs nya ändringar till diskar i programmet. Site Recovery spårar ändringarna delta i loggfiler, som kräver ytterligare lagringsutrymme. Loggfilerna lagras lokalt tills den inledande replikeringen är klar. 

    Om tillräckligt med utrymme är inte tillgängligt för loggfiler och ögonblicksbild (AVHDX) replikeringen hamnar i läget omsynkronisering och replikeringen aldrig är klar. I värsta fall behöver du ytterligare 100 procent ledigt utrymme för VHD-storlek för inledande replikering.
* När den inledande replikeringen är klar startar deltareplikering. Site Recovery spårar ändringarna delta i loggfiler som lagras på volymen där de virtuella hårddiskarna för den virtuella datorn finns. Dessa loggfiler replikeras till Azure med en frekvens som konfigurerade kopia. Det kan ta lite tid att replikera loggfilerna till Azure, baserat på den tillgängliga nätverksbandbredden. 

    Om det inte tillräckligt med ledigt utrymme som är tillgänglig för att lagra loggfilerna, har replikeringen pausats. Sedan blir replikeringsstatus för den virtuella datorn ”omsynkronisering krävs”.
* Om nätverksbandbredden inte är tillräckligt för att skicka filerna till Azure, hämta loggfilerna piled på volymen. I ett scenario med sämsta när de loggfiler storlek ökas till 50 procent av storleken på VHD blir replikeringen av den virtuella datorn ”omsynkronisering krävs”. I värsta fall behöver du ytterligare 50 procent ledigt utrymme för VHD-storlek för deltareplikering.

**Hyper-V-värd**: Listan över profilerade Hyper-V-servrar. Om en server är en del av ett Hyper-V-kluster grupperas alla klusternoder ihop.

**Volym (VHD-sökväg)**: Varje volym av en Hyper-V-värd där VHD:er/VHDX:er finns. 

**Tillräckligt ledigt utrymme (GB)**: Tillgängligt ledigt utrymme på volymen.

**Totalt lagringsutrymme som krävs på volymen (GB)**: det totala lediga utrymmet som krävs på volymen för inledande replikering som lyckades och deltareplikering. 

**Totalt antal ytterligare lagringsutrymme som ska etableras på volymen för replikering som lyckades (GB)**: det totala ytterligare utrymmet som måste etableras på volymen för inledande replikering som lyckades och deltareplikering rekommenderar.

## <a name="initial-replication-batching"></a>Inledande batchbearbetning av replikering 

### <a name="why-do-i-need-initial-replication-batching"></a>Varför jag behöver inledande replikering batchbearbetning?
Om de virtuella datorerna skyddas på samma gång, är krav för ledigt utrymme mycket högre. Om det inte finns tillräckligt med lagringsutrymme som är tillgänglig, övergår replikeringen av de virtuella datorerna i läget för omsynkronisering. Nätverksbandbredd är också mycket högre för att slutföra inledande replikering av alla virtuella datorer tillsammans. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Inledande batchbearbetning av replikering för en vald RPO
Det här kalkylbladet innehåller detaljerad vy av varje batch för IR. För varje RPO skapas ett separat IR-batchbearbetningsark. 

När du har följt lokal lagring krav rekommendation för varje volym är vad du behöver för att replikera lista över virtuella datorer som kan skyddas parallellt. Dessa virtuella datorer är grupperade i en batch och det kan finnas flera batchar. Skydda virtuella datorer i ordningen de angivna batch. Skydda först Batch 1 virtuella datorer. När den inledande replikeringen är klar, skydda Batch 2 virtuella datorer och så vidare. Du kan hämta listan över batchar och motsvarande virtuella datorer från det här bladet. 

![IR batchbearbetning information](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Ytterligare IR batchbearbetning information](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Varje batch innehåller följande information 
**Hyper-V-värd**: Hyper-V-värden för den virtuella datorn som ska skyddas.

**Virtuella**: den virtuella datorns som ska skyddas. 

**Kommentarer**: Om det krävs någon åtgärd för en specifik volym på en virtuell dator anges kommentaren här. Om tillräckligt med ledigt utrymme är inte tillgängligt på en volym, till exempel kommentaren säger ”Lägg till ytterligare lagringsutrymme för att skydda den här virtuella datorn”.

**Volymen (VHD-sökväg)**: namn på volymer där den Virtuella datorns virtuella hårddiskar finns. 

**Ledigt utrymme på volymen (GB)**: ledigt diskutrymme på volymen för den virtuella datorn. Vid beräkningen av ledigt utrymme på volymerna övervägs det använda diskutrymmet för deltareplikering av de virtuella datorerna för föregående batchar vars VHD:er finns på samma volym. 

Till exempel VM1, VM2 och VM3 som finns på en volym kan säga E:\VHDpath. Ledigt utrymme på volymen är 500 GB före replikering. VM1 är en del av Batch-1, VM2 är en del av Batch-2 och VM3 är en del av Batch3. För VM1 är det lediga tillgängliga utrymmet 500 GB. För VM2 är det lediga utrymmet 500 – nödvändiga diskutrymmet för deltareplikering för VM1. Om VM1 kräver 300 GB diskutrymme för deltareplikering, är det lediga utrymmet för VM2 500 GB – 300 GB = 200 GB. På samma sätt kräver VM2 300 GB för deltareplikering. Ledigt utrymme för VM3 är 200 GB-300 GB =-100 GB.

**Lagring som krävs på volymen för inledande replikering (GB)**: Det lediga utrymmet som krävs för VM-volymen för lyckad inledande replikering.

**Lagringsutrymme som krävs på volymen för deltareplikering (GB)**: det lediga utrymmet på volymen som krävs för den virtuella datorn för deltareplikering.

**Ytterligare lagring som krävs baserat på brist för att undvika replikeringsfel (GB)**: Ytterligare lagringsutrymme som krävs på volymen för den virtuella datorn. Det är max för inledande replikering och delta replikering lagring kravet på diskutrymme minus det lediga utrymmet på volymen.

**Minsta bandbredd som krävs för inledande replikering (Mbit/s)**: Den minsta bandbredd som krävs för inledande replikering för den virtuella datorn.

**Minsta bandbredd som krävs för deltareplikering (Mbps)**: minsta bandbredd som krävs för deltareplikering för den virtuella datorn.

### <a name="network-utilization-details-for-each-batch"></a>Information om nätverksanvändning för varje batch 
Varje batchtabell innehåller en sammanfattning av nätverksanvändningen för varje batch.

**Tillgänglig bandbredd för batch**: den tillgängliga bandbredden för gruppen efter att hänsyn tagits till den föregående batchen delta replikering bandbredd.

**Uppskattad tillgänglig bandbredd för inledande replikering av batch**: Den tillgängliga bandbredden för inledande replikering av batchens virtuella datorer. 

**Ungefärlig förbrukad bandbredd för deltareplikering av batch**: Den bandbredd som krävs för deltareplikering av batchens virtuella datorer. 

**Uppskattad tid för inledande replikering för batch (hh: mm)**: den uppskattade den inledande replikeringstid i timmar: minuter.



## <a name="next-steps"></a>Nästa steg
Läs mer om [kostnadsuppskattning](hyper-v-deployment-planner-cost-estimation.md).
