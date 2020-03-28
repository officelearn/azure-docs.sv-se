---
title: Project Akustik Unity Baka Handledning
titlesuffix: Azure Cognitive Services
description: Den här självstudien beskriver akustik bakning med Project Acoustics in Unity.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243131"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Project Akustik Unity Baka Handledning
Den här självstudien beskriver akustikbakning med hjälp av Project Acoustics in Unity.

Programvarukrav:
* [Unity 2018.2+](https://unity3d.com) för Windows eller MacOS
* [Plug-in-program för projektakustik som är integrerat i unity-projektet](unity-integration.md) eller [exempelinnehållet Project Acoustics Unity](unity-quickstart.md)
* *Valfritt:* Ett [Azure Batch-konto](create-azure-account.md) för att påskynda bakar med hjälp av cloud computing

## <a name="open-the-project-acoustics-bake-window"></a>Öppna fönstret Projektakustik baka
I Unity väljer du **Akustik** på **fönstermenyn.**

![Unity-redigeraren med alternativet Akustik markerat på fönstermenyn](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Skapa ett navigeringsnät
Project Acoustics använder ett navigeringsnät för att placera avsökningspunkter för simulering. Du kan använda arbetsflödet för [navigeringsnät](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)enhet . Eller så kan du använda ett annat 3D-modelleringspaket för att designa ditt eget nät.

## <a name="mark-acoustic-scene-objects"></a>Markera akustiska scenobjekt
Project Acoustics bygger på två typer av scenobjekt för simulering:
- De objekt som reflekterar och occlude ljud i simuleringen
- Spelarens navigeringsnät som begränsar lyssnarens avsökningspunkter i simuleringen

Båda objekttyperna markeras med hjälp av fliken **Objekt.**

Eftersom markeringsobjekt helt enkelt lägger till *komponenterna AcousticsGeometry* eller *AcousticsNavigation* i objektet kan du också använda [standardarbetsflödet för Unity-komponent](https://docs.unity3d.com/Manual/UsingComponents.html) för att markera eller avmarkera objekt. Du kan bara markera nätrendare och terräng. Alla andra objekttyper ignoreras. Kryssrutorna markerar eller avmarkerar alla objekt som påverkas.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Markera akustisk ocklusion och reflektionsgeometri
Öppna fliken **Objekt** i fönstret **Akustik.** Markera alla objekt som *Akustikgeometri* om de ska ockludera, reflektera eller absorbera ljud. Akustik geometri kan innehålla saker som mark, väggar, tak, fönster och fönsterglas, mattor och stora möbler. Du kan använda valfri godtycklig nivå av komplexitet för dessa objekt. Eftersom scenen är voxelized före simulering, mycket detaljerade maskor, såsom träd som har många blad, är inte dyrare att baka än förenklade objekt.

Ta inte med saker som inte bör påverka akustiken, till exempel osynliga kollisionsnät.

Ett objekts transformering under sondberäkning (via fliken **Sonder)** fixeras i bakningsresultaten. Om några markerade objekt i scenen flyttas senare måste sondberäkning och bakning göras om.

### <a name="mark-the-navigation-mesh"></a>Markera navigeringsnätet
Navigeringsnät som skapades via Unity-arbetsflödet kommer att plockas upp av Akustiksystemet. Om du vill använda egna nät markerar du dem på fliken **Objekt.**

### <a name="for-reference-the-objects-tab-parts"></a>Som referens: Tabbdelarna Objekt
De delar av fliksidan (bilden efter beskrivningarna) är:

1. Knapparna för tabbmarkering (med fliken **Objekt** markerad). Använd dessa knappar för att flytta genom de olika stegen i en akustik baka, från vänster till höger.
1. En kort beskrivning av vad du kan göra med den här fliken.
1. Tillgängliga filter för hierarkifönstret. Använd de här alternativen om du vill filtrera hierarkifönstret till objekt av den angivna typen så att du enkelt kan markera dem. Om du ännu inte har markerat något för akustik, kommer att välja de två sista alternativen visar ingenting. Dessa alternativ hjälper dig dock att hitta objekt när de har markerats.
1. När inga objekt är markerade visar det här avsnittet status för alla objekt i scenen.
    * Totalt: Det totala antalet aktiva, icke dolda objekt.
    * Ignoreras: Antalet objekt som inte är nätrendare eller terräng.
    * Mesh: Antalet mesh renderare objekt.
    * Terräng: Antalet terrängobjekt.
    * Geometri: Antalet mask- eller terrängobjekt som är markerade som **Akustikgeometri**.
    * Navigering: Antalet mask- eller terrängobjekt som **markerats som Akustiknavigering**. Det här numret inkluderar inte Unity NavMesh.
1. Det totala antalet "markeringsbara" objekt i scenen, som bara är nätrenader och terräng. Använd kryssrutorna för att markera (lägg till rätt komponent i) dessa objekt som geometri eller navigering för akustik.
1. När inget är markerat påminner den här anteckningen dig om att markera objekt för markering om det behövs. Du kan också markera en eller båda kryssrutorna för att markera alla objekt i scenen.
1. När objekt markeras visas status för endast markerade objekt i det här avsnittet.
1. Det totala antalet "markeringsbara" markerade objekt. Om du markerar eller avmarkerar kryssrutorna markeras eller markeras endast de markerade objekten.

Om du inte har något markerat i scenen ser fliken **Objekt** ut som följande bild.

![Fliken Akustikobjekt med inget markerat](media/objects-tab-no-selection-detail.png)

Om du har markerat något i scen- eller hierarkifönstret ser fliken ut som följande bild.

![Fliken Akustikobjekt med markeringar](media/objects-tab-selection-detail.png)

Om vissa objekt är markerade och vissa inte är det, visar lämpliga kryssrutor ett "blandat" värde, till exempel följande bild.

![Fliken Akustikobjekt med ett blandat urval av ikoner markerade](media/mixed-object-selection-detail.png)

Markera kryssrutan om du vill markera alla objekt. Avmarkera den om du vill avmarkera alla objekt.

Objekt kan markeras för både geometri och navigering.

## <a name="select-acoustic-materials"></a>Välj akustiska material
När objekten har markerats väljer du knappen **Material.** Tilldela sedan akustiska material. Projektet akustik materialsystem är knuten till Unity visuella materialsystem. För att två föremål ska ha separata akustiska material måste de ha separata visuella material.

Det akustiska materialvalet bestämmer hur mycket ljudenergi som reflekteras tillbaka från varje yta. Standard akustiskt material har absorption som liknar stål. Project Acoustics föreslår material baserat på det visuella materialets namn. Du kan också tilldela det akustiska materialet **Custom** till ett material för att aktivera ett justerbart absorptionskoefficientreglage.

Efterklangstiden för ett visst material i ett rum är omvänt relaterad till dess absorptionskoefficient. De flesta material har absorptionsvärden i intervallet 0,01 till 0,20. Material som har absorptionskoefficienter utanför detta intervall är mycket absorberande.

![Ett diagram visar den negativa korrelationen mellan efterklangstiden och absorptionskoefficienten.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Som referens: Delar av fliken Material
![Fliken Akustikmaterial i Unity](media/materials-tab-detail.png)
1. Knappen **Material** visar den här fliken.
2. En kort beskrivning av vad du kan göra med den här fliken.
3. När den här kryssrutan är markerad visas endast material som används av objekt som markeras som **Akustikgeometri.** Annars visas allt material som används i scenen.
4. Använd de här alternativen om du vill ändra ordningen på alternativen på en meny i kolumnen **Akustik** (#6). Sortera akustiska material **efter namn** eller **absorptivity**, från låg till hög.
5. En alfabetiskt sorterad lista över material som används i scenen. Om kryssrutan **Visa markerad endast** är markerad (#3) visas endast material som används av objekt som är markerade som **Akustikgeometri.** Välj ett material här för att markera alla objekt i scenen som använder materialet.
6. Det akustiska material som scenmaterialet har tilldelats. Välj ett objekt om du vill ändra det akustiska material som tilldelats scenmaterialet. Om du vill ändra sorteringsordningen för dessa menyer använder du alternativen **Sortera akustik efter** (#4).
7. Den akustiska absorptionskoefficienten för det material som valts i kolonnen till vänster (#6). Ett värde på 0 betyder perfekt reflekterande (ingen absorption), medan 1 betyder perfekt absorptiva (ingen reflektion). Absorptionskoefficienten kan inte ändras om inte det valda materialet är **anpassat.**
8. För ett material som är markerat som **Anpassat**aktiveras skjutreglaget. Du kan flytta skjutreglaget eller skriva ett värde för att tilldela en absorptionskoefficient.

## <a name="calculate-and-review-listener-probe-locations"></a>Beräkna och granska avsökningsplatser för lyssnare
När du har tilldelat materialet växlar du till fliken **Avsökningar.** Välj **Beräkna** om du vill placera avsökningspunkter för simulering.

### <a name="what-the-calculate-button-does"></a>Vad "Beräkna"-knappen gör
Knappen **Beräkna** använder din valda akustiska scengeometri för att förbereda din scen för simulering:

- Det tar geometrin från scenen maskor och beräknar en *voxel volym*. Voxel volymen är en volym av hela din scen består av små kubiska "voxels." Voxel-storleken bestäms av simuleringsfrekvensen, som styrs av inställningen **Simuleringsupplösning.** Varje voxel är märkt som "utomhus" eller innehåller scengeometri. Om en voxel innehåller geometri, är voxel märkt med absorptionskoefficienten för det material som är tilldelat till den geometrin.
- Den använder navigeringsnäten för att placera lyssnarens avsökningspunkter. Algoritmen balanserar konkurrerande oro för rumslig täckning och simuleringstid och filstorlek. Det syftar till att se till att smala korridorer och små utrymmen alltid har viss täckning. Typiska sondpunktsantal sträcker sig från 100 för små scener till några tusen för stora scener.

Beroende på storleken på din scen och hastigheten på din maskin, kan dessa beräkningar ta flera minuter.

### <a name="review-voxel-and-probe-placement"></a>Granska voxel och sondplacering
Förhandsgranska både voxel-data och avsökningspunktsplatser för att se till att du är redo att baka din scen. Ett ofullständigt navigeringsnät eller en saknad eller extra akustisk geometri syns vanligtvis väl i förhandsgranskningen. Aktivera eller inaktivera voxel- och sondplacering med hjälp av **Gizmos-menyn.**

![Gizmos-menyn i Unity](media/gizmos-menu.png)

Voxels som innehåller akustisk geometri visas som gröna kuber. Utforska din scen, och kontrollera att allt som ska vara geometri har voxels. Scenkameran måste vara inom cirka 5 meter från objektet för voxels att visa.

Om du jämför voxels som skapats med grov upplösning kontra fin upplösning ser du att de grova voxels är dubbelt så stora.

![Den grova voxels förhandsvisning i Unity redaktör](media/voxel-cubes-preview.png)

Simuleringsresultat interpoleras mellan lyssnarens avsökningspunktsplatser vid körning. Kontrollera att det finns sondpunkter nära alla platser där spelaren förväntas gå i scenen.

![Förhandsgranskningen av sonderna i Unity-redigeraren](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Var försiktig med scenbyten
Scennamnet används för att ansluta scenen till filer som lagrar avsökningspunkten placering och voxelization. Om du byter namn på scenen efter att avsökningspunkter har beräknats går materialtilldelnings- och placeringsdata förlorade och ska köras om.

### <a name="for-reference-parts-of-the-probes-tab"></a>Som referens: Delar av fliken Avsökningar
![Fliken Akustikavsökningar i Unity](media/probes-tab-detail.png)

1. Knappen **Avsökningar** visar den här fliken.
2. En kort beskrivning av vad du kan göra på den här fliken.
3. Använd dessa alternativ för att ställa in grov eller fin simuleringsupplösning. Grov är snabbare, men det finns kompromisser. Mer information finns i [Baka upplösning](bake-resolution.md).
4. Anger var akustikdatafiler ska läggas. Välj knappen "**...**" för att komma åt en mappväljare. Standardplatsen är *Tillgångar/AcousticsData*. En undermapp för *redigerare* skapas också på den här platsen. Mer information finns i [Datafiler som lagts till av bakningsprocessen](#Data-Files)senare i den här artikeln.
5. Prefixet som anges här används för att namnge datafilerna för den här scenen. Standard är "Acoustics_*[Scennamn]*".
6. När avsökningarna har beräknats inaktiveras de kontroller som vi just beskrev. Välj knappen **Rensa** om du vill radera beräkningarna och aktivera kontrollerna så att du kan räkna om med nya inställningar.
7. Välj **Beräkna** för att voxelize scenen och beräkna avsökningspunkt platser. Beräkningen görs lokalt på din maskin. Det måste göras innan du gör en baka.

I den här versionen av Project Acoustics kan avsökningar inte placeras manuellt. Använd den automatiska processen på fliken **Avsökningar.**

Mer information om grov kontra fin upplösning finns i [Baka upplösning](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Baka din scen med hjälp av Azure Batch
Du kan baka din scen på ett beräkningskluster i molnet med hjälp av Azure Batch-tjänsten. Plugin-programmet Project Acoustics Unity ansluter direkt till Azure Batch för att instansiera, hantera och riva ett Azure Batch-kluster för varje bakning. På fliken **Baka** anger du dina Azure-autentiseringsuppgifter, väljer en klusterdatortyp och storlek och väljer sedan **Baka**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Som referens: Delar av fliken Baka
![Fliken Akustik bakar i Unity](media/bake-tab-details.png)

1. Knappen **Baka** visar den här fliken.
2. En kort beskrivning av vad du kan göra på den här sidan.
3. Ange dina Azure-autentiseringsuppgifter i dessa fält när ditt Azure-konto har skapats. Mer information finns i [Skapa ett Azure Batch-konto](create-azure-account.md).
4. Fältet Docker-bildtagg för verktygsuppsättningen Akustik.
5. Öppnar Azure-portalen för att hantera dina prenumerationer, övervaka användning och visa faktureringsinformation.
6. Anger den azure batch-beräkningsnodtyp som ska användas för beräkning. Nodtypen måste stödjas av din Azure datacenter-plats. Om du är osäker, lämna som **Standard_F8s_v2**.
7. Antalet noder som ska användas för beräkningen. Detta tal påverkar baktiden. Den begränsas av kärnallokeringen för Azure Batch. Standardallokeringen tillåter endast två 8-kärnnoder eller en 16-kärnnod, men den kan expanderas. Mer information om grundläggande allokeringsbegränsningar finns i [Skapa ett Azure Batch-konto](create-azure-account.md).
8. Markera den här kryssrutan om du vill konfigurera beräkningspoolen så att [noder med låg prioritet används](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Beräkningsnoder med låg prioritet har mycket lägre kostnad. Men de kanske inte alltid är tillgängliga eller kan föregripas när som helst.
9. Avsökningen räknas för din scen enligt beräknat på fliken **Avsökningar.** Antalet avsökningar bestämmer antalet simuleringar som måste köras i molnet. Du kan inte ange fler noder än det finns avsökningar.
10. En uppskattning av den tid som jobbet tar att köra i molnet, exklusive starttid för nod. När jobbet har börjat köras visar det här fältet en uppskattning av hur länge tills du får tillbaka resultaten.
11. Den totala mängden datortid som behövs för att köra simuleringarna. Det här värdet är den totala mängden beräkningstid för nod som ska användas i Azure. Mer information finns i [Uppskatta Azure baka kostnad](#Estimating-bake-cost) senare i den här artikeln.
12. Det här meddelandet talar om var resultatet av bakningen sparas när jobbet är klart.
13. *(Avancerad användning endast:)* Den här knappen tvingar Unity att glömma en baka som du har skickat in. Om du till exempel har hämtat resultaten med en annan dator väljer du knappen **Rensa läge** för att glömma jobbet. Resultatfilen, när den är klar, hämtas *inte.* *Detta är inte samma sak som att avbryta jobbet. Jobbet, om det körs, fortsätter att köras i molnet.*
14. Välj den här knappen om du vill skicka bakningen till molnet. När ett jobb körs blir den här knappen **Avbryt jobb**.
15. Välj den här knappen om du vill förbereda för bearbetning av [akustiksimulering på datorn](#Local-bake).
16. Detta område visar status för baka. När bakningen är klar visas "Nedladdad".

Du kan alltid få fullständig information om aktiva jobb, beräkningspooler och lagring i [Azure-portalen](https://portal.azure.com).

När ett jobb körs ändras etiketten **Baka** till **Avbryt jobb**. Använd den här knappen om du vill avbryta det pågående jobbet. Du uppmanas att bekräfta. Det går inte att ångra att ett jobb avbryts. När du avbryter kommer inga resultat att vara tillgängliga, men du debiteras fortfarande för all Azure-beräkningstid som används.

När du har startat en bakning kan du stänga Unity. Beroende på projektet, nodtyp och antal noder kan en molnbakning ta flera timmar. Statusen för bakningsjobbet uppdateras när du laddar om projektet och öppnar fönstret Akustik. Om jobbet är slutfört hämtas utdatafilen.

Av säkerhetsskäl lagras Azure-autentiseringsuppgifter på din lokala dator och associeras med din Unity-redigerare. De används bara för att upprätta en säker anslutning till Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Hitta status för ett jobb som körs på Azure-portalen

1. Hitta baka jobb-ID på **fliken Baka.**

    ![Unity bakar jobb-ID markerat på fliken Baka](media/unity-job-id.png)  

2. Öppna [Azure-portalen,](https://portal.azure.com)gå till batchkontot som användes för bakningen och välj **Jobb**.

    ![Länken Jobb i Azure-portalen](media/azure-batch-jobs.png)  

3. Sök efter jobb-ID:t i listan över jobb.

   ![Status för bakningsjobbet som markerats i Azure-portalen](media/azure-bake-job-status.png)  

4. Välj jobb-ID om du vill visa status för relaterade aktiviteter och den övergripande jobbstatusen.

   ![Status för bakningsuppgift](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Uppskatta Azure baka kostnad

Om du vill uppskatta vad en bakning kommer att kosta börjar du med värdet **Uppskattad beräkningskostnad,** som är en varaktighet. Multiplicera det värdet med timkostnaden i din lokala valuta för den **VM-nodtyp** som du väljer. Observera att resultatet inte innehåller nodtiden som behövs för att få igång noderna.

Anta till exempel att du väljer **Standard_F8s_v2** för nodtypen, som har en kostnad på 0,40 USD/tim. Om den **beräknade beräkningskostnaden** är 3 timmar och 57 minuter blir den beräknade kostnaden för att köra jobbet 0,40 USD * ~4 timmar = ~1,60 $. Den faktiska kostnaden kommer sannolikt att vara lite högre på grund av den extra tiden för att få noderna igång.

Hitta timnoder kostnad vid [Azure Batch-priser](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Välj **Beräkningsoptimerad** eller **Hög prestandaberäkning** som kategori.)

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a>Baka din scen på datorn
Du kan också baka din scen på din egen dator. Den här metoden kan vara användbar för att experimentera med akustik för små scener innan du skapar ett Azure Batch-konto. Men observera att lokal akustik simulering kan ta lång tid beroende på storleken på scenen.

### <a name="minimum-hardware-requirements"></a>Minsta maskinvarukrav
* En x86-64-processor med minst 8 kärnor och 32 GB RAM
* [Hyper-V aktiverat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) för att köra Docker

Som ett exempel, i vår testning på en 8-kärnig maskin, Intel Xeon E5-1660 @ 3 GHz och 32 GB RAM:
* En liten scen med 100 sonder tog ca 2 timmar för en grov baka eller 32 timmar för en fin baka.
* En medelstor scen med 1000 sonder tog ca 20 timmar för en grov baka eller 21 dagar för en fin baka.

### <a name="set-up-docker"></a>Ställ in Docker
Installera och konfigurera Docker på datorn som bearbetar simuleringen:
1. Installera [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Öppna Docker-inställningar, gå till **Avancerat**och konfigurera resurserna för minst 8 GB RAM-minne. Ju fler processorer som du kan allokera till Docker, desto snabbare kommer baken att slutföras.  
![Exempel på dockerinställningar](media/docker-settings.png)
1. Gå till **Delade enheter**och aktivera delning för den enhet som används för bearbetning.  
![Alternativ för Docker-delad enhet](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Kör den lokala baka
1. Välj knappen **Förbered lokal baka** på fliken **Baka.** Välj sedan en mappplats som du vill spara indatafiler och körningsskript på. Du kan sedan köra bakningen på vilken dator som helst så länge den uppfyller de lägsta maskinvarukraven och du installerar Docker genom att kopiera mappen till den datorn.
2. Starta simuleringen genom att köra skriptet *runlocalbake.bat* på Windows eller *runlocalbake.sh* skriptet på MacOS. Det här skriptet hämtar project acoustics docker-avbildningen med den verktygsuppsättning som krävs för simuleringsbearbetning och startar simuleringen.
3. När simuleringen är klar kopierar du tillbaka den resulterande *.ess-filen* till Unity-projektet. Om du vill vara säker på att Unity känner igen den som en binär fil lägger du till ".bytes" i filtillägget (till exempel "Scene1.ace.bytes"). De detaljerade loggarna för simuleringen lagras i *AcousticsLog.txt.* Om du stöter på några problem bör du granska den här filen för att diagnostisera problemet.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a>Datafiler som lagts till av bakningsprocessen

Följande fyra datafiler skapas under bakningsprocessen. En innehåller simuleringsresultaten och levereras med din titel. De andra lagrar Unity editor-relaterade data.

Simuleringsresultat:
* *Tillgångar/AkustikData/Akustik\_[SceneName].ace.byte:* Den här filen är uppslagstabellen för körning. Den innehåller simuleringsresultat och voxelized akustiska scenelement. Du kan ändra namn och plats för den här filen på fliken **Avsökningar.**

   *Var noga med att inte ta bort simuleringsresultatfilen. Det går inte att återställa förutom genom att göra om scenen.*

Redigerare datafiler:
* *Tillgångar/Redigerare/[SceneName]\_AcousticsParameters.asset*: Den här filen lagrar de data som du anger i fält i akustikgränssnittet. Du kan inte ändra filens namn och plats.
* *Tillgångar/AkustikData/Editor/Acoustics_[SceneName].vox*: Den här filen lagrar den voxelized akustikgemetrin och de materialegenskaper som beräknas när du väljer knappen **Beräkna** på fliken **Avsökningar.** Du kan ändra namn och plats för den här filen på fliken **Avsökningar.**
* *Tillgångar/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*: Den här filen lagrar simuleringsparametrar som beräknas när du väljer **Beräkna**. Du kan ändra namn och plats för den här filen på fliken **Avsökningar.**

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurera uppslagstabellen för akustik
Dra **prefab för projektakustik** från projektpanelen till scenen:

![Akustiken prefab i Unity](media/acoustics-prefab.png)

Markera **projectacoustics-spelobjektet** och gå till dess inspektörspanel. Ange platsen för bakresultatet (.ace-filen i *Assets/AcousticsData*): Dra det till skriptet Acoustics Manager eller välj cirkelknappen bredvid textrutan.

![Akustikchefen prefab i Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Nästa steg
* Utforska [unity-designkontrollerna](unity-workflow.md).
* Utforska [designkoncept för projektakustik](design-process.md).
