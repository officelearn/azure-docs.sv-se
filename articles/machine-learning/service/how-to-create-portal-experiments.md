---
title: Använd Azures automatiserade ML-gränssnitt för att träna & distribuera modeller
titleSuffix: Azure Machine Learning
description: Skapa, hantera och distribuera automatiserade Machine Learning-experiment i Azure Machine Learnings landnings sida för arbets ytor (för hands version).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 3a47977f2589227347582dc6fcaff25120e380d7
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034834"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learnings-workspace-landing-page-preview"></a>Skapa, utforska och distribuera automatiserade Machine Learning-experiment med Azure Machine Learning s landnings sida för arbets ytan (för hands version)

 I den här artikeln får du lära dig hur du skapar, utforskar och distribuerar automatiserade maskin inlärnings experiment i Azure Machine Learningens landnings sida för arbets ytan utan en enda rad kod. Automatisk maskin inlärning automatiserar processen med att välja den bästa algoritmen som ska användas för dina data, så att du snabbt kan skapa en maskin inlärnings modell. [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md).

 Om du föredrar en mer kod baserad upplevelse kan du också [Konfigurera dina automatiserade maskin inlärnings experiment i python](how-to-configure-auto-train.md) med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En Azure Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="get-started"></a>Kom igång


1. Logga in på [sidan med landnings sidan för arbets ytan](https://ml.azure.com/workspaceportal/). 

1. Välj din prenumeration och arbets yta. 

1. Navigera till den vänstra rutan. Välj **Automatisk ml** under avsnittet **redigering** .

[![Azure Portal navigerings fönster](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Om det här är första gången du gör ett experiment visas skärmen **Kom igång** . 

Annars ser du din automatiserade instrument panel för **maskin inlärning** med en översikt över alla automatiserade maskin inlärnings experiment, inklusive de som har skapats med SDK: n. Här kan du filtrera och utforska dina körningar efter datum, experiment namn och körnings status.

## <a name="create-and-run-experiment"></a>Skapa och kör experiment

1. Välj **Skapa experiment** och fyll i formuläret.

1. Ange ett unikt experiment namn.

1. Välj en beräkning för data profilering och utbildnings jobb. Det finns en lista över dina befintliga beräkningar i list rutan. Om du vill skapa en ny beräkning följer du anvisningarna i steg 4.

1. Välj **skapa en ny beräkning** för att konfigurera din beräknings kontext för det här experimentet.

    Fält|Beskrivning
    ---|---
    Namn på Machine Learning-beräkning| Ange ett unikt namn som identifierar din beräknings kontext.
    Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning.
    Ytterligare inställningar| *Min nod*: Ange det minsta antalet noder för din beräkning. Det minsta antalet noder för AML-beräkning är 0. Om du vill aktivera data profilering måste du ha 1 eller flera noder. <br> *Högsta nod*: Ange det maximala antalet noder för din beräkning. Standardvärdet är 6 noder för en AML-beräkning.
    
    Välj **Skapa**. Det kan ta några minuter att skapa en ny beräkning.

    >[!NOTE]
    > Ditt beräknings namn anger om den beräkning som du väljer/skapar profilering är *aktive rad*. (Mer information finns i avsnittet om avsnitts [data profilering](#profile) ).

1. Välj en data uppsättning från din lagrings behållare eller skapa en genom att ladda upp en fil från den lokala datorn till behållaren. Offentlig för hands version stöder endast lokala fil överföringar och Azure Blob Storage-konton.

    >[!Important]
    > Krav för tränings data:
    >* Data måste vara i tabell form.
    >* Värdet som du vill förutse (mål kolumnen) måste finnas i data.

    1. Om du vill skapa en ny data uppsättning från en fil på din lokala beräkning väljer du **Bläddra** och väljer sedan filen. 

    1. Ge din data uppsättning ett unikt namn och ange en valfri beskrivning. 

    1. Välj **Nästa** för att överföra den till standard lagrings behållaren som skapas automatiskt med din arbets yta eller Välj en lagrings behållare som du vill använda för experimentet. 

    1. Granska **inställningarna och för hands versions** formuläret för noggrannhet. Formuläret fylls i intelligent baserat på filtypen. 

        Fält| Beskrivning
        ----|----
        Fil format| Definierar layout och typ av data som lagras i en fil.
        Avgränsare| Ett eller flera tecken för att ange avgränsningen mellan separata, oberoende regioner i oformaterad text eller andra data strömmar.
        Kodning| Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.
        Kolumnrubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.
    
        Välj **Nästa**.

    1. **Schema** formuläret fylls i intelligent utifrån valen i formuläret **Inställningar och för hands version** . Konfigurera data typen för varje kolumn, granska kolumn namnen och välj vilka kolumner som **inte ska ingå** i experimentet. 
            
        Välj **Nästa.**

1. Välj utbildnings jobb typ: klassificering, regression eller Prognosticering.

1. Välj mål kolumn; Det här är den kolumn som du vill göra förutsägelser på.

1. För Prognosticering:
    1. Välj tids kolumn: Den här kolumnen innehåller de tids data som ska användas.

    1. Välj horisont för prognos: Ange hur många tidsenheter (minuter/timmar/dagar/veckor/månader/år) som modellen ska kunna förutsäga till framtiden. Den ytterligare modellen krävs för att förutsäga i framtiden. den mindre exakta den blir. [Lär dig mer om prognostisering av prognoser och prognoser](how-to-auto-train-forecast.md).

1. Valfritt Avancerade inställningar: ytterligare inställningar som du kan använda för att styra utbildnings jobbet bättre. Annars tillämpas standardvärdena utifrån experiment val och data. 

    Avancerade inställningar|Beskrivning
    ------|------
    Primärt mått| Främsta mått som används för att värdera din modell. [Lär dig mer om modell mått](how-to-configure-auto-train.md#explore-model-metrics).
    Avslutnings villkor| När något av dessa villkor uppfylls avslutas utbildnings jobbet innan det slutförs fullständigt. <br> *Utbildnings jobb tid (minuter)* : Hur lång tid det tar för utbildnings jobbet att köras.  <br> *Max antal iterationer*: Maximalt antal pipeliner (iterationer) som ska testas i utbildnings jobbet. Jobbet kan inte köra fler än det angivna antalet iterationer. <br> *Mät*värdes tröskel:  Lägsta mått Poäng för alla pipeliner. Detta säkerställer att om du har ett definierat målmått som du vill nå, ägnar du inte mer tid åt övnings jobbet än nödvändigt.
    Förbearbeta| Välj det här alternativet om du vill aktivera eller inaktivera förbearbetningen som gjorts genom automatisk maskin inlärning. För bearbetning inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner. [Läs mer om för bearbetning](#preprocess).
    Validering| Välj ett av de kors validerings alternativ som ska användas i övnings jobbet. [Läs mer om kors validering](how-to-configure-auto-train.md).
    Samtidighet| Välj de gränser för flera kärnor som du vill använda när du använder data bearbetning i flera kärnor.
    Blockerad algoritm| Välj algoritmer som du vill undanta från utbildnings jobbet.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Data profilering & sammanfattnings statistik

Du kan få en mängd olika sammanfattnings statistik över din data uppsättning för att kontrol lera om din data uppsättning är ML-redo. För icke-numeriska kolumner innehåller de bara grundläggande statistik som min, max och antal fel. För numeriska kolumner kan du också granska deras statistiska moment och uppskatta quantiles. Mer specifikt innehåller vår data profil:

>[!NOTE]
> Tomma poster visas för funktioner med irrelevanta typer.

Statistik|Beskrivning
------|------
Funktion| Namn på den kolumn som sammanfattas.
Profil| Infogad visualisering baserat på den härledda typen. Strängar, booleska värden och datum har till exempel värde antal, medan decimaler (numeriska värden) har ungefärligt histogram. På så sätt får du en snabb förståelse för data fördelningen.
Typ distribution| Antal värden i en kolumn. Nullvärden är deras egna typ, så den här visualiseringen är användbar för att identifiera udda eller saknade värden.
type|Den härledda typen för kolumnen. Möjliga värden är: strängar, booleska värden, datum och decimaler.
Min| Minsta värde för kolumnen. Tomma poster visas för funktioner vars typ inte har en inbyggd ordning (t. ex. booleska värden).
Max| Max värde för kolumnen. 
Count| Totalt antal saknade och icke-saknade poster i kolumnen.
Antal saknas inte| Antal poster i kolumnen som inte saknas. Tomma strängar och fel behandlas som värden, så de kommer inte att bidra till det antal som saknas.
Quantiles| Ungefärligt värde vid varje quantile för att ge en uppfattning om data fördelningen.
Medelvärde| Aritmetiskt medelvärde eller genomsnitt för kolumnen.
Standardavvikelse| Mått på mängden spridning eller variation för den här kolumnens data.
Varians| Mått på hur långt spridning av den här kolumnens data är från det genomsnittliga värdet. 
Snedhet| Mått på hur olika data från den här kolumnen kommer från en normal distribution.
Toppighet| Mått på hur mycket data som har staplats i den här kolumnens data jämförs med en normal distribution.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Avancerade alternativ för för bearbetning

När du konfigurerar experimenten kan du aktivera den avancerade inställningen `Preprocess`. Detta innebär att följande steg för för bearbetning av data och funktionalisering utförs automatiskt.

|Förbehandlings&nbsp;steg| Beskrivning |
| ------------- | ------------- |
|Ta bort hög kardinalitet- eller inga varians-funktioner|Ta bort dessa från inlärnings-och validerings uppsättningar, inklusive funktioner med alla värden som saknas, samma värde på alla rader eller med mycket hög kardinalitet (till exempel hash-värden, ID: n eller GUID).|
|Sedan imputera värden som saknas|För numeriska funktioner måste du räkna ut med medelvärdet av värdena i kolumnen.<br/><br/>För kategoriska-funktioner ska du räkna med det mest frekventa värdet.|
|Generera ytterligare funktioner|För DateTime-funktioner: År, månad, dag, veckodag, dag på år, kvartal, vecka på år, timme, minut och sekund.<br/><br/>För text funktioner: Term frekvens baserat på unigrams, bi-gram och Tri-Character-gram.|
|Transformera och koda |Numeriska funktioner med få unika värden omvandlas till kategoriska-funktioner.<br/><br/>En-frekvent kodning utförs för kategoriska med låg kardinalitet. för hög kardinalitet, en-frekvent-hash-kodning.|
|Word-inbäddningar|Text upplärda som konverterar vektorer med text-token till menings vektorer med en förtränad modell. Varje ords inbäddnings vektor i ett dokument sammanställs tillsammans för att skapa en dokument funktions vektor.|
|Mål kodningar|För kategoriska-funktioner, mappar varje kategori med genomsnittligt målvärde för Regressions problem och till sannolikheten för varje klass för klassificerings problem. Frekvens-baserad viktning och mellanliggande kors validering används för att minska överbelastningen av mappningen och bruset som orsakas av glesa data kategorier.|
|Kodning av text mål|För text inmatare används en staplad linjär modell med ord uppsättnings ord för att generera sannolikheten för varje klass.|
|Vikt på bevis (WoE)|Beräknar WoE som ett mått på korrelation av kategoriska-kolumner till mål kolumnen. Det beräknas som loggen för förhållandet mellan sannolikheten i förhållande till inaktuella klasser. Det här steget matar ut en numerisk funktions kolumn per klass och tar bort behovet av att explicit kräva att värden som saknas och avvikare behandlas.|
|Kluster avstånd|Tågen a k: kluster modell på alla numeriska kolumner.  Utdata k nya funktioner, en ny numerisk funktion per kluster, som innehåller avståndet från varje exempel till centroid för varje kluster.|

## <a name="run-experiment-and-view-results"></a>Kör experimentet och visa resultaten

Välj **Starta** för att köra experimentet. Experimentet som förbereder processen tar några minuter.

### <a name="view-experiment-details"></a>Visa experiment information

När experiment förberedelse fasen är färdig visas skärmen körnings information som börjar fyllas i. Den här skärmen visar en fullständig lista över de modeller som skapats. Som standard är modellen som visar högsta baserat på det valda måttet överst i listan. När utbildnings jobbet försöker utföra fler modeller läggs de till i listan iteration och diagram. Använd upprepnings diagrammet för att få en snabb jämförelse av måtten för de modeller som har producerats hittills.

Utbildnings jobb kan ta en stund för varje pipeline att slutföras.

[![Körnings informations instrument panel](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visa information om tränings körningar

Öka detalj nivån för någon av utmatnings modellerna för att se information om utbildnings körning, till exempel prestanda mått och distributions diagram. [Lär dig mer om diagram](how-to-understand-automated-ml.md).

[![Upprepnings information](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuera din modell

När du har den bästa modellen till hands är det dags att distribuera den som en webb tjänst för att förutse nya data.

Med automatisk ML får du hjälp med att distribuera modellen utan att skriva kod:

1. Du har ett par alternativ för distribution. 

    + Alternativ 1: Om du vill distribuera den bästa modellen (enligt de mått kriterier som du har definierat) väljer du distribuera bästa modell på sidan körnings information.

    + Alternativ 2: Om du vill distribuera en speciell modell iteration från det här experimentet kan du gå nedåt i modellen för att öppna sidan körnings information och välja distribuera modell.

1. Fyll i fönstret **distribuera modell** .

    Fält| Value
    ----|----
    Distributionsnamn| Ange ett unikt namn för din distribution.
    Distributions Beskrivning| Ange en beskrivning för att bättre identifiera vad den här distributionen är för.
    Bedömnings skript| Skapa eller ladda upp din egen bedömnings fil automatiskt. [Läs mer om bedömnings skript](how-to-deploy-and-where.md#script)
    Miljö skript| Skapa eller ladda upp din egen miljö fil automatiskt.
    >[!Important]
    > Fil namn måste vara under 32 tecken och måste börja och sluta med alfanumeriska tecken. Får innehålla bindestreck, under streck, punkter och alfanumeriska tecken mellan. Blank steg är inte tillåtna.

1. Välj **Distribuera**. Distributionen kan ta ungefär 20 minuter att slutföra.

    Följande meddelande visas när distributionen har slutförts.

    ![Distributionen är klar](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

Nu har du en fungerande webb tjänst för att generera förutsägelser!

## <a name="next-steps"></a>Nästa steg

* Testa självstudien från slut punkt till slut punkt [för att skapa ditt första automatiserade ml-experiment med Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md) och Azure Machine Learning.
* [Förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).
* [Lär dig hur du använder en webb tjänst](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
