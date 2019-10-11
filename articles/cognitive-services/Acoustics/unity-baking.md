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
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243131"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Själv studie kurs om projekt akustiska enhet
I den här självstudien beskrivs akustiska ljud med hjälp av projekt akustiska produkter.

Program varu krav:
* [Unity 2018.2 +](https://unity3d.com) för Windows eller MacOS
* [Plugin-programmet för projekt akustiskt som är integrerat i ditt Unity-projekt](unity-integration.md) eller i avsnittet [Project akustisker Unit-exempel innehåll](unity-quickstart.md)
* *Valfritt:* Ett [Azure Batch konto](create-azure-account.md) för att påskynda bagerien med hjälp av molnbaserad data behandling

## <a name="open-the-project-acoustics-bake-window"></a>Öppna fönstret projekt akustiskt i bak-fönstret
I Unity väljer du **akustiskt** från menyn **fönster** .

![Enhets redigeraren med akustiskt alternativ markerat på Fönster-menyn](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Skapa ett navigerings nät
Projekt akustiska använder ett navigerings nät för att placera lyssnar avsöknings punkter för simulering. Du kan använda [arbets flödet](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)för att navigera i nätverk. Eller så kan du använda ett annat 3D-modellerings paket för att utforma ditt eget nät.

## <a name="mark-acoustic-scene-objects"></a>Markera akustiska scen objekt
Projekt akustiska är beroende av två typer av scen objekt för simulering:
- Objekt som återspeglar och occlude ljud i simuleringen
- Navigerings nätet i Media Player som begränsar lyssnar avsöknings punkter i simuleringen

Båda objekt typerna markeras med hjälp av fliken **objekt** .

Eftersom märkning av objekt bara lägger till *AcousticsGeometry* -eller *AcousticsNavigation* -komponenterna i objektet, kan du också använda [arbets flödet standard Unity Component](https://docs.unity3d.com/Manual/UsingComponents.html) för att markera eller avmarkera objekt. Du kan bara markera nät åter givning och terräng. Alla andra objekt typer kommer att ignoreras. Kryss rutorna Markera eller avmarkera alla påverkade objekt.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Markera akustiskt ocklusion och reflektions geometri
Öppna fliken **objekt** i fönstret **akustiska** objekt. Markera alla objekt som *akustiska geometrier* om de ska occlude, reflektera eller absorbera ljud. Akustiska geometrier kan innehålla saker som mark, väggar, tak, fönster, fönster, Rugs och stora möbler. Du kan använda valfri godtycklig komplexitets nivå för dessa objekt. Eftersom scenen är voxelized före simuleringen är mycket detaljerade maskor, till exempel träd som har många löv, inte mer kostsamt att bete sig än för enkla objekt.

Inkludera inte saker som inte påverkar akustiskt, t. ex. osynliga kollisions nät.

Ett objekts transformering under avsöknings beräkningen (via fliken **avsökningar** ) korrigeras i bageri resultatet. Om ett markerat objekt i scenen flyttas senare måste avsöknings beräkningen och bakningen göras om.

### <a name="mark-the-navigation-mesh"></a>Markera navigerings nätet
Navigations nät som skapats genom Unity-arbetsflödet hämtas av akustiskt system. Om du vill använda dina egna nät markerar du dem på fliken **objekt** .

### <a name="for-reference-the-objects-tab-parts"></a>Referens: fliken objekt delar
De delar av fliksidan (som visas efter beskrivningarna) är:

1. Val knappar för flikar (med fliken **objekt** markerad). Använd de här knapparna för att gå igenom de olika stegen i en akustisk, från vänster till höger.
1. En kort beskrivning av vad du kan göra med hjälp av den här fliken.
1. Tillgängliga filter för fönstret hierarki. Använd de här alternativen för att filtrera hierarkifönstret till objekt av den angivna typen så att du enkelt kan markera dem. Om du ännu inte har markerat något för akustiska alternativ visas inga andra alternativ när du väljer de sista två alternativen. Dessa alternativ hjälper dock att hitta objekt när de har marker ATS.
1. När inga objekt har marker ATS visar det här avsnittet status för alla objekt i scenen.
    * Totalt: det totala antalet aktiva, icke-dolda objekt.
    * Ignoreras: antalet objekt som inte är nät åter givningar eller terränger.
    * Nät: antalet nät åter givnings objekt.
    * Terräng: antalet terräng objekt.
    * Geometri: antalet nät-eller terräng objekt som marker ATS som **akustiska geometrier**.
    * Navigering: antalet nät-eller terräng objekt som marker ATS som **akustiska navigering**. Det här talet inkluderar inte Unit NavMesh.
1. Det totala antalet "markerade" objekt i scenen, som endast är nät åter givning och terräng. Använd kryss rutorna för att markera (Lägg till lämplig komponent i) objekten som geometri eller navigering för akustiska objekt.
1. När inget är markerat påminner den här kommentaren dig om att välja objekt för markering, om det behövs. Du kan också markera en eller båda kryss rutorna för att markera alla objekt i scenen.
1. När objekt är markerat visar det här avsnittet endast status för de valda objekten.
1. Det totala antalet "markerings bara" markerade objekt. Om du markerar eller avmarkerar kryss rutorna markeras de markerade objekten.

Om du inte har valt något i din scen ser fliken **objekt** ut som följande bild.

![Fliken akustiska objekt med inget valt](media/objects-tab-no-selection-detail.png)

Om något är markerat i din scen-eller hierarki-fönster ser fliken ut som på följande bild.

![Fliken akustiska objekt med markeringar](media/objects-tab-selection-detail.png)

Om några objekt är markerade och vissa inte är det, visar lämpliga kryss rutor ett "blandat" värde, som i följande bild.

![Fliken akustiska objekt med ett blandat urval av ikoner markerade](media/mixed-object-selection-detail.png)

Markera kryss rutan för att markera alla objekt. Avmarkera det om du vill avmarkera alla objekt.

Objekt kan markeras för både geometri och navigering.

## <a name="select-acoustic-materials"></a>Välj akustiskt material
När dina objekt har marker ATS väljer du knappen **material** . Tilldela sedan akustiskt material. Projektet akustiskt material system är knutet till system för enhetens visuella material. För att två objekt ska ha separat akustiskt material måste de ha separata visuella material.

Valet av akustiskt material avgör hur mycket ljud energi som återspeglas från varje yta. Standard akustiska materialet har absorption på liknande sätt som stål. Projekt akustiska förslag ger material baserat på namnet på det visuella materialet. Du kan också tilldela det akustiska materialet **anpassat** till ett material för att aktivera ett justerbart absorptions snåla skjutreglage.

Reverberation tiden för ett material i ett rum är i motsatt förhållande till dess absorptions-koefficient. De flesta material har absorptions värden i intervallet 0,01 till 0,20. Material som har absorptions koefficienter utanför det här intervallet är mycket absorberande.

![Ett diagram visar den negativa korrelationen för Reverberation-tiden och absorptions faktorn.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Referens: delar av fliken material
![Fliken akustiskt material i Unity](media/materials-tab-detail.png)
1. Knappen **material** visar den här fliken.
2. En kort beskrivning av vad du kan göra med hjälp av den här fliken.
3. När den här kryss rutan är markerad visas endast material som används av objekt som är markerade som **akustiska geometrier** . Annars visas allt material som används i scenen.
4. Använd de här alternativen om du vill ändra ordningen på alternativen på en meny i den **akustiska** kolumnen (#6). Sortera akustiskt material efter **namn** eller från **Absorptivity**, från låg till hög.
5. En alfabetiskt sorterad lista över material som används i scenen. Om kryss rutan **Visa endast** markerade är markerad (#3) visas endast material som används av objekt som är markerade som **akustiska geometrier** . Välj ett material här för att markera alla objekt i den scen som använder det materialet.
6. Det akustiska material som scen materialet har tilldelats. Välj ett objekt för att ändra det akustiska material som är tilldelat scen materialet. Om du vill ändra sorterings ordningen för dessa menyer använder du alternativen **Sortera ljud efter** (#4).
7. Koefficienten för akustiskt absorption av det material som valts i kolumnen till vänster (#6). Värdet 0 innebär en perfekt reflekterande (ingen absorption) medan 1 betyder en perfekt absorptive (ingen reflektion). Det går inte att ändra absorptions koefficienten om inte det valda materialet är **anpassat.**
8. För ett material som marker ATS som **anpassat**, aktive ras skjutreglaget. Du kan flytta skjutreglaget eller ange ett värde för att tilldela en absorptions faktor.

## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska lyssnare avsöknings platser
När du har tilldelat materialet växlar du till fliken **avsökningar** . Välj **Beräkna** för att placera lyssnar avsöknings punkter för simulering.

### <a name="what-the-calculate-button-does"></a>Så här fungerar knappen "beräkna"
Knappen **Beräkna** använder den valda akustiska Scene-geometrin för att förbereda din scen för simulering:

- Den tar geometrin från scenens maskor och beräknar en *Voxel volym*. Voxel-volymen är en volym av hela din scen som består av liten kubisk "voxels". Voxel storlek bestäms av simulerings frekvensen, som styrs av **simulerings** inställningen för simulering. Varje Voxel har marker ATS som "öppen luft" eller innehåller en scen geometri. Om en Voxel innehåller geometri, märks Voxel med absorptions koefficienten för det material som är tilldelat den geometrin.
- Navigerings näten används för att placera lyssnar avsöknings punkter. Algoritmen balanserar konkurrerande problem med Spatial täckning och simulerings tid och fil storlek. Det syftar till att se till att smala korridorer och små utrymmen alltid har viss täckning. En typisk avsöknings punkt räknar mellan 100 för små scener till några tusen för stora scener.

Beroende på storleken på din scen och datorns hastighet kan dessa beräkningar ta flera minuter.

### <a name="review-voxel-and-probe-placement"></a>Granska Voxel och avsöknings placering
Förhandsgranska både Voxel-data och avsöknings plats platser för att se till att du är redo att ta din scen. Ett ofullständigt navigations nät eller en extra akustisk geometri är vanligt vis lätt att se i förhands granskningen. Aktivera eller inaktivera placering av Voxel och avsökning med hjälp av **Gizmos** -menyn.

![Gizmos-menyn i Unity](media/gizmos-menu.png)

Voxels som innehåller akustisk geometri visas som gröna kuber. Utforska din scen och kontrol lera att allt som ska vara geometri har voxels. Scen kameran måste vara inom cirka 5 meter av objektet för att voxels ska kunna visas.

Om du jämför voxels som har skapats med grov upplösning jämfört med fin upplösning ser du att den grova voxels är dubbelt så stor.

![Avvisningen av grov voxels i Unity-redigeraren](media/voxel-cubes-preview.png)

Simulerings resultat interpoleras mellan lyssnar platsens platser vid körning. Kontrol lera att det finns avsöknings punkter nära alla platser där spelaren förväntas gå till scenen.

![Förhands granskningen av avsökningarna i Unit-redigeraren](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Ta hand om motivets namn
Scen namnet används för att ansluta scenen till filer som lagrar avsöknings punktens placering och voxelization. Om du byter namn på scenen efter att avsöknings punkterna har beräknats, går material tilldelningen och placerings data förlorade och bör köras igen.

### <a name="for-reference-parts-of-the-probes-tab"></a>Referens: delar av fliken avsökningar
![Fliken akustiska avsökningar i Unit](media/probes-tab-detail.png)

1. Knappen **avsökningar** visar den här fliken.
2. En kort beskrivning av vad du kan göra på den här fliken.
3. Använd de här alternativen om du vill ange en upplösning för grov simulering. Grovheten går snabbare, men det finns kompromisser. Mer information finns i [problemlösning](bake-resolution.md).
4. Anger var akustiska data filer ska läggas till. Välj knappen " **...** " för att få åtkomst till en väljare för mappar. Standard platsen är *till gångar/AcousticsData*. Även en *redigerings* -undermapp skapas på den här platsen. Mer information finns i [datafiler som har lagts till av processen](#Data-Files)i efterhand, längre fram i den här artikeln.
5. Prefixet som anges här används för att namnge datafilerna för den här scenen. Standardvärdet är "Acoustics_ *[scen namn]* ".
6. När avsökningarna har beräknats är kontrollerna som vi nyss beskrivna inaktiverade. Välj **Rensa** -knappen för att radera beräkningarna och aktivera kontrollerna så att du kan beräkna med nya inställningar.
7. Välj **Beräkna** för att voxelize scenen och beräkna avsöknings punkt platser. Beräkningen görs lokalt på din dator. Det måste göras innan du gör en bageri.

I den här versionen av projekt Akustisker går det inte att placera avsökningar manuellt. Använd den automatiserade processen på fliken **avsökningar** .

Mer information om grovhet jämfört med fin upplösning finns i [problemlösning](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Bageri din scen genom att använda Azure Batch
Du kan gå till din scen på ett beräknings kluster i molnet genom att använda tjänsten Azure Batch. Plugin-programmet för Project Akustiske Unit ansluter direkt till Azure Batch för att instansiera, hantera och riva ned ett Azure Batch-kluster för varje bak. På fliken **bak** anger du dina autentiseringsuppgifter för Azure, väljer en kluster dator typ och storlek och väljer sedan **bak**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Referens: delar av fliken bageri
![Fliken akustiskt bak i enhet](media/bake-tab-details.png)

1. På **bak** -knappen visas den här fliken.
2. En kort beskrivning av vad du kan göra på den här sidan.
3. Ange dina autentiseringsuppgifter för Azure i de här fälten när ditt Azure-konto har skapats. Mer information finns i [skapa ett Azure Batch-konto](create-azure-account.md).
4. Fältet Docker-bildtagg för akustiska verktyg.
5. Öppnar Azure Portal för att hantera prenumerationer, övervaka användning och Visa fakturerings information.
6. Anger Azure Batch Compute Node-typ som ska användas för beräkning. Nodtypen måste stödjas av Azure Data Center-platsen. Om du inte är säker lämnar du som **Standard_F8s_v2**.
7. Antalet noder som ska användas för beräkningen. Det här värdet påverkar bageri tiden. Den begränsas av din Azure Batch Core-allokering. Standardallokeringen tillåter endast två 8-core-noder eller 1 16-core-nod, men den kan utökas. Mer information om begränsningar för kärn allokering finns i [skapa ett Azure Batch-konto](create-azure-account.md).
8. Markera den här kryss rutan om du vill konfigurera din Compute-pool för att använda [noder med låg prioritet](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Compute-noder med låg prioritet har mycket lägre kostnad. Men de är kanske inte alltid tillgängliga eller kan aktive ras när som helst.
9. Antalet avsökningar för din scen enligt beräkningen på fliken **avsökningar** . Antalet avsökningar bestämmer antalet simuleringar som ska köras i molnet. Du kan inte ange fler noder än vad som finns i avsökningar.
10. En uppskattning av hur lång tid det tar att köra jobbet i molnet, exklusive start tiden för noden. När jobbet börjar köras visar det här fältet en uppskattning av hur lång tid det tar innan du får tillbaka resultatet.
11. Den totala mängden bearbetnings tid som krävs för att köra simuleringarna. Det här värdet är den totala mängden beräknings tid för noder som ska användas i Azure. Mer information finns i [uppskatta Azure-kostnader för bageri](#Estimating-bake-cost) senare i den här artikeln.
12. Det här meddelandet visar var du kommer att spara resultatet av hembakat när jobbet är klart.
13. *(Endast avancerad användning:)* den här knappen tvingar fram en enhet att glömma från en bageri som du har skickat. Om du till exempel har hämtat resultaten med hjälp av en annan dator, väljer du knappen **Rensa tillstånd** för att glömma jobbet. Resultat filen, när det är klart, kommer *inte* att laddas ned. *Detta är inte samma sak som att avbryta jobbet. Jobbet fortsätter att köras i molnet om det körs.*
14. Välj den här knappen för att skicka hembakat till molnet. När ett jobb körs **avbryts**den här knappen.
15. Välj den här knappen för att förbereda för bearbetning av [ljud simulering på din dator](#Local-bake).
16. Det här avsnittet visar status för bak sidan. När bagerien är klar visas "Hämtad".

Du kan alltid få fullständig information om aktiva jobb, Compute-pooler och lagring i [Azure Portal](https://portal.azure.com).

När ett jobb körs ändras etiketten från **bak** -knappen till **Avbryt jobb**. Använd den här knappen för att avbryta jobbet som pågår. Du uppmanas att bekräfta. Det går inte att ångra ett jobb. När du avbryter blir inga resultat tillgängliga, men du debiteras fortfarande för alla Azure Compute-tider som används.

När du har startat en bageri kan du stänga Unity. Det kan ta flera timmar, beroende på projektet, nodtypen och antalet noder. Status för jobbet uppdateras när du laddar om projektet och öppnar fönstret akustiska. Om jobbet har slutförts kommer utdatafilen att hämtas.

Av säkerhets nivå lagras Azure-autentiseringsuppgifter på den lokala datorn och är kopplade till din Unity-redigerare. De används endast för att upprätta en säker anslutning till Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Hitta status för ett jobb som körs på Azure Portal

1. Hitta jobb-ID: t i **bak-fliken.**

    ![Jobb-ID: t för enhetens bak sidan är markerat på fliken bak Sidan](media/unity-job-id.png)  

2. Öppna [Azure Portal](https://portal.azure.com), gå till det batch-konto som användes för bagerien och välj **jobb**.

    ![Länken jobb i Azure Portal](media/azure-batch-jobs.png)  

3. Sök efter jobb-ID i listan med jobb.

   ![Status för jobbet för jobbet som marker ATS i Azure Portal](media/azure-bake-job-status.png)  

4. Välj jobb-ID för att se status för de relaterade aktiviteterna och den övergripande jobb statusen.

   ![Status för bageri uppgift](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Uppskatta kostnad för Azure-bageri

Om du vill uppskatta vad en bageri kommer att kosta börjar du med det **beräknade beräknings kostnad** svärdet, som är en varaktighet. Multiplicera värdet med Tim kostnaden i din lokala valuta för den **VM-nodtyp** som du väljer. Observera att resultatet inte inkluderar den Node-tid som krävs för att hämta noderna.

Anta till exempel att du väljer **Standard_F8s_v2** för nodtypen, som har kostnaden $0.40/tim. Om den **uppskattade beräknings kostnaden** är 3 timmar och 57 minuter blir den uppskattade kostnaden för att köra jobbet $0,40 * ~ 4 timmar = ~ $1,60. Den faktiska kostnaden är förmodligen lite högre på grund av extra tiden för att hämta noderna.

Hitta kostnad för varje timme till [Azure Batch priser](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Välj **Compute-optimerad** eller **hög prestanda beräkning** som kategori.)

## <a name="Local-bake"></a>Bageri din scen på din dator
Du kan också bageri din scen på din egen dator. Den här metoden kan vara användbar för att experimentera med akustiska ljud för små scener innan du skapar ett Azure Batch-konto. Men Observera att den lokala akustiska simuleringen kan ta lång tid beroende på scenens storlek.

### <a name="minimum-hardware-requirements"></a>Minimi krav för maskin vara
* En x86-64-processor med minst 8 kärnor och 32 GB RAM
* [Hyper-V aktiverat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) för att köra Docker

I vårt exempel test på en dator med 8 kärnor, Intel Xeon E5-1660 @ 3 GHz och 32 GB RAM:
* En liten scen med 100 avsökningar tog ungefär 2 timmar för en grov bageri eller 32 timmar för en fin bageri.
* En scen i medelhög storlek med 1 000 avsökningar tog cirka 20 timmar för en grov bageri eller 21 dagar.

### <a name="set-up-docker"></a>Konfigurera Docker
Installera och konfigurera Docker på den dator som ska bearbeta simuleringen:
1. Installera [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Öppna Docker-inställningar, gå till **Avancerat**och konfigurera resurserna för minst 8 GB RAM-minne. Ju fler processorer du kan allokera till Docker, desto snabbare kommer bagerien att slutföras.  
@no__t Docker-inställningar för 0Sample @ no__t-1
1. Gå till **delade enheter**och aktivera delning för den enhet som används för bearbetning.  
0Docker alternativ för delad enhet @ no__t-1 @no__t

### <a name="run-the-local-bake"></a>Kör den lokala bak Sidan
1. Välj knappen **Förbered lokal inbaka** på fliken **bak** sidan. Välj sedan en mapp där du vill spara indatafilerna och skripten för körning till. Du kan sedan köra hembakat på valfri dator så länge det uppfyller minimi kraven för maskin vara och du installerar Docker genom att kopiera mappen till den datorn.
2. Starta simuleringen genom att köra skriptet *runlocalbake. bat* på Windows eller *runlocalbake.sh* -skriptet på MacOS. Det här skriptet hämtar Docker-avbildningen för projektet akustiskt med de verktyg som krävs för simulerings bearbetning och startar simuleringen.
3. När simuleringen är klar kopierar du den resulterande *. ACE* -filen tillbaka till ditt Unity-projekt. Om du vill kontrol lera att Unity känner igen den som en binär fil lägger du till ". byte" i fil namns tillägget (till exempel "SCENE1. ACE. byte"). Detaljerade loggar för simuleringen lagras i *AcousticsLog. txt.* Om du stöter på problem kan du kontrol lera den här filen för att diagnostisera problemet.

## <a name="Data-Files"></a>Datafiler som lagts till av processen vid bagerien

Följande fyra datafiler skapas under processen. En innehåller simulerings resultatet och medföljer din rubrik. De andra lagrar enhets beroende redigerings data.

Simulerings resultat:
* *Till gångar/AcousticsData/akustisker @ no__t-1 [SceneName]. ACE. byte*: den här filen är Sök tabellen för körnings miljön. Den innehåller simulerings resultat och voxelized akustiska scen element. Du kan ändra namnet och platsen för filen på fliken **avsökningar** .

   *Var noga med att inte ta bort simulerings resultat filen. Det går inte att återvinna, förutom genom att inning av scenen.*

Editor-datafiler:
* *Till gångar/redigerare/[SceneName] \_AcousticsParameters. till gång*: den här filen lagrar de data som du anger i fält i akustiskt användar gränssnitt. Du kan inte ändra namnet och platsen för den här filen.
* *Till gångar/AcousticsData/redaktör/Acoustics_ [SceneName]. VOX*: den här filen lagrar voxelized akustiska geometri och de material egenskaper som beräknas när du väljer knappen **Beräkna** på fliken **avsökningar** . Du kan ändra namnet och platsen för filen på fliken **avsökningar** .
* *Till gångar/AcousticsData/redigerare/akustiska @ no__t-1 [SceneName] @no__t -2config. XML*: den här filen lagrar simulerings parametrar som beräknas när du väljer **Beräkna**. Du kan ändra namnet och platsen för filen på fliken **avsökningar** .

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurera ljud uppslags tabellen
Dra Prefab för **projektet akustiskt** från projekt panelen till din scen:

![De akustiska prefabna i enhet](media/acoustics-prefab.png)

Välj Game-objektet **ProjectAcoustics** och gå till panelen kontrollant. Ange platsen för ditt bageri resultat (. ACE-filen i *Asset/AcousticsData*): dra den till akustiskt-Manager-skriptet eller Välj knappen Ring bredvid text rutan.

![Akustiska Prefab i Union](media/acoustics-manager.png)

## <a name="next-steps"></a>Nästa steg
* Utforska [design kontrollerna för Unity](unity-workflow.md).
* Utforska [design koncept för projekt akustiska metoder](design-process.md).
