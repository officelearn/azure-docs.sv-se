---
title: Projektet Akustik Unreal ändamålet självstudien
titlesuffix: Azure Cognitive Services
description: Det här dokumentet beskriver processen för att skicka in en Akustik ändamålet med hjälp av Redigeraren för Unreal-tillägget.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: michem
ms.openlocfilehash: afe4421bea27ff029bd4a1a7808241a54027a6ac
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136397"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Projektet Akustik Unreal ändamålet självstudien
Det här dokumentet beskriver processen för att skicka in en Akustik ändamålet med hjälp av Redigeraren för Unreal-tillägget.

Det finns fem steg för att göra en ändamålet:

1. Skapa eller tagga dina spelare navigering nät
2. Taggen Akustik geometri
3. Tilldela geometri akustiska material egenskaper
4. Förhandsgranska avsökningen placering
5. Skapa

## <a name="open-the-project-acoustics-editor-mode"></a>Öppna projektet Akustik redigeringsläget

Importera projekt Akustik plugin-programmet paketet i projektet. Om du vill ha hjälp med detta finns i den [Unreal integrering](unreal-integration.md) avsnittet. När du har integrerat plugin-programmet, öppna Gränssnittet Akustik genom att klicka på ikonen nytt Akustik läge.

![Öppna Akustik läge](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Taggen aktörer för Akustik

Fliken objekt är den första fliken som visas när du öppnar Akustik-läge. Använd den här fliken tagg aktörer i din nivå, som lägger till den **AcousticsGeometry** eller **AcousticsNavigation** taggar till aktörer.

Välj ett eller flera objekt i världen Outliner eller Använd den **flera** avsnittet för att välja alla objekt i en specifik kategori. När objekt har markerats kan du använda den **taggning** avsnitt för att tillämpa den önskade taggen till de valda objekten.

### <a name="for-reference-the-objects-tab-parts"></a>Referens: Objekt fliken delar

![Unreal fliken Objektinformation](media/unreal-objects-tab-details.png)

1. Fliken val av knapparna (**objekt** fliken markerad). Använd knapparna för att gå igenom de olika stegen för att göra en Akustik ändamålet uppifrån och ned.
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan.
3. Tillgängliga väljare för aktörerna i nivån.
4. Klicka på **Välj** väljer alla objekt i den nivå som matchar minst en av de markerade aktör typerna.
5. Klicka på **avmarkera alla** rensar den aktuella markeringen. Det här är samma som nått ESC-tangenten.
6. Använd dessa alternativknappar för att välja om du vill tillämpa taggen Geometry eller navigeringen på valda aktörer.
7. Klicka på **taggen** lägger till den markerade taggen i alla markerade aktörer.
8. Klicka på **ta bort märkord** tar bort den markerade taggen från alla markerade aktörer.
9. Klicka på **Välj taggade** rensa markeringen och Välj alla aktörer med den markerade taggen.
10. Dessa statistik visar hur många aktörer är märkta med varje taggtyp av.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tagga Akustik är spärrat och reflektion geometri

Öppna fliken objekt i fönstret Akustik. Markera alla objekt som Akustik geometri om de ska occlude, återspeglar eller absorbera ljud. Akustik geometri kan vara grunden, väggar, tak, windows och fönstret glas, mattor och stora möbler. Du kan använda en valfri nivå av komplexitet för dessa objekt. Eftersom scenen är voxelized innan simulering, är mycket detaljerad nät, till exempel träd med många små bladen inte dyrare att skapa än förenklad objekt.

Omfatta inte saker som inte ska påverka Akustik, till exempel osynligt kollision nät.

Ett objekts transformeringen vid tidpunkten för avsökning beräkningen (via fliken avsökningar nedan) har lösts i resultaten för ändamålet. Flytta någon av de markerade objekten i scenen behöver göra om avsökningen beräkningen och rebaking scenen.

## <a name="create-or-tag-a-navigation-mesh"></a>Skapa eller tagga ett navigering nät

Ett navigering-nät används för att placera avsökningen punkter för simulering. Du kan använda Unreal's [Nav nät gränser volym](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), eller ange din egen navigering nät. Du måste markera minst ett objekt som **Akustik navigering**.

## <a name="select-acoustic-materials"></a>Välj akustiska material

När dina objekt är taggade klickar du på den **material** för att gå till fliken material. Den här fliken används för att ange väsentliga egenskaper för varje material på nivån. Innan alla aktörer märks, är det tomt.

Akustiska material styra mängden ljud energi som visas från varje ytan. Det akustiska materialet som standard har absorption liknar konkret. Projektet Akustik föreslår baserat på namnet på scen-samband.

Genljudet tidpunkten för ett visst material i ett rum är relaterat till dess absorptionskoefficient, med de flesta material med absorption värden i intervallet 0,01 0.20 omvänt. Det är mycket absorberande material med absorptionskoefficient ovanför det här intervallet. Till exempel om ett rum ljud för reverberant ändra akustiska materialet väggar, våning eller taket till något högre absorptivity. Akustiska samband tilldelningen gäller för alla aktörer som använder scen materialet.

![Eko graf](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Referens: Delar av fliken material

![Unreal fliken Objektinformation](media/unreal-materials-tab-details.png)

1. Den **material** fliken knapp, som används för att få fram den här sidan.
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan.
3. Listan över material som används i den nivå som kommer från aktörer taggas som **AcousticsGeometry**. När du klickar på ett material här Markera alla objekt i scenen som använder materialet.
4. Visar det akustiska materialet att scen materialet har tilldelats. Klicka på en listruta för att tilldela en scen material till en annan akustiska material.
5. Visar den akustiska absorptionskoefficienten material som valde i föregående kolumn. Värdet noll innebär perfekt reflekterande (inga absorption), samtidigt som värdet 1 innebär perfekt absorptive (inga reflektion). Ändra det här värdet kommer att uppdatera Akustik Material (steg #4) till **anpassad**.


## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska lyssnare avsökningen platser

När du har tilldelat material, växla till den **avsökningar** fliken.

### <a name="for-reference-parts-of-the-probes-tab"></a>Referens: Delar av fliken avsökningar

![Avsökningar fliken information](media/unreal-probes-tab-details.png)

1. Den **avsökningar** fliken knapp som används för att få fram den här sidan
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan
3. Används för att välja en grov eller bra simulering upplösning. Grov är snabbare, men har vissa kompromisser. Se [grov eller finjustera upplösning](#Coarse-vs-Fine-Resolution) nedan för information.
4. Välj platsen där Akustik datafiler ska placeras med hjälp av det här fältet. Klicka på knappen med ”...” för att använda en Mappväljare för. Läs mer om datafiler [datafiler](#Data-Files) nedan.
5. Datafilerna för den här scen namnges med prefixet som anges här. Standardvärdet är ”[namn på] _AcousticsData”.
6. Klicka på den **Calculate** knappen till voxelize scenen och beräkna avsökningen-objekt. Detta utförs lokalt på din dator och måste göras innan du gör en ändamålet. När avsökningar har beräknats kontroller ovan kommer att inaktiveras och den här knappen kommer att ändras för att säga **Rensa**. Klicka på den **Rensa** knappen för att radera beräkningar och aktivera kontroller så att du kan beräkna om med nya inställningarna.

Avsökningar måste placeras via den automatiska processen som anges i den **avsökningar** fliken.


### <a name="what-the-calculate-button-calculates"></a>Knappen ”Calculate” beräknar

Den **Calculate** tar alla data som du har angett (geometri, navigering, material och inställningen grov/fine) och går igenom flera steg:

1. Det tar geometrin från scenen nät och beräknar en voxel volym. Voxel volymen är en 3-dimensionell volym som omsluter din hela scen och består av små m3 ”voxels”. Storleken på voxels bestäms av simulering frekvens, vilket anges genom den **simulering lösning** inställningen. Varje voxel har markerats som antingen ”öppna luften” eller som innehåller scen geometri. Om en voxel innehåller geometri märks voxel med absorptionskoefficienten material som tilldelats den geometrin.
2. Därefter använder navigering data för att beräkna akustiskt intressanta platser där spelaren försätts. Försök att hitta en stor uppsättning platserna som innehåller mindre områden, till exempel dörrar och dvs och sedan till rum, öppna blanksteg. För små scener är detta vanligtvis färre än 100 platser, medan stora scener kan ha upp till tusen.
3. För var och en av de sista lyssnare platser som beräknar avgör den ett antal parametrar, till exempel hur ”öppet” är utrymmet, storleken på det utrymme som den tillhör, osv.
4. Resultatet av dessa beräkningar lagras i filer på den plats du anger (se [datafiler](#Data-Files) nedan)

Dessa beräkningar kan ta flera minuter beroende på storleken på din scen och hastigheten på din dator.

När dessa beräkningar är klar kan förhandsgranska du både voxel data och avsökning-objekt för att säkerställa att ändamålet ger dig goda resultat. Sådant som ett felaktigt navigering nät eller saknas/extra geometri vanligtvis syns snabbt i förhandsversionen, så du kan korrigera den.


## <a name="debug-display"></a>Felsöka visning

När avsökningen beräkningen har slutförts, en ny aktören kommer att visas i världen-Outliner kallas **AcousticsDebugRenderer**. Kontrollera den **rendera avsökningar** och **rendera Voxels** kryssrutorna aktiverar debug visas inom visningsområdet redigeraren.

![Akustik felsöka återgivning](media/acoustics-debug-renderer.png)

Om du inte ser några voxels eller avsökningar som finns på din nivå kan du kontrollera i realtid rendering är aktiverat i visningsområdet.

![Aktivera i realtid rendering](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels visas i fönstret scen som grön kuber runt deltagande geometri. Voxels som innehåller endast air visas inte. Det finns en stor grön ruta runt hela scenen som anger den fullständiga voxel volym som ska användas i simuleringen.
Flytta din scen och kontrollera att den akustiskt occluding geometrin har voxels. Kontrollera också att icke-Akustik objekt som har gjorts voxelized kollision nät. Scen kameran måste vara inom cirka 5 taxor för objektet för voxels ska visas.

Om du jämför voxels som skapats med grov upplösning vs bra lösning, visas grov voxels är två gånger så stora.

![Voxel förhandsversion](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Avsökningen punkter

Avsökningen punkter är synonyma med möjliga player (lyssnare)-platser. När gräddning, beräknar simuleringen Akustik ansluta alla möjliga källplatser till varje avsökning punkt. Vid körning interpolerade lyssnare platsen bland i närheten avsökningen punkter.

Det är viktigt att kontrollera att avsökningen punkter finns var som helst spelaren förväntas färdas i scenen. Avsökningen pekar placeras på nätet navigering av projektet Akustik-motorn och kan inte flyttas eller redigeras, så se till navigering nät omfattar alla möjliga player platser genom att kontrollera avsökning punkter.

![Avsökningar förhandsversion](media/unreal-probes-preview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Grov vs bra lösning

Den enda skillnaden mellan grov och bra lösning inställningar är den frekvens med vilken simuleringen utförs. Fine använder en frekvens som är dubbelt så mycket som grov.
Även om det kan verka enkla, har ett antal effekter på akustiska simuleringen:

* Våglängden för grov två gånger är så långa så bra och voxels är därför två gånger så stora.
* Simulering tiden är direkt relaterad till den voxel storlek, vilket gör en grov ändamålet cirka 16 gånger snabbare än en bra ändamålet.
* Vara det går inte att simuleras portaler (till exempel dörrar eller windows) och mindre än storleken voxel. Grov inställningen kan göra att vissa av dessa mindre portaler för inte simuleras; Därför kan skickar de inte ljud via vid körning. Du kan se om detta sker genom att visa voxels.
* Lägre simulering frekvensen resulterar i mindre diffraction runt hörn och kanter.
* Ljud källor får inte finnas inuti ”fyllts” voxels, som är voxels som innehåller geometri – detta resulterar i utan ljud. Det är svårare att placera ljud källor så att de inte är i större voxels av grov än när du använder inställningen bra.
* Större voxels kommer intrude mer till portaler, enligt nedan. Den första bilden har skapats med hjälp av grov, medan andra är samma nyckeln med hjälp av bra lösning. Som anges med röd markeringar, är det mycket mindre intrång i nyckeln med hjälp av bra inställningen. Den blå linjen är nyckeln som definieras av geometri, medan den röda linjen är effektiva akustiska portalen definieras av voxel storlek. Hur den här intrång spelar i en viss situation beror helt på hur voxels stämmer överens med portalen, vilket avgörs av storlek och platserna för dina objekt i scenen geometri.

![Grov nyckeln](media/unreal-coarse-bake.png)

![Bra nyckeln](media/unreal-fine-bake.png)

## <a name="bake-your-level-using-azure-batch"></a>Skapa din nivå med hjälp av Azure Batch

Du kan skapa din scen med ett beräkningskluster i molnet med Azure Batch-tjänsten. Plugin-programmet projekt Akustik Unreal ansluter direkt till Azure Batch för att skapa en instans av, hantera och plocka ner ett Azure Batch-kluster för varje ändamålet. Ange dina autentiseringsuppgifter för Azure på fliken ändamålet, Välj en typ av kluster-dator och storlek och på ändamålet.

### <a name="for-reference-parts-of-the-bake-tab"></a>Referens: Delar av fliken ändamålet

![Skapa fliken](media/unreal-bake-tab-details.png)

1. Knappen Skapa flik används för att ta fram den här sidan.
2. En kort beskrivning av vad du gör på den här sidan.
3. Fält att ange dina autentiseringsuppgifter för Azure när du har skapat ditt Azure-konto. Mer information finns i [skapa ett Azure Batch-konto](create-azure-account.md).
4. Starta Azure-portalen för att hantera dina prenumerationer, övervaka användning och visa faktureringsinformation osv. 
5. Azure batch-beräkningsnod nodtyp för beräkningen. Nodtypen måste stödjas av din Azure data center plats. Om det inte att lämna på **Standard_F8s_v2**.
6. Antalet noder som ska användas för den här beräkningen. Det antal som du anger här påverkar hur lång tid att slutföra ändamålet och begränsas av Azure Batch core allokeringen. Standardallokeringen kan du bara tillåter två 8 kärnor noder eller ett 16 kärnor nod, men kan utökas. Mer information om core allokering begränsningar finns i [skapa ett Azure Batch-konto](create-azure-account.md).
7. Markera kryssrutan för att konfigurera din beräknings-pool för att använda [lågprioritetsnoder](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Beräkningsnoder med låg prioritet har mycket lägre kostnad, men de kanske inte alltid är tillgänglig eller kan avbrytas när som helst.
8. Mängden tid som gått för det förväntas ta för jobbet ska köras i molnet. Detta inkluderar inte starttiden för noden. Detta är om hur lång tid det bör vara innan du kommer tillbaka resultaten när jobbet börjar köras. Observera att detta är endast en uppskattning.
9. Den totala arbetstiden som behövs för att köra simuleringar. Det här är den totala mängden noden beräkningstiden som ska användas i Azure. Se [Estimating ändamålet kostnaden](#Estimating-bake-cost) nedan för mer information om hur du använder det här värdet.
10. Klicka på knappen ändamålet om du vill skicka ändamålet till molnet. När ett jobb körs, visar detta **Avbryt jobb** i stället. Om det inte finns några fel på den här fliken, eller om arbetsflödet på den **avsökningar** fliken inte har slutförts, den här knappen kommer att inaktiveras.
11. Antalet avsökning för din scen som beräknas på den **avsökningar** fliken. Antal avsökningar avgör hur många simuleringar som måste köras i molnet. Du kan inte ange fler noder än vad som finns på avsökningar.
12. Det här meddelandet anger att den aktuella statusen för jobbet, eller om det finns några fel i den här fliken, vilka dessa fel är.

Du kan alltid få fullständig information om aktiva jobb, beräkningspooler och lagring på den [Azure-portalen](https://portal.azure.com).

När ett jobb körs den **skapa** knappen ändras till **Avbryt jobb**. Använd den här knappen för att avbryta pågående jobb. Avbryter ett jobb kan inte ångra, inga resultat att bli tillgänglig och du kommer fortfarande att debiteras när som helst Azure compute används före eventuellt avbrott.

När du har startat en ändamålet, kan du stänga Unreal. En cloud-ändamålet kan ta flera timmar beroende på projektet, nodtyp och antalet noder. Ändamålet jobbets status kommer att uppdateras när du uppdatera projektet och öppna fönstret Akustik. Om jobbet har slutförts, laddas utdatafilen ned.

Azure-autentiseringsuppgifterna lagras på ett säkert sätt på den lokala datorn och som är associerade med ditt Unreal projekt. De används endast för att upprätta en säker anslutning till Azure.

### <a name="Estimating-bake-cost"></a> Beräknar Azure ändamålet kostnad

Om du vill beräkna vad en viss ändamålet kostar måste ta värdet som visas för **beräknade Compute kostnad**, vilket är en varaktighet och multiplicera som av de varje timme kostar i din lokala valuta av den **VM nodtyp** du har valt. Resultatet innehåller inte noden tiden behövs för att komma noderna och körs. Exempel: Om du väljer **Standard_F8s_v2** för nodtypen, vilket kostar $ 0,40/timme, och den uppskattade kostnaden för Compute är 3 timmar och 57 minuter, den uppskattade kostnaden för att köra jobbet kommer att $0,40 * ~ 4 timmar = ~ 1,60. Verklig kostnad kommer antagligen vara lite högre på grund av den extra tid att hämta de noder som är igång. Du kan hitta noden per timme kostnaden på den [priser för Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Välj ”Beräkningsoptimerad” eller ”databehandling med höga prestanda” för kategorin).

### <a name="reviewing-the-bake-results"></a>Granska resultaten ändamålet

När ändamålet har slutförts kan du kontrollera att voxels och avsökning punkter är i deras förväntade platser genom att köra runtime-plugin-programmet.

## <a name="Data-Files"></a>Datafiler

Det finns fyra datafiler som skapats av det här plugin-programmet vid olika tidpunkter. Endast en av dem behövs vid körning och placeras i ditt projekts innehåll/Akustik-mappen, som läggs till automatiskt till ditt projekt paketering sökväg. De övriga tre finns i mappen Akustik Data och paketeras inte.

* **[Project]/Config/ProjectAcoustics.cfg**: Den här filen lagrar du anger i fälten i Användargränssnittet för Akustik-läge. Platsen och namnet på den här filen kan inte ändras. Det finns andra värden som lagras i den här filen som påverkar ändamålet, men de är för avancerade användare och ska inte ändras.
* **[Projekt] / innehåll/Akustik / [%{levelname/]\_AcousticsData.ace**: Den här filen är vad skapas under ändamålet simuleringen och innehåller lookup-data som används av körningen för att återge Akustik av din scen. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.
* **[Projekt] / plugin-program/ProjectAcoustics/AcousticsData / [%{levelname/]\_AcousticsData.vox**: Här lagras voxelized Akustik geometri och egenskaper för material. Beräknas med hjälp av den **Calculate** knappen på den **avsökningar** fliken. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Den här filen lagras parametrar som beräknas med hjälp av den **Calculate** knappen på den **avsökningar** fliken. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.

Var noga så att inte ta bort den *.ace-fil som hämtats från Azure. Den här filen inte är återställningsbara utom vid rebaking scenen.

## <a name="next-steps"></a>Nästa steg
* Utforska den [utforma kontroller för Unreal](unreal-workflow.md)
* Utforska den [projekt Akustik designbegrepp](design-process.md)

