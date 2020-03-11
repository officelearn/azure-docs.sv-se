---
title: Använda autoML för att skapa modeller & distribuera
titleSuffix: Azure Machine Learning
description: Skapa, granska och distribuera automatiserade maskin inlärnings modeller med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 706b67216d1037440fd1641d9bc82deee2c43109
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79078447"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Skapa, granska och distribuera automatiserade maskin inlärnings modeller med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar, utforskar och distribuerar automatiserade maskin inlärnings modeller utan en enda kodrad i Azure Machine Learning Studio-gränssnittet. Automatisk maskin inlärning är en process där den bästa Machine Learning-algoritmen som används för dina aktuella data väljs åt dig. Med den här processen kan du snabbt skapa maskin inlärnings modeller. [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md).
 
För ett slut punkt till slut punkts exempel kan du prova [självstudien för att skapa en klassificerings modell med Azure Machine Learning s automatiserade ml-gränssnitt](tutorial-first-experiment-automated-ml.md). 

[Konfigurera automatiserade maskin inlärnings experiment](how-to-configure-auto-train.md) med Azure Machine Learning SDK för en python-kod baserad upplevelse.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnads fritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En Azure Machine Learning arbets yta med en typ av **Enterprise-utgåva**. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).  Information om hur du uppgraderar en befintlig arbets yta till Enterprise Edition finns i [Uppgradera till Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Kom igång

1. Logga in på Azure Machine Learning på https://ml.azure.com. 

1. Välj din prenumeration och arbets yta. 

1. Navigera till den vänstra rutan. Välj **Automatisk ml** under avsnittet **författare** .

[navigerings fönstret ![Azure Machine Learning Studio](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Om det här är första gången du gör ett experiment visas en tom lista och länkar till dokumentationen. 

Annars visas en lista över dina senaste automatiserade maskin inlärnings experiment, inklusive de som har skapats med SDK: n. 

## <a name="create-and-run-experiment"></a>Skapa och kör experiment

1. Välj **+ ny automatiserad ml-körning** och fyll i formuläret.

1. Välj en data uppsättning från din lagrings behållare eller skapa en ny data uppsättning. Data uppsättningar kan skapas från lokala filer, webb-URL: er, data lager eller Azure Open-datauppsättningar. 

    >[!Important]
    > Krav för tränings data:
    >* Data måste vara i tabell form.
    >* Värdet som du vill förutse (mål kolumnen) måste finnas i data.

    1. Om du vill skapa en ny data uppsättning från en fil på den lokala datorn väljer du **Bläddra** och väljer sedan filen. 

    1. Ge din data uppsättning ett unikt namn och ange en valfri beskrivning. 

    1. Välj **Nästa** för att öppna **formuläret data lager och fil markering**. I det här formuläret väljer du var du vill ladda upp din data uppsättning. standard lagrings behållare som skapas automatiskt med din arbets yta eller Välj en lagrings behållare som du vill använda för experimentet. 

    1. Granska **inställningarna och för hands versions** formuläret för noggrannhet. Formuläret fylls i intelligent baserat på filtypen. 

        Field| Beskrivning
        ----|----
        Fil format| Definierar layout och typ av data som lagras i en fil.
        Avgränsare| Ett eller flera tecken för att ange avgränsningen mellan separata, oberoende regioner i oformaterad text eller andra data strömmar.
        Kodning| Identifierar vilken bit till Character-schema tabell som ska användas för att läsa din data uppsättning.
        Kolumn rubriker| Anger hur data uppsättningens huvuden, om det finns, kommer att behandlas.
        Hoppa över rader | Anger hur många rader som ska hoppas över i data uppsättningen.
    
        Välj **Nästa**.

    1. **Schema** formuläret fylls i intelligent utifrån valen i formuläret **Inställningar och för hands version** . Konfigurera data typen för varje kolumn, granska kolumn namnen och välj vilka kolumner som **inte ska ingå** i experimentet. 
            
        Välj **Nästa.**

    1. Formuläret **bekräfta information** är en sammanfattning av den information som tidigare har fyllts i i **grundläggande information** och **Inställningar och för hands** formulär. Du kan också välja att skapa en data profil för din data uppsättning med en profilerings aktive rad beräkning. Läs mer om [data profilering](#profile).

        Välj **Nästa**.
1. Välj den nyligen skapade data uppsättningen när den visas. Du kan också visa en förhands granskning av data uppsättningen och exempel statistiken. 

1. I formuläret **Konfigurera körning** anger du ett unikt experiment namn.

1. Välj en mål kolumn. Det här är den kolumn som du vill göra förutsägelser på.

1. Välj en beräkning för data profilering och utbildnings jobb. Det finns en lista över dina befintliga beräkningar i list rutan. Följ instruktionerna i steg 7 för att skapa en ny beräkning.

1. Välj **skapa en ny beräkning** för att konfigurera din beräknings kontext för det här experimentet.

    Field|Beskrivning
    ---|---
    Compute-namn| Ange ett unikt namn som identifierar din beräknings kontext.
    Storlek på virtuell dator| Välj storlek på den virtuella datorn för din beräkning.
    Min/max-noder (i avancerade inställningar)| Du måste ange 1 eller fler noder för att kunna profilera data. Ange det maximala antalet noder för din beräkning. Standardvärdet är 6 noder för en AML-beräkning.
    
    Välj **Skapa**. Det kan ta några minuter att skapa en ny beräkning.

    >[!NOTE]
    > Ditt beräknings namn anger om den beräkning som du väljer/skapar profilering är *aktive rad*. (Mer information finns i avsnittet om avsnitts [data profilering](#profile) ).

    Välj **Nästa**.

1. I formuläret **uppgifts typ och inställningar** väljer du uppgifts typ: klassificering, regression eller Prognosticering. Se [definiera aktivitets typer](how-to-define-task-type.md) för mer information.

    1. För klassificering kan du också aktivera djup inlärning som används för text featurizations.

    1. För Prognosticering:
        1. Välj tids kolumn: den här kolumnen innehåller de tids data som ska användas.

        1. Välj prognos Horisont: Ange hur många tidsenheter (minuter/timmar/dagar/veckor/månader/år) som modellen ska kunna förutsäga till framtiden. Den ytterligare modellen krävs för att förutsäga i framtiden. den mindre exakta den blir. [Lär dig mer om prognostisering av prognoser och prognoser](how-to-auto-train-forecast.md).

1. Valfritt Visa ytterligare konfigurations inställningar: ytterligare inställningar som du kan använda för att styra utbildnings jobbet bättre. Annars tillämpas standardvärdena utifrån experiment val och data. 

    Ytterligare konfigurationer|Beskrivning
    ------|------
    Primärt mått| Främsta mått som används för att värdera din modell. [Lär dig mer om modell mått](how-to-configure-auto-train.md#explore-model-metrics).
    Automatisk funktionalisering| Välj det här alternativet om du vill aktivera eller inaktivera förbearbetningen som gjorts genom automatisk maskin inlärning. För bearbetning inkluderar automatisk rensning av data, förberedelser och transformering för att generera syntetiska funktioner. Stöds inte för aktivitets typen tids serie prognos. [Läs mer om för bearbetning](#featurization). 
    Förklara bästa modell | Välj om du vill aktivera eller inaktivera för att visa att den rekommenderade bästa modellen är klar
    Blockerad algoritm| Välj algoritmer som du vill undanta från utbildnings jobbet.
    Avslutnings kriterium| När något av dessa villkor uppfylls stoppas utbildnings jobbet. <br> *Utbildnings jobb tid (timmar)* : hur lång tid det tar att köra utbildnings jobbet. <br> *Mät*värdes tröskel: minsta mått Poäng för alla pipeliner. Detta säkerställer att om du har ett definierat målmått som du vill nå, ägnar du inte mer tid åt övnings jobbet än nödvändigt.
    Validering| Välj ett av de kors validerings alternativ som ska användas i övnings jobbet. [Läs mer om kors validering](how-to-configure-auto-train.md).
    Samtidighet| *Max. antal samtidiga iterationer*: maximalt antal pipelines (iterationer) som ska testas i utbildnings jobbet. Jobbet kan inte köra fler än det angivna antalet iterationer.

1. Valfritt Visa funktionalisering-inställningar: om du väljer att aktivera **Automatisk funktionalisering** i formuläret för **ytterligare konfigurations inställningar** är det här formuläret där du anger vilka kolumner som featurizations ska utföras på och väljer vilket statistisk värde som ska användas för det saknade värdet imputations.

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
Typ|Den härledda typen för kolumnen. Möjliga värden är: strängar, booleska värden, datum och decimaler.
Min| Minsta värde för kolumnen. Tomma poster visas för funktioner vars typ inte har en inbyggd ordning (t. ex. booleska värden).
Max| Max värde för kolumnen. 
Antal| Totalt antal saknade och icke-saknade poster i kolumnen.
Antal saknas inte| Antal poster i kolumnen som inte saknas. Tomma strängar och fel behandlas som värden, så de kommer inte att bidra till det antal som saknas.
Quantiles| Ungefärligt värde vid varje quantile för att ge en uppfattning om data fördelningen.
Medelvärde| Aritmetiskt medelvärde eller genomsnitt för kolumnen.
Standardavvikelse| Mått på mängden spridning eller variation för den här kolumnens data.
Varians| Mått på hur långt spridning av den här kolumnens data är från det genomsnittliga värdet. 
Snedhet| Mått på hur olika data från den här kolumnen kommer från en normal distribution.
Toppighet| Mått på hur mycket data som har staplats i den här kolumnens data jämförs med en normal distribution.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Avancerade alternativ för funktionalisering

Automatisk maskin inlärning erbjuder förbehandling och data guardrails automatiskt, som hjälper dig att identifiera och hantera potentiella problem med dina data. 

### <a name="preprocessing"></a>Förbearbeta

|Förbearbeta&nbsp;steg| Beskrivning |
| ------------- | ------------- |
|Ta bort hög kardinalitet- eller inga varians-funktioner|Ta bort dessa från inlärnings-och validerings uppsättningar, inklusive funktioner med alla värden som saknas, samma värde på alla rader eller med mycket hög kardinalitet (till exempel hash-värden, ID: n eller GUID).|
|Sedan imputera värden som saknas|För numeriska funktioner måste du räkna ut med medelvärdet av värdena i kolumnen.<br/><br/>För kategoriska-funktioner ska du räkna med det mest frekventa värdet.|
|Generera ytterligare funktioner|För DateTime-funktioner: år, månad, dag, dag i vecka, dag på år, kvartal, vecka på året, timme, minut, sekund.<br/><br/>För text funktioner: term frekvens baserat på unigrams, bi-gram och Tri-Character-gram.|
|Transformera och koda |Numeriska funktioner med få unika värden omvandlas till kategoriska-funktioner.<br/><br/>En-frekvent kodning utförs för kategoriska med låg kardinalitet. för hög kardinalitet, en-frekvent-hash-kodning.|
|Word-inbäddningar|Text upplärda som konverterar vektorer med text-token till menings vektorer med en förtränad modell. Varje ords inbäddnings vektor i ett dokument sammanställs tillsammans för att skapa en dokument funktions vektor.|
|Mål kodningar|För kategoriska-funktioner, mappar varje kategori med genomsnittligt målvärde för Regressions problem och till sannolikheten för varje klass för klassificerings problem. Frekvens-baserad viktning och mellanliggande kors validering används för att minska överbelastningen av mappningen och bruset som orsakas av glesa data kategorier.|
|Kodning av text mål|För text inmatare används en staplad linjär modell med ord uppsättnings ord för att generera sannolikheten för varje klass.|
|Vikt på bevis (WoE)|Beräknar WoE som ett mått på korrelation av kategoriska-kolumner till mål kolumnen. Det beräknas som loggen för förhållandet mellan sannolikheten i förhållande till inaktuella klasser. Det här steget matar ut en numerisk funktions kolumn per klass och tar bort behovet av att explicit kräva att värden som saknas och avvikare behandlas.|
|Kluster avstånd|Tågen a k: kluster modell på alla numeriska kolumner.  Utdata k nya funktioner, en ny numerisk funktion per kluster, som innehåller avståndet från varje exempel till centroid för varje kluster.|

### <a name="data-guardrails"></a>Data guardrails

Data guardrails tillämpas automatiskt för att hjälpa dig att identifiera eventuella problem med dina data (t. ex. saknade värden, klass obalans) och hjälp med att vidta åtgärder för förbättrade resultat. Det finns många metod tips som är tillgängliga och som kan användas för att uppnå pålitliga resultat. 

I följande tabell beskrivs de data guardrails som stöds för tillfället och associerade status värden som användarna kan komma att komma åt när de skickar experimentet.

Guardrail|Status|Villkors&nbsp;för&nbsp;-utlösare
---|---|---
&nbsp;värden som saknas&nbsp;Imputation |**Parametrarna** <br> <br> **Fastsatt**|    Inget saknat värde i någon av de inmatade&nbsp;kolumnerna <br> <br> Vissa kolumner saknar värden
Korsvalidering|**Möjligt**|Om ingen explicit verifierings uppsättning anges
Hög&nbsp;kardinalitet&nbsp;funktion&nbsp;identifiering|    **Parametrarna** <br> <br>**Möjligt**|    Inga funktioner för hög kardinalitet upptäcktes <br><br> Inmatade kolumner med hög kardinalitet upptäcktes
Identifiering av klass balans    |**Parametrarna** <br><br><br>**Aviserad** |Klasser är balanserade i tränings data; En data mängd betraktas som balanserade om varje klass har god representation i data uppsättningen, mätt enligt antal och samplings förhållandet <br> <br> Klasser i tränings data är obalanserade
Data konsekvens för Time-serien|**Parametrarna** <br><br><br><br> **Fastsatt** |<br> Det valda {horisont-, fördröjnings-, rullande Window}-värdet har analyser ATS och inga potentiella minnes problem har identifierats. <br> <br>De valda värdena för {Horisont, fördröjning, rullande fönster} analyserades och kan orsaka att experimentet får slut på minne. Fördröjningen eller rullnings fönstret har inaktiverats.

## <a name="run-experiment-and-view-results"></a>Kör experimentet och visa resultaten

Klicka på **Slutför** för att köra experimentet. Experimentförberedelserna kan ta upp till 10 minuter. För träningsjobb kan det ta ytterligare 2–3 minuter för varje pipeline att slutföra körningen.

### <a name="view-experiment-details"></a>Visa experiment information

Skärmen **körnings information** öppnas på fliken **information** . Den här skärmen visar en sammanfattning av experiment körningen inklusive ett statusfält överst bredvid körnings numret. 

Fliken **Modeller** innehåller en lista över de modeller som skapats ordnade efter måttpoängen. Som standard visas modellen med högst poäng utifrån det valda måttet överst i listan. När träningsjobbet testar fler modeller läggs de till i listan. Använd det här för att få en snabb jämförelse av måtten för de modeller som har producerats hittills.

[instrument panel för ![körnings information](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visa information om tränings körningar

Öka detalj nivån för alla färdiga modeller för att se information om utbildnings körning, som att köra mått på fliken **modell information** eller prestanda diagram på fliken **visualiseringar** . [Läs mer om diagram](how-to-understand-automated-ml.md).

[![upprepnings information](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuera din modell

När du har den bästa modellen till hands är det dags att distribuera den som en webb tjänst för att förutse nya data.

Med automatisk ML får du hjälp med att distribuera modellen utan att skriva kod:

1. Du har ett par alternativ för distribution. 

    + Alternativ 1: om du vill distribuera den bästa modellen (enligt de mått kriterier som du har definierat) väljer du knappen **distribuera bästa modell** på fliken **information** .

    + Alternativ 2: om du vill distribuera en speciell modell iteration från det här experimentet går du nedåt i modellen för att öppna fliken **modell information** och väljer **distribuera modell**.

1. Fyll i fönstret **distribuera modell** .

    Field| Värde
    ----|----
    Namn| Ange ett unikt namn för din distribution.
    Beskrivning| Ange en beskrivning för att bättre identifiera vad den här distributionen är för.
    Compute-typ| Välj den typ av slut punkt som du vill distribuera: *Azure Kubernetes service (AKS)* eller *Azure Container Instance (ACI)* .
    Compute-namn| *Gäller endast för AKS:* Välj namnet på det AKS-kluster som du vill distribuera till.
    Aktivera autentisering | Välj för att tillåta tokenbaserad eller nyckelbaserad autentisering.
    Använda anpassade distributions till gångar| Aktivera den här funktionen om du vill överföra ditt eget bedömnings skript och miljö fil. [Lär dig mer om bedömnings skript](how-to-deploy-and-where.md#script).

    >[!Important]
    > Fil namn måste vara under 32 tecken och måste börja och sluta med alfanumeriska tecken. Får innehålla bindestreck, under streck, punkter och alfanumeriska tecken mellan. Blank steg är inte tillåtna.

    Menyn *Avancerat* erbjuder standard distributions funktioner, till exempel inställningar för [data insamling](how-to-enable-app-insights.md) och resursutnyttjande. Om du vill åsidosätta dessa standardinställningar gör du det på den här menyn.

1. Välj **Distribuera**. Distributionen kan ta ungefär 20 minuter att slutföra.

Nu har du en fungerande webb tjänst för att generera förutsägelser! Du kan testa förutsägelserna genom att fråga tjänsten från [Power BI inbyggda Azure Machine Learning-supporten](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder en webb tjänst](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).
* [Lär dig mer om automatisk maskin inlärning](concept-automated-ml.md) och Azure Machine Learning.
