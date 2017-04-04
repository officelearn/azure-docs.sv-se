---
title: Ett enkelt experiment i Machine Learning Studio | Microsoft Docs
description: "Den här självstudien om Machine Learning vägleder dig genom ett enkelt dataexperiment. Vi kommer förutsäga priset för en bil med hjälp av en regressionsalgoritm."
keywords: "experiment, linjär regression,machine learning algoritmer, machine learning självstudier, teknik för förutsägbar modellering, dataexperiment"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0539e9d04c61d35de56a29d350c07654c095c576
ms.lasthandoff: 03/22/2017


---

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Självstudie om Machine Learning: Skapa ditt första dataexperiment i Azure Machine Learning Studio

Om du aldrig har använt **Azure Machine Learning Studio** tidigare är den här självstudien något för dig.

I den här självstudiekursen går vi igenom hur du använder Studio för första gången för att skapa ett Machine Learning-experiment. I experimentet testas en analytisk modell som beräknar priset på en bil utifrån olika variabler som märke och tekniska specifikationer.

> [!NOTE]
> I den här självstudiekursen går vi igenom grunderna för hur du drar och släpper moduler till ditt experiment, kopplar ihop dem, kör experimentet och tittar på resultatet. Vi diskuterar inte det allmänna avsnittet om Machine Learning eller hur du väljer och använder de över 100 inbyggda modulerna för algoritmer och datamodifieringar som ingår i Studio.
>
>Om du inte har använt Machine Learning tidigare kan det vara bra att börja med videoserien [Datavetenskap för nybörjare](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Med den här videoserien får du en bra introduktion till Machine Learning med ord och begrepp som är lätta att förstå.
>
>Om du är bekant med Machine Learning men är ute efter mer allmän information om Machine Learning Studio och vilka algoritmer som ingår hittar du några bra resurser här:
>
- [Vad är Machine Learning Studio?](machine-learning-what-is-ml-studio.md) – Detta är en översikt över Studio på hög nivå.
- [Machine Learning-grunder med algoritmexempel](machine-learning-basics-infographic-with-algorithm-examples.md) – Den här informationsgrafiken är praktisk om du vill lära dig mer om de olika typerna av algoritmexempel som ingår i Machine Learning Studio.
- [Machine Learning-guide](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) – I den här guiden finns liknande information som i informationsgrafiken ovan, men i ett interaktivt format.
- [Machine Learning-algoritmfacit](machine-learning-algorithm-cheat-sheet.md) och [Så här väljer du algoritmer för Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md) – I den här hämtningsbara affischen och medföljande artikeln diskuteras Studio-algoritmerna ingående.
- [Machine Learning Studio: Algorithm and Module Help](https://msdn.microsoft.com/library/azure/dn905974.aspx) (Machine Learning Studio: Hjälp för algoritmer och moduler) – Det här är den fullständiga referensen för alla Studio-moduler, däribland Machine Learning-algoritmer,

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Hur kan Machine Learning Studio hjälpa?

Machine Learning Studio gör det enkelt att konfigurera ett experiment med dra och släpp-moduler som har förprogrammerats med tekniker för förutsägbar modellering.

Med en interaktiv, visuell arbetsyta kan du dra och släppa ***datauppsättningar*** och analysera ***moduler*** till en interaktiv arbetsyta. Du kopplar ihop dem och skapar ett ***experiment*** som du kör i Machine Learning Studio.
Du ***skapar en modell***, ***tränar *** och ***poängsätter och testar den***.

Du kan iterera din modelldesign, redigera experimentet och köra det tills du får de resultat du är ute efter. När din modell är klar kan du publicera den som en ***webbtjänst*** så andra kan skicka nya data till den och få förutsägelser i utbyte.

## <a name="open-machine-learning-studio"></a>Öppna Machine Learning Studio

För att komma igång med Studio går du till [https://studio.azureml.net](https://studio.azureml.net). Om du har loggat in på Machine Learning Studio förut klickar du på **Logga in**. Annars klickar du på **Registrera dig här** och väljer mellan den kostnadsfria versionen och betalversionen.

![Logga in på Machine Learning Studio][sign-in-to-studio]
<br/>
***Logga in på Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Fem steg för att skapa ett experiment

Den här självstudiekursen om Machine Learning består av fem grundläggande steg där du bygger ett experiment i Machine Learning Studio för att skapa, träna och betygsätta din modell:

- **Skapa en modell**
    - [Steg 1: Hämta data]
    - [Steg 2: Förbereda data]
    - [Steg 3: Definiera funktioner]
- **Träna modellen**
    - [Steg 4: Välja och tillämpa en inlärningsalgoritm]
- **Poängsätta och testa modellen**
    - [Steg 5: Förutsäga nya bilpriser]

[Steg 1: Hämta data]: #step-1-get-data
[Steg 2: Förbereda data]: #step-2-prepare-the-data
[Steg 3: Definiera funktioner]: #step-3-define-features
[Steg 4: Välja och tillämpa en inlärningsalgoritm]: #step-4-choose-and-apply-a-learning-algorithm
[Steg 5: Förutsäga nya bilpriser]: #step-5-predict-new-automobile-prices

> [!TIP] 
> En arbetskopia av följande experiment finns i [Cortana Intelligence-galleriet](https://gallery.cortanaintelligence.com). Gå till **[ditt första datavetenskapsexperiment – Förutsägelse av bilpriser](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** och klicka på **Open in Studio** (Öppna i Studio) för att hämta en kopia av experimentet till din Machine Learning Studio-arbetsyta.


## <a name="step-1-get-data"></a>Steg 1: Hämta data

Det första du måste använda Machine Learning till är data.
Du kan använda någon av flera exempeluppsättningar med data som ingår i Machine Learning Studio, eller så kan du importera data från flera källor. I det här exemplet kommer vi att använda exempeluppsättningen **Automobile price data (Raw)**, som ingår i arbetsytan.
Den här datauppsättningen innehåller poster för ett antal olika bilar, inklusive uppgifter om modell, tekniska specifikationer och pris.

Så här gör du för att få datauppsättningen till experimentet.

1. Skapa ett nytt experiment genom att klicka på **+Nytt** längst ned i Machine Learning Studio-fönstret, välja **Experiment** och sedan **Tomt experiment**.

2. Experimentet får ett standardnamn som visas överst i arbetsytan. Markera texten och byt namn på den till ett mer beskrivande namn, exempelvis **Förutsägelse av bilpriser**. Namnet behöver inte vara unikt.

    ![Byt namn på experimentet][rename-experiment]

2. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Skriv **automobile** i sökrutan överst på den här paletten för att leta upp datauppsättningen **Automobile price data (Raw)**. Dra datauppsättningen till experimentarbetsytan.

    ![Hitta uppsättningen med bildata och dra den till experimentets arbetsyta][type-automobile]
    <br/>
    ***Hitta uppsättningen med bildata och dra den till experimentets arbetsyta***

Om du vill se hur den här informationen ser ut klickar du på utdataporten längst ned i uppsättningen med bildata och väljer sedan **Visualisera**.

![Klicka på utdataporten och välj "Visualisera"][select-visualize]
<br/>
***Klicka på utdataporten och välj "Visualisera"***

> [!TIP]
> Datauppsättningar och moduler har ingångs- och utgångsportar som representeras av små cirklar – ingångsportar högst upp och utgångsportar längst ned.
Om du vill skapa ett flöde av data via ditt experiment ansluter du en utgångsport från en modul till en port på en annan.
Du kan när som helst klicka på en datauppsättnings eller moduls utgångsport för att se hur dina data ser ut vid den tidpunkten i dataflödet.

I den här exempeldatauppsättningen visas varje förekomst av en bil som en rad, och variablerna som är kopplade till varje bil visas som kolumner. Utifrån variablerna för en viss bil ska vi försöka förutsäga priset i kolumnen längst till höger (kolumn 26, ”pris”).

![Visa bildata i datavisualiseringsfönstret][visualize-auto-data]
<br/>
***Visa bildata i datavisualiseringsfönstret***

Stäng visualiseringsfönstret genom att klicka på ”**x**” i det övre högra hörnet.

## <a name="step-2-prepare-the-data"></a>Steg 2: Förbereda data

En datauppsättning kräver vanligtvis viss bearbetning i förväg innan den kan analyseras. Du kanske till exempel har lagt märke till värdena som saknas i kolumnerna på olika rader. Dessa värden som saknas måste rensas bort så att modellen kan analysera informationen korrekt. I vårt fall ska vi ta bort alla rader som har värden som saknas. Dessutom har kolumnen **normalized-losses** många värden som saknas, så vi ska utesluta kolumnen från modellen helt och hållet.

> [!TIP]
> Du måste rensa värden som saknas i indata för att kunna använda de flesta moduler.

Först lägger vi till en modul som tar bort kolumnen **normalized-losses** helt, och sedan lägger vi till en annan modul som tar bort rader där data saknas.

1. Skriv **välj kolumner** i sökrutan överst på modulpaletten för att leta upp modulen [Välj kolumner i datauppsättning][select-columns]. Dra sedan modulen till experimentarbetsytan. Den här modulen gör att vi kan välja vilka kolumner med data som vi vill ta med eller utelämna i modellen.

2. Anslut utdataporten för datauppsättningen **Automobile price data (Raw)** till indataporten för modulen [Välj kolumner i datauppsättning][select-columns].

    ![Lägg till modulen "Välj kolumner i datauppsättning" till experimentets arbetsyta och koppla den][type-select-columns]
    <br/>
    ***Lägg till modulen "Välj kolumner i datauppsättning" till experimentets arbetsyta och koppla den***

3. Klicka på modulen [Välj kolumner i datauppsättning][select-columns] och klicka på **Starta kolumnväljaren** i rutan **Egenskaper**.

    - Klicka på **Med regler** till vänster.
    - Under **Börjar med** klickar du på **Alla kolumner**. Detta uppmanar [Välj kolumner i datauppsättning][select-columns] att gå igenom alla kolumner (utom de som vi ska utesluta).
    - I listrutorna väljer du **Exkludera** och **kolumnnamn** och klickar sedan i textrutan. En lista med kolumner visas. Välj **normalized-losses** så läggs den till i textrutan.
    - Stäng kolumnväljaren genom att klicka på bockmarkeringen (OK) (längst ned till höger).

    ![Starta kolumnväljaren och utelämna kolumnen "normalized-losses"][launch-column-selector]
    <br/>
    ***Starta kolumnväljaren och utelämna kolumnen "normalized-losses"***

    Egenskapsrutan för **Välj kolumner i datauppsättning** anger nu att alla kolumner i datauppsättningen tas med utom **normalized-losses**.

    ![Egenskapsrutan visar att kolumnen "normalized-losses" är utelämnad][showing-excluded-column]
    <br/>
    ***Egenskapsrutan visar att kolumnen "normalized-losses" är utelämnad***

    > [!TIP]
    Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. I vårt exempel dubbelklickar du på modulen [Välj kolumner i datauppsättning][select-columns] och skriver kommentaren ”Exkludera normaliserade förluster”.
    >
    >


    ![Lägg till en kommentar genom att dubbelklicka på en modul][add-comment]
    <br/>
    ***Lägg till en kommentar genom att dubbelklicka på en modul***

3. Dra modulen [Rensa data som saknas][clean-missing-data] till experimentarbetsytan och anslut den till modulen [Välj kolumner i datauppsättning][select-columns]. I fönstret **Egenskaper** väljer du **Ta bort hela raden** under **Rensningsläge**. När du gör det uppmanas modulen [Rensa data som saknas][clean-missing-data] att rensa data genom att ta bort rader som har värden som saknas. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.

    ![Ställ in rengöringsläget på "Ta bort hela raden" för modulen "Rensa data som saknas"][set-remove-entire-row]
    <br/>
    ***Ställ in rengöringsläget på "Ta bort hela raden" för modulen "Rensa data som saknas"***

4. Kör försöket genom att klicka på **KÖR** längst ned på sidan.

    När experimentet är klart visas alla moduler med en grön bockmarkering som bekräftar att de har slutförts. Notera också statusen **Har slutförts** i det övre högra hörnet.

![Efter körning bör experimentet se ut ungefär så här][early-experiment-run]
<br/>
***Efter körning bör experimentet se ut ungefär så här***

> [!TIP]
> Varför kör vi experimentet nu? När experimentet körs överförs kolumndefinitionerna för våra data från datauppsättningen genom modulen [Välj kolumner i datauppsättning][select-columns] och genom modulen [Rensa data som saknas][clean-missing-data]. Detta innebär att alla moduler som vi ansluter till [Rensa data som saknas][clean-missing-data] får samma information.

Allt vi har gjort i experimentet hittills är att rensa bort data. Om du vill visa den rensade datauppsättningen klickar du på den vänstra indataporten för modulen [Rensa data som saknas][clean-missing-data] och väljer **Visualisera**. Observera att kolumnen **normalized-losses** inte längre ingår och att det inte finns några värden som saknas.

Nu när vi har rensat bort data kan vi ange vilka funktioner som vi vill använda i förutsägelsemodellen.

## <a name="step-3-define-features"></a>Steg 3: Definiera funktioner

Inom Machine Learning är *funktioner* enskilda mätbara egenskaper av något du är intresserad av. I vår datauppsättning representerar varje rad en bil och varje kolumn är en funktion i den bilen.

Det krävs en del experimenterande och kunskap om det problem som ska lösas för att hitta en bra uppsättning funktioner för att skapa en förutsägelsemodell. Vissa funktioner är bättre för att förutsäga målet än andra. Dessutom kan vissa funktioner ha en stark korrelation med andra funktioner och går att ta bort. Till exempel är city-mpg och highway-mpg nära relaterade så att vi kan behålla en och ta bort den andra utan att förutsägelsen påverkas avsevärt.

Vi ska skapa en modell som använder en delmängd av funktionerna i vår datauppsättning. Du kan komma tillbaka senare och välja andra funktioner, köra experimentet igen och se om du får bättre resultat. Men om du vill starta ska du prova följande funktioner:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Dra en till modul av typen [Välj kolumner i datauppsättning][select-columns] till experimentarbetsytan. Anslut den vänstra utdataporten för modulen [Rensa data som saknas][clean-missing-data] till indataporten för modulen [Välj kolumner i datauppsättning][select-columns].

    ![Anslut modulen "Välj kolumner i datauppsättning" till modulen "Rensa data som saknas"][connect-clean-to-select]
    <br/>
    ***Anslut modulen "Välj kolumner i datauppsättning" till modulen "Rensa data som saknas”***

2. Dubbelklicka på modulen och skriv ”Välj funktioner för förutsägelse”.

2. Klicka på **Starta kolumnväljaren** i fönstret **Egenskaper**.

3. Klicka på **Med regler**.

4. Under **Börjar med** klickar du på **Inga kolumner**. I filterraden väljer du **Inkludera** och **kolumnnamn** och markerar listan över kolumnnamn i textrutan. Detta uppmanar modulen att bara ta med de kolumner (funktioner) som vi anger.

5. Klicka på bockmarkeringen (OK).

    ![Markera de kolumner (funktioner) som ska ingå i förutsägelsen][select-columns-to-include]
    <br/>
    ***Markera de kolumner (funktioner) som ska ingå i förutsägelsen***

Då skapas en filtrerad datauppsättning som enbart innehåller de funktioner vi vill skicka till inlärningsalgoritmen som vi kommer att använda i nästa steg. Du kan komma tillbaka senare och försöka igen med andra funktioner.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Steg 4: Välja och tillämpa en inlärningsalgoritm

Nu när våra data är klara är det dags att gå vidare och träna och testa vår förutsägelsemodell. Vi ska använda våra data för att träna modellen och sedan testa den för att se hur väl den kan förutsäga priser.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Klassificering* och *regression* är två typer av övervakade Machine Learning-algoritmer. Klassificering förutsäger ett svar från en definierad uppsättning kategorier, till exempel en färg (röd, blå eller grön). Regression används för att förutsäga ett tal.

Eftersom vi vill förutsäga pris, vilket är ett tal, använder vi en regressionsalgoritm. I det här exemplet kommer vi att använda en *linjär regressionsmodell*.

> [!TIP]
> Om du vill veta mer om olika typer av Machine Learning-algoritmer och när du ska använda dem kan du titta på den första videon i serien Datavetenskap för nybörjare, och [de fem frågorna och svaren om datavetenskap](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Du kan också ta en titt på informationsgrafiken [Machine Learning-grunder med algoritmexempel](machine-learning-basics-infographic-with-algorithm-examples.md), eller kolla in[Machine Learning-algoritmfacit](machine-learning-algorithm-cheat-sheet.md).

Vi tränar modellen genom att ge den en uppsättning data som innehåller priset. Modellen söker efter data och letar efter samband mellan en bils funktioner och dess pris. Därefter testar vi modellen – den får en uppsättning funktioner för bilar vi känner till, så vi kan se hur nära modellen kommer att förutsäga det kända priset.

Vi använder våra data både för träning och testning av modellen genom att dela in data i separata tränings- och testningsdatauppsättningar.

1. Markera och dra modulen [Dela data][split] till experimentarbetsytan och anslut den till den sista modulen av typen [Välj kolumner i datauppsättning][select-columns].

2. Klicka på modulen [Dela data][split] för att välja den. Leta upp **Del av rader i den första utdatauppsättningen** (i fönstret **Egenskaper** på höger sida i arbetsytan) och ange 0,75. På så sätt kan vi använda 75 procent av våra data för att träna modellen, och lämna 25 procent för testning (senare kommer du att kunna experimentera med olika procentandelar).

    ![Ställ in värdet för "Dela data" på 0,75][set-split-data-percentage]
    <br/>
    ***Ställ in värdet för "Dela data" på 0,75***

    > [!TIP]
    > Genom att ändra parametern **Slumptal** kan du generera olika slumpmässiga prov för träning och testning. Den här parametern styr den pseudoslumpmässiga talgeneratorns startvärden (seeding).

2. Kör experimentet. När experimentet har körts överför modulerna [Välj kolumner i datauppsättning][select-columns] och [Dela data][split] kolumndefinitionerna till de moduler som vi ska lägga till härnäst.  

3. Välj inlärningsalgoritmen genom att expandera kategorin **Machine Learning** på modulpaletten till vänster om arbetsytan och expandera **Initiera modell**. Nu visas flera kategorier av moduler som kan användas för att initiera algoritmer för Machine Learning. I det här experimentet väljer du modulen [Linjär regression][linear-regression] under kategorin **Regression** och drar den till arbetsytan för experimentet.
(Du kan också hitta modulen genom att skriva "linjär regression" i rutan Sök på paletten.)

4. Leta upp och dra modulen [Träna modell][train-model] till arbetsytan för experimentet. Anslut utdataporten för modulen [Linjär regression][linear-regression] till den vänstra indataporten för modulen [Träna modell][train-model] och anslut träningsresultatet (vänster port) för modulen [Dela data][split] till den högra indatasporten för modulen [Träna modell][train-model].

    ![Anslut modulen "Träna modell" både till modulerna "Linjär regression" och "Dela data"][connect-train-model]
    <br/>
    ***Anslut modulen "Träna modell" både till modulerna "Linjär regression" och "Dela data"***

5. Klicka på modulen [Träna modell][train-model], klicka på **Starta kolumnväljaren** i rutan **Egenskaper** och välj kolumnen **price**. Det här är det värde som vår modell ska förutsäga.

    Du väljer **pris**kolumnen i kolumnväljaren genom att dra den från listan över **tillgängliga kolumner** till listan över **markerade kolumner**.

    ![Välj priskolumnen för modulen "Träna modell"][select-price-column]
    <br/>
    ***Välj priskolumnen för modulen "Träna modell"***

6. Kör experimentet.

Nu har vi en tränad regressionsmodell som kan användas för att poängsätta nya bildata för att göra prisförutsägelser.

![Efter körning bör experimentet se ut ungefär så här][second-experiment-run]
<br/>
***Efter körning bör experimentet se ut ungefär så här***

## <a name="step-5-predict-new-automobile-prices"></a>Steg 5: Förutsäga nya bilpriser

Nu när vi har tränat modellen med 75 procent av våra data kan vi använda den för att bedöma de övriga 25 procenten av dessa data och se hur bra vår modell fungerar.

1. Leta upp och dra modulen [Poängsätta modell][score-model] till arbetsytan för experimentet. Anslut utdataporten för modulen [Träna modell][train-model] till den vänstra indataporten för [Poängsätta modell][score-model]. Anslut utdataporten för testning (den högra porten) för modulen [Dela data][split] till den högra indataporten för [Poängsätta modell][score-model].

    ![Anslut modulen "Poängsätta modell" både till modulerna "Träna modell" och "Dela data"][connect-score-model]
    <br/>
    ***Anslut modulen "Poängsätta modell" både till modulerna "Träna modell" och "Dela data"***

2. Kör experimentet och visa utdata från modulen [Poängsätta modell][score-model] (klicka på utdataporten för [Poängsätta modell][score-model] och välj **Visualisera**). Utdata innehåller de förväntade värdena för pris och de kända värdena från testdata.  

    ![Utdata från modulen "Poängsätta modell"][score-model-output]
    <br/>
    ***Utdata från modulen "Poängsätta modell"***

3. Slutligen testar vi resultatets kvalitet. Markera och dra modulen [Utvärdera modell][evaluate-model] till experimentarbetsytan och anslut utdataporten för modulen [Poängsätta modell][score-model] till den vänstra indataporten för [Utvärdera modell][evaluate-model].

    > [!TIP]
    > Det finns två indataportar eftersom modulen [Utvärdera modell][evaluate-model] kan användas för att jämföra två modeller sida vid sida. Senare kan du lägga till ytterligare en algoritm till experimentet och använda [Utvärdera modell][evaluate-model] för att se vilken som ger bäst resultat.

4. Kör experimentet.

Du visar utdata från modulen [Utvärdera modell][evaluate-model] genom att klicka på utdataporten och sedan välja **Visualisera**.

![Utvärderingsresultat av för experimentet][evaluation-results]
<br/>
***Utvärderingsresultat av för experimentet***

För vår modell visas följande statistik:

- **Medelabsolutfel** (MAE): Medelvärdet av absoluta fel (ett *fel* är skillnaden mellan det förväntade och faktiska värdet).
- **Medelkvadratfel** (RMSE): Kvadratroten av genomsnittet av kvadratfel i förutsägelser som görs mot testdatauppsättningen.
- **Relativa absoluta fel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
- **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
- **Bestämningskoefficient**: Kallas också för **R-kvadratvärdet** och är ett statistiskt mått som anger hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelser bättre överensstämmer med de faktiska värdena. För **bestämningskoefficient** är förutsägelserna bättre ju närmare värdet är ett (1,0).

## <a name="final-experiment"></a>Slutligt experiment

Det slutliga experimentet bör se ut ungefär så här:

![Det slutliga experimentet][complete-linear-regression-experiment]
<br/>
***Det slutliga experimentet***

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört en första självstudie om Machine Learning och skapat ett experiment kan du fortsätta att förbättra modellen och sedan distribuera den som en förutsägbar webbtjänst.

- **Iterera om du vill förbättra modellen** – Du kan till exempel ändra de funktioner du använder i din förutsägelse. Du kan också ändra egenskaperna för algoritmen för [Linjär regression][linear-regression] eller prova med en helt annan algoritm. Du kan också lägga till flera Machine Learning-algoritmer i experimentet samtidigt och jämföra två av dem med hjälp av modulen [Utvärdera modell][evaluate-model].
Ett exempel på hur du kan jämföra flera modeller i ett enda experiment finns [Compare Regressors](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) (Jämför regressorer) i [Cortana Intelligence-galleriet](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Kopiera eventuella iterationer av experimentet genom att använda knappen **SPARA SOM** längst ned på sidan. Du kan visa alla iterationer av experimentet genom att klicka på **VISA KÖRNINGSHISTORIK** längst ned på sidan. Mer information finns i [Hantera iterationer av experiment i Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

- **Distribuera modellen som en förutsägbar webbtjänst** – När du är nöjd med din modell kan du distribuera den som en webbtjänst som kan användas för att förutsäga bilpriser med hjälp av nya data. Mer information finns i [Distribuera en Azure Machine Learning-webbtjänst][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Vill du veta mer? En mer omfattande och detaljerad genomgång av processen för att skapa, träna, värdera och distribuera en modell finns i [Utveckla en förutsägelselösning med hjälp av Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/machine-learning-create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/machine-learning-create-experiment/rename-experiment.png
[visualize-auto-data]:./media/machine-learning-create-experiment/visualize-auto-data.png
[select-visualize]: ./media/machine-learning-create-experiment/select-visualize.png
[showing-excluded-column]:./media/machine-learning-create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/machine-learning-create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/machine-learning-create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/machine-learning-create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/machine-learning-create-experiment/second-experiment-run.png
[connect-score-model]:./media/machine-learning-create-experiment/connect-score-model.png
[evaluation-results]:./media/machine-learning-create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/machine-learning-create-experiment/type-automobile.png
[type-select-columns]:./media/machine-learning-create-experiment/type-select-columns.png
[launch-column-selector]:./media/machine-learning-create-experiment/launch-column-selector.png
[add-comment]:./media/machine-learning-create-experiment/add-comment.png
[connect-clean-to-select]:./media/machine-learning-create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/machine-learning-create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/machine-learning-create-experiment/connect-train-model.png
[select-price-column]:./media/machine-learning-create-experiment/select-price-column.png

[score-model-output]:./media/machine-learning-create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

