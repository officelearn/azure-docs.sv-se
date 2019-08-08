---
title: Själv studie kurs om projekt akustiska Unreal
titlesuffix: Azure Cognitive Services
description: Det här dokumentet beskriver processen för att skicka en akustiskt bak med Unreal Editor-tillägget.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854892"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Själv studie kurs om projekt akustiska Unreal
Det här dokumentet beskriver processen för att skicka en akustiskt bak med Unreal Editor-tillägget.

Det finns fem steg för att göra en bageri:

1. Skapa eller tagga ditt navigerings nät i Media Player
2. Tagga akustiska geometri
3. Tilldela egenskaper för akustiskt material till geometri
4. Förhandsgranska avsöknings placering
5. Skapa

## <a name="open-the-project-acoustics-editor-mode"></a>Öppna redigeraren för Project akustiskt läge

Importera plugin-paketet för Project Akustiske-plugin-programmet till projektet. Hjälp med detta finns i avsnittet [Unreal-integration](unreal-integration.md) . När plugin-programmet är integrerat öppnar du det akustiska användar gränssnittet genom att klicka på ikonen nytt akustiskt läge.

![Skärm bild av alternativet Unreal Editor akustiskt läge](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tagga aktörer för akustiska ljud

Fliken objekt är den första fliken som visas när du öppnar akustiskt läge. Använd den här fliken för att tagga aktörer på din nivå, som lägger till **AcousticsGeometry** -eller **AcousticsNavigation** -Taggar i aktörerna.

Välj ett eller flera objekt i världen eller Använd avsnittet för **Mass val** för att välja alla objekt i en viss kategori. När objekt har marker ATS använder du **taggnings** avsnittet för att tillämpa den önskade taggen på de valda objekten.

Om någon saknar **AcousticsGeometry** -eller **AcousticsNavigation** -tagg ignoreras den i simuleringen. Endast statiska maskor, nav maskor och landskap stöds. Om du taggar något annat kommer det att ignoreras.

### <a name="for-reference-the-objects-tab-parts"></a>Som referens: Fliken objekt delar

![Skärm bild av fliken akustiska objekt i Unreal](media/unreal-objects-tab-details.png)

1. Fliken Val knappar (fliken**objekt** har valts). Använd de här knapparna för att gå igenom de olika stegen för att göra en akustiskt bak från början till slutet.
2. En kort beskrivning av vad du behöver göra med den här sidan.
3. Tillgängliga väljare för aktörer på nivån.
4. Om du klickar på **Välj** markeras alla objekt på den nivå som matchar minst en av de kontrollerade aktörs typerna.
5. Om du klickar på **avmarkera alla** raderas det aktuella valet. Detta är detsamma som att trycka på ESC-tangenten.
6. Använd de här alternativ knapparna för att välja om geometrin eller navigerings tag gen ska användas för de valda aktörerna.
7. Om du klickar på **tagga** läggs den markerade taggen till i alla valda aktörer.
8. Om du klickar på **Avtagga** tas den valda taggen bort från alla valda aktörer.
9. Om du klickar på **Välj taggat** raderas det aktuella valet och du kan välja alla aktörer med den markerade taggen.
10. Dessa statistik visar hur många aktörer som är taggade med varje typ av tagg.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tagga akustiska ocklusion och reflektions geometri

Öppna fliken objekt i fönstret akustiska objekt. Markera alla objekt som akustiska geometrier om de ska occlude, reflektera eller absorbera ljud. Akustiska geometrier kan innehålla saker som mark, väggar, tak, Windows & fönster glas, Rugs och stora möbler. Du kan använda valfri godtycklig komplexitets nivå för dessa objekt. Eftersom scenen är voxelized före simuleringen är mycket detaljerade maskor, t. ex. träd med många små löv, inte mer kostsamt för att kunna flyttas över enkla objekt.

Inkludera inte saker som inte påverkar akustiskt, t. ex. osynliga kollisions nät.

Ett objekts transformering vid tidpunkten för avsöknings beräkningen (via fliken avsökningar nedan) åtgärdas i bageri resultatet. Om du flyttar något av de markerade objekten i scenen måste du göra om avsöknings beräkningen och flytta scenen.

### <a name="create-or-tag-a-navigation-mesh"></a>Skapa eller tagga ett navigerings nät

Ett navigerings nät används för att placera avsöknings punkter för simulering. Du kan använda Unreal nav-nätgränser- [volym](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), eller så kan du ange ett eget navigerings nät. Du måste tagga minst ett objekt som **akustisk navigering**. Om du använder Unreal navigerings nät ser du till att du har skapat det först.

### <a name="acoustics-volumes"></a>Akustiska volymer ###

Det finns ytterligare en avancerad anpassning som du kan göra i navigerings områdena med **akustiska volymer**. **Akustiska volymer** är aktörer som du kan lägga till i din scen som låter dig välja områden som ska tas med och ignoreras från navigations nätet. Aktören visar en egenskap som kan växlas mellan "include" och "exclude". "Inkludera"-volymer garanterar att endast områden i navigerings nätet i dem beaktas och "exkludera"-volymer Markera de områdena som ska ignoreras. "Exkludera"-volymer används alltid efter "inkludera"-volymer. Kom ihåg att tagga **akustiska volymer** som **akustiska navigering** genom den vanliga processen på fliken objekt. Dessa aktörer märks ***inte*** automatiskt.

![Skärm bild av egenskaper för akustiska volymer i Unreal](media/unreal-acoustics-volume-properties.png)

"Exkludera"-volymer är huvudsakligen avsedda att ge detaljerad kontroll över var de inte ska placera avsökningar för att intensifiera resursanvändningen.

![Skärm bild av exkluderande akustiska volymer i Unreal](media/unreal-acoustics-volume-exclude.png)

"Inkludera"-volymer är användbara för att skapa manuella avsnitt i en scen, till exempel om du vill dela upp din scen i flera akustiska zoner. Om du till exempel har en stor scen, många kilo meter i kvadrat och du har två intresse områden som du vill ha på. Du kan rita två stora "inkludera"-volymer i scenen och producera ACE-filer för var och en av dem i taget. Sedan kan du använda Utlös ande volymer tillsammans med skiss anrop för att läsa in lämplig ACE-fil när spelaren närmar sig varje panel.

**Akustiska volymer** begränsar bara navigeringen och ***inte*** geometrin. Varje avsökning i en "include"- **akustisk volym** kommer fortfarande att hämta all nödvändig geometri utanför volymen när de utför våg simuleringar. Därför bör det inte finnas några discontinuities i ocklusion eller andra akustiska ljud som orsakas av att spelarna korsar varandra från ett avsnitt till ett annat.

## <a name="select-acoustic-materials"></a>Välj akustiskt material

När dina objekt är taggade klickar du på knappen **material** för att gå till fliken material. Den här fliken används för att ange material egenskaper för varje material på nivån. Innan alla aktörer är taggade är de tomma.

Akustiskt material styr mängden ljud energi som återspeglas från varje yta. Standard akustiska materialet har absorption på liknande sätt som betong. Projekt akustiska förslag ger material baserat på scen materialets namn.

Reverberation tiden för ett material i ett rum är i motsatt förhållande till dess absorptions-koefficient, med merparten av materialet med absorptions värden i 0,01 till 0,20-intervallet. Material med absorptions koefficienter över det här intervallet är mycket absorberande. Om ett rum till exempel låter alltför reverberant, ändrar du det akustiska materialet på väggarna, golvet eller taket till något högre absorptivity. Den akustiska material tilldelningen gäller alla aktörer som använder detta scen material.

![Diagram över negativ korrelation av Reverberation tid med absorptions koefficient](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Som referens: Delar av fliken material

![Skärm bild av fliken akustiska objekt i Unreal](media/unreal-materials-tab-details.png)

1. Knappen **material** -fliken som används för att öppna den här sidan.
2. En kort beskrivning av vad du behöver göra med den här sidan.
3. Listan över material som används på nivån, tas från aktörerna märkta som **AcousticsGeometry**. Om du klickar på ett material här markeras alla objekt i den scen som använder det materialet.
4. Visar det akustiska material som scen materialet har tilldelats. Klicka på en listruta för att omtilldela ett scen material till ett annat akustiskt material.
5. Visar koefficienten för akustisk absorption för det material som valts i föregående kolumn. Värdet noll innebär en perfekt reflekterande (ingen absorption) medan värdet 1 innebär en perfekt absorptive (ingen reflektion). Om du ändrar det här värdet uppdateras akustiskt material (steg #4) till **anpassat**.

Om du gör ändringar i materialet i din scen, måste du växla flikar i plugin-programmet för projekt för att se ändringarna som visas på fliken **material** .

## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska lyssnare avsöknings platser

När du har tilldelat materialet växlar du till fliken avsökningar.

### <a name="for-reference-parts-of-the-probes-tab"></a>Som referens: Delar av fliken avsökningar

![Skärm bild av fliken akustiska avsökningar i Unreal](media/unreal-probes-tab-details.png)

1. Fliken avsökningar som används för att öppna den här sidan
2. En kort beskrivning av vad du behöver göra med den här sidan
3. Använd det här för att välja en grov simulerings upplösning. Grovheten går snabbare, men har vissa kompromisser. Mer information finns i artikeln om [problemlösning](bake-resolution.md) nedan.
4. Välj den plats där akustiska datafiler ska placeras med det här fältet. Klicka på knappen med "..." använda en mapp väljare. Mer information om datafiler finns i [datafiler](#Data-Files) nedan.
5. Datafilerna för den här scenen får namnet med hjälp av det prefix som anges här. Standardvärdet är "[nivå namn] _AcousticsData".
6. Klicka på knappen **Beräkna** för att voxelize scenen och beräkna plats för avsöknings platser. Detta görs lokalt på din dator och måste utföras innan du gör en bageri. När avsökningarna har beräknats kommer kontrollerna ovan att inaktive ras och den här knappen ändras till säg **klart**. Klicka på **Rensa** om du vill radera beräkningarna och aktivera kontrollerna så att du kan beräkna om med hjälp av nya inställningar.

Avsökningar måste placeras genom den automatiserade processen som finns på fliken avsökningar.


### <a name="what-the-calculate-button-calculates"></a>Hur beräknas knappen "beräkna"

Med knappen **Beräkna** tar du med alla data som du har angett hittills (geometri, navigering, material och fin-/fin inställning) och går igenom flera steg:

1. Den tar geometrin från scenens maskor och beräknar en Voxel volym. Voxel-volymen är en 3-dimensionell volym som omsluter hela scenen och består av liten kubisk "voxels". Storleken på voxels bestäms av simulerings frekvensen, som anges av inställningen för **simulerings upplösning** . Varje Voxel markeras som antingen "öppen luft" eller innehåller en scen geometri. Om en Voxel innehåller geometri taggas Voxel med absorptions koefficienten för det material som har tilldelats den geometrin.
2. Den använder sedan navigerings data för att beräkna akustiskt intressanta platser där spelaren kan gå. Det försöker hitta en rimlig liten uppsättning av dessa platser som innehåller mindre områden som doorways och korridoren, och sedan till rum, för att öppna blank steg. För små scener är detta vanligt vis färre än 100 platser, medan stora scener kan ha upp till 1000.
3. För var och en av de sista lyssnar platser som den beräknar, bestäms ett antal parametrar, till exempel hur "Open" är utrymmet, storleken på rummet det är i osv.
4. Resultatet av dessa beräkningar lagras i filer på den plats som du anger (se [datafiler](#Data-Files) nedan)

Beroende på storleken på din scen och datorns hastighet kan dessa beräkningar ta flera minuter.

När dessa beräkningar har slutförts kan du förhandsgranska både Voxel-data och avsöknings punkt platser för att se till att bagerien ger dig bästa resultat. Sådant som ett dåligt navigerings nät eller en extra geometri som saknas visas vanligt vis snabbt i förhands granskningen, så att du kan åtgärda det.


## <a name="debug-display"></a>Fel söknings visning

När avsöknings beräkningen har slutförts visas en ny aktör i världen med namnet **AcousticsDebugRenderer**. Genom att Markera kryss rutorna rendera avsökningar och **rendera Voxels** aktive ras fel sökningen i redigerings visnings området.

![Skärm bild som visar akustiska fel sökning åter givnings aktör i Unreal-redigeraren](media/acoustics-debug-renderer.png)

Om du inte ser några voxels eller avsökningar som finns på din nivå kontrollerar du att real tids åter givning är aktiverat i visnings området.

![Skärm bild av alternativ för rendering i real tid i Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels visas i scen fönstret som gröna kuber runt den deltagande geometrin. Voxels som endast innehåller luft visas inte. Det finns en stor grön ruta runt hela scenen som anger den kompletta Voxel-volymen som ska användas i simuleringen.
Flytta runt din scen och kontrol lera att den akustiskt Occluding geometrin har voxels. Kontrol lera också att icke-akustiska objekt som kollisions nät inte har voxelized. Scen kameran måste vara inom cirka 5 meter av objektet för att voxels ska kunna visas.

Om du jämför voxels som har skapats med grov upplösning jämfört med fin upplösning ser du att de grova voxels är två gånger stora.

![Skärm bild av Akustisker voxels Preview i Unreal-redigeraren](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Avsöknings punkter

Avsöknings punkter är synonymer med möjliga Player-platser (lyssnare). Vid inningen beräknar simuleringen de ljud som ansluter alla möjliga käll platser till varje avsöknings punkt. Vid körning interpoleras lyssnar platsen mellan nära avsöknings punkter.

Det är viktigt att kontrol lera att avsöknings punkter finns överallt där spelaren förväntas resa i scenen. Avsöknings punkter placeras på navigerings nätet av motorn för akustiska ljud och kan inte flyttas eller redige ras, så se till att navigerings nätet täcker alla möjliga spelare genom att inspektera avsöknings punkterna.

![Skärm bild av för hands versionen av akustiska avsökningar i Unreal](media/unreal-probes-preview.png)

Se [problemlösning](bake-resolution.md) för att få mer information om grova vs-lösningar.

## <a name="bake-your-level-using-azure-batch"></a>Bageri din nivå med Azure Batch

Du kan gå till din scen med ett beräknings kluster i molnet med hjälp av tjänsten Azure Batch. Projektet Akustisker Unreal-plugin-programmet ansluter direkt till Azure Batch för att instansiera, hantera och riva ned ett Azure Batch-kluster för varje bak. På fliken hemskrivet anger du dina autentiseringsuppgifter för Azure, väljer en kluster dator typ och storlek och klickar på bak sidan.

### <a name="for-reference-parts-of-the-bake-tab"></a>Som referens: Delar av fliken bageri

![Skärm bild av fliken akustiskt i Unreal](media/unreal-bake-tab-details.png)

1. Fliken bageri som används för att öppna den här sidan.
2. En kort beskrivning av vad du kan göra på den här sidan.
3. Fält för att ange dina Azure-autentiseringsuppgifter när ditt Azure-konto har skapats. Mer information finns i [skapa ett Azure Batch-konto](create-azure-account.md).
4. Starta Azure Portal för att hantera dina prenumerationer, övervaka användning och Visa fakturerings information osv. 
5. Azure Batch Compute Node-nodtyp som ska användas för beräkningen. Nodtypen måste stödjas av Azure Data Center-platsen. Om du inte är säker lämnar du på **Standard_F8s_v2**.
6. Antal noder som ska användas för den här beräkningen. Numret du anger här påverkar tiden för att slutföra bagerien och begränsas av din Azure Batch Core-allokering. Standardallokeringen tillåter bara två 8 core-noder eller 1 16 core-nod, men kan expanderas. Mer information om begränsningar för kärn allokering finns i [skapa ett Azure Batch-konto](create-azure-account.md).
7. Markera den här kryss rutan om du vill konfigurera din Compute-pool för att använda [noder med låg prioritet](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Compute-noder med låg prioritet har mycket lägre kostnad men de kanske inte alltid är tillgängliga eller kan aktive ras när som helst.
8. Förfluten tid som det förväntas ta för jobbet att köras i molnet. Detta inkluderar inte start tid för nod. När jobbet börjar köras är det hur lång tid det tar innan du får tillbaka resultatet. Observera att detta bara är en uppskattning.
9. Den totala mängden bearbetnings tid som krävs för att köra simuleringarna. Detta är den totala mängden beräknings tid för noder som ska användas i Azure. Mer information om hur du använder det här värdet finns i avsnittet om [uppskattning av bageri kostnader](#Estimating-bake-cost) nedan.
10. Klicka på knappen bak för att skicka in hembakat till molnet. När ett jobb körs visar detta **Avbryt jobb** i stället. Om det finns några fel på den här fliken, eller om arbets flödet på fliken avsökningar inte har slutförts, kommer den här knappen att inaktive ras.
11. Antalet avsökningar för din scen enligt beräkningen på fliken avsökningar. Antalet avsökningar bestämmer hur många simuleringar som behöver köras i molnet. Du kan inte ange fler noder än vad som finns i avsökningar.
12. Det här meddelandet innehåller information om den aktuella statusen för jobbet, eller om det finns fel på den här fliken, vad dessa fel är.

Du kan alltid få fullständig information om aktiva jobb, Compute-pooler och lagring på [Azure Portal](https://portal.azure.com).

När ett jobb körs på **bak** -knappen ändras till **Avbryt jobb**. Använd den här knappen om du vill avbryta pågående jobb. Det går inte att ångra ett jobb, inga resultat kommer att vara tillgängliga och du kommer fortfarande att debiteras för alla Azure-beräknings tider som används innan du avbryter.

När du har startat en bageri kan du stänga Unreal. Det kan ta flera timmar, beroende på projektet, nodtypen och antalet noder. Status för jobbet uppdateras när du laddar om projektet och öppnar fönstret akustiska. Om jobbet har slutförts kommer utdatafilen att hämtas.

Azure-autentiseringsuppgifterna lagras på ett säkert sätt på din lokala dator och är kopplade till ditt Unreal-projekt. De används enbart för att upprätta en säker anslutning till Azure.

### <a name="Estimating-bake-cost"></a>Uppskatta kostnad för Azure-bageri

För att uppskatta vad en viss bageri kostar, tar du det värde som visas för uppskattad **beräknings kostnad**, vilket är en varaktighet och multiplicerar det med Tim kostnaden i din lokala valuta för den **VM-nodtyp** som du har valt. I resultatet ingår inte den Node-tid som krävs för att få noderna igång. Om du till exempel väljer **Standard_F8s_v2** för nodtypen, som har kostnaden $0.40/tim, och den uppskattade beräknings kostnaden är 3 timmar och 57 minuter, blir den uppskattade kostnaden för att köra jobbet $0,40 * ~ 4 timmar = ~ $1,60. Den faktiska kostnaden är förmodligen lite högre på grund av extra tiden för att hämta noderna. Du hittar kostnaden för varje timme på sidan [Azure Batch prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Välj "Compute-optimerad" eller "hög prestanda beräkning" för kategorin).

### <a name="reviewing-the-bake-results"></a>Granska bak resultat

När bagerien är klar kontrollerar du att voxels och avsöknings punkterna finns på deras förväntade platser genom att köra runtime-pluginprogrammet.

## <a name="Data-Files"></a>Datafiler

Det finns fyra datafiler som skapas av det här plugin-programmet vid olika tidpunkter. Endast en av dem behövs vid körning och placeras i projektets innehåll/Akustisker-mapp, som läggs till automatiskt i ditt projekts förpacknings Sök väg. De andra tre finns inuti mappen akustiska data och är inte paketerade.

* **[Project]/config/ProjectAcoustics.cfg**: Den här filen lagrar de data som du anger i fält i användar gränssnittet för akustiska lägen. Det går inte att ändra den här filens plats och namn. Det finns andra värden lagrade i den här filen som påverkar hembakat, men de är för avancerade användare och bör inte ändras.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData. ACE**: Den här filen är vad som skapas vid en bageri simulering och innehåller de uppslags data som används av körnings miljön för att återge akustiskheten i din scen. Du kan ändra plats och namn på den här filen med hjälp av fälten på fliken avsökningar. Om du vill byta namn på den här filen efter att den har skapats, tar du bort UAsset från ditt Unreal-projekt, byter namn på filen utanför Unreal i Utforskaren och importerar sedan filen till Unreal för att skapa en ny UAsset. Att byta namn på själva UAsset fungerar inte.
* **[Project]/plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData. VOX**: I den här filen lagras voxelized akustiska geometri och material egenskaperna. Beräknas med hjälp av knappen **Beräkna** på fliken avsökningar. Du kan ändra plats och namn på den här filen med hjälp av fälten på fliken avsökningar.
* **[Project]/plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config. XML**: Den här filen lagrar parametrar som beräknats med knappen **Beräkna** på fliken avsökningar. Du kan ändra plats och namn på den här filen med hjälp av fälten på fliken avsökningar.

Ta hand om du inte vill ta bort *. ACE-filen som hämtats från Azure. Den här filen är inte återställnings bar, förutom genom att inning av scenen.

## <a name="next-steps"></a>Nästa steg
* Utforska [design kontrollerna för Unreal](unreal-workflow.md)
* Utforska [design koncepten för projekt akustiska metoder](design-process.md)

