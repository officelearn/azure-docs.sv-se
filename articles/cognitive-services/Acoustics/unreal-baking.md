---
title: Project Akustik Unreal Baka Tutorial
titlesuffix: Azure Cognitive Services
description: I det här dokumentet beskrivs processen att skicka in en akustikbakning med hjälp av tillägget Unreal editor.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854892"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Project Akustik Unreal Baka Tutorial
I det här dokumentet beskrivs processen att skicka in en akustikbakning med hjälp av tillägget Unreal editor.

Det finns fem steg för att göra en baka:

1. Skapa eller tagga spelarens navigeringsnät
2. Tag akustik geometri
3. Tilldela akustiska materialegenskaper till geometri
4. Placering av förhandsversionen av avsökning
5. Baka

## <a name="open-the-project-acoustics-editor-mode"></a>Öppna redigeringsläget För projektakustik

Importera plugin-paketet Project Acoustics till projektet. Mer information om detta finns i avsnittet [Unreal Integration.](unreal-integration.md) När insticksprogrammet är integrerat öppnar du Akustikgränssnittet genom att klicka på den nya ikonen Akustikläge.

![Skärmbild av alternativet Overklig editorakustikläge](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tag skådespelare för akustik

Objektfliken är den första fliken som visas när du öppnar akustikläget. Använd den här fliken om du vill tagga aktörer på din nivå, som lägger till **taggarna AcousticsGeometry** eller **AcousticsNavigation** i skådespelarna.

Markera ett eller flera objekt i världskonturen eller använd avsnittet **Massmarkering** för att markera alla objekt i en viss kategori. När objekt har markerats använder du avsnittet **Taggning** för att använda den önskade taggen på de markerade objekten.

Om något varken har **AcousticsGeometry** eller **AcousticsNavigation** tagg, kommer det att ignoreras i simuleringen. Endast statiska nät, navigeringsnät och landskap stöds. Om du taggar något annat ignoreras det.

### <a name="for-reference-the-objects-tab-parts"></a>Som referens: Tabbdelarna Objekt

![Skärmbild av fliken Akustikobjekt i Unreal](media/unreal-objects-tab-details.png)

1. Knapparna för tabbmarkering ( fliken**Objekt** markerad). Använd dessa knappar för att gå igenom de olika stegen för att göra en akustik baka, uppifrån och ner.
2. En kort beskrivning av vad du behöver göra med den här sidan.
3. Tillgängliga väljare för aktörer på nivån.
4. Om **du** klickar på Markera markeras alla objekt på den nivå som matchar minst en av de markerade aktörstyperna.
5. Om du klickar på **Avmarkera alla** kommer den aktuella markeringen att rensas. Detta är samma sak som att slå på flyktnyckeln.
6. Använd de här alternativknapparna för att välja om taggen Geometry eller Navigation ska användas på de valda skådespelarna.
7. Om du klickar på **Tagg** läggs den markerade taggen till i alla markerade aktörer.
8. Om du klickar på **Ta bort taggen** tas den markerade taggen bort från alla markerade aktörer.
9. Om du klickar på **Välj taggad avmarkeras** den aktuella markeringen och alla aktörer med den markerade taggen markeras.
10. Dessa statistik visar hur många aktörer som är taggade med varje taggtyp.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag akustik ocklusion och reflektion geometri

Öppna fliken Objekt i fönstret Akustik. Markera alla objekt som Akustikgeometri om de ska ockludera, reflektera eller absorbera ljud. Akustik geometri kan innehålla saker som mark, väggar, tak, fönster & fönster glas, mattor och stora möbler. Du kan använda valfri godtycklig nivå av komplexitet för dessa objekt. Eftersom scenen är voxelized före simulering, mycket detaljerade maskor, såsom träd med många små blad, är inte dyrare att baka än förenklade objekt.

Ta inte med saker som inte bör påverka akustiken, till exempel osynliga kollisionsnät.

Ett objekts transformering vid tidpunkten för sondberäkningen (via fliken Sonder nedan) fixeras i bakresultaten. Flytta något av de markerade objekten i scenen kommer att kräva att göra om sonden beräkning och rebaking scenen.

### <a name="create-or-tag-a-navigation-mesh"></a>Skapa eller tagga ett navigeringsnät

Ett navigeringsnät används för att placera avsökningspunkter för simulering. Du kan använda Unreals [Nav Mesh Bounds-volym](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)eller ange ett eget navigeringsnät. Du måste tagga minst ett objekt som **Akustik navigering**. Om du använder Unreals navigeringsnät kontrollerar du att du har byggt det först.

### <a name="acoustics-volumes"></a>Akustik volymer ###

Det finns ytterligare, avancerad anpassning som du kan göra på dina navigeringsområden med **Akustikvolymer**. **Akustik Volymer** är aktörer som du kan lägga till i din scen som gör att du kan välja områden att inkludera och ignorera från navigeringsnätet. Aktören exponerar en egenskap som kan växlas mellan "Inkludera" och "Uteslut". Inkludera volymer säkerställer att endast områden i navigeringsnätet i dem beaktas och "Uteslut" volymer markerar de områden som ska ignoreras. "Uteslut"-volymer tillämpas alltid efter "Inkludera"-volymer. Se till att **tagga Akustikvolymer** som **akustik navigering** genom den vanliga processen på fliken Objekt. Dessa aktörer är ***inte*** automatiskt taggade.

![Skärmbild av akustikvolymegenskaper i Unreal](media/unreal-acoustics-volume-properties.png)

"Exkluderingsvolymer" är främst avsedda att ge finkornig kontroll över var man inte ska placera sonder för åtdragning av resursanvändning.

![Skärmbild av Exclude Acoustics Volume in Unreal](media/unreal-acoustics-volume-exclude.png)

"Inkludera" volymer är användbara för att skapa manuella avsnitt av en scen, till exempel om du vill dela upp din scen i flera akustiska zoner. Till exempel, om du har en stor scen, många kilometer ruta, och du har två områden av intresse du vill baka akustik på. Du kan rita två stora "Inkludera" volymer i scenen och producera ACE-filer för var och en av dem en i taget. Sedan i spelet kan du använda utlösa volymer i kombination med skissanrop för att ladda lämplig ACE-fil när spelaren närmar sig varje bricka.

**Akustik Volymer** begränsar endast navigeringen och ***inte*** geometrin. Varje sond inuti en "Inkludera" **Akustik Volym** kommer fortfarande att dra in alla nödvändiga geometri utanför volymen när du utför vågsimuleringar. Därför bör det inte finnas några avbrott i ocklusion eller annan akustik till följd av spelaren passerar från en sektion till en annan.

## <a name="select-acoustic-materials"></a>Välj akustiska material

När dina objekt har taggats klickar du på knappen **Material** för att gå till fliken Material. Den här fliken används för att ange materialegenskaper för varje material i nivån. Innan några aktörer är taggade, kommer det att vara tomt.

De akustiska materialen styr mängden ljudenergi som reflekteras tillbaka från varje yta. Standard akustiskt material har absorption liknar betong. Project Acoustics föreslår material baserat på scenmaterialnamnet.

Efterklangstiden för ett visst material i ett rum är omvänt relaterad till dess absorptionskoefficient, där de flesta material har absorptionsvärden i intervallet 0,01 till 0,20. Material med absorptionskoefficienter ovanför detta intervall är mycket absorberande. Till exempel, om ett rum låter för genljudande, ändra det akustiska materialet i väggar, golv eller tak till något av högre absorptivitet. Den akustiska materialuppgiften gäller alla aktörer som använder det scenmaterialet.

![Diagram som visar negativ korrelation för efterklangstid med absorptionskoefficient](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Som referens: Delar av fliken Material

![Skärmbild av fliken Akustikobjekt i Unreal](media/unreal-materials-tab-details.png)

1. Knappen **Material,** som används för att visa den här sidan.
2. En kort beskrivning av vad du behöver göra med den här sidan.
3. Listan över material som används i nivån, tagen från aktörerna taggade som **AcousticsGeometry**. Om du klickar på ett material här kommer alla objekt i scenen som använder det materialet.
4. Visar det akustiska material som scenmaterialet har tilldelats. Klicka på en listruta om du vill tilldela om ett scenmaterial till ett annat akustiskt material.
5. Visar den akustiska absorptionskoefficienten för det material som valts i föregående kolumn. Ett värde på noll betyder perfekt reflekterande (ingen absorption), medan ett värde på 1 betyder perfekt absorptiva (ingen reflektion). Om du ändrar det här värdet uppdateras Akustikmaterialet (steg #4) till **Anpassad**.

Om du gör ändringar i materialet i scenen måste du byta flikar i insticksprogrammet För projektakustik för att se ändringarna återspeglas på fliken **Material.**

## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska avsökningsplatser för lyssnare

När du har tilldelat materialet växlar du till fliken **Sonder.**

### <a name="for-reference-parts-of-the-probes-tab"></a>Som referens: Delar av fliken Avsökningar

![Skärmbild av fliken Akustikavsökningar i Unreal](media/unreal-probes-tab-details.png)

1. Knappen **Sonder** flik som används för att visa den här sidan
2. En kort beskrivning av vad du behöver göra med den här sidan
3. Använd detta för att välja en grov eller fin simuleringsupplösning. Grov är snabbare, men har vissa kompromisser. Se [Baka upplösning](bake-resolution.md) nedan för mer information.
4. Välj den plats där akustikdatafilerna ska placeras med det här fältet. Klicka på knappen med "..." om du vill använda en mappväljare. Mer information om datafiler finns i [Datafiler](#Data-Files) nedan.
5. Datafilerna för den här scenen kommer att namnges med hjälp av prefixet som anges här. Standard är "[Level Name]_AcousticsData".
6. Klicka på knappen **Beräkna** för att voxelisera scenen och beräkna avsökningspunktens platser. Detta görs lokalt på din maskin, och måste göras innan du gör en baka. När avsökningarna har beräknats inaktiveras kontrollerna ovan och den här knappen ändras till att säga **Rensa**. Klicka på knappen **Rensa** om du vill radera beräkningarna och aktivera kontrollerna så att du kan räkna om med nya inställningar.

Avsökningar måste placeras genom den automatiserade process som finns på fliken **Avsökningar.**


### <a name="what-the-calculate-button-calculates"></a>Vad knappen "Beräkna" beräknar

**Knappen Beräkna** tar alla data som du har angett hittills (geometri, navigering, material och grov/fin inställning) och går igenom flera steg:

1. Det tar geometrin från scenen maskor och beräknar en voxel volym. Voxel volymen är en 3-dimensionell volym som omsluter hela din scen, och består av små kubiska "voxels". Storleken på voxels bestäms av simuleringsfrekvensen, som ställs in av inställningen **Simuleringsupplösning.** Varje voxel är markerad som antingen "utomhus" eller innehåller scengeometri. Om en voxel innehåller geometri taggas voxel med absorptionskoefficienten för det material som tilldelats den geometrin.
2. Den använder sedan navigeringsdata för att beräkna akustiskt intressanta platser där spelaren kan gå. Den försöker hitta en någorlunda liten uppsättning av dessa platser som innehåller mindre områden som dörröppningar och korridorer, och sedan till rum, till öppna ytor. För små scener är detta vanligtvis färre än 100 platser, medan stora scener kan ha upp till tusen.
3. För var och en av de slutliga lyssnarplatserna som den beräknar bestämmer den ett antal parametrar som hur "öppet" är utrymmet, storleken på rummet det är i, etc.
4. Resultaten av dessa beräkningar lagras i filer på den plats du anger (Se [datafiler](#Data-Files) nedan)

Beroende på storleken på din scen och hastigheten på din maskin, kan dessa beräkningar ta flera minuter.

När dessa beräkningar är klara kan du förhandsgranska både voxel-data och sonden punkt platser för att säkerställa att baka ger dig goda resultat. Saker som ett dåligt navigeringsnät eller saknad/extra geometri visas vanligtvis snabbt i förhandsgranskningen så att du kan korrigera det.


## <a name="debug-display"></a>Felsökningsvisning

När sondberäkningen är klar visas en ny aktör i World Outliner som kallas **AcousticsDebugRenderer**. Om du markerar **kryssrutorna Rendera avsökningar** och **Rendera Voxels** aktiveras felsökningsvisningen i redigeringsvyn.

![Skärmbild som visar Akustiken Debug Renderer-aktör i Unreal Editor](media/acoustics-debug-renderer.png)

Om du inte ser några voxels eller avsökningar överlagrad på din nivå kontrollerar du att rendering i realtid är aktiverat i visningsområdet.

![Skärmbild av återgivningsalternativet i realtid i Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels (avkäxor)

Voxels visas i scenfönstret som gröna kuber runt deltagande geometri. Voxels som endast innehåller luft visas inte. Det finns en stor grön ruta runt hela scenen som betecknar den kompletta voxelvolymen som kommer att användas i simuleringen.
Flytta runt din scen och kontrollera att den akustiskt ockluderande geometrin har voxels. Kontrollera också att icke-akustik objekt såsom kollision maskor inte har voxelized. Scenkameran måste vara inom cirka 5 meter från objektet för voxels att visa.

Om du jämför voxels skapas med grov upplösning vs fin upplösning, kommer du att se att grova voxels är dubbelt så stora.

![Skärmbild av förhandsvisning av Akustik voxels i Unreal editor](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Avsökningspunkter

Probe punkter är synonymt med möjliga spelare (lyssnare) platser. Vid bakning beräknar simuleringen akustiken som förbinder alla möjliga källplatser med varje sondpunkt. Vid körning interpoleras lyssnarplatsen bland närliggande avsökningspunkter.

Det är viktigt att kontrollera att sondpunkter finns överallt där spelaren förväntas resa i scenen. Avsökningspunkter placeras på navigeringsnätet av Project Acoustics-motorn och kan inte flyttas eller redigeras, så se till att navigeringsnätet täcker alla möjliga spelarplatser genom att inspektera avsökningspunkterna.

![Skärmbild av förhandsvisning av Akustikprober i Unreal](media/unreal-probes-preview.png)

Se [Baka upplösning](bake-resolution.md) för mer information om grov vs fin upplösning.

## <a name="bake-your-level-using-azure-batch"></a>Grädda din nivå med Azure Batch

Du kan baka din scen med ett beräkningskluster i molnet med azure batch-tjänsten. Projektet Akustik Unreal-plugint ansluter direkt till Azure Batch för att instansiera, hantera och riva ett Azure Batch-kluster för varje bakning. På fliken Baka anger du dina Azure-autentiseringsuppgifter, väljer en klusterdatortyp och storlek och klickar på Grädda.

### <a name="for-reference-parts-of-the-bake-tab"></a>Som referens: Delar av bakfliken

![Skärmbild av fliken Akustik baka i Unreal](media/unreal-bake-tab-details.png)

1. Knappen Baka flik som används för att visa den här sidan.
2. En kort beskrivning av vad du ska göra på den här sidan.
3. Fält för att ange dina Azure-autentiseringsuppgifter när ditt Azure-konto har skapats. Mer information finns i [Skapa ett Azure Batch-konto](create-azure-account.md).
4. Starta Azure-portalen för att hantera dina prenumerationer, övervaka användning och visa faktureringsinformation etc. 
5. Azure batch compute nodtyp som ska användas för beräkningen. Nodtypen måste stödjas av din Azure-datacenterplats. Om inte säker, lämna **på Standard_F8s_v2**.
6. Antal noder som ska användas för den här beräkningen. Numret du anger här påverkar tiden för att slutföra bakningen och begränsas av din Azure Batch-kärnallokering. Standardallokeringen tillåter endast två 8 kärnnoder eller en 16-kärnnod, men kan expanderas. Mer information om grundläggande allokeringsbegränsningar finns i [Skapa ett Azure Batch-konto](create-azure-account.md).
7. Markera den här kryssrutan om du vill konfigurera beräkningspoolen så att [noder med låg prioritet används](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Beräkningsnoder med låg prioritet har mycket lägre kostnad, men de kanske inte alltid är tillgängliga eller kan föregripas när som helst.
8. Hur lång tid det förväntas ta för jobbet att köras i molnet. Detta inkluderar inte nodstarttid. När jobbet börjar köras, det handlar om hur lång tid det ska vara innan du får tillbaka resultaten. Observera att detta bara är en uppskattning.
9. Den totala mängden datortid som behövs för att köra simuleringarna. Det här är den totala mängden beräkningstid för nod som ska användas i Azure. Se [Uppskatta baka kostnad](#Estimating-bake-cost) nedan för mer information om hur du använder detta värde.
10. Klicka på knappen Baka om du vill skicka bakningen till molnet. När ett jobb körs visas i stället **avbryt jobb.** Om det finns några fel på den här fliken, eller om arbetsflödet på fliken Avsökningar inte har **slutförts,** inaktiveras den här knappen.
11. Avsökningen räknas för din scen enligt beräknat på fliken **Avsökningar.** Antalet avsökningar bestämmer antalet simuleringar som måste köras i molnet. Du kan inte ange fler noder än det finns avsökningar.
12. Det här meddelandet anger jobbets aktuella status, eller om det finns några fel på den här fliken, vilka dessa fel är.

Du kan alltid få fullständig information om aktiva jobb, beräkningspooler och lagring på [Azure-portalen](https://portal.azure.com).

Medan ett jobb körs ändras knappen **Baka** till **Avbryt jobb**. Använd den här knappen om du vill avbryta det pågående jobbet. Att avbryta ett jobb kan inte ångras, inga resultat kommer att vara tillgängliga och du debiteras fortfarande för all Azure-beräkningstid som används före annulleringen.

När du har startat en bakning, kan du stänga Unreal. Beroende på projektet, nodtyp och antal noder kan en molnbakning ta flera timmar. Statusen för bakningsjobbet uppdateras när du laddar om projektet och öppnar fönstret Akustik. Om jobbet har slutförts hämtas utdatafilen.

Azure-autentiseringsuppgifterna lagras säkert på din lokala dator och associeras med ditt Unreal-projekt. De används endast för att upprätta en säker anslutning till Azure.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Uppskatta Azure baka kostnad

Om du vill uppskatta vad en viss bakning kommer att kosta, ta det värde som visas för **uppskattad beräkningskostnad**, vilket är en varaktighet, och multiplicera det med timkostnaden i din lokala valuta för den **VM-nodtyp** du valt. Resultatet kommer inte att inkludera nodtiden som behövs för att få noderna igång. Om du till exempel väljer **Standard_F8s_v2** för nodtypen, som har en kostnad på 0,40 USD/tim och den beräknade beräkningskostnaden är 3 timmar och 57 minuter, blir den beräknade kostnaden för att köra jobbet 0,40 USD * ~4 timmar = ~1,60 USD. Den faktiska kostnaden kommer sannolikt att vara lite högre på grund av den extra tiden för att få noderna igång. Du hittar kostnaden för nod per timme på sidan [Azure Batch-prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (välj "Beräkningsoptimerad" eller "Hög prestandaberäkning" för kategorin).

### <a name="reviewing-the-bake-results"></a>Granska baka resultat

När baka är klar, kontrollera att voxels och sond punkter är i sina förväntade platser genom att köra runtime plugin.

## <a name="data-files"></a><a name="Data-Files"></a>Datafiler

Det finns fyra datafiler som skapats av denna plugin på olika punkter. Endast en av dem behövs vid körning och placeras i projektets content/akustik-mapp, som automatiskt läggs till i projektets förpackningssökväg. De andra tre finns i mappen Akustikdata och är inte paketerade.

* **[Project]/Config/ProjectAcoustics.cfg**: Den här filen lagrar de data du anger i fält i användargränssnittet för akustikläge. Det går inte att ändra platsen och namnet på den här filen. Det finns andra värden som lagras i den här filen som påverkar bakningen, men de är för avancerade användare och bör inte ändras.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Den här filen är vad som skapas under bakningssimuleringen och innehåller uppslagsdata som används av körningen för att återge akustiken i din scen. Platsen och namnet på den här filen kan ändras med hjälp av fälten på fliken **Avsökningar.** Om du vill byta namn på den här filen när den har skapats tar du bort UAsset från unreal-projektet, byter namn på filen utanför Unreal i Utforskaren och importerar sedan filen till Unreal för att skapa en ny UAsset. Att byta namn på UAsset av sig själv kommer inte att fungera.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Den här filen lagrar den voxelized akustikgeometrin och materialegenskaperna. Beräknad med knappen **Beräkna** på fliken **Avsökningar.** Platsen och namnet på den här filen kan ändras med hjälp av fälten på fliken **Avsökningar.**
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Den här filen lagrar parametrar som beräknas med hjälp av knappen **Beräkna** på fliken **Avsökningar.** Platsen och namnet på den här filen kan ändras med hjälp av fälten på fliken **Avsökningar.**

Var noga med att inte ta bort filen *.ace som hämtats från Azure. Den här filen kan inte återställas förutom genom att återskapa scenen.

## <a name="next-steps"></a>Nästa steg
* Utforska [designkontrollerna för Unreal](unreal-workflow.md)
* Utforska [designkoncepten För projektets akustik](design-process.md)

