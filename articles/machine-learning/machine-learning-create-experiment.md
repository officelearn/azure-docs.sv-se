<properties
    pageTitle="Ett enkelt experiment i Machine Learning Studio | Microsoft Azure"
    description="Den här självstudien om Machine Learning vägleder dig genom ett enkelt dataexperiment. Vi kommer förutsäga priset för en bil med hjälp av en regressionsalgoritm."
    keywords="experiment, linjär regression,machine learning algoritmer, machine learning självstudier, teknik för förutsägbar modellering, dataexperiment"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# Självstudie om Machine Learning: Skapa ditt första dataexperiment i Azure Machine Learning Studio

Den här självstudien om Machine Learning vägleder dig genom ett enkelt dataexperiment. Vi kommer att skapa en linjär regressionsmodell som beräknar priset på en bil utifrån olika variabler som märke och tekniska specifikationer. Vi ska göra detta genom att utveckla och iterera ett enkelt experiment med en förutsägelseanalys med hjälp av Azure Machine Learning Studio.

*Förutsägelseanalys* är en typ av datavetenskap som använder aktuella data för att förutsäga framtida resultat. Om du vill se ett mycket enkelt exempel på förutsägelseanalys ska du se på Data Science for Beginners video 4 (Datavetenskap för nybörjare, video 4): [Predict an answer with a simple model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (Förutsäga ett svar med en enkel modell) (längd: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Hur kan Machine Learning Studio hjälpa?

Machine Learning Studio gör det enkelt att konfigurera ett experiment med dra och släpp-moduler som har förprogrammerats med tekniker för förutsägbar modellering. Om du vill köra experimentet och förutsäga ett svar ska du använda Machine Learning Studio för att *skapa en modell*, *träna modellen* och *bedöma och testa modellen*.

Gå till Machine Learning Studio: [https://studio.azureml.net](https://studio.azureml.net). Om du har loggat in Machine Learning Studio förut klickar du på **Logga in här**. Annars klickar du på **Registrera dig** och väljer mellan den kostnadsfria versionen och betalversionen.

Mer allmän information om Machine Learning Studio finns i [Vad är Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

## Fem steg för att skapa ett experiment

Den här självstudiekursen om maskininlärning består av fem grundläggande steg där du bygger ett experiment i Machine Learning Studio för att skapa, träna och betygsätta din modell:

- Skapa en modell
    - [Steg 1: Hämta data]
    - [Steg 2: Förbearbeta data]
    - [Steg 3: Definiera funktioner]
- Träna modellen
    - [Steg 4: Välja och tillämpa en inlärningsalgoritm]
- Poängsätta och testa modellen
    - [Steg 5: Förutsäga nya bilpriser]

[Steg 1: Hämta data]: #step-1-get-data
[Steg 2: Förbearbeta data]: #step-2-preprocess-data
[Steg 3: Definiera funktioner]: #step-3-define-features
[Steg 4: Välja och tillämpa en inlärningsalgoritm]: #step-4-choose-and-apply-a-learning-algorithm
[Steg 5: Förutsäga nya bilpriser]: #step-5-predict-new-automobile-prices


## Steg 1: Hämta data

Du kan välja någon av flera exempeluppsättningar med data som ingår i Machine Learning Studio och du kan importera data från flera källor. I det här exemplet kommer vi att använda den medföljande exempeluppsättningen **Automobile price data (Raw)**.
Den här datauppsättningen innehåller poster för ett antal olika bilar, inklusive uppgifter om modell, tekniska specifikationer och pris.

1. Starta ett nytt experiment genom att klicka på **+Nytt** längst ned i Machine Learning Studio-fönstret, välj **Experiment** och välj sedan **Tomt experiment**. Markera det fördefinierade experimentnamnet överst på arbetsytan och byt namn på experimentet till ett mer beskrivande namn, till exempel **Förutsägelse av bilpriser**.

2. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Skriv **automobile** i sökrutan överst på den här paletten för att leta upp datauppsättningen **Automobile price data (Raw)**.

    ![Palettsökning][screen1a]

3. Dra datauppsättningen till experimentarbetsytan.

    ![Datauppsättning][screen1]

Om du vill se hur den här informationen ser ut klickar du på utdataporten längst ned i uppsättningen med bildata och väljer sedan **Visualisera**.

![Modulens utgående port][screen1c]

Variablerna i datauppsättningen visas som kolumner och varje instans av en bil visas som en rad. Kolumnen längst till höger (kolumn 26 med rubriken ”price”) är en målvariabel som vi ska försöka förutsäga.

![Visualisering av datauppsättningar][screen1b]

Stäng visualiseringsfönstret genom att klicka på ”**x**” i det övre högra hörnet.

## Steg 2: Förbearbeta data

En datauppsättning kräver vanligtvis viss bearbetning i förväg innan den kan analyseras. Du kanske har lagt märke till värdena som saknas i kolumnerna på olika rader. Dessa värden som saknas måste rensas bort så att modellen kan analysera informationen korrekt. I vårt fall ska vi ta bort alla rader som har värden som saknas. Dessutom har kolumnen **normalized-losses** många värden som saknas, så vi ska utesluta kolumnen från modellen helt och hållet.

> [AZURE.TIP] Du måste rensa värden som saknas i indata för att kunna använda de flesta moduler.

Först ska vi ta bort kolumnen **normalized-losses** och sedan tar vi bort rader med data som saknas.

1. Skriv **välj kolumner** i sökrutan överst på modulpaletten för att leta upp modulen [Välj kolumner i datauppsättning][select-columns]. Dra modulen till experimentarbetsytan och koppla den till utdataporten för datauppsättningen **Automobile price data (Raw)**. Den här modulen gör att vi kan välja vilka kolumner med data som vi vill ta med eller utelämna i modellen.

2. Välj modulen [Välj kolumner i datauppsättning][select-columns] och klicka på **Starta kolumnväljaren** i rutan **Egenskaper**.

    - Klicka på **Med regler** till vänster.
    - Under **Börjar med** klickar du på **Alla kolumner**. Detta uppmanar [Välj kolumner i datauppsättning][select-columns] att gå igenom alla kolumner (utom de som vi ska utesluta).
    - I listrutorna väljer du **Exkludera** och **kolumnnamn** och klickar sedan i textrutan. En lista med kolumner visas. Välj **normalized-losses** så läggs den till i textrutan.
    - Stäng kolumnväljaren genom att klicka på bockmarkeringen (OK).

    ![Välja kolumner][screen3]

    Egenskapsrutan för **Välj kolumner i datauppsättning** anger att alla kolumner i datauppsättningen tas med utom **normalized-losses**.

    ![Välj egenskaper för Välj kolumner i datauppsättning][screen4]

    > [AZURE.TIP] Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. I vårt exempel dubbelklickar du på modulen [Välj kolumner i datauppsättning][select-columns] och skriver kommentaren ”Exkludera normalized-losses”.

3. Dra modulen [Rensa data som saknas][clean-missing-data] till arbetsytan för experimentet och koppla den till modulen [Välj kolumner i datauppsättning][select-columns]. I rutan **Egenskaper** väljer du **Ta bort hela raden** under **Rensningsläge** för att rensa data genom att ta bort rader utan värden. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.

    ![Egenskaper för Rensa data som saknas][screen4a]

4. Kör experimentet genom att klicka på **Kör** under arbetsytan för experimentet.

När experimentet är klart visas alla moduler med en grön bockmarkering som bekräftar att de har slutförts. Notera också statusen **Har slutförts** i det övre högra hörnet.

![Första körningen av experimentet][screen5]

Allt vi har gjort i experimentet hittills är att rensa bort data. Om du vill visa den rensade datauppsättningen klickar du på den vänstra utdataporten för modulen [Rensa data som saknas][clean-missing-data] (”Rensad datauppsättning”) och väljer **Visualisera**. Observera att kolumnen **normalized-losses** inte längre ingår och att det inte finns några värden som saknas.

Nu när vi har rensat bort data kan vi ange vilka funktioner som vi vill använda i förutsägelsemodellen.

## Steg 3: Definiera funktioner

Inom maskininlärning är *funktioner* enskilda mätbara egenskaper av något du är intresserad av. I vår datauppsättning representerar varje rad en bil och varje kolumn är en funktion i den bilen.

Det krävs en del experimenterande och kunskap om det problem som ska lösas för att hitta en bra uppsättning funktioner för att skapa en förutsägelsemodell. Vissa funktioner är bättre för att förutsäga målet än andra. Dessutom har vissa funktioner en stark korrelation med andra funktioner (till exempel city-mpg kontra highway-mpg), så de tillför inte mycket ny information till modellen och kan därför tas bort.

Vi ska skapa en modell som använder en delmängd av funktionerna i vår datauppsättning. Du kan komma tillbaka och välja andra funktioner, köra experimentet igen och se om du får bättre resultat. Men om du vill starta ska du prova följande funktioner:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Dra en modul av typen [Välj kolumner i datauppsättning][select-columns] till arbetsytan för experimentet och koppla den till den vänstra utdataporten för modulen [Rensa data som saknas][clean-missing-data]. Dubbelklicka på modulen och skriv ”Välj funktioner för förutsägelse”.

2. Klicka på **Starta kolumnväljaren** i fönstret **Egenskaper**.

3. Klicka på **Med regler**.

4. Under **Börja med** klickar du på **Inga kolumner** och väljer sedan **Inkludera** och **kolumnnamn** på filterraden. Ange vår lista med kolumnnamn. Detta uppmanar modulen att bara ta med de kolumner som vi anger.

    > [AZURE.TIP] Genom att köra experimentet har vi säkerställt att kolumndefinitionerna för våra data passerar från datauppsättningen genom modulen [Rensa data som saknas][clean-missing-data]. Det innebär att andra moduler som du ansluter också kommer att ha information från datauppsättningen.

5. Klicka på bockmarkeringen (OK).

![Välja kolumner][screen6]

Nu genereras datauppsättningen som vi ska använda i inlärningsalgoritmen i nästa steg. Du kan komma tillbaka senare och försöka igen med andra funktioner.

## Steg 4: Välja och tillämpa en inlärningsalgoritm

Nu när våra data är klara är det dags att gå vidare och träna och testa vår förutsägelsemodell. Vi ska använda våra data för att träna modellen och sedan testa den för att se hur väl den kan förutsäga priser. För tillfället behöver du inte bry dig om varför vi behöver träna och testa en modell.

*Klassificering* och *regression* är två typer av övervakade maskininlärningstekniker. Klassificering förutsäger ett svar från en definierad uppsättning kategorier, till exempel en färg (röd, blå eller grön). Regression används för att förutsäga ett tal.

Eftersom vi vill förutsäga pris, vilket är ett tal, använder vi en regressionsmodell. I det här exemplet ska vi träna en enkel *linjär regressionsmodell* och i nästa steg ska vi testa den.

1. Vi använder våra data både för träning och testning genom att dela in dem i separata tränings- och testningsuppsättningar. Markera och dra modulen [Dela data][split] till arbetsytan för experimentet och koppla den till utdataporten för den senaste modulen av typen [Välj kolumner i datauppsättning][select-columns]. Ange **Del av rader i den första utdatauppsättningen** till 0,75. På så sätt kan vi använda 75 procent av våra data för att träna modellen, och lämna 25 procent för testning.

    > [AZURE.TIP] Genom att ändra parametern **Slumptal** kan du generera olika slumpmässiga prov för träning och testning. Den här parametern styr den pseudoslumpmässiga talgeneratorns startvärden (seeding).

2. Kör experimentet. Detta gör att modulerna [Välj kolumner i datauppsättning][select-columns] och [Dela data][split] överför kolumndefinitionerna till de moduler som vi ska lägga till härnäst.  

3. Välj inlärningsalgoritmen genom att expandera kategorin **Machine Learning** på modulpaletten till vänster om arbetsytan och expandera **Initiera modell**. Nu visas flera kategorier av moduler som kan användas för att initiera algoritmer för maskininlärning.

    I det här experimentet väljer du modulen [Linear Regression][linear-regression] under kategorin **Regression** (du kan också hitta modulen genom att skriva ”Linear Regression” i sökrutan på paletten) och drar den till arbetsytan för experimentet.

4. Leta upp och dra modulen [Träna modell][train-modell] till arbetsytan för experimentet. Koppla den vänstra indataporten till utdataporten för modulen [Linear Regression][linear-regression]. Koppla den högra indataporten till utdataporten för träning (den vänstra porten) för modulen [Dela data][split].

5. Välj modulen [Träna modell][train-modell], klicka på **Starta kolumnväljaren** i rutan **Egenskaper** och välj kolumnen **price**. Det här är det värde som vår modell ska förutsäga.

    ![Välj kolumnen ”price”.][screen7]

6. Kör experimentet.

Resultatet är en tränad regressionsmodell som kan användas för att poängsätta nya prov för att göra förutsägelser.

![Tillämpa maskininlärningsalgoritmen][screen8]

## Steg 5: Förutsäga nya bilpriser

Nu när vi har tränat modellen med 75 procent av våra data kan vi använda den för att bedöma de övriga 25 procenten av dessa data och se hur bra vår modell fungerar.

1. Leta upp och dra modulen [Poängsätta modell][score-model] till arbetsytan för experimentet och koppla den vänstra indataporten till utdataporten för modulen [Träna modell][train-modell]. Koppla den högra indataporten till utdataporten för testning (den högra porten) för modulen [Dela data][split].  

    ![Modulen Poängsätta modell][screen8a]

2. Kör experimentet och visa utdata från modulen [Poängsätta modell][score-model] genom att klicka på utdataporten och sedan välja **Visualisera**. Utdata innehåller de förväntade värdena för pris och de kända värdena från testdata.  

3. För att slutligen testa resultatets kvalitet markerar du och drar modulen [Utvärdera modell][evaluate-model] till arbetsytan för expreimentet och kopplar den vänstra indataporten till utdataporten för modulen [Poängsätta modell][score-model]. (Det finns två indataportar eftersom modulen [Utvärdera modell][evaluate-model] kan användas för att jämföra två modeller.)

4. Kör experimentet.

Du visar utdata från modulen [Utvärdera modell][evaluate-model] genom att klicka på utdataporten och sedan välja **Visualisera**. För vår modell visas följande statistik:

- **Medelabsolutfel** (MAE): Medelvärdet av absoluta fel (ett *fel* är skillnaden mellan det förväntade och faktiska värdet).
- **Medelkvadratfel ** (RMSE): Kvadratroten av genomsnittet av kvadratfel i förutsägelser som görs mot testdatauppsättningen.
- **Relativa absoluta fel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
- **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
- **Bestämningskoefficient**: Kallas också för **R-kvadratvärdet** och är ett statistiskt mått som anger hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelser bättre överensstämmer med de faktiska värdena. För **bestämningskoefficient** är förutsägelserna bättre ju närmare värdet är ett (1,0).

![Utvärderingsresultat][screen9]

Det slutliga experimentet bör se ut så här:

![Självstudie om maskininlärning: Experiment med linjär regression som använder tekniker för förutsägelsemodellering.][screen10]

## Nästa steg

Nu när du har slutfört en första självstudie om maskininlärning och har skapat ett experiment kan du iterera det för att förbättra modellen. Du kan till exempel ändra funktionerna som du använder i din förutsägelse. Du kan också ändra egenskaperna för algoritmen för [Linear Regression][linear-regression] eller prova med en helt annan algoritm. Du kan också lägga till flera maskininlärningsalgoritmer i experimentet samtidigt och jämföra två med hjälp av modulen [Utvärdera modell][evaluate-model].

> [AZURE.TIP] Använd knappen **Spara som** under arbetsytan för experimentet för att kopiera eventuella iterationer av experimentet. Du kan visa alla iterationer av experimentet genom att klicka på **Visa körningshistorik** under arbetsytan. Mer information finns i [Hantera iterationer av experiment i Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

När du är nöjd med din modell kan du distribuera den som en webbtjänst som kan användas för att förutsäga bilpriser med hjälp av nya data. Mer information finns i [Distribuera en Azure Machine Learning-webbtjänst][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

En mer omfattande och detaljerad genomgång av tekniker för förutsägelsemodellering för att skapa, träna, värdera och distribuera en modell finns i [Utveckla en förutsägelselösning med hjälp av Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-modell]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=sep16_HO1-->


