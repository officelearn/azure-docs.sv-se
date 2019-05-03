---
title: Skapa och utforska experiment i portalen
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar och hanterar automatiserade maskininlärningsexperiment i portalen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 8b6d7f791300a970e71fda4f1d56354a45d07afd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029902"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Skapa och utforska automatiserade machine learning-experiment i Azure portal (förhandsversion)

 I den här artikeln lär du dig att skapa, köra och utforska automatiserade machine learning-experiment i Azure-portalen utan en enda rad kod. Automatiserad maskininlärning automatiserar processen att välja den bästa algoritmen använder för dina specifika data, så att du snabbt kan skapa en modell för maskininlärning. [Mer information om automatisk maskininlärning](https://docs.microsoft.com/azure/machine-learning/service/concept-automated-ml).

 Om du föredrar en mer kod baserat-upplevelsen kan du också [konfigurera dina automatiserade maskininlärningsexperiment i Python](how-to-configure-auto-train.md) med den [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en arbetsyta för Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Kom igång

Navigera till den vänstra rutan i din arbetsyta. Välj Automatisk Machine Learning under avsnittet redigering (förhandsversion).

![Azure portal navigeringsfönstret](media/how-to-create-portal-experiments/nav-pane.png)

 Om det här är första gången du gör alla experiment med automatiserade Machine Learning, ser du följande:

![Landningssida för Azure portal experiment](media/how-to-create-portal-experiments/landing-page.png)

Annars ser du automatisk machine learning-instrumentpanel med en översikt över alla dina automatiserade machine learning-experiment och körs, inklusive de som kör med hjälp av SDK. Här kan du filtrera och utforska dina körningar efter datum, experimentera namn och Körningsstatus.

![Azure portal experiment instrumentpanel](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Skapa ett experiment

Välj knappen Skapa Experiment att fylla i följande format.

![Skapa formulär-experiment](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Ange experimentnamnet på ditt.

1. Välj en beräkning för profilering av data och utbildningsjobb. En lista med din befintliga beräkningarna är tillgänglig i listrutan. Följ anvisningarna i steg 3 för att skapa en ny beräkning.

1. Välj Skapa en ny beräkningsknappen för att öppna den under rutan och konfigurera din beräkningskontext för det här experimentet.

    ![Skapa ny beräkning för experiment](media/how-to-create-portal-experiments/create-new-compute.png)

    Fält|Beskrivning
    ---|---
    Namn på Machine Learning-beräkning| Ange ett unikt namn som identifierar din beräkningskontexten.
    Storlek på virtuell dator| Välj VM-storlek för din beräkning.
    Ytterligare inställningar| *Min noden*: Ange det minsta antalet noder för din databearbetning. Det minsta antalet noder för AML-instanser är 0. Om du vill aktivera profilering av data, måste du ha 1 eller flera noder. <br> *Max noden*: Ange det maximala antalet noder för din databearbetning. Standardvärdet är 6-noder i ett AML Compute.

      Om du vill starta skapandet av din ny beräkning, Välj **skapa**. Det kan ta en stund.

      >[!NOTE]
      > Namnet på din beräkning anger om den beräkning som du väljer/Skapa *profilering aktiverat*. (Se 7b för mer information på Profileringen av data).

1. Välj ett lagringskonto för dina data. Förhandsversionen stöder bara lokala filöverföringar och Azure Blob Storage-konton.

1. Välj en lagringsbehållare.

1. Välj en datafil från storage-behållare eller ladda upp en fil från din lokala dator till behållaren.

    ![Välj fil för experiment](media/how-to-create-portal-experiments/select-file.png)

1. Använd flikarna preview och profilen ytterligare konfigureringsalternativ för dina data för det här experimentet.

    1. På fliken Förhandsgranska indikera om dina data innehåller rubriker och välj funktioner (kolumner) för med den **ingår** växla knappar i varje kolumn för funktionen.

        ![Dataförhandsgranskning](media/how-to-create-portal-experiments/data-preview.png)

    1. På fliken profil kan du se den [dataprofil](#profile) per funktion, samt distribution, typ och sammanfattande statistik (medelvärde, median, max per minut och så vidare) för var och en.

        ![Data-fliken profil](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Följande felmeddelande visas om din beräkningskontext är **inte** profilering aktiverat: *Profilering av data är endast tillgänglig för beräkningsmål som redan körs*.

1. Markera utbildning jobbtyp: klassificering, regression eller prognostisering.

1. Välj målkolumnen. Den kolumn som du vill göra förutsägelserna på.

1. För prognostisering:
    1. Välj time-kolumn: Den här kolumnen innehåller tidsdata som ska användas.
    1. Välj prognoser horizon: Ange hur många tidsenheter (minuter/timmar/dagar/vecka/månad/år) modellen att kunna förutsäga i framtiden. Modellen är ju längre krävs för att förutsäga i framtiden, desto mindre exakta blir det. [Mer information om prognoser och prognos horizon](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Valfritt) Avancerade inställningar: ytterligare inställningar som du kan använda för att få bättre kontroll över utbildningsjobbet.

    Avancerade inställningar|Beskrivning
    ------|------
    Primär mått| Viktigaste mått som används för bedömning av din modell. [Mer information om modellen mått](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Avsluta-villkor| När något av dessa villkor uppfylls, slutar utbildningsjobbet innan fullständig slutförande. <br> *Utbildning jobbtid (minuter)*: Hur lång tid att tillåta att utbildningsjobbet ska köras.  <br> *Maxantal upprepningar*: Högsta antalet tillåtna pipelines (iterationer) att testa i utbildningsjobbet. Jobbet körs inte mer än det angivna antalet iterationer. <br> *Mått poäng tröskelvärdet*:  Minsta mått poäng för alla pipelines. Detta säkerställer att om du har ett definierat mål-mått som du vill nå kan du inte lägga mer tid på utbildningsjobbet än nödvändigt.
    Förbearbeta| Välj det här alternativet om du vill aktivera eller inaktivera Förbearbeta som görs av automatiserade maskininlärning. Förbearbeta innehåller automatisk datarensning, förbereda och omvandling för att generera syntetiska funktioner. [Mer information om Förbearbeta](#preprocess).
    Validering| Välj något av alternativen mellan verifiering ska användas i utbildningsjobbet. [Läs mer om hur du mellan verifiering](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Samtidighet| Markera flera kärnor-gränser som du vill använda när du använder flera kärnor beräkning.
    Blockerade algoritmen| Välja algoritmer som du vill undanta från utbildningsjobbet.

   ![Avancerade inställningar formulär](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Mer information om fält klickar du på information beskrivningen.

<a name="profile"></a>

### <a name="data-profiling"></a>Data som profilering

Du kan få en omfattande mängd sammanfattande statistik över din datauppsättning för att verifiera att din datauppsättning är redo för ML. De omfattar endast grundläggande statistik som min, max och antal fel för icke-numeriska kolumner. Numeriska kolumner kan du även läsa sina statistiska stund och uppskattade quantiles. Mer specifikt innehåller vår dataprofil:

* **Funktionen**: namnet på den kolumn som är som summeras.

* **Profilen**: en infogad visualisering utifrån typ härledd. Till exempel strängar, booleska värden och datum kommer att ha antal värden, medan decimaler (siffror) har ungefärligt histogram. På så sätt kan du få en snabb uppfattning om distributionen av data.

* **Skriv distribution**: en i Radvärde antal typer i en kolumn. Null-värden är sin egen typ, så att den här visualiseringen är användbart för att upptäcka udda eller saknade värden.

* **Typ**: den antydda typen för kolumnen. Några möjliga värden är: strängar, booleska värden, datum och decimaler.

* **Min**: det minsta värdet i kolumnen. Tomma poster visas för funktioner som vars typ inte har en inbyggd ordning (t.ex. booleska värden).

* **Max**: det högsta värdet för kolumnen. Tomma poster visas som ”min”, funktioner med irrelevanta typer.

* **Antal**: det totala antalet poster som saknas och icke-saknas i kolumnen.

* **Inte saknar antal**: antalet poster i kolumnen som inte är saknas. Observera att tomma strängar och fel behandlas som värden, så inte kommer att bidra till det ”inte saknas antalet”.

* **Quantiles** (0.1, 1, 5, 25, 50, 75, 95, 99 och 99,9% med intervall på): de uppskattade värdena på varje quantile att ge en uppfattning om distributionen av data. Tomma poster visas för funktioner med irrelevanta typer.

* **Innebära**: det aritmetiska medelvärdet för kolumnen. Tomma poster visas för funktioner med irrelevanta typer.

* **Standardavvikelse**: standardavvikelsen för kolumnen. Tomma poster visas för funktioner med irrelevanta typer.

* **Avvikelse**: variansen för kolumnen. Tomma poster visas för funktioner med irrelevanta typer.

* **Snedställningar**: snedställningar för kolumnen. Tomma poster visas för funktioner med irrelevanta typer.

* **Datamängds**: en datamängds kolumnen. Tomma poster visas för funktioner med irrelevanta typer.

Du kan dessutom använda statistiken för att bestämma om du vill inkludera eller exkludera vissa kolumner. Du kan styra vilka som ska användas i dina automatiserade machine learning-experiment genom att förflytta väljaren för varje kolumn.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Avancerade Förbearbeta

När du konfigurerar dina experiment, du kan aktivera avancerade inställningen `Preprocess`. Detta innebär det att följande data Förbearbeta och funktionalisering stegen utförs automatiskt.

|Förbearbeta&nbsp;steg| Beskrivning |
| ------------- | ------------- |
|Ta bort hög kardinalitet- eller inga varians-funktioner|Ta bort dessa från utbildnings- och verifiering mängder, inklusive funktioner med alla värden som saknas, samma värde på alla rader eller med mycket hög kardinalitet (till exempel hashvärden, ID: N eller GUID).|
|Sedan imputera värden som saknas|Sedan imputera med medelvärdet av värdena i kolumnen för numeriska funktioner.<br/><br/>Sedan imputera med vanligast förekommande värdet för kategoriska funktioner.|
|Generera ytterligare funktioner|För DateTime-funktioner: År, månad, dag, dag i vecka, dag på år, kvartal, vecka på året, timme, minut, sekund.<br/><br/>För Text-funktioner: Termen frekvensen baserat på unigrams, bi-g och tre-tecknet-g.|
|Omvandla och koda |Numeriska funktioner med få unika värden omvandlas till kategoriska funktioner.<br/><br/>En frekvent kodning utförs för låg kardinalitet kategoriska; för hög kardinalitet ett hot hash-kodning.|
|Ordinbäddningar|Text upplärda som konverterar vektorer av text-token till mening vektorer med hjälp av en förtränade modell. Bädda in vektor för varje ord i ett dokument sammanställs tillsammans för att skapa en funktion vektor för dokumentet.|
|Mål-kodningar|Kategoriska funktioner mappar i varje kategori med genomsnittlig målvärdet för regressionsproblem och klassen sannolikhet för varje klass för klassificering, problem. Frekvensbaserad viktas och k vikning korsvalidering används för att minska över montering av mappnings- eller brus på grund av null-optimerade datakategorier.|
|Textkodning mål|För textinmatning används en stående linjär modell med egenskapsuppsättning av ord för att generera sannolikheten för varje klass.|
|Vikten av bevis (missöden)|Beräknar missöden som ett mått på korrelation mellan kategoriska kolumner att målkolumnen. Det beräknas som en logg för förhållandet mellan i klassen vs out klass sannolikhet. Det här steget matar ut en numeriska kolumn per klass och eliminerar behovet av att uttryckligen sedan imputera värden som saknas och avvikare behandling.|
|Avståndet i kluster|Träna en klustringsmodell för k-means på alla numeriska kolumner.  Utdata k nya funktioner, en ny numeriska per kluster som innehåller avståndet från varje prov som axelcentroid för varje kluster.|

## <a name="run-experiment"></a>Kör experimentet

Kör experimentet genom att klicka på Start.
  
Experimentet förbereda processen tar några minuter.

## <a name="view-results"></a>Visa resultat

När förberedelsefasen experimentet är klart visas skärmen körningsinformation. Detta ger dig en fullständig lista över modeller som skapats. Som standard den modell som bedömer de högsta baserat på dina parametrar kommer att överst i listan. När utbildningsjobbet försöker ut fler modeller, ser du dem lagts till i listan och i diagrammet.
Använd diagrammet för att få en snabb jämförelse av mått för modeller som producerats hittills.

![Information om instrumentpanelen för körning](media/how-to-create-portal-experiments/run-details.png)

Du kan öka detaljnivån för någon av de utdata-modellerna som öppnas med information om den modellen inklusive prestanda och distribution av diagram och mått. [Mer information om diagram](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Iteration information](media/how-to-create-portal-experiments/dashboard.png)

Utbildning jobb kan ta en stund för varje pipeline som ska slutföras.

## <a name="deploy-model"></a>Distribuera modell

När du har den bästa modellen till hands, är det dags att distribuera den som en webbtjänst för att förutsäga på nya data.

Automatiserad ML hjälper dig att distribuera modellen utan att skriva kod:

1. Kör sammanfattning-rutan till höger väljer du ”registrera modellen”.

    ![Registrera modellen](media/how-to-create-portal-experiments/register-model.png)

1. När modellen har registrerats kommer du att kunna hämta bedömningsskriptet som ska användas under distributionen.

    ![Ladda ned bedömningsskriptet](media/how-to-create-portal-experiments/download-scoring-script.png)

1. När du har bedömningsskriptet kan gå till sidan ”modeller” (i det vänstra navigeringsfönstret under **tillgångar**).

    ![Modellen navigeringsfönstret](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Markera kryssrutan bredvid den modell som du har registrerat och välj ”Skapa avbildning”.

    Du kan identifiera modellen genom en beskrivning som innehåller ID: T och iteration körningsnumret, i följande format: **< Run_ID > _ < Iteration_number > _Model**.

1. Ange ett namn för avbildningen och överför bedömningsfil som du har hämtat tidigare. [Mer information om bedömning skript](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where.md#script).

    Du kan använda dina egna bedömningsskript och Conda-fil. Om du inte har en Conda-fil, [skapa egna](tutorial-deploy-models-with-aml.md#create-environment-file) och ladda upp den tillsammans med eventuella andra ytterligare filer som du kanske vill använda.

    ![Skapa en bild-formulär](media/how-to-create-portal-experiments/create-image.png)

1. Välj knappen ”Skapa” för att börja skapa avbildningar. Detta tar några minuter att slutföra när klar, visas ett meddelande i det översta fältet.

1. Gå till fliken ”avbildningar”, markera kryssrutan bredvid den avbildning du vill distribuera och välj ”Skapa distribution”.

    ![Bild skapa distributionsskärmen](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Ange ett unikt namn.

1. (valfritt) Ange en beskrivning av distributionen.

1. Välj måltyp för beräkning du använder. 

    ![Skapa formulär-distribution](media/how-to-create-portal-experiments/create-deployment.png)

1. Välj ”Skapa” för att starta distributionsprocessen, det tar flera minuter att slutföra.

1. Klart! Du har en operational webbtjänst för att generera förutsägelser.

## <a name="next-steps"></a>Nästa steg

* [Hur du använder en distribuerad modell](how-to-consume-web-service.md).
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md).