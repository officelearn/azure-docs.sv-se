---
title: Skapa Akustik med projekt Akustik
titlesuffix: Azure Cognitive Services
description: Det här dokumentet beskriver processen för att skicka in en Akustik ändamålet med hjälp av Redigeraren för Unity-tillägget.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c37e050cd762cb173d64f78b5267e4ad252d17a9
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902254"
---
# <a name="bake-acoustics"></a>Skapa Akustik

Det här dokumentet beskriver processen för att skicka in en Akustik ändamålet med hjälp av Redigeraren för Unity-tillägget. Mer bakgrundsinformation om Akustik Se [vad är Akustik](what-is-acoustics.md). Finns en Snabbstartsguide i [komma igång](getting-started.md).

## <a name="import-the-plugin"></a>Importera plugin-programmet

Importera Akustik-plugin-paketet i projektet. Öppna sedan Akustik-Användargränssnittet genom att välja **Fönster > Akustik** Unity-menyn:

![Öppna Akustik fönster](media/WindowAcoustics.png)

## <a name="principles"></a>Principer

Fönstret Akustik verktyget samlar in information i Akustik motor som behövs för att beräkna Akustik för din scen. Det finns fem steg för att göra en ändamålet:

1. Skapa eller markera din player navigering nät
2. Markera Akustik geometri
3. Tilldela geometri akustiska material egenskaper
4. Förhandsgranska avsökningen placering
5. Skapa

När ändamålet är klar kan du se [Design processöversikt för Akustik](design-process.md) valfritt efter ändamålet design anvisningar.

## <a name="create-or-mark-a-navigation-mesh"></a>Skapa eller markera ett navigering nät

Ett navigering-nät används för att placera avsökningen punkter för simulering. Du kan använda Unity's [navigering nät arbetsflöde](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), eller ange din egen navigering nät. Navigering nät som skapats med arbetsflödet för Unitys hämtas av Akustik systemet. Om du vill använda din egen nät, markera dem från den **objekt** fliken enligt beskrivningen i nästa steg.

## <a name="objects-tab"></a>Fliken objekt

Öppna den **objekt** fliken den **Akustik** fönster. Använd den här fliken kan du markera objekt i din scen som lägger bara till den **AcousticsGeometry** eller **AcousticsNavigation** komponenter till objektet. Du kan också använda den [standard Unity komponenten arbetsflöde](https://docs.unity3d.com/Manual/UsingComponents.html) att markera eller avmarkera objekt.

Välj en eller flera objekt i fönstret scen eller hierarki och sedan markera eller avmarkera dem för **AcousticsGeometry** eller **AcousticsNavigation** enligt beskrivningen nedan. Om inget är markerat kan du markera eller avmarkera allt innehåll i scenen på samma gång.

Endast nät renderare och Terrains kan markeras. Alla andra typer av objekt ignoreras. Kryssrutorna ska markera eller avmarkera alla påverkade objekt.

Om du har inget valt i din scen ser den ut som på följande bild:

![Objekt fliken ingen markering](media/ObjectsTabNoSelectionDetail.png)

Om du har något valde i fönstret scen eller hierarki, ser den ut som på följande bild:

![Objekt fliken ingen markering](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Objekt fliken delar

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

Om vissa objekt markeras och andra inte, visas en ”blandat” värdet i respektive kryssruta:

![Blandat värde kryssrutan](media/MixedObjectSelectionDetail.png)

Klicka på kryssrutan tvingar alla objekt har markerats och klickar på igen kommer avmarkera alla objekt.

Objekt kan markeras för både geometri och navigering.

### <a name="guidelines-for-marking-objects"></a>Riktlinjer för att markera objekt

Se till att markera alla objekt som **Akustik geometri** om de ska occlude återspeglar eller absorbera ljud. Akustik geometri kan vara grunden, väggar, tak, windows och fönstret glas, mattor och stora möbler. Det är Okej att inkludera mindre objekt, till exempel ljus, dekorativa element, armaturerna, eftersom de inte avsevärt öka ändamålet kostnad. Det är viktigt att inte missa viktiga element, till exempel grunden eller ett tak. Dessutom omfatta inte saker som inte ska påverka Akustik, till exempel kollision nät.

Ett objekts transformeringen vid tidpunkten för avsökning beräkningen (via den **avsökningar** fliken nedan) har lösts i resultaten för ändamålet. Flytta någon av de markerade objekten i scenen behöver göra om avsökningen beräkningen och rebaking scenen.

## <a name="materials-tab"></a>Material fliken

När dina objekt markeras, klickar du på den **material** för att gå till fliken material.

### <a name="parts-of-the-materials-tab"></a>Delar av fliken material

![Material fliken information](media/MaterialsTabDetail.png)

1. Den **material** fliken knapp, som används för att få fram den här sidan.
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan.
3. Markera endast material som används av objekt som markerats som **Akustik geometri** visas. I annat fall visas allt material som används i scenen.
4. Använd dessa alternativ för att ändra ordningen på den nedrullningsbara menyn som visas när du klickar på en listruta i kolumnen Akustik nedan (6). **Namn på** sorterar akustiska material efter namn. ”Absorptivity” sorterade efter absorptivity från låg till hög.
5. Lista över material som används i scenen sorteras alfabetiskt. Om den **markerats Visa endast** kryssrutan är markerad (#3), endast material som används av objekt som markerats som **Akustik geometri** visas. När du klickar på ett material här Markera alla objekt i scenen som använder materialet.
6. Visar det akustiska materialet att scen materialet har tilldelats. Klicka på en listruta för att tilldela en scen material till en annan akustiska material. Du kan ändra sorteringsordningen av menyn som visas när du klickar på ett objekt här med den **sortera Akustik efter:** alternativ ovan (#4).
7. Visar den akustiska absorptionskoefficienten material som valde i föregående kolumn. Värdet noll innebär perfekt reflekterande (inga absorption), samtidigt som värdet 1 innebär perfekt absorptive (inga reflektion). Absorptionskoefficienten kan inte ändras, såvida inte det valda materialet är ”anpassad”.
8. För ett material som tilldelats till ”anpassad”, skjutreglaget inaktiveras inte längre och du kan välja absorptionskoefficienten med hjälp av skjutreglaget eller genom att skriva in ett värde. Mer information om väsentliga egenskaper finns i [Design processöversikt för Akustik](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Riktlinjer för att tilldela material (eller absorption värden)

Den här fliken som försöker gissa ditt material för att absorbera är baserat på namnet. Om din scen samband namn är LivingRoom_WoodTable, kommer den första akustiska material som tilldelats att ”trä”. Material som vid en känd material inte kan fastställas tilldelas material ”standard”, som har absorption liknar konkret.

Du kan omtilldela akustiska material för att varje scen material. Till exempel om ett rum ljud för reverberant ändra akustiska materialet väggar, våning eller taket till något högre absorptivity. Akustiska samband tilldelningen gäller för alla objekt som använder scen materialet.

## <a name="probes-tab"></a>Avsökningar fliken

När du har tilldelat material, växla till den **avsökningar** fliken.

### <a name="parts-of-the-probes-tab"></a>Delar av fliken avsökningar

![Avsökningar fliken information](media/ProbesTabDetail.png)

1. Den **avsökningar** fliken knapp som används för att få fram den här sidan
2. En kort beskrivning av vad du behöver göra med hjälp av den här sidan
3. Du kan använda dessa för att välja en grov eller bra simulering upplösning. Grov är snabbare, men har vissa kompromisser. Se [”grov vs Fine lösning”](#Coarse-vs-Fine-Resolution) nedan för information.
4. Välj platsen där Akustik datafiler ska placeras med hjälp av det här fältet. Klicka på knappen med ”...” för att använda en Mappväljare för. Standardvärdet är **tillgångar/AcousticsData**. En **redigeraren** undermapp skapas även under den här platsen. Läs mer om datafiler [”filer”](#Data-Files) nedan.
5. Datafilerna för den här scen namnges med prefixet som anges här. Standardvärdet är ”Acoustics_ [scen Name]”.
6. När avsökningar har beräknats, inaktiveras kontrollerna som ovan. Klicka på den **Rensa** knappen för att radera beräkningar och aktivera kontroller så att du kan beräkna om med nya inställningarna.
7. Klicka på den **beräkna...**  för att voxelize scenen och beräkna avsökningen-objekt. Detta utförs lokalt på din dator och måste göras innan du gör en ändamålet.

I den här versionen av projektet Akustik avsökningar kan inte placeras manuellt och måste placeras via den automatiska processen som anges i den **avsökningar** fliken.

### <a name="what-the-calculate-button-calculates"></a>Knappen ”beräkna...” beräknar

Den **beräkna...**  tar alla data som du har angett (geometri, navigering, material och inställningen grov/Fine) och går igenom flera steg:

1. Det tar geometrin från scenen nät och beräknar en voxel volym. Voxel volymen är en 3-dimensionell volym som omsluter din hela scen och består av små m3 ”voxels”. Storleken på voxels bestäms av simulering frekvens, vilket anges genom den **simulering lösning** inställningen. Varje voxel har markerats som antingen ”öppna luften” eller som innehåller scen geometri. Om en voxel innehåller geometri märks voxel med absorptionskoefficienten material som tilldelats den geometrin.
2. Därefter använder navigering data för att beräkna akustiskt intressanta platser där spelaren försätts. Försök att hitta en stor uppsättning platserna som innehåller mindre områden, till exempel dörrar och dvs och sedan till rum, öppna blanksteg. För små scener är detta vanligtvis färre än 100 platser, medan stora scener kan ha upp till tusen.
3. För var och en av de sista lyssnare platser som beräknar avgör den ett antal parametrar, till exempel hur ”öppet” är utrymmet, storleken på det utrymme som den tillhör, osv.
4. Resultatet av dessa beräkningar lagras i filer på den plats du anger (se [”filer”](#Data-Files) nedan)

Dessa beräkningar kan ta flera minuter beroende på storleken på din scen och hastigheten på din dator.

När dessa beräkningar är klar kan förhandsgranska du både voxel data och avsökning-objekt för att säkerställa att ändamålet ger dig goda resultat. Sådant som ett felaktigt navigering nät eller saknas/extra geometri vanligtvis syns snabbt i förhandsversionen, så du kan korrigera den.

### <a name="scene-rename"></a>Byt namn på scen

Scennamnet används för att ansluta scenen till lagring avsökningen punkt placering och voxelization-filer. Om scenen har bytt namn när avsökningen beräknas, material tilldelning och placering av data går förlorad och ska köras igen.

## <a name="debug-display-through-gizmos"></a>Felsöka via Gizmos

Som standard både den **avsökningar** och **Voxels** gizmos är aktiverade. Dessa visas voxels och avsökning peka platser som beräknades. De kan aktiveras eller inaktiveras med hjälp av menyn Gizmos:

![Gizmos menyn](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Voxels visas i fönstret scen som grön kuber runt deltagande geometri. Voxels som innehåller endast air visas inte. Det finns en stor grön ruta runt hela scenen som anger den fullständiga voxel volym som ska användas i simuleringen.
Flytta din scen och kontrollera att allt som ska vara geometri har voxels. Scen kameran måste vara inom cirka 5 taxor för objektet för voxels ska visas.

Om du jämför voxels som skapats med grov upplösning vs bra lösning, visas grov voxels är två gånger så stora.

![Voxel förhandsversion](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Avsökningen punkter

Avsökningen punkter är synonyma med möjliga player (lyssnare)-platser. När du gör en ändamålet beräknas Akustik för en bra källa var som helst i scenen för var och en av de här punkterna för avsökning. Om spelaren inte är direkt på en plats för avsökning punkt, används data från avsökningen punkterna närmast spelaren för interpolation parametrar på den platsen.

Därför är det viktigt att se till att avsökningen punkter finns var som helst spelaren förväntas färdas i scenen och att små områden och dörrar korrekt visas. Avsökningen punkter kan inte placeras av projektet Akustik motorn baserat på tolkning av din scen geometri och flyttas eller redigeras i förhandsversionen, Designer. Använd dem i stället att kontrollera korrektheten av geometry-märkning och navigering tipset data.

![Avsökningar förhandsversion](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Grov vs bra lösning

Den enda skillnaden mellan upplösning grov och Fine är den frekvens med vilken simuleringen utförs. Fine använder en frekvens som är dubbelt så stort som grov.
Även om det kan verka enkla, har ett antal effekter på akustiska simuleringen:

* Våglängd för grov är två gånger så länge som bra och därför voxels är två gånger så stora.
* Simulering tiden är direkt relaterade till voxel-storlek, vilket gör en grov skapa cirka 16 gånger snabbare än en bra ändamålet.
* Vara det går inte att simuleras portaler (till exempel dörrar eller windows) och mindre än storleken voxel. Grov inställningen kan göra att vissa av dessa mindre portaler för inte simuleras; Därför kan skickar de inte ljud via vid körning. Du kan se om detta sker genom att visa voxels.
* Lägre simulering frekvensen resulterar i mindre diffraction runt hörn och kanter.
* Ljud källor får inte finnas inuti ”fyllts” voxels, som är voxels som innehåller geometri – detta resulterar i utan ljud. Det är svårare att hitta bra källor så att de inte är i större voxels av grov än den använder inställningen bra.
* Större voxels kommer intrude mer till portaler, enligt nedan. Den första bilden har skapats med hjälp av grov, medan andra är samma nyckeln med hjälp av bra lösning. Som anges med röd markeringar, är det mycket mindre intrång i nyckeln med hjälp av bra inställningen. Den blå linjen är nyckeln som definieras av geometri, medan den röda linjen är effektiva akustiska portalen definieras av voxel storlek. Hur den här intrång spelar i en viss situation beror helt på hur voxels stämmer överens med portalen, vilket avgörs av storlek och platserna för dina objekt i scenen geometri.

![Grov nyckeln](media/CoarseVoxelDoorway.png)

![Bra nyckeln](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Skapa fliken

När du är nöjd med förhandsgranskningsdata kan du använda den **skapa** flik för att skicka ändamålet till molnet.

### <a name="parts-of-the-bake-tab"></a>Delar av fliken ändamålet

![Skapa fliken](media/BakeTabDetails.png)

1. Knappen Skapa flik används för att ta fram den här sidan.
2. En kort beskrivning av vad du gör på den här sidan.
3. Fält att ange dina autentiseringsuppgifter för Azure när du har skapat ditt Azure-konto. Mer information finns i [skapa ett Azure Batch-konto](create-azure-account.md).
4. Docker bildtagg för Akustik verktygsuppsättningen.
5. Starta Azure-portalen för att hantera dina prenumerationer, övervaka användning och visa faktureringsinformation osv. 
6. Azure batch-beräkningsnod nodtyp för beräkningen. Nodtypen måste stödjas av din Azure data center plats. Om det inte att lämna på **Standard_F8s_v2**.
7. Antalet noder som ska användas för den här beräkningen. Det antal som du anger här påverkar hur lång tid att slutföra ändamålet och begränsas av Azure Batch core allokeringen. Standardallokeringen kan du bara tillåter två 8 kärnor noder eller ett 16 kärnor nod, men kan utökas. Mer information om core allokering begränsningar finns i [skapa ett Azure Batch-konto](create-azure-account.md).
8. Markera kryssrutan för att konfigurera din beräknings-pool för att använda [lågprioritetsnoder](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Beräkningsnoder med låg prioritet har mycket lägre kostnad, men de kanske inte alltid är tillgänglig eller kan avbrytas när som helst.
9. Antalet avsökning för din scen som beräknas på den **avsökningar** fliken. Antal avsökningar avgör hur många simuleringar som måste köras i molnet. Du kan inte ange fler noder än vad som finns på avsökningar.
10. Mängden tid som gått för det förväntas ta för jobbet ska köras i molnet. Detta inkluderar inte starttiden för noden. Detta är om hur lång tid det bör vara innan du kommer tillbaka resultaten när jobbet börjar köras. Observera att detta är endast en uppskattning.
11. Den totala arbetstiden som behövs för att köra simuleringar. Det här är den totala mängden noden beräkningstiden som ska användas i Azure. Se [Estimating ändamålet kostnaden](#Estimating-bake-cost) nedan för mer information om hur du använder det här värdet.
12. Det här meddelandet visar var resultatet av ändamålet ska sparas när jobbet har slutförts.
13. (Endast för avancerad användning) Om du skickat av någon anledning att tvinga Unity att glömma en ändamålet (t.ex. du har hämtat resultat i en annan dator), klickar du på den **rensa tillstånd** knappen att glömma om jobbet som har skickats. Alltså resultatfilen, när du är klar, kommer **inte** laddas ned och **detta är inte detsamma som att avbryta jobbet**. Jobbet fortsätter om kör, att köras i molnet.
14. Klicka på knappen ändamålet om du vill skicka ändamålet till molnet. När ett jobb körs, visar detta **Avbryt jobb** i stället.
15. Förbereder för att bearbeta Akustik simulering på lokala datorer. Se [lokala ändamålet](#Local-bake) för mer information.  
16. Det här området visar status för ändamålet. När du är klar bör du se **hämtade**.

Du kan alltid få fullständig information om aktiva jobb, beräkningspooler och lagring på den [Azure-portalen](https://portal.azure.com).

När ett jobb körs den **skapa** knappen ändras till **Avbryt jobb**. Använd den här knappen för att avbryta pågående jobb. Du blir ombedd att bekräfta innan jobbet avbryts. Avbryter ett jobb kan inte ångra, inga resultat att bli tillgänglig och du kommer fortfarande att debiteras för alla Azure beräkningstiden som används.

När du har startat en ändamålet, kan du stänga Unity. En cloud-ändamålet kan ta flera timmar beroende på projektet, nodtyp och antalet noder. Ändamålet jobbets status kommer att uppdateras när du uppdatera projektet och öppna fönstret Akustik. Om jobbet har slutförts, laddas utdatafilen ned.

Azure-autentiseringsuppgifterna lagras på ett säkert sätt på den lokala datorn och som är associerade med dina Unity-redigeraren. De används endast för att upprätta en säker anslutning till Azure.

### <a name="Estimating-bake-cost"></a> Beräknar Azure ändamålet kostnad

Om du vill beräkna vad en viss ändamålet kostar måste ta värdet som visas för **beräknade Compute kostnad**, vilket är en varaktighet och multiplicera som av de varje timme kostar i din lokala valuta av den **VM nodtyp** du har valt. Resultatet innehåller inte noden tiden behövs för att komma noderna och körs. Exempel: Om du väljer **Standard_F8s_v2** för nodtypen, vilket kostar $ 0,40/timme, och den uppskattade kostnaden för Compute är 3 timmar och 57 minuter, den uppskattade kostnaden för att köra jobbet kommer att $0,40 * ~ 4 timmar = ~ 1,60. Verklig kostnad kommer antagligen vara lite högre på grund av den extra tid att hämta de noder som är igång. Du kan hitta noden per timme kostnaden på den [priser för Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Välj ”Beräkningsoptimerad” eller ”databehandling med höga prestanda” för kategorin).

### <a name="reviewing-the-bake-results"></a>Granska resultaten ändamålet

När ändamålet har slutförts kan du kontrollera att voxels och avsökning punkter är i deras förväntade platser genom att köra runtime-plugin-programmet. Mer information finns i [Design processöversikt för Akustik](design-process.md).

## <a name="Local-bake"></a>Lokala ändamålet
Lokala ändamålet körs Akustik simulering på din dator i stället för att överföra det till Azure Batch-kluster. Detta kan vara ett bra alternativ för att experimentera med Akustik utan att kräva en Azure-prenumeration. Observera att Akustik simuleringen beräkningsmässigt krävande och kan ta lång tid beroende på storleken på scen, simulering konfiguration och raw datorkraft för bearbetning av datorn.

### <a name="minimum-hardware-requirements"></a>Minsta maskinvarukrav
64-bitars Intel-processor med minst 8 kärnor och 32 GB RAM-minne eller högre.

Till exempel på en dator med 8 kärnor med Intel Xeon E5-1660 @ 3 GHz och 32 GB RAM-
* Liten scen med 100 avsökningar tar ~ 2 timmar för en grov ändamålet och ~ 32 timmar för en bra lösning ändamålet.
* Större scen med 1000 avsökningar kan ta upp till ~ 20 timmar för en grov upplösning och ~ 21 dagar för en bra lösning ändamålet.

### <a name="setup-docker"></a>Konfigurera Docker
Installera och konfigurera Docker på den dator som kommer att bearbeta simuleringen-
1. Installera den [Docker verktygsuppsättningen](https://www.docker.com/products/docker-desktop).
2. Starta Docker-inställningar, navigera till ”avancerat” alternativ och konfigurera resurser om du vill ha på minst 8 GB RAM-minne. Fler processorer som du kan allokera till Docker, desto snabbare ändamålet slutförs. ![Exempel på Docker-inställningar](media/DockerSettings.png)
3. Navigera till ”delade enheter” och aktivera delning av enheten som används för bearbetning.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Kör lokala ändamålet
1. Klicka på ”förbereda lokala skapa” ändamålet-fliken och välj en mapp där indatafiler och köra skript kommer att sparas. Du kan sedan köra ändamålet på valfri dator så länge som den uppfyller minimikraven på maskinvara och har Docker installerat genom att kopiera mappen till den datorn.
2. Starta simuleringen med hjälp av ”runlocalbake.bat”-skriptet. Det här skriptet ska hämta projektet Akustik Docker-avbildning med verktyg som behövs för simulering bearbetning och starta simuleringen. 
3. När simuleringen har slutförts kan du kopiera den resulterande filen .ace tillbaka till ditt Unity-projekt. Kontrollera Unity tolkas som en binär fil genom att lägga till ”.bytes” filnamnstillägget (till exempel ”Scene1.ace.bytes”). Detaljerade loggar för simuleringen lagras i ”AcousticsLog.txt”. Om du stöter på problem, kan du dela den här filen som hjälper till med diagnos.

## <a name="Data-Files"></a>Datafiler

Det finns fyra datafiler som skapats av det här plugin-programmet vid olika tidpunkter. Endast en av dem krävs vid körning, de övriga tre är därför i mappar med namnet ”Editor” så att de inte kompileras till ditt projekt.

* **Tillgångar och redigerare / [SceneName]\_AcousticsParameters.asset**: den här filen lagrar data som du anger i fälten i Användargränssnittet för Akustik. Platsen och namnet på den här filen kan inte ändras. Det finns andra värden som lagras i den här filen som påverkar ändamålet, men de är för avancerade användare och ska inte ändras.
* **Tillgångar/AcousticsData/Akustik\_[SceneName].ace.bytes**: den här filen är vad skapas under ändamålet simuleringen och innehåller lookup-data som används av körningen för att återge Akustik av din scen. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: här lagras voxelized Akustik geometri och egenskaper för material. Beräknas med hjälp av den **Calculate...**  på fliken avsökningar. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.
* **Tillgångar/AcousticsData/redigeraren/Akustik\_[SceneName]\_config.xml**: här lagras parametrar som beräknas med hjälp av den **Calculate...**  knappen på den **avsökningar** fliken. Platsen och namnet på den här filen kan ändras utifrån fälten i den **avsökningar** fliken.

Var försiktig så att inte ta bort den *. ace.bytes har laddat ned från ändamålet. Den här filen inte är återställningsbara utom vid rebaking scenen.

## <a name="next-steps"></a>Nästa steg
* Tillämpa ändamålet resultat till ljud källor i [Design processöversikt för Akustik](design-process.md).

