---
title: Projektet Akustik Unity ändamålet självstudien
titlesuffix: Azure Cognitive Services
description: Den här självstudien beskrivs Akustik gräddning med projekt Akustik i Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8875674b0f9c621a573dda591b4dc2b6f018a83c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494790"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projektet Akustik Unity ändamålet självstudien
Den här självstudien beskrivs Akustik gräddning med projekt Akustik i Unity.

Programvarukrav:
* [Unity 2018.2 +](http://unity3d.com) för Windows
* [Projektet Akustik plugin-programmet integreras i dina Unity-projekt](unity-integration.md) eller [projekt Akustik Unity-exemplen](unity-quickstart.md)
* Valfritt: En [Azure Batch-konto](create-azure-account.md) för att påskynda bakes med molnbaserad databehandling

## <a name="open-the-project-acoustics-bake-window"></a>Öppna projektet Akustik skapa fönster
Välj **Fönster > Akustik** Unity-menyn:

![Skärmbild av Unity-redigerare med menyalternativet för Akustik fönstret markerat](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Skapa en navigering nät
Projektet Akustik använder ett navigering nät så att lyssnare avsökningen punkter för simulering. Du kan använda Unity's [navigering nät arbetsflöde](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), eller använda en annan 3D-modellering paketet för att utforma din egen nät. 

## <a name="mark-acoustic-scene-objects"></a>Markera akustiska scen objekt
Projektet Akustik förlitar sig på två typer av objekt som scen simulering: de objekt som ska visas och occlude ljud i simuleringen och navigering player-nät som avgränsar lyssnare avsökningen punkter i simuleringen. Båda objekttyperna av markeras med hjälp av den **objekt** fliken. 

Eftersom Markera objekt lägger bara till den **AcousticsGeometry** eller **AcousticsNavigation** komponenter till objektet, du kan också använda den [standard Unity komponenten arbetsflöde](https://docs.unity3d.com/Manual/UsingComponents.html)att markera eller avmarkera objekt. Endast nät renderare och Terrains kan markeras. Alla andra typer av objekt ignoreras. Kryssrutorna ska markera eller avmarkera alla påverkade objekt.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Markera akustiska är spärrat och reflektion geometri
Öppna den **objekt** fliken den **Akustik** fönster. Markera alla objekt som **Akustik geometri** om de ska occlude återspeglar eller absorbera ljud. Akustik geometri kan vara grunden, väggar, tak, windows och fönstret glas, mattor och stora möbler. Du kan använda en valfri nivå av komplexitet för dessa objekt. Eftersom scenen är voxelized innan simulering, är mycket detaljerad nät, till exempel träd med många små bladen inte dyrare att skapa än förenklad objekt.

Omfatta inte saker som inte ska påverka Akustik, till exempel osynligt kollision nät.

Ett objekts transformeringen vid tidpunkten för avsökning beräkningen (via den **avsökningar** fliken nedan) har lösts i resultaten för ändamålet. Flytta någon av de markerade objekten i scenen behöver göra om avsökningen beräkningen och rebaking scenen.

### <a name="mark-the-navigation-mesh"></a>Markera navigering nät
Navigering nät som skapats med arbetsflödet för Unitys hämtas av Akustik systemet. Om du vill använda din egen nät, markera dem från den **objekt** fliken.

### <a name="for-reference-the-objects-tab-parts"></a>Referens: Objekt fliken delar
Delar av sidan är:

1. Fliken val av knapparna (**objekt** fliken markerad). Använd knapparna för att gå igenom de olika stegen för att göra en Akustik ändamålet från vänster till höger.
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan.
3. Tillgängliga filter för hierarkin fönster. Används för att filtrera fönstret hierarkin till objekt av den angivna typen så att du enkelt kan markera dem. Om du inte ännu har markerats för Akustik visas att välja de sista två alternativ inget. De kan dock vara användbar för att hitta markerade objekt när du har gjort det.
4. Det här avsnittet visas status för alla objekt i scenen när inga objekt har markerats:
    * Totalt - det totala antalet aktiva, icke-dolda objekt i scenen.
    * Ignoreras – antal objekt som inte är nät renderare eller Terrains.
    * Nät - antal Återgivare nät objekt i scenen
    * Terräng - terräng antalet objekt i scenen
    * Geometry - antalet nät eller terräng objekt i scenen som markerats som ”Akustik geometri”
    * Navigering – antalet nät eller terräng objekt i scenen som markerats som ”Akustik navigering”. Det här talet omfattar inte Unity's NavMesh.
5. Visar det totala antalet ”mark-kan-objekt i scenen, vilket är endast nät renderare och Terrains. Visar du kan använda för att markera kryssrutorna (Lägg till lämplig komponent till) de objekt som geometry eller navigeringen för Akustik
6. När inget är markerat, påminner dig om att välja objekt för märkning om det behövs i den här anteckningen. Du kan också kontrollera ena eller båda kryssrutorna för att markera alla objekt i scenen utan att välja vad som helst.
7. När objekt har markerats i det här avsnittet visas status för endast de valda objekten.
8. Visar det totala antalet ”mark-” valda objekt. Markera eller avmarkera kryssrutorna Markera eller avmarkera bara de valda objekten.

Om du har inget valt på scenen ser fliken objekt som på följande bild:

![Skärmbild av Akustik objekt fliken med ingen markering](media/objects-tab-no-selection-detail.png)

Om du har något valde i fönstret scen eller hierarki, ser den ut som på följande bild:

![Skärmbild av Akustik objekt fliken med val av visas](media/objects-tab-selection-detail.png)

Om vissa objekt markeras och andra inte, visas en ”blandat” värdet i respektive kryssruta:

![Skärmbild av Akustik objekt fliken med blandad val av ikonen markerat](media/mixed-object-selection-detail.png)

Klicka på kryssrutan tvingar alla objekt har markerats och klickar på igen kommer avmarkera alla objekt.

Objekt kan markeras för både geometri och navigering.

## <a name="select-acoustic-materials"></a>Välj akustiska material
När dina objekt markeras, klickar du på den **material** knappen och tilldela akustiska material. Projektet Akustik material systemet är kopplad till Unity visual material systemet: två objekt ha separata akustiska material, måste de har separata visual material.

Akustiska material styra mängden ljud energi som visas från varje ytan. Det akustiska materialet som standard har absorption liknar konkret. Projektet Akustik föreslår material baserat på det visuella samband namnet. Du kan tilldela en material som du kan aktivera en absorption koefficienten skjutreglage akustiska material ”anpassad”.

Genljudet tidpunkten för ett visst material i ett rum är relaterat till dess absorptionskoefficient, med de flesta material med absorption värden i intervallet 0,01 0.20 omvänt. Material med absorptionskoefficient utanför det här intervallet är mycket absorberande.

![Diagram som visar negativt korrelation genljudet tid med större](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Referens: Delar av fliken material
![Skärmbild av Akustik material flik i Unity](media/materials-tab-detail.png)

1. Den **material** fliken knapp, som används för att få fram den här sidan.
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan.
3. Markera endast material som används av objekt som markerats som **Akustik geometri** visas. I annat fall visas allt material som används i scenen.
4. Använd dessa alternativ för att ändra ordningen på den nedrullningsbara menyn som visas när du klickar på en listruta i kolumnen Akustik nedan (6). **Namn på** sorterar akustiska material efter namn. ”Absorptivity” sorterade efter absorptivity från låg till hög.
5. Lista över material som används i scenen sorteras alfabetiskt. Om den **markerats Visa endast** kryssrutan är markerad (#3), endast material som används av objekt som markerats som **Akustik geometri** visas. När du klickar på ett material här Markera alla objekt i scenen som använder materialet.
6. Visar det akustiska materialet att scen materialet har tilldelats. Klicka på en listruta för att tilldela en scen material till en annan akustiska material. Du kan ändra sorteringsordningen av menyn som visas när du klickar på ett objekt här med den **sortera Akustik efter:** alternativ ovan (#4).
7. Visar den akustiska absorptionskoefficienten material som valde i föregående kolumn. Värdet noll innebär perfekt reflekterande (inga absorption), samtidigt som värdet 1 innebär perfekt absorptive (inga reflektion). Absorptionskoefficienten kan inte ändras, såvida inte det valda materialet är ”anpassad”.
8. För ett material som tilldelats till ”anpassad”, skjutreglaget inaktiveras inte längre och du kan välja absorptionskoefficienten med hjälp av skjutreglaget eller genom att skriva in ett värde.

## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska lyssnare avsökningen platser
När du har tilldelat material, växla till den **avsökningar** fliken och klicka på **Calculate** att placera lyssnare avsökningen punkter för simulering.

### <a name="what-the-calculate-button-calculates"></a>Knappen ”beräkna...” beräknar
Den **beräkna...**  knappen använder din valda akustiska scen geometri för att förbereda din scen för simulering:

1. Det tar geometrin från scenen nät och beräknar en voxel volym. Voxel volymen är en 3-dimensionell volym som omsluter din hela scen och består av små m3 ”voxels”. Storleken på voxels bestäms av simulering frekvens, vilket anges genom den **simulering lösning** inställningen. Varje voxel har markerats som antingen ”öppna luften” eller som innehåller scen geometri. Om en voxel innehåller geometri märks voxel med absorptionskoefficienten material som tilldelats den geometrin.
2. Navigering mesh(es) används för att placera lyssnare avsökningen punkter. Algoritmen balanserar konkurrerande frågor för rumsliga täckning och simulering och storlek, samtidigt som begränsar korridorer och små utrymmen har alltid vissa delar av täckning. Vanliga avsökningen punkt räknar sträcker sig från 100 för små scener till några tusen för stora scener.

Dessa beräkningar kan ta flera minuter beroende på storleken på din scen och hastigheten på din dator.

### <a name="review-voxel-and-probe-placement"></a>Granska voxel och avsökning placering
Förhandsgranska både voxel data och objekt så är du redo att skapa din scen avsökningen. En ofullständig navigering nät eller saknas eller är extra akustiska geometri syns vanligtvis snabbt i förhandsversionen. Placering av Voxel och avsökning kan aktiveras eller inaktiveras med hjälp av menyn Gizmos:

![Skärmbild av Gizmos menyn i Unity](media/gizmos-menu.png)

Voxels som innehåller akustiska geometri visas som grön kuber. Utforska din scen och kontrollera att allt som ska vara geometri har voxels. Scen kameran måste vara inom cirka 5 taxor för objektet för voxels ska visas.

Om du jämför voxels som skapats med grov upplösning vs bra lösning, visas grov voxels är två gånger så stora.

![Skärmbild av grov voxels förhandsversion i Unity-redigeraren](media/voxel-cubes-preview.png)

Simuleringsresultat interpolerade mellan lyssnare avsökningen platser vid körning. Kontrollera att det finns avsökningen poäng nära helst spelaren förväntas färdas i scenen.

![Skärmbild av avsökningar förhandsversion i Unity-redigeraren](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Var noga med scen byter namn på
Scennamnet används för att ansluta scenen till lagring avsökningen punkt placering och voxelization-filer. Om scenen har bytt namn när avsökningen beräknas, material tilldelning och placering av data går förlorad och ska köras igen.

### <a name="for-reference-parts-of-the-probes-tab"></a>Referens: Delar av fliken avsökningar
![Skärmbild av Akustik avsökningar flik i Unity](media/probes-tab-detail.png)

1. Den **avsökningar** fliken knapp som används för att få fram den här sidan
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan
3. Du kan använda dessa för att välja en grov eller bra simulering upplösning. Grov är snabbare, men har vissa kompromisser. Se [skapa upplösning](bake-resolution.md) nedan för information.
4. Välj platsen där Akustik datafiler ska placeras med hjälp av det här fältet. Klicka på knappen med ”...” för att använda en Mappväljare för. Standardvärdet är **tillgångar/AcousticsData**. En **redigeraren** undermapp skapas även under den här platsen. Läs mer om datafiler [datafiler](#Data-Files) nedan.
5. Datafilerna för den här scen namnges med prefixet som anges här. Standardvärdet är ”Acoustics_ [scen Name]”.
6. När avsökningar har beräknats, inaktiveras kontrollerna som ovan. Klicka på den **Rensa** knappen för att radera beräkningar och aktivera kontroller så att du kan beräkna om med nya inställningarna.
7. Klicka på den **beräkna...**  för att voxelize scenen och beräkna avsökningen-objekt. Detta utförs lokalt på din dator och måste göras innan du gör en ändamålet.

I den här versionen av projektet Akustik avsökningar kan inte placeras manuellt och måste placeras via den automatiska processen som anges i den **avsökningar** fliken.

Se [skapa lösning](bake-resolution.md) för mer information om grov vs funkar lösning.

## <a name="bake-your-scene-using-azure-batch"></a>Skapa din scen med Azure Batch
Du kan skapa din scen med ett beräkningskluster i molnet med Azure Batch-tjänsten. Projektet Akustik Unity-plugin-programmet ansluter direkt till Azure Batch för att skapa en instans av, hantera och plocka ner ett Azure Batch-kluster för varje ändamålet. På den **skapa** fliken, ange dina autentiseringsuppgifter för Azure, Välj en typ av kluster-dator och storlek och klicka på **skapa**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Referens: Delar av fliken ändamålet
![Skärmbild av Akustik skapa flik i Unity](media/bake-tab-details.png)

1. Knappen Skapa flik används för att ta fram den här sidan.
2. En kort beskrivning av vad du gör på den här sidan.
3. Fält att ange dina autentiseringsuppgifter för Azure när du har skapat ditt Azure-konto. Mer information finns i [skapa ett Azure Batch-konto](create-azure-account.md).
4. Docker bildtagg för Akustik verktygsuppsättningen.
5. Starta Azure-portalen för att hantera dina prenumerationer, övervaka användning och visa faktureringsinformation osv. 
6. Azure batch-beräkningsnod nodtyp för beräkningen. Nodtypen måste stödjas av din Azure data center plats. Om det inte att lämna på **Standard_F8s_v2**.
7. Antalet noder som ska användas för den här beräkningen. Det antal som du anger här påverkar hur lång tid att slutföra ändamålet och begränsas av Azure Batch core allokeringen. Standardallokeringen kan du bara tillåter två 8 kärnor noder eller ett 16 kärnor nod, men kan utökas. Mer information om core allokering begränsningar finns i [skapa ett Azure Batch-konto](create-azure-account.md).
8. Markera kryssrutan för att konfigurera din beräknings-pool för att använda [lågprioritetsnoder](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Beräkningsnoder med låg prioritet har mycket lägre kostnad, men de kanske inte alltid är tillgänglig eller kan avbrytas när som helst.
9. Antalet avsökning för din scen som beräknas på den **avsökningar** fliken. Antal avsökningar avgör hur många simuleringar som måste köras i molnet. Du kan inte ange fler noder än vad som finns på avsökningar.
10. Mängden tid som gått för det förväntas ta innan jobbet ska köras i molnet. Detta inkluderar inte starttiden för noden. Detta är om hur lång tid det bör vara innan du kommer tillbaka resultaten när jobbet börjar köras. Observera att detta är endast en uppskattning.
11. Den totala arbetstiden som behövs för att köra simuleringar. Det här är den totala mängden noden beräkningstiden som ska användas i Azure. Se [Estimating ändamålet kostnaden](#Estimating-bake-cost) nedan för mer information om hur du använder det här värdet.
12. Det här meddelandet visar var resultatet av ändamålet ska sparas när jobbet har slutförts.
13. (Endast för avancerad användning) Om du skickat av någon anledning att tvinga Unity att glömma en ändamålet (t.ex. du har hämtat resultat i en annan dator), klickar du på den **rensa tillstånd** knappen att glömma om jobbet som har skickats. Alltså resultatfilen, när du är klar, kommer **inte** laddas ned och **detta är inte detsamma som att avbryta jobbet**. Jobbet fortsätter om kör, att köras i molnet.
14. Klicka på den **skapa** knapp för att skicka ändamålet till molnet. När ett jobb körs, visar detta **Avbryt jobb** i stället.
15. Förbereder för bearbetning [Akustik simulering på din dator](#Local-bake).
16. Det här området visar status för ändamålet. När du är klar bör du se **hämtade**.

Du kan alltid få fullständig information om aktiva jobb, beräkningspooler och lagring på den [Azure-portalen](https://portal.azure.com).

När ett jobb körs den **skapa** knappen ändras till **Avbryt jobb**. Använd den här knappen för att avbryta pågående jobb. Du blir ombedd att bekräfta innan jobbet avbryts. Avbryter ett jobb kan inte ångra, inga resultat att bli tillgänglig och du kommer fortfarande att debiteras för alla Azure beräkningstiden som används.

När du har startat en ändamålet, kan du stänga Unity. En cloud-ändamålet kan ta flera timmar beroende på projektet, nodtyp och antalet noder. Ändamålet jobbets status kommer att uppdateras när du uppdatera projektet och öppna fönstret Akustik. Om jobbet har slutförts, laddas utdatafilen ned.

Azure-autentiseringsuppgifterna lagras på ett säkert sätt på den lokala datorn och som är associerade med dina Unity-redigeraren. De används endast för att upprätta en säker anslutning till Azure.

### <a name="Estimating-bake-cost"></a> Beräknar Azure ändamålet kostnad

Om du vill beräkna vad en viss ändamålet kostar måste ta värdet som visas för **beräknade Compute kostnad**, vilket är en varaktighet och multiplicera som av de varje timme kostar i din lokala valuta av den **VM nodtyp** du har valt. Resultatet innehåller inte noden tiden behövs för att komma noderna och körs. Exempel: Om du väljer **Standard_F8s_v2** för nodtypen, vilket kostar $ 0,40/timme, och den uppskattade kostnaden för Compute är 3 timmar och 57 minuter, den uppskattade kostnaden för att köra jobbet kommer att $0,40 * ~ 4 timmar = ~ 1,60. Verklig kostnad kommer antagligen vara lite högre på grund av den extra tid att hämta de noder som är igång. Du kan hitta noden per timme kostnaden på den [priser för Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Välj ”Beräkningsoptimerad” eller ”databehandling med höga prestanda” för kategorin).

## <a name="Local-bake"></a> Skapa din scen på din dator
Du kan skapa din scen på din dator. Detta kan vara användbart för att experimentera med Akustik med små scener innan du skapar ett Batch-konto. Obs Akustik simuleringen kan ta lång tid beroende på storleken på scenen.

### <a name="minimum-hardware-requirements"></a>Minsta maskinvarukrav
* En x86-64-processor med minst 8 kärnor och 32 GB RAM

Till exempel i våra tester på en dator 8 kärnor med Intel Xeon E5-1660 @ 3 GHz och 32 GB RAM-
* En liten scen med 100 avsökningar kan ta ungefär två timmar för en grov ändamålet eller 32 timmar för en bra ändamålet.
* En medelstor scen med 1000 avsökningar kan ta ungefär 20 timmar för en grov ändamålet eller 21 dagar för en bra ändamålet.

### <a name="setup-docker"></a>Konfigurera Docker
Installera och konfigurera Docker på den dator som kommer att bearbeta simuleringen-
1. Installera den [Docker verktygsuppsättningen](https://www.docker.com/products/docker-desktop).
2. Starta Docker-inställningar, navigera till ”avancerat”-alternativ och konfigurera resurser för att ha minst 8GB RAM-minne. Fler processorer som du kan allokera till Docker, desto snabbare ändamålet slutförs. ![Skärmbild av exempel på Docker-inställningar](media/docker-settings.png)
3. Navigera till ”delade enheter” och aktivera delning av enheten som används för bearbetning.![Skärmbild av Docker delade Enhetsalternativ](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Kör lokala ändamålet
1. Klicka på ”förbereda lokala skapa” på den **skapa** fliken och välj en mapp där indatafiler och köra skript kommer att sparas. Du kan sedan köra ändamålet på valfri dator så länge som den uppfyller minimikraven på maskinvara och har Docker installerat genom att kopiera mappen till den datorn.
2. Starta simuleringen med hjälp av ”runlocalbake.bat”-skriptet. Det här skriptet ska hämta projektet Akustik Docker-avbildning med verktyg som behövs för simulering bearbetning och starta simuleringen. 
3. När simuleringen har slutförts kan du kopiera den resulterande filen .ace tillbaka till ditt Unity-projekt. Kontrollera Unity tolkas som en binär fil genom att lägga till ”.bytes” filnamnstillägget (till exempel ”Scene1.ace.bytes”). Detaljerade loggar för simuleringen lagras i ”AcousticsLog.txt”. Om du stöter på problem, kan du dela den här filen som hjälper till med diagnos.

## <a name="Data-Files"></a> Filer har lagts till av ändamålet-process

Det finns fyra datafiler som skapades under processen för ändamålet. En innehåller simulering resultatet och levereras tillsammans med rubriken. De andra lagra redigeringsprogram för Unity-relaterade data.

Simuleringsresultat:
* **Tillgångar/AcousticsData/Akustik\_[SceneName].ace.bytes**: Detta är uppslagstabellen och innehåller simuleringsresultat och voxelized akustiska scen element. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.

Var noga så att inte ta bort resultatfilen simulering. Det är inte återställningsbara utom av rebaking scenen.

Redigeraren för filer:
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: Den här filen lagrar du anger i fälten i Användargränssnittet för Akustik. Platsen och namnet på den här filen kan inte ändras.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Den här filen lagras voxelized Akustik geometrin och samband egenskaperna som beräknas med hjälp av den **Calculate...**  på fliken avsökningar. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Den här filen lagras simulering parametrar beräknas med hjälp av den **Calculate...**  knappen på den **avsökningar** fliken. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurera Akustik uppslagstabell
Dra och släpp den **projekt Akustik** prefab från projektpanelen i din scen:

![Skärmbild av Akustik prefab i Unity](media/acoustics-prefab.png)

Klicka på den **ProjectAcoustics** spel objektet och gå till dess inspector panel. Ange platsen för ändamålet resultatet (. FÖRBERED-fil i **tillgångar/AcousticsData**) genom att dra och släppa den till skriptet Akustik Manager eller genom att klicka på knappen cirkel bredvid textrutan.

![Skärmbild av Akustik Manager prefab i Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Nästa steg
* Utforska den [utforma kontroller för Unity](unity-workflow.md)
* Utforska den [projekt Akustik designbegrepp](design-process.md)

