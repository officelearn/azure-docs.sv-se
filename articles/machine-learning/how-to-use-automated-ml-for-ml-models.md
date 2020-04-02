---
title: Använd autoML för att skapa modeller & distribuera
titleSuffix: Azure Machine Learning
description: Skapa, granska och distribuera automatiserade maskininlärningsmodeller med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: aa85e80f1a90191a0a34a6962437c27a9d57ef65
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547557"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Skapa, granska och distribuera automatiserade maskininlärningsmodeller med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar, utforskar och distribuerar automatiserade maskininlärningsmodeller utan en enda kodrad i Azure Machine Learnings studiogränssnitt. Automatiserad maskininlärning är en process där den bästa maskininlärningsalgoritmen som du kan använda för dina specifika data. Med den här processen kan du snabbt generera maskininlärningsmodeller. [Läs mer om automatiserad maskininlärning](concept-automated-ml.md).
 
Om du vill ha ett exempel från på slut- och provar du [självstudien för att skapa en klassificeringsmodell med Azure Machine Learnings automatiska ML-gränssnitt](tutorial-first-experiment-automated-ml.md). 

Konfigurera [dina automatiserade maskininlärningsexperiment](how-to-configure-auto-train.md) med Azure Machine Learning SDK för en Python-kodbaserad upplevelse.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En Azure Machine Learning-arbetsyta med en typ av **Enterprise Edition**. Se [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).  Information om hur du uppgraderar en befintlig arbetsyta till Enterprise Edition finns i [Uppgradera till Enterprise-utgåvan](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Kom igång

1. Logga in på Azure https://ml.azure.comMachine Learning på . 

1. Välj prenumeration och arbetsyta. 

1. Navigera till den vänstra rutan. Välj **Automatiserat ML** under avsnittet **Författare.**

[![Navigeringsfönstret i Azure Machine Learning studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Om det är första gången du gör några experiment visas en tom lista och länkar till dokumentation. 

Annars visas en lista över dina senaste automatiserade maskininlärningsexperiment, inklusive de som har skapats med SDK. 

## <a name="create-and-run-experiment"></a>Skapa och köra experiment

1. Välj **+ Ny automatiserad ML-körning** och fyll i formuläret.

1. Välj en datauppsättning från lagringsbehållaren eller skapa en ny datauppsättning. Datauppsättningar kan skapas från lokala filer, webb-url:ar, datalager eller Azure öppna datauppsättningar. 

    >[!Important]
    > Krav för utbildningsdata:
    >* Data måste vara i tabellform.
    >* Värdet som du vill förutsäga (målkolumn) måste finnas i data.

    1. Om du vill skapa en ny datauppsättning från en fil på den lokala datorn väljer du **Bläddra** och väljer sedan filen. 

    1. Ge din datauppsättning ett unikt namn och ge en valfri beskrivning. 

    1. Välj **Nästa** om du vill öppna **formuläret Datastore och filval**. I det här formuläret väljer du var du vill ladda upp din datauppsättning. standardlagringsbehållaren som skapas automatiskt med arbetsytan eller välj en lagringsbehållare som du vill använda för experimentet. 

    1. Läs formuläret **Inställningar och förhandsgranska** för noggrannhet. Formuläret är intelligent fyllt baserat på filtypen. 

        Field| Beskrivning
        ----|----
        Filformat| Definierar layout och typ av data som lagras i en fil.
        Avgränsare| Ett eller flera tecken för att ange gränsen mellan separata, oberoende regioner i oformaterad text eller andra dataströmmar.
        Kodning| Identifierar vilken bit till tecken schematabell som ska användas för att läsa datauppsättningen.
        Kolumnrubriker| Anger hur eventuella rubriker för datauppsättningen ska behandlas.
        Hoppa över rader | Anger hur många, om några, rader som hoppas över i datauppsättningen.
    
        Välj **Nästa**.

    1. **Formuläret Schema** fylls intelligent i, baserat på valen i formuläret Inställningar **och förhandsgranskning.** Här konfigurerar du datatypen för varje kolumn, granskar kolumnnamnen och väljer vilka kolumner som **inte ska inkluderas** för experimentet. 
            
        Välj **Nästa.**

    1. Formuläret **Bekräfta information** är en sammanfattning av informationen som tidigare fyllts i formulären Grundläggande **information** och Inställningar **och förhandsgranskning.** Du har också möjlighet att skapa en dataprofil för din datauppsättning med hjälp av en profileringsaktiverad beräkning. Läs mer om [dataprofilering](#profile).

        Välj **Nästa**.
1. Markera den nyskapade datauppsättningen när den visas. Du kan också visa en förhandsgranskning av datauppsättningen och exempelstatistiken. 

1. Ange ett unikt experimentnamn i formuläret **Konfigurera körning.**

1. Välj en målkolumn. Detta är den kolumn som du vill göra förutsägelser på.

1. Välj en beräkning för dataprofilering och utbildningsjobb. En lista över dina befintliga beräkningar finns i listrutan. Om du vill skapa en ny beräkning följer du instruktionerna i steg 7.

1. Välj **Skapa en ny beräkning** för att konfigurera beräkningskontexten för det här experimentet.

    Field|Beskrivning
    ---|---
    Beräkningsnamn| Ange ett unikt namn som identifierar din beräkningskontext.
    Storlek för virtuell dator| Välj storleken på den virtuella datorn för din beräkning.
    Min / Max noder (i avancerade inställningar)| Om du vill profilera data måste du ange 1 eller fler noder. Ange det maximala antalet noder för din beräkning. Standardär 6 noder för en AML-beräkning.
    
    Välj **Skapa**. Det kan ta några minuter att skapa en ny beräkning.

    >[!NOTE]
    > Ditt beräkningsnamn anger om den beräkning du väljer/skapar *är aktiverad.* (Se avsnittet [dataprofilering](#profile) för mer information).

    Välj **Nästa**.

1. I formuläret **Aktivitetstyp och inställningar** väljer du aktivitetstypen: klassificering, regression eller prognostisering. Mer [information finns i hur du definierar aktivitetstyper.](how-to-define-task-type.md)

    1. För klassificering kan du också aktivera djupinlärning som används för text mediktor.

    1. För prognoser:
        1. Välj tidskolumn: Den här kolumnen innehåller tidsdata som ska användas.

        1. Välj prognoshorisont: Ange hur många tidsenheter (minuter/timmar/dagar/veckor/månader/år) som modellen kan förutsäga för framtiden. Ju längre modellen krävs för att förutsäga i framtiden, desto mindre exakt blir den. [Läs mer om prognoser och prognoshorisont](how-to-auto-train-forecast.md).

1. (Valfritt) Visa inställningar för tilläggskonfiguration: ytterligare inställningar som du kan använda för att bättre styra utbildningsjobbet. Annars används standardvärden baserat på val av experiment och data. 

    Ytterligare konfigurationer|Beskrivning
    ------|------
    Primärt mått| Huvudmått som används för att göra mål på din modell. [Läs mer om modellmått](how-to-configure-auto-train.md#explore-model-metrics).
    Automatisk featurization| Välj det här alternativet om du vill aktivera eller inaktivera förbearbetningen som görs genom automatiserad maskininlärning. Förbearbetning inkluderar automatisk datarensning, förberedelse och omvandling för att generera syntetiska funktioner. Stöds inte för aktivitetstypen tidsserieprognoser. [Läs mer om förbearbetning](#featurization). 
    Förklara bästa modellen | Välj det här alternativet om du vill aktivera eller inaktivera för att visa explainability av den rekommenderade bästa modellen
    Blockerad algoritm| Välj algoritmer som du vill utesluta från utbildningsjobbet.
    Avslutningskriterium| När något av dessa kriterier är uppfyllda stoppas utbildningsjobbet. <br> *Utbildning jobbtid (timmar)*: Hur länge att låta utbildning jobbet att köra. <br> *Tröskelvärde för måttpoäng:* Lägsta måttpoäng för alla pipelines. Detta säkerställer att om du har ett definierat målmått som du vill nå, spenderar du inte mer tid på utbildningsjobbet än nödvändigt.
    Validering| Välj ett av de korsvalideringsalternativ som ska användas i utbildningsjobbet. [Läs mer om korsvalidering](how-to-configure-auto-train.md).
    Samtidighet| *Max samtidiga iterationer:* Maximalt antal pipelines (iterationer) för att testa i utbildningsjobbet. Jobbet kommer inte att köras mer än det angivna antalet iterationer.

1. (Valfritt) Visa featurization-inställningar: Om du väljer att aktivera **Automatisk medsättning** i formuläret **Ytterligare konfigurationsinställningar,** är det här formuläret där du anger vilka kolumner som ska utföras på dessa medinställningar och väljer vilket statistiskt värde som ska användas för saknade värdeimputationer.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Dataprofilering & sammanfattningsstatistik

Du kan få en mängd olika sammanfattande statistik över dina datauppsättningar för att kontrollera om din datauppsättning är ML-klar. För icke-numeriska kolumner innehåller de bara grundläggande statistik som min, max och felantal. För numeriska kolumner kan du också granska deras statistiska ögonblick och uppskattade kvantiler. Vår dataprofil innehåller:

>[!NOTE]
> Tomma poster visas för funktioner med irrelevanta typer.

Statistik|Beskrivning
------|------
Funktion| Namnet på den kolumn som sammanfattas.
Profil| In-line visualisering baserat på typen härleds. Strängar, booleans och datum har till exempel värdeantal, medan decimaler (numeriska) har approximerat histogram. Detta gör att du kan få en snabb förståelse för fördelningen av data.
Typfördelning| In-line värde antal typer i en kolumn. Nulls är sin egen typ, så den här visualiseringen är användbar för att identifiera udda eller saknade värden.
Typ|Härledd typ av kolumn. Möjliga värden är: strängar, booleans, datum och decimaler.
Min| Minsta värde för kolumnen. Tomma poster visas för funktioner vars typ inte har någon inbyggd ordning (t.ex. booleans).
Max| Maximalt värde för kolumnen. 
Antal| Totalt antal poster som saknas och saknas i kolumnen.
Antal saknas inte| Antal poster i kolumnen som inte saknas. Tomma strängar och fel behandlas som värden, så de bidrar inte till antalet "saknas inte".
Kvantiler| Approximerat värderar på varje quantile för att ge en avkänning av fördelningen av datan.
Medelvärde| Aritmetiskt medelvärde eller medelvärde av kolonnen.
Standardavvikelse| Mått på mängden spridning eller variation av den här kolumnens data.
Varians| Mått på hur långt sprida ut denna kolumn data är från dess genomsnittliga värde. 
Snedhet| Mått på hur olika den här kolumnens data är från en normalfördelning.
Toppighet| Mått på hur kraftigt tailed denna kolumn data jämförs med en normal fördelning.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Avancerade mediseringsalternativ

Automatiserad maskininlärning erbjuder förbearbetning och dataskyddsräckor automatiskt, för att hjälpa dig att identifiera och hantera potentiella problem med dina data. 

### <a name="preprocessing"></a>Förbehandling

|Steg för&nbsp;förbearbetning| Beskrivning |
| ------------- | ------------- |
|Släpp hög kardinalitet eller ingen varians funktioner|Släpp dessa från utbildnings- och valideringsuppsättningar, inklusive funktioner med alla värden som saknas, samma värde på alla rader eller med extremt hög kardinalitet (till exempel hashar, ID eller GUIDs).|
|Tillskriv värden som saknas|För numeriska funktioner, imputera med medelvärdet av värden i kolumnen.<br/><br/>För kategoriska funktioner, impute med de vanligaste värdet.|
|Generera ytterligare funktioner|För DateTime-funktioner: År, Månad, Dag, Veckodag, Veckodag, Kvartal, Vecka på året, Timme, Minut, Sekund.<br/><br/>För textfunktioner: Termfrekvens baserat på unigram, bi-gram och tri-tecken-gram.|
|Omforma och koda |Numeriska funktioner med få unika värden omvandlas till kategoriska funktioner.<br/><br/>En-het kodning utförs för låg kardinalitet kategorisk; för hög kardinalitet, en-hot-hash kodning.|
|Word-inbäddningar|Textstöverenare som konverterar vektorer av texttoken till meningsvektorer med hjälp av en förtränad modell. Varje ords inbäddningsvektor i ett dokument sammanställs för att skapa en dokumentfunktionsvektor.|
|Målkodningar|För kategoriska funktioner mappar du varje kategori med medelvärdet för regressionsproblem och klasssannolikheten för varje klass för klassificeringsproblem. Frekvensbaserad viktning och k-fold cross validering tillämpas för att minska övermontering av kartläggning och buller som orsakas av glesa datakategorier.|
|Textmålkodning|För textinmatning används en staplad linjär modell med påse ord för att generera sannolikheten för varje klass.|
|Bevisens vikt (Ve)|Beräknar Ve som ett mått på korrelation av kategoriska kolumner till målkolumnen. Det beräknas som loggen för förhållandet mellan i klassen vs out-of-class sannolikheter. Det här steget matar ut en numerisk funktionskolumn per klass och tar bort behovet av att uttryckligen tillskriva saknade värden och avvikare behandling.|
|Klusteravstånd|Tränar en k-medels klustringsmodell på alla numeriska kolumner.  Utdata k nya funktioner, en ny numerisk funktion per kluster, som innehåller avståndet för varje prov till centroid för varje kluster.|

### <a name="data-guardrails"></a>Data skyddsräcken

Data skyddsräcken används när automatisk featurization är aktiverat eller validering är inställd på auto. Data guardrails hjälper dig att identifiera potentiella problem med dina data (t.ex. saknade värden, klassobalans) och hjälpa till att vidta korrigerande åtgärder för bättre resultat. Det finns många bästa metoder som finns tillgängliga och kan tillämpas för att uppnå tillförlitliga resultat. Användare kan granska databevakningsräcken i studion på fliken **Data guardrails** i en automatiserad ML-körning eller genom att ställa ```show_output=True``` in ett experiment med Python SDK. I följande tabell beskrivs de databevakningsräcken som för närvarande stöds och de associerade statusar som användare kan stöta på när de skickar in sina experiment.

Skyddsräcke|Status|Villkor&nbsp;&nbsp;för utlösare
---|---|---
Saknade funktionsvärden imputering |**Passerade** <br><br><br> **Klar**| Inga saknade funktionsvärden upptäcktes i dina träningsdata. Läs mer om [värdeimputering som saknas.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Saknade funktionsvärden upptäcktes i dina träningsdata och tillräknades.
Hantering av hög kardinalitetsfunktion |**Passerade** <br><br><br> **Klar**| Dina ingångar analyserades och inga funktioner med hög kardinalitet upptäcktes. Läs mer om [identifiering av hög kardinalitetsfunktion.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Hög kardinalitet funktioner upptäcktes i dina ingångar och hanterades.
Delningshantering för validering |**Klar**| *Valideringskonfigurationen var inställd på "auto" och träningsdata innehöll **mindre** än 20 000 rader.* <br> Varje iteration av den tränade modellen validerades genom korsvalidering. Läs mer om [valideringsdata.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *Valideringskonfigurationen var inställd på "auto" och träningsdata innehöll **mer** än 20 000 rader.* <br> Indata har delats upp i en träningsdatauppsättning och en valideringsdatauppsättning för validering av modellen.
Identifiering av klassbalansering |**Passerade** <br><br><br><br> **Larmade** | Dina indata analyserades och alla klasser är balanserade i dina träningsdata. En datauppsättning anses vara balanserad om varje klass har god representation i datauppsättningen, mätt i antal och förhållandet mellan prov. <br><br><br> Obalanserade klasser upptäcktes i dina indata. För att åtgärda modell bias fixa balansering problemet. Läs mer om [obalanserade data.](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml#imbalance)
Identifiering av minnesproblem |**Passerade** <br><br><br><br> **Klar** |<br> De valda {horizon, lag, rolling window}-värdena analyserades och inga potentiella problem utanför minnet upptäcktes. Läs mer om [prognoskonfigurationer i tidsserier.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>De valda värdena {horisont, fördröjning, rullande fönster} analyserades och kan leda till att experimentet tar. Fördröjnings- eller rullande fönsterkonfigurationer har inaktiverats.
Frekvensdetektering |**Passerade** <br><br><br><br> **Klar** |<br> Tidsserien analyserades och alla datapunkter är i linje med den identifierade frekvensen. <br> <br> Tidsserien analyserades och datapunkter som inte stämmer överens med den identifierade frekvensen upptäcktes. Dessa datapunkter togs bort från datauppsättningen. Läs mer om [dataförberedelser för prognostisering i tidsserier.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

#### <a name="data-guardrail-states"></a>Data Guardrail stater
Data guardrails kommer att visa en av tre stater: "Godkänd", "Klar eller "Varnad".

Status| Beskrivning
----|----
Passerade| Inga dataproblem upptäcktes och ingen användaråtgärd krävs. 
Klart| Ändringar har tillämpats på dina data. Vi uppmuntrar användare att granska de korrigerande åtgärder som Automated ML vidtog för att se till att ändringarna överensstämmer med de förväntade resultaten. 
Larmade| Ett dataproblem som inte kunde åtgärdas upptäcktes. Vi uppmuntrar användare att ändra och åtgärda problemet. 

Tidigare version av Automated ML visade ett fjärde tillstånd: "Fast". Nyare experiment kommer inte att visa detta tillstånd, och alla skyddsräcken som visade "Fast" tillstånd kommer nu att visa "Klar".   

## <a name="run-experiment-and-view-results"></a>Kör experiment- och visningsresultat

Välj **Slutför** om du vill köra experimentet. Experimentförberedelserna kan ta upp till 10 minuter. För träningsjobb kan det ta ytterligare 2–3 minuter för varje pipeline att slutföra körningen.

### <a name="view-experiment-details"></a>Visa experimentinformation

Skärmen **Kör detalj** öppnas på fliken **Detaljer.** På den här skärmen visas en sammanfattning av experimentkörningen med ett statusfält högst upp bredvid körningsnumret. 

Fliken **Modeller** innehåller en lista över de modeller som skapats ordnade efter måttpoängen. Som standard visas modellen med högst poäng utifrån det valda måttet överst i listan. När träningsjobbet testar fler modeller läggs de till i listan. Använd det här för att få en snabb jämförelse av måtten för de modeller som har producerats hittills.

[![Kör informationsinstrumentpanel](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visa information om träningskörning

Öka detaljnivån för någon av de slutförda modellerna för att se information om träningskörning, till exempel kör mått på fliken **Modellinformation** eller prestandadiagram på fliken **Visualiseringar.** [Läs mer om diagram](how-to-understand-automated-ml.md).

[![Detaljer för iteration](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuera din modell

När du har den bästa modellen till hands är det dags att distribuera den som en webbtjänst för att förutsäga nya data.

Automatiserad ML hjälper dig att distribuera modellen utan att skriva kod:

1. Du har ett par alternativ för distribution. 

    + Alternativ 1: Om du vill distribuera den bästa modellen (enligt de måttkriterier du har definierat) väljer du knappen **Distribuera bästa modell** på fliken **Information.**

    + Alternativ 2: Om du vill distribuera en specifik modelliteration från det här experimentet kan du öka detaljnivån på modellen för att öppna fliken **Modellinformation** och välja **Distribuera modell**.

1. Fyll i **fönstret Distribuera modell.**

    Field| Värde
    ----|----
    Namn| Ange ett unikt namn för distributionen.
    Beskrivning| Ange en beskrivning för att bättre identifiera vad den här distributionen är till för.
    Beräkningstyp| Välj den typ av slutpunkt som du vill distribuera: *Azure Kubernetes Service (AKS)* eller *Azure Container Instance (ACI)*.
    Beräkningsnamn| *Gäller endast AKS:* Välj namnet på det AKS-kluster som du vill distribuera till.
    Aktivera autentisering | Välj det här om du vill tillåta tokenbaserad eller nyckelbaserad autentisering.
    Använda anpassade distributionsresurser| Aktivera den här funktionen om du vill ladda upp ditt eget bedömningsskript och miljöfil. [Läs mer om att göra mål på skript](how-to-deploy-and-where.md#script).

    >[!Important]
    > Filnamnen måste vara under 32 tecken och måste börja och sluta med alfanumeriska. Kan innehålla streck, understreck, punkter och alfanumeriska mellan. Blanksteg är inte tillåtna.

    Menyn *Avancerat* erbjuder standarddistributionsfunktioner som [inställningar för datainsamling](how-to-enable-app-insights.md) och resursanvändning. Om du vill åsidosätta dessa standardvärden gör du det på den här menyn.

1. Välj **Distribuera**. Distributionen kan ta cirka 20 minuter att slutföra.

Nu har du en operativ webbtjänst för att generera förutsägelser! Du kan testa förutsägelserna genom att fråga tjänsten från [Power BI:s inbyggda Azure Machine Learning-stöd](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder en webbtjänst](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Förstå automatiserade maskininlärningsresultat](how-to-understand-automated-ml.md).
* [Läs mer om automatiserad maskininlärning](concept-automated-ml.md) och Azure Machine Learning.
