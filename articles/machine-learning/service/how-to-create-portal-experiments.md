---
title: Skapa och utforska experiment i portalen
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar och hanterar automatiserade maskin inlärnings experiment i portalen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 1bfc415b2e4dbc66e2afeae73b78079fb027a60c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358839"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Skapa och utforska automatiserade maskin inlärnings experiment i Azure Portal (för hands version)

 I den här artikeln får du lära dig hur du skapar, kör och utforskar automatiserade maskin inlärnings experiment i Azure Portal utan en enda rad kod. Automatisk maskin inlärning automatiserar processen med att välja den bästa algoritmen som ska användas för dina data, så att du snabbt kan skapa en maskin inlärnings modell. [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md).

 Om du föredrar en mer kod baserad upplevelse kan du också [Konfigurera dina automatiserade maskin inlärnings experiment i python](how-to-configure-auto-train.md) med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en Azure Machine Learning service-arbetsyta](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Kom igång

Navigera till den vänstra rutan i din arbets yta. Välj automatiserade Machine Learning under avsnittet redigering (för hands version).

![Azure Portal navigerings fönster](media/how-to-create-portal-experiments/nav-pane.png)

 Om det här är första gången du utför experiment med automatiserade Machine Learning, ser du följande:

![Azure Portal experimentets landnings sida](media/how-to-create-portal-experiments/landing-page.png)

Annars visas din automatiserade instrument panel för maskin inlärning med en översikt över alla automatiserade maskin inlärnings experiment, inklusive de som skapats med SDK: n. Här kan du filtrera och utforska dina körningar efter datum, experiment namn och körnings status.

![Azure Portal experiment instrument panel](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Skapa ett experiment

Klicka på knappen Skapa experiment för att fylla i följande formulär.

![Skapa experiment formulär](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Ange ditt experiment namn.

1. Välj en beräkning för data profilering och utbildnings jobb. Det finns en lista över dina befintliga beräkningar i list rutan. Följ instruktionerna i steg 3 för att skapa en ny beräkning.

1. Välj knappen Skapa en ny beräkning för att öppna rutan nedan och konfigurera din beräknings kontext för det här experimentet.

    ![Skapa ny beräkning för experimentering](media/how-to-create-portal-experiments/create-new-compute.png)

    Fält|Beskrivning
    ---|---
    Namn på Machine Learning-beräkning| Ange ett unikt namn som identifierar din beräknings kontext.
    Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning.
    Ytterligare inställningar| *Min nod*: Ange det minsta antalet noder för din beräkning. Det minsta antalet noder för AML-beräkning är 0. Om du vill aktivera data profilering måste du ha 1 eller flera noder. <br> *Högsta nod*: Ange det maximala antalet noder för din beräkning. Standardvärdet är 6 noder för en AML-beräkning.

      Om du vill starta skapandet av den nya beräkningen väljer du **skapa**. Det kan ta en stund.

      >[!NOTE]
      > Ditt beräknings namn anger om den beräkning som du väljer/skapar profilering är *aktive rad*. (Se 7b för mer information om data profilering).

1. Välj ett lagrings konto för dina data. Offentlig för hands version stöder endast lokala fil överföringar och Azure Blob Storage-konton.

1. Välj en lagrings behållare.

1. Välj en datafil från lagrings behållaren eller ladda upp en fil från den lokala datorn till behållaren.

    ![Experimentera genom att välja data fil](media/how-to-create-portal-experiments/select-file.png)

1. Använd flikarna för hands version och profil för att ytterligare konfigurera dina data för det här experimentet.

    1. På fliken förhands granskning anger du om dina data innehåller rubriker och väljer funktioner (kolumner) för utbildning med hjälp av knapparna som **ingår** i varje funktions kolumn.

        ![Data för hands version](media/how-to-create-portal-experiments/data-preview.png)

    1. På fliken profil kan du Visa [data profilen](#profile) efter funktion, samt distribution, typ och sammanfattnings statistik (medelvärde, median, max/min och så vidare) för varje.

        ![Fliken Data profil](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Följande fel meddelande visas om din beräknings kontext **inte** har Aktiver ATS för profilering: *Data profilering är bara tillgängligt för beräknings mål som redan körs*.

1. Välj utbildnings jobb typ: klassificering, regression eller Prognosticering.

1. Välj mål kolumn. Den kolumn som du vill göra förutsägelserna på.

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

   ![Formulär för avancerade inställningar](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Om du vill ha mer information om fält klickar du på informations verktygets beskrivning.

<a name="profile"></a>

### <a name="data-profiling"></a>Data profilering

Du kan få en mängd olika sammanfattnings statistik över din data uppsättning för att kontrol lera om din data uppsättning är ML-redo. För icke-numeriska kolumner innehåller de bara grundläggande statistik som min, max och antal fel. För numeriska kolumner kan du också granska deras statistiska moment och uppskatta quantiles. Mer specifikt innehåller vår data profil:

* **Funktion**: namnet på kolumnen som sammanfattas.

* **Profil**: en infogad visualisering som baseras på den härledda typen. Strängar, booleska värden och datum har till exempel värde antal, medan decimaler (numeriska värden) har ungefärligt histogram. På så sätt får du en snabb förståelse för data fördelningen.

* **Typ distribution**: ett värde för antal rader i en kolumn. Nullvärden är deras egna typ, så den här visualiseringen är användbar för att identifiera udda eller saknade värden.

* **Typ**: kolumnens härledda typ. Möjliga värden är: strängar, booleska värden, datum och decimaler.

* **Min**: det minsta värdet för kolumnen. Tomma poster visas för funktioner vars typ inte har en inbyggd ordning (t. ex. booleska värden).

* **Max**: Max värdet för kolumnen. Som "min" visas tomma poster för funktioner med irrelevanta typer.

* **Count**: det totala antalet poster som saknas och som inte saknas i kolumnen.

* **Antal saknas**: antalet poster i kolumnen som inte saknas. Observera att tomma strängar och fel behandlas som värden, så de kommer inte att bidra till "inte det antal som saknas".

* **Quantiles** (vid 0,1, 1, 5, 25, 50, 75, 95, 99 och 99,9% intervall): de ungefärliga värdena vid varje quantile för att ge en uppfattning om data fördelningen. Tomma poster visas för funktioner med irrelevanta typer.

* **Medelvärde**: det aritmetiska medelvärdet för kolumnen. Tomma poster visas för funktioner med irrelevanta typer.

* **Standard avvikelse**: kolumnens standard avvikelse. Tomma poster visas för funktioner med irrelevanta typer.

* **Varians**: kolumnens varians. Tomma poster visas för funktioner med irrelevanta typer.

* **Snedhet**: kolumnens snedhet. Tomma poster visas för funktioner med irrelevanta typer.

* Mängd: kolumnens kolumn. Tomma poster visas för funktioner med irrelevanta typer.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Avancerad för bearbetning

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

Klicka på Starta om du vill köra experimentet. Experimentet som förbereder processen tar några minuter.

### <a name="view-experiment-details"></a>Visa experiment information

När experiment förberedelse fasen är färdig visas skärmen körnings information. Det ger dig en fullständig lista över de modeller som skapats. Som standard är modellen som visar högsta baserat på dina parametrar överst i listan. När utbildnings jobbet försöker utföra fler modeller läggs de till i listan iteration och diagram. Använd upprepnings diagrammet för att få en snabb jämförelse av måtten för de modeller som har producerats hittills.

Utbildnings jobb kan ta en stund för varje pipeline att slutföras.

![Körnings informations instrument panel](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Visa information om tränings körningar

Öka detalj nivån för någon av utmatnings modellerna för att se information om utbildnings körning, till exempel prestanda mått och distributions diagram. [Lär dig mer om diagram](how-to-understand-automated-ml.md).

![Upprepnings information](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Distribuera modell

När du har den bästa modellen till hands är det dags att distribuera den som en webb tjänst för att förutse nya data.

Med automatisk ML får du hjälp med att distribuera modellen utan att skriva kod:

1. Du har några alternativ för distribution. 
    1. Om du vill distribuera den bästa modellen baserat på de mått kriterier som du har angett för experimentet väljer du **distribuera bästa modell** på sidan **körnings information** .

        ![Knappen distribuera modell](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Om du vill distribuera en speciell modell iteration kan du öka detalj nivån i modellen för att öppna den angivna körnings informations sidan och välja **distribuera modell**.

        ![Knappen distribuera modell](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Det första steget är att registrera modellen i tjänsten. Välj "Registrera modell" och vänta tills registrerings processen har slutförts.

    ![Distribuera modell bladet](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. När modellen har registrerats kan du hämta bedömnings skriptet (scoring.py) och miljö skriptet (condaEnv. yml) som ska användas under distributionen.

1. När bedömnings skriptet och miljö skriptet hämtas går du till bladet **till gångar** i det vänstra navigerings fönstret och väljer **modeller**.

    ![Modeller i navigerings fönstret](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Välj den modell du registrerade och välj "Skapa avbildning".

    Du kan identifiera modellen med hjälp av en beskrivning, som innehåller körnings-ID, upprepnings nummer, i följande format: *< Run_ID > _ < Iteration_number > _Model*

    ![Modeller: Skapa avbildning](media/how-to-create-portal-experiments/model-create-image.png)

1. Ange ett namn för avbildningen. 
1. Klicka på knappen **Bläddra** bredvid rutan "bedömnings fil" för att överföra bedömnings filen (scoring.py) som du tidigare har laddat ned.

1. Klicka på knappen **Bläddra** bredvid rutan "Conda File" för att ladda upp miljö filen (condaEnv. yml) som du tidigare har laddat ned.

    Du kan använda ett eget bedömnings skript och en Conda-fil, samt Ladda upp ytterligare filer. [Läs mer om bedömnings skript](how-to-deploy-and-where.md#script).

      >[!Important]
      > Fil namn måste vara under 32 tecken och måste börja och sluta med alfanumeriska tecken. Får innehålla bindestreck, under streck, punkter och alfanumeriska tecken mellan. Blank steg är inte tillåtna.

    ![Skapa avbildning](media/how-to-create-portal-experiments/create-image.png)

1. Klicka på knappen "skapa" för att starta skapandet av avbildningen. Det tar några minuter att slutföra. När du är klar visas ett meddelande i det översta fältet.
1. Gå till fliken "bilder", markera kryss rutan bredvid den avbildning som du vill distribuera och välj "skapa distribution". [Läs mer om distributioner](how-to-deploy-and-where.md).

    Det finns två alternativ för distribution.
     + Azure Container Instance (ACI) – Detta används mer för testnings ändamål snarare än drifts distribution i stor skala. Se till att fylla i värdena för minst en kärna för _processor reserv kapacitet_och minst en gigabyte (GB) för _minnes reserv kapacitet_
     + Azure Kubernetes service (AKS)) – det här alternativet är för distribution i stor skala. Du måste ha en AKS-baserad beräknings klar.

     ![Avbildningar Skapa distribution](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Välj **Skapa** när du är klar. Det kan ta flera minuter att distribuera modellen för varje pipeline för att slutföra körningen.

1. Klart! Du har en fungerande webb tjänst för att generera förutsägelser.

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md) och Azure Machine Learning.
* [Förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).
* [Lär dig hur du använder en webb tjänst](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
