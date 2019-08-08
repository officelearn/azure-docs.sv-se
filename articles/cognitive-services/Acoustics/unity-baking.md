---
title: Själv studie kurs om projekt akustiska enhet
titlesuffix: Azure Cognitive Services
description: I den här självstudien beskrivs akustiska ljud med projekt akustiska i Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e26df58de08d0941b5e3165852ed0b26f8890f66
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854936"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Själv studie kurs om projekt akustiska enhet
I den här självstudien beskrivs akustiska ljud med projekt akustiska i Unity.

Program varu krav:
* [Unity 2018.2 +](https://unity3d.com) för Windows
* [Project Akustiske-plugin-program som är integrerad i ditt Unity-projekt](unity-integration.md) eller i avsnittet [Project akustisker union exempel innehåll](unity-quickstart.md)
* Valfritt: Ett [Azure Batch konto](create-azure-account.md) för att påskynda bagerier med molnbaserad data behandling

## <a name="open-the-project-acoustics-bake-window"></a>Öppna fönstret projekt akustiskt i bak-fönstret
Välj **fönster > ljud** från menyn uppmenyn:

![Skärm bild av meny alternativet Unitbar Editor med akustiskt fönster markerat](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Skapa ett navigerings nät
Projekt akustiska använder ett navigerings nät för att placera lyssnar avsöknings punkter för simulering. Du kan använda enhetens [navigerings nät arbets flöde](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)eller använda ett annat 3D-modellerings paket för att utforma ditt eget nät. 

## <a name="mark-acoustic-scene-objects"></a>Markera akustiska scen objekt
Projekt akustiskt förlitar sig på två typer av scen objekt för simulering: objekten som reflekterar och occlude ljud i simuleringen och navigerings nätet i Media Player som begränsar lyssnar avsöknings punkter i simuleringen. Båda objekt typerna markeras med hjälp av fliken **objekt** . 

Eftersom märkning av objekt bara lägger till **AcousticsGeometry** -eller **AcousticsNavigation** -komponenterna i objektet, kan du också använda [arbets flödet standard Unity Component](https://docs.unity3d.com/Manual/UsingComponents.html) för att markera eller avmarkera objekt. Endast nät åter givning och terränger kan markeras. Alla andra objekt typer kommer att ignoreras. Kryss rutorna markerar eller avmarkerar alla berörda objekt.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Markera akustiskt ocklusion och reflektions geometri
Öppna fliken **objekt** i fönstret **akustiska** objekt. Markera alla objekt som **akustiska geometrier** om de ska occlude, reflektera eller absorbera ljud. Akustiska geometrier kan innehålla saker som mark, väggar, tak, Windows & fönster glas, Rugs och stora möbler. Du kan använda valfri godtycklig komplexitets nivå för dessa objekt. Eftersom scenen är voxelized före simuleringen är mycket detaljerade maskor, t. ex. träd med många små löv, inte mer kostsamt för att kunna flyttas över enkla objekt.

Inkludera inte saker som inte påverkar akustiskt, t. ex. osynliga kollisions nät.

Ett objekts transformering vid tidpunkten för avsöknings beräkningen (via fliken avsökningar nedan) åtgärdas i bageri resultatet. Om du flyttar något av de markerade objekten i scenen måste du göra om avsöknings beräkningen och flytta scenen.

### <a name="mark-the-navigation-mesh"></a>Markera navigerings nätet
Navigations nät som skapats med enhets arbets flödet hämtas av akustiskt system. Om du vill använda dina egna nät markerar du dem på fliken **objekt** .

### <a name="for-reference-the-objects-tab-parts"></a>Som referens: Fliken objekt delar
Webb sidans delar är:

1. Fliken Val knappar (fliken**objekt** har valts). Använd dessa knappar för att gå igenom de olika stegen för att göra en akustiskt bak från vänster till höger.
2. En kort beskrivning av vad du behöver göra med den här sidan.
3. Tillgängliga filter för fönstret hierarki. Använd detta för att filtrera hierarkifönstret till objekt av den angivna typen så att du enkelt kan markera dem. Om du ännu inte har markerat något för akustiska alternativ visas inga av de två sista alternativen. De kan dock vara användbara för att hitta markerade objekt när du har gjort det.
4. När inga objekt har marker ATS visar det här avsnittet status för alla objekt i scenen:
    * Totalt-totalt antal aktiva, icke-dolda objekt i scenen.
    * Ignoreras – antalet objekt som inte är nät åter givningar eller terränger.
    * Nät – antalet nät åter givnings objekt i scenen
    * Terräng – antalet terräng objekt i scenen
    * Geometry – antalet nät-eller terräng objekt i den scen som har marker ATS som "akustisk geometri"
    * Navigering – antalet nät-eller terräng objekt i scenen som har marker ATS som "akustiska navigering". Det här talet inkluderar inte units NavMesh.
5. Visar det totala antalet objekt som kan markeras i scenen, som endast är nät åter givning och terräng. Visar kryss rutor som du kan använda för att markera (lägga till lämplig komponent till) objekt som geometri eller navigering för akustiska enheter
6. När inget är markerat påminner den här kommentaren dig om att välja objekt som ska markeras vid behov. Du kan också markera en eller båda kryss rutorna för att markera alla objekt i scenen utan att välja något.
7. När objekt är markerat visar det här avsnittet endast status för de valda objekten.
8. Visar det totala antalet markerade objekt som kan markeras. Genom att markera eller avmarkera kryss rutan markeras eller avmarkeras bara de markerade objekten.

Om du inte har valt något i din scen kommer fliken objekt att se ut som på följande bild:

![Skärm bild av fliken akustiska objekt utan något val](media/objects-tab-no-selection-detail.png)

Om du har valt något i din scen-eller hierarki-fönster ser det ut som på följande bild:

![Skärm bild av fliken akustiska objekt med markering visas](media/objects-tab-selection-detail.png)

Om några objekt har marker ATS och vissa inte är det visas ett "blandat" värde i motsvarande kryss ruta:

![Skärm bild av fliken akustiska objekt med blandad markerings ikon markerad](media/mixed-object-selection-detail.png)

Om du klickar på kryss rutan tvingas alla objekt att markeras och om du klickar på igen avmarkeras alla objekt.

Objekt kan markeras för både geometri och navigering.

## <a name="select-acoustic-materials"></a>Välj akustiskt material
När dina objekt har marker ATS klickar du på knappen **material** och tilldelar akustiskt material. Projektet akustiskt material system är knutet till system för enhetens visuella material: för att två objekt ska ha separat akustiskt material måste de ha separata visuella material.

Akustiskt material styr mängden ljud energi som återspeglas från varje yta. Standard akustiska materialet har absorption på liknande sätt som betong. Projekt akustiska förslag ger material baserat på namnet på det visuella materialet. Du kan tilldela det akustiska materialet "anpassat" till ett material för att aktivera ett absorptions effektivt skjutreglage.

Reverberation tiden för ett material i ett rum är i motsatt förhållande till dess absorptions-koefficient, med merparten av materialet med absorptions värden i 0,01 till 0,20-intervallet. Material med absorptions koefficienter utanför det här intervallet är mycket absorberande.

![Diagram över negativ korrelation av Reverberation tid med absorptions koefficient](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Som referens: Delar av fliken material
![Skärm bild av fliken akustiskt material i Unity](media/materials-tab-detail.png)

1. Knappen **material** -fliken som används för att öppna den här sidan.
2. En kort beskrivning av vad du behöver göra med den här sidan.
3. När det här alternativet markeras visas endast material som används av objekt som är markerade som **akustiska geometri** . Annars visas allt material som används i scenen.
4. Använd de här alternativen om du vill ändra ordningen på list menyn som visas när du klickar på en listruta i den akustiska kolumnen nedan (#6). **Namn** sorterar akustiskt material efter namn. "Absorptivity" sorterar dem i ordningen av Absorptivity från låg till hög.
5. Listan med material som används i scenen, sorterad i alfabetisk ordning. Om kryss rutan **Visa endast markerade** är markerad (#3) visas endast material som används av objekt som är markerade som **akustiska geometri** . Om du klickar på ett material här markeras alla objekt i den scen som använder det materialet.
6. Visar det akustiska material som scen materialet har tilldelats. Klicka på en listruta för att omtilldela ett scen material till ett annat akustiskt material. Du kan ändra sorterings ordningen för den meny som visas när du klickar på ett objekt här med hjälp av **sorterings ljuden genom att:** alternativen ovan (#4).
7. Visar koefficienten för akustisk absorption för det material som valts i föregående kolumn. Värdet noll innebär en perfekt reflekterande (ingen absorption) medan värdet 1 innebär en perfekt absorptive (ingen reflektion). Det går inte att ändra absorptions koefficienten om inte det valda materialet är "anpassat".
8. För ett material som har tilldelats "Custom" är skjutreglaget inte längre inaktiverat och du kan välja absorptions koefficienten med skjutreglaget eller genom att skriva in ett värde.

## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska lyssnare avsöknings platser
När du har tilldelat materialet växlar du till fliken avsökningar och klickar på **Beräkna** för att placera lyssnar avsöknings punkter för simulering.

### <a name="what-the-calculate-button-calculates"></a>Vad är "beräkna..." knappen beräknar
Med hjälp av knappen **Beräkna...** används din valda akustiska scens geometri för att förbereda din scen för simulering:

1. Den tar geometrin från scenens maskor och beräknar en Voxel volym. Voxel-volymen är en 3-dimensionell volym som omsluter hela scenen och består av liten kubisk "voxels". Storleken på voxels bestäms av simulerings frekvensen, som anges av inställningen för **simulerings upplösning** . Varje Voxel markeras som antingen "öppen luft" eller innehåller en scen geometri. Om en Voxel innehåller geometri taggas Voxel med absorptions koefficienten för det material som har tilldelats den geometrin.
2. Det använder navigations nät (ES) för att placera lyssnar avsöknings punkter. Algoritmen balanserar de konkurrerande problemen med Spatial täckning och simulerings tid och fil storlek, samtidigt som du ser till att smala korridorer och små utrymmen alltid har en viss mängd täckning. En typisk avsöknings punkt räknar mellan 100 för små scener till några tusen för stora scener.

Beroende på storleken på din scen och datorns hastighet kan dessa beräkningar ta flera minuter.

### <a name="review-voxel-and-probe-placement"></a>Granska Voxel och avsöknings placering
Förhandsgranska både Voxel-data och platsen för avsöknings punkter för att se till att du är redo att klistra in din scen. Ett ofullständigt navigations nät eller en extra akustisk geometri visas vanligt vis snabbt i förhands granskningen. Placering av Voxel och avsökning kan aktive ras eller inaktive ras med Gizmos-menyn:

![Skärm bild av Gizmos-menyn i Unity](media/gizmos-menu.png)

Voxels som innehåller akustisk geometri visas som gröna kuber. Utforska din scen och kontrol lera att allt som ska vara geometri har voxels. Scen kameran måste vara inom cirka 5 meter av objektet för att voxels ska kunna visas.

Om du jämför voxels som har skapats med grov upplösning jämfört med fin upplösning ser du att de grova voxels är två gånger stora.

![Skärm bild av grov voxels Preview i Unity Editor](media/voxel-cubes-preview.png)

Simulerings resultat interpoleras mellan lyssnar platsens platser vid körning. Kontrol lera att det finns avsöknings punkter nära vilken plats som spelaren förväntas resa i scenen.

![Skärm bild av för hands versionen av avsökningar i Unity Editor](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Ta hand om motivets namn
Scen namnet används för att ansluta scenen till filer som lagrar avsöknings punktens placering och voxelization. Om scenen har fått ett nytt namn när avsöknings punkterna har beräknats, går material tilldelningen och placerings data förlorade och ska köras igen.

### <a name="for-reference-parts-of-the-probes-tab"></a>Som referens: Delar av fliken avsökningar
![Skärm bild av fliken akustiska avsökningar i Unity](media/probes-tab-detail.png)

1. Fliken avsökningar som används för att öppna den här sidan
2. En kort beskrivning av vad du behöver göra med den här sidan
3. Använd dessa för att välja en grov simulerings upplösning. Grovheten går snabbare, men har vissa kompromisser. Mer information finns i artikeln om [problemlösning](bake-resolution.md) nedan.
4. Välj den plats där akustiska datafiler ska placeras med det här fältet. Klicka på knappen med "..." använda en mapp väljare. Standardvärdet är **till gångar/AcousticsData**. En **redigerare** -undermapp kommer också att skapas på den här platsen. Mer information om datafiler finns i [datafiler](#Data-Files) nedan.
5. Datafilerna för den här scenen får namnet med hjälp av det prefix som anges här. Standardvärdet är "Acoustics_ [scen namn]".
6. När avsökningarna har beräknats kommer kontrollerna ovan att inaktive ras. Klicka på **Rensa** om du vill radera beräkningarna och aktivera kontrollerna så att du kan beräkna om med hjälp av nya inställningar.
7. Klicka på knappen **Beräkna...** för att voxelize scenen och beräkna avsöknings platsens platser. Detta görs lokalt på din dator och måste utföras innan du gör en bageri.

I den här versionen av projekt Akustisker går det inte att placera avsökningar manuellt och måste placeras genom den automatiserade processen som finns på fliken avsökningar.

Se [problemlösning](bake-resolution.md) för att få mer information om grova vs-lösningar.

## <a name="bake-your-scene-using-azure-batch"></a>Bageri din scen med Azure Batch
Du kan gå till din scen med ett beräknings kluster i molnet med hjälp av tjänsten Azure Batch. Plugin-programmet för Project Akustiske Unit ansluter direkt till Azure Batch för att instansiera, hantera och riva ned ett Azure Batch-kluster för varje bak. På fliken hemskrivet anger du dina autentiseringsuppgifter för Azure, väljer en kluster dator typ och storlek och klickar på **bak**sidan.

### <a name="for-reference-parts-of-the-bake-tab"></a>Som referens: Delar av fliken bageri
![Skärm bild av fliken akustiskt i enhet](media/bake-tab-details.png)

1. Fliken bageri som används för att öppna den här sidan.
2. En kort beskrivning av vad du kan göra på den här sidan.
3. Fält för att ange dina Azure-autentiseringsuppgifter när ditt Azure-konto har skapats. Mer information finns i [skapa ett Azure Batch-konto](create-azure-account.md).
4. Docker-bildtagg för akustiska verktyg.
5. Starta Azure Portal för att hantera dina prenumerationer, övervaka användning och Visa fakturerings information osv. 
6. Azure Batch Compute Node-nodtyp som ska användas för beräkningen. Nodtypen måste stödjas av Azure Data Center-platsen. Om du inte är säker lämnar du på **Standard_F8s_v2**.
7. Antal noder som ska användas för den här beräkningen. Numret du anger här påverkar tiden för att slutföra bagerien och begränsas av din Azure Batch Core-allokering. Standardallokeringen tillåter bara två 8 core-noder eller 1 16 core-nod, men kan expanderas. Mer information om begränsningar för kärn allokering finns i [skapa ett Azure Batch-konto](create-azure-account.md).
8. Markera den här kryss rutan om du vill konfigurera din Compute-pool för att använda [noder med låg prioritet](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Compute-noder med låg prioritet har mycket lägre kostnad men de kanske inte alltid är tillgängliga eller kan aktive ras när som helst.
9. Antalet avsökningar för din scen enligt beräkningen på fliken avsökningar. Antalet avsökningar bestämmer hur många simuleringar som behöver köras i molnet. Du kan inte ange fler noder än vad som finns i avsökningar.
10. Hur lång tid det förväntas ta för jobbet att köras i molnet. Detta inkluderar inte start tid för nod. När jobbet börjar köras är det hur lång tid det tar innan du får tillbaka resultatet. Observera att detta bara är en uppskattning.
11. Den totala mängden bearbetnings tid som krävs för att köra simuleringarna. Detta är den totala mängden beräknings tid för noder som ska användas i Azure. Mer information om hur du använder det här värdet finns i avsnittet om [uppskattning av bageri kostnader](#Estimating-bake-cost) nedan.
12. Det här meddelandet visar var du kommer att spara resultatet av bagerien när jobbet har slutförts.
13. (Endast avancerad användning) Om du av någon anledning behöver tvinga fram en enhet att glömma bort en bageri som du har skickat (t. ex. hämtade resultat med en annan dator) klickar du på knappen **Rensa tillstånd** för att glömma bort det jobb som har skickats. Observera att det innebär att resultat filen, när det är klart, **inte** laddas ned och att **det inte är detsamma som att avbryta jobbet**. Jobbet fortsätter att köras i molnet om det körs.
14. Klicka på knappen **bak** för att skicka in hembakat till molnet. När ett jobb körs visar detta **Avbryt jobb** i stället.
15. Förbereder bearbetning av [ljud simulering på din dator](#Local-bake).
16. Det här avsnittet visar status för bak sidan. När det är klart bör det Visa **laddat ned**.

Du kan alltid få fullständig information om aktiva jobb, Compute-pooler och lagring på [Azure Portal](https://portal.azure.com).

När ett jobb körs på **bak** -knappen ändras till **Avbryt jobb**. Använd den här knappen om du vill avbryta pågående jobb. Du uppmanas att bekräfta innan jobbet avbryts. Det går inte att ångra ett jobb, inga resultat är tillgängliga och du kommer fortfarande att debiteras för alla Azure-beräknings tider som används.

När du har startat en bageri kan du stänga Unity. Det kan ta flera timmar, beroende på projektet, nodtypen och antalet noder. Status för jobbet uppdateras när du laddar om projektet och öppnar fönstret akustiska. Om jobbet har slutförts kommer utdatafilen att hämtas.

Azure-autentiseringsuppgifterna lagras på ett säkert sätt på din lokala dator och är kopplade till din Unity-redigerare. De används enbart för att upprätta en säker anslutning till Azure.

### <a name="Estimating-bake-cost"></a>Uppskatta kostnad för Azure-bageri

För att uppskatta vad en viss bageri kostar, tar du det värde som visas för uppskattad **beräknings kostnad**, vilket är en varaktighet och multiplicerar det med Tim kostnaden i din lokala valuta för den **VM-nodtyp** som du har valt. I resultatet ingår inte den Node-tid som krävs för att få noderna igång. Om du till exempel väljer **Standard_F8s_v2** för nodtypen, som har kostnaden $0.40/tim, och den uppskattade beräknings kostnaden är 3 timmar och 57 minuter, blir den uppskattade kostnaden för att köra jobbet $0,40 * ~ 4 timmar = ~ $1,60. Den faktiska kostnaden är förmodligen lite högre på grund av extra tiden för att hämta noderna. Du hittar kostnaden för varje timme på sidan [Azure Batch prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Välj "Compute-optimerad" eller "hög prestanda beräkning" för kategorin).

## <a name="Local-bake"></a>Bageri din scen på din dator
Du kan använda din scen på din egen dator. Detta kan vara användbart för att experimentera med akustiska scener med små scener innan du skapar ett Azure Batch-konto. Observera att den akustiska simuleringen kan ta lång tid beroende på scenens storlek.

### <a name="minimum-hardware-requirements"></a>Minimi krav för maskin vara
* En x86-64-processor med minst 8 kärnor och 32 GB RAM

Som exempel i vårt test på en dator med 8 kärnor med Intel Xeon E5-1660 @ 3 GHz och 32 GB RAM-
* En liten scen med 100 avsökningar kan ta ungefär 2 timmar för en grov bageri eller 32 timmar för en fin bageri.
* En medels Tor scen med 1000 avsökningar kan ta ungefär 20 timmar för en grov bageri eller 21 dagar.

### <a name="setup-docker"></a>Konfigurera Docker
Installera och konfigurera Docker på den dator som ska bearbeta simuleringen –
1. Installera Docker- [verktyg](https://www.docker.com/products/docker-desktop).
2. Starta Docker-inställningar, navigera till alternativen Avancerat och konfigurera resurser så att de har minst 8 GB RAM-minne. Ju fler processorer du kan allokera till Docker, desto snabbare kommer bagerien att slutföras. ![Skärm bild av exempel på Docker-inställningar](media/docker-settings.png)
3. Navigera till delade enheter och aktivera delning för den enhet som används för bearbetning.![Skärm bild av alternativ för delad enhet i Docker](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Kör lokalt bak
1. Klicka på knappen "Förbered lokal inbaka" på fliken **bageri** och välj en mapp där indatafilerna och körnings skripten ska sparas. Du kan sedan köra bagerien på valfri dator så länge den uppfyller minimi kraven på maskin vara och har Docker installerat genom att kopiera mappen till den datorn.
2. Starta simuleringen med hjälp av skriptet "runlocalbake. bat". Det här skriptet hämtar Docker-avbildningen för projektet akustiskt med de verktyg som krävs för simulerings bearbetning och starta simuleringen. 
3. När simuleringen är klar kopierar du den resulterande. ACE-filen tillbaka till ditt Unity-projekt. För att se till att Unit känner igen detta som en binär fil lägger du till ". byte" i fil namns tillägget (till exempel "SCENE1. ACE. byte"). Detaljerade loggar för simuleringen lagras i "AcousticsLog. txt". Om du stöter på problem kan du dela den här filen för att hjälpa till med diagnostiseringen.

## <a name="Data-Files"></a>Datafiler som lagts till av processen vid bagerien

Det finns fyra datafiler som skapas under bageri processen. En innehåller simulerings resultatet och medföljer din rubrik. De andra lagrar enhets beroende redigerings data.

Simulerings resultat:
* **Till gångar/AcousticsData/akustisker\_[SceneName]. ACE. byte**: Det här är uppslags tabellen för körning och innehåller simulerings resultat och voxelized akustiska scen element. Du kan ändra plats och namn på den här filen med hjälp av fälten på fliken avsökningar.

Ta hand om att inte ta bort simulerings resultat filen. Den går inte att återvinna, förutom genom att inning av scenen.

Editor-datafiler:
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: Den här filen lagrar de data som du anger i fält i akustiskt användar gränssnitt. Det går inte att ändra den här filens plats och namn.
* **Till gångar/AcousticsData/redaktör/Acoustics_ [SceneName]. VOX**: Den här filen lagrar voxelized akustiska geometri och de material egenskaper som beräknas med hjälp av knappen **Beräkna...** på fliken avsökningar. Du kan ändra plats och namn på den här filen med hjälp av fälten på fliken avsökningar.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Den här filen lagrar simulerings parametrar som beräknats med hjälp av knappen **Beräkna...** på fliken avsökningar. Du kan ändra plats och namn på den här filen med hjälp av fälten på fliken avsökningar.

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurera ljud uppslags tabellen
Dra och släpp **projektet akustiska** Prefab från projekt panelen till din scen:

![Skärm bild av akustiska Prefab i Unity](media/acoustics-prefab.png)

Klicka på **ProjectAcoustics** Game-objektet och gå till panelen kontrollant. Ange platsen för ditt bageri resultat (. ACE-fil, i **till gångar/AcousticsData**) genom att dra och släppa den i skriptet akustiskt Manager eller genom att klicka på cirkel knappen bredvid text rutan.

![Skärm bild av akustiska Prefab i Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Nästa steg
* Utforska [design kontrollerna för Unity](unity-workflow.md)
* Utforska [design koncepten för projekt akustiska metoder](design-process.md)

