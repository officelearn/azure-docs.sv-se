---
title: Använd automatisk ML för att bygga och distribuera Machine Learning-modeller
titleSuffix: Azure Machine Learning service
description: Skapa, hantera och distribuera automatiserade maskin inlärnings experiment i Azure Portal
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/02/2019
ms.openlocfilehash: 2f6d45613120d02dd96a9fe0a14ce388d20cf0c6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990572"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Skapa, utforska och distribuera automatiserade maskin inlärnings experiment i Azure Portal (för hands version)

 I den här artikeln får du lära dig hur du skapar, utforskar och distribuerar automatiserade maskin inlärnings experiment i Azure Portal utan en enda rad kod. Automatisk maskin inlärning automatiserar processen med att välja den bästa algoritmen som ska användas för dina data, så att du snabbt kan skapa en maskin inlärnings modell. [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md).

 Om du föredrar en mer kod baserad upplevelse kan du också [Konfigurera dina automatiserade maskin inlärnings experiment i python](how-to-configure-auto-train.md) med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md).

## <a name="get-started"></a>Kom igång

Navigera till den vänstra rutan i din arbets yta. Välj automatiserade Machine Learning under avsnittet redigering (för hands version).

![Azure Portal navigerings fönster](media/how-to-create-portal-experiments/nav-pane.png)

 Om det här är första gången du gör ett experiment visas skärmen **Välkommen till automatiserat Machine Learning** . 

Annars ser du din automatiserade instrument panel för **maskin inlärning** med en översikt över alla automatiserade maskin inlärnings experiment, inklusive de som har skapats med SDK: n. Här kan du filtrera och utforska dina körningar efter datum, experiment namn och körnings status.

## <a name="create-an-experiment"></a>Skapa ett experiment

Välj **Skapa experiment** och fyll i formuläret **skapa ett nytt automatiserat maskin inlärnings experiment** .

1. Ange ett unikt experiment namn.

1. Välj en beräkning för data profilering och utbildnings jobb. Det finns en lista över dina befintliga beräkningar i list rutan. Följ instruktionerna i steg 3 för att skapa en ny beräkning.

1. Välj **skapa en ny beräkning** för att konfigurera din beräknings kontext för det här experimentet.

    Fält|Beskrivning
    ---|---
    Namn på Machine Learning-beräkning| Ange ett unikt namn som identifierar din beräknings kontext.
    Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning.
    Ytterligare inställningar| *Min nod*: Ange det minsta antalet noder för din beräkning. Det minsta antalet noder för AML-beräkning är 0. Om du vill aktivera data profilering måste du ha 1 eller flera noder. <br> *Högsta nod*: Ange det maximala antalet noder för din beräkning. Standardvärdet är 6 noder för en AML-beräkning.

      Välj **Skapa**. Det kan ta några minuter att skapa en ny beräkning.

      >[!NOTE]
      > Ditt beräknings namn anger om den beräkning som du väljer/skapar profilering är *aktive rad*. (Se 7b för mer information om data profilering).

1. Välj ett lagrings konto för dina data. 

1. Välj en lagrings behållare.

1. Välj en datafil från lagrings behållaren eller ladda upp en fil från den lokala datorn till behållaren. Offentlig för hands version stöder endast lokala fil överföringar och Azure Blob Storage-konton.
    >[!Important]
    > Krav för tränings data:
    >* Data måste vara i tabell form.
    >* Värdet som du vill förutse (mål kolumnen) måste finnas i data.

    [![Välj datafil](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. Använd flikarna för hands version och profil för att ytterligare konfigurera dina data för det här experimentet.

    1. På fliken **förhands granskning** anger du om dina data innehåller rubriker och väljer funktioner (kolumner) för utbildning med hjälp av knapparna som **ingår** i varje funktions kolumn.

    1. På fliken **profil** kan du Visa [data profilen](#profile) efter funktion, samt distribution, typ och sammanfattnings statistik (medelvärde, median, max/min och så vidare) för varje.

        >[!NOTE]
        > Följande fel meddelande visas om din beräknings kontext **inte** har Aktiver ATS för profilering: *Data profilering är bara tillgängligt för beräknings mål som redan körs*.

1. Välj utbildnings jobb typ: klassificering, regression eller Prognosticering.

1. Välj mål kolumn; Det här är den kolumn som du vill göra förutsägelser på.

1. För Prognosticering:
    1. Välj tids kolumn: Den här kolumnen innehåller de tids data som ska användas.

    1. Välj horisont för prognos: Ange hur många tidsenheter (minuter/timmar/dagar/veckor/månader/år) som modellen ska kunna förutsäga till framtiden. Den ytterligare modellen krävs för att förutsäga i framtiden. den mindre exakta den blir. [Lär dig mer om prognostisering av prognoser och prognoser](how-to-auto-train-forecast.md).

1. Valfritt Avancerade inställningar: ytterligare inställningar som du kan använda för att styra utbildnings jobbet bättre.

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

![Upprepnings information](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-your-model"></a>Distribuera din modell

När du har den bästa modellen till hands är det dags att distribuera den som en webb tjänst för att förutse nya data.

Med automatisk ML får du hjälp med att distribuera modellen utan att skriva kod:

1. Du har ett par alternativ för distribution. 

    + Alternativ 1: Om du vill distribuera den bästa modellen (enligt de mått kriterier som du har definierat) väljer du distribuera bästa modell på sidan körnings information.

    + Alternativ 2: Om du vill distribuera en speciell modell iteration från det här experimentet kan du gå nedåt i modellen för att öppna sidan körnings information och välja distribuera modell.
1. Fyll i fönstret **distribuera modell**

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
