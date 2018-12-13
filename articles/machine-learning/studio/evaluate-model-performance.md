---
title: Utvärdera modellprestanda – Azure Machine Learning Studio | Microsoft Docs
description: Den här artikeln visar hur du utvärderar prestanda för en modell i Azure Machine Learning Studio och ger en kort förklaring av mått som är tillgängliga för den här uppgiften.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 501a9834e598fc8b1c11a86ef0ae9db1c19a66a7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269948"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio"></a>Så här utvärderar du modellens prestanda i Azure Machine Learning Studio

Den här artikeln visar hur du utvärderar prestanda för en modell i Azure Machine Learning Studio och ger en kort förklaring av mått som är tillgängliga för den här uppgiften. Tre vanliga scenarier för övervakad inlärning visas: 

* Regression
* Binär klassificering 
* multiklass-baserad klassificering



Utvärdera prestanda i en modell är en av de grundläggande stegen i data science process. Den visar hur lyckade bedömnings (förutsägelser) för en datauppsättning har av en trained model. 

Azure Machine Learning har stöd för utvärdering av modellen via två av dess huvudsakliga machine learning-moduler: [Utvärdera modellen] [ evaluate-model] och [Kontrollera modellen][cross-validate-model]. Dessa moduler kan du se hur modellen presterar när det gäller ett antal mått som är vanliga i machine learning och statistik.

## <a name="evaluation-vs-cross-validation"></a>Jämfört med utvärdering Korsvalidering
Utvärdering och mellan verifiering är standard sätt att mäta prestanda i din modell. De båda generera utvärderingsmått som du kan kontrollera eller jämföra med de andra modeller.

[Utvärdera modellen] [ evaluate-model] förväntar sig en poängsatta datauppsättning som indata (eller 2 om du vill jämföra prestanda för två olika modeller). Det innebär att du behöver att träna modellen med den [träna modell] [ train-model] modulen och göra förutsägelser om vissa datauppsättning med hjälp av den [Poängmodell] [ score-model]modulen innan du kan utvärdera resultaten. Utvärderingen baseras på poängsatta etiketter/troliga tillsammans med SANT etiketterna, som matats ut av den [Poängmodell] [ score-model] modulen.

Du kan också använda mellan verifieringen för att utföra ett antal träna poäng utvärdera åtgärder (10 vikningar) automatiskt på olika delmängder av indata. Indata är indelat i 10 delar, där är ett reserverat för testning, och de andra 9 för utbildning. Den här processen upprepas 10 gånger och utvärderingsmått är ett genomsnitt. På så sätt kan bestämma hur väl en modell skulle generalisera till nya datauppsättningar. Den [Kontrollera modellen] [ cross-validate-model] modulen tar ett omdöme modellen och vissa taggade datauppsättning och matar ut utvärderingsresultat av var och en av de 10 vikningar, förutom genomsnittlig resultaten.

I följande avsnitt kommer vi bygga enkla regression och klassificering modeller och utvärdera deras prestanda genom att använda både den [utvärdera modell] [ evaluate-model] och [Kontrollera modell ] [ cross-validate-model] moduler.

## <a name="evaluating-a-regression-model"></a>Utvärderar en regressionsmodell
Anta att vi vill förutsäga pris för en bil med hjälp av vissa funktioner, till exempel dimensioner, hästkrafter, motorn specifikationer och så vidare. Det här är en typisk regressionsproblem där en målvariabel (*pris*) är en kontinuerlig numeriskt värde. Vi kan anpassa en enkel linjär regressionsmodell som, med funktionen värdena på en viss bil kan förutsäga priset på den bil. Den här regressionsmodellen kan användas för att bedöma vi tränats på samma datauppsättning. När vi har förväntade priserna för alla bilarna utvärdera det prestanda för modellen genom att titta på hur mycket förutsägelserna avviker från priserna som faktiskt i genomsnitt. För att illustrera detta använder vi den *Automobile price data (Raw) datauppsättning* tillgängliga i den **sparade datauppsättningar** avsnitt i Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler i din arbetsyta i Azure Machine Learning Studio:

* Bil price data (Raw)
* [Linjär Regression][linear-regression]
* [Träna modell][train-model]
* [Poängsätta modell][score-model]
* [Utvärdera modellen][evaluate-model]

Ansluta portarna som visas nedan i figur 1 och ange kolumnen etikett i den [Träningsmodell] [ train-model] modulen *pris*.

![Utvärderar en regressionsmodell](./media/evaluate-model-performance/1.png)

Figur 1. Utvärderar en regressionsmodell.

### <a name="inspecting-the-evaluation-results"></a>Kontrollera utvärderingsresultaten
När du har kört experimentet som du kan klicka på utdataporten för den [utvärdera modell] [ evaluate-model] modul och välj *visualisera* att se utvärderingsresultaten. Utvärderingsmått som är tillgängliga för regressionsmodeller är: *Medelabsolutfel*, *rot absoluta fel*, *relativa absoluta fel*, *relativt cirkels fel*, och *koefficienten för Bestämning*.

Termen ”error” här representerar skillnaden mellan det förväntade och värdet true. Det absoluta värdet eller kvadraten av denna skillnad beräknas vanligtvis att samla in den totala storleken på fel i alla instanser som skillnaden mellan det förväntade och SANT värdet kan vara negativt i vissa fall. Felmått mäta förutsägande prestanda i en regressionsmodell när det gäller medelvärdet skillnader mellan dess förutsägelser från värdet true. Nedre felvärdena gör modellen är bättre att göra förutsägelser. Ett övergripande fel mått noll innebär att modellen passar data perfekt.

Bestämningskoefficient, som även kallas R cirkels, är också ett standardiserat sätt att mäta hur väl modellen passar data. Den kan tolkas som variation som beskrivs i modellen. En högre andel är bättre i detta fall där 1 anger en perfekt passning.

![Linjär Regression Utvärderingsmått](./media/evaluate-model-performance/2.png)

Figur 2. Linjär Regression Utvärderingsmått.

### <a name="using-cross-validation"></a>Med hjälp av plattformsoberoende verifiering
Som vi nämnde tidigare kan du utföra upprepade utbildning, bedömning och utvärderingar som automatiskt med den [Kontrollera modellen] [ cross-validate-model] modulen. Allt du behöver i det här fallet är en datauppsättning, en omdöme modell och en [Kontrollera modellen] [ cross-validate-model] modulen (se bilden nedan). Du måste ange etikettkolumnen *pris* i den [Kontrollera modellen] [ cross-validate-model] modulens egenskaper.

![Verifiera en regressionsmodell mellan](./media/evaluate-model-performance/3.png)

Figur 3. Cross-validerar en regressionsmodell.

När experimentet har körts kan du inspektera utvärderingsresultaten genom att klicka på den högra utdataporten för den [Kontrollera modellen] [ cross-validate-model] modulen. Detta ger en detaljerad vy av mått för varje iteration (vikning) och genomsnittlig resultaten för var och en av mått (bild 4).

![Korsvalidering resultatet av en regressionsmodell](./media/evaluate-model-performance/4.png)

Figur 4. Korsvalidering resultatet av en regressionsmodell.

## <a name="evaluating-a-binary-classification-model"></a>Utvärderar en binär Klassificeringsmodell
I ett scenario med binär klassificering en målvariabel har bara två möjliga resultat, till exempel: {0, 1} eller {FALSKT, SANT}, {negativt, positivt}. Anta att du får en datauppsättning som är olämpligt för barn anställda med några demografiska och anställning variabler och att du uppmanas att förutsäga inkomstnivå, en binär variabel med värdena {”< = 50 K” ”, > 50 K”}. Med andra ord klassen negativt representerar de medarbetare som gör mindre än eller lika med 50 K per år och klassen positivt representerar alla andra anställda. Som i scenariot regression skulle vi tränar en modell, bedöma vissa data och utvärdera resultaten. Den största skillnaden här är valet av Azure Machine Learning beräknar mått- och utdata. För att visa intäkter på förutsägelse scenario kan vi använder den [vuxet](http://archive.ics.uci.edu/ml/datasets/Adult) datauppsättning för att skapa ett Azure Machine Learning-experiment och utvärdera prestanda för en tvåklassförhöjt logistic regression-modellen, en binär som används ofta klassificerare.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler i din arbetsyta i Azure Machine Learning Studio:

* Vuxet insamlade binära Intäktsklassificering datauppsättning
* [Två Logistic Regression][two-class-logistic-regression]
* [Träna modell][train-model]
* [Poängsätta modell][score-model]
* [Utvärdera modellen][evaluate-model]

Ansluta portarna som visas nedan i figur 5 och ange kolumnen etikett i den [Träningsmodell] [ train-model] modulen *inkomst*.

![Utvärderar en binär Klassificeringsmodell](./media/evaluate-model-performance/5.png)

Figur 5. Utvärderar en binär Klassificeringsmodell.

### <a name="inspecting-the-evaluation-results"></a>Kontrollera utvärderingsresultaten
När du har kört experimentet som du kan klicka på utdataporten för den [utvärdera modell] [ evaluate-model] modul och välj *visualisera* att se utvärderingsresultaten (bild 7). Utvärderingsmått som är tillgängliga för binär klassificering modeller är: *Precision*, *Precision*, *återkalla*, *F1 poäng*, och *AUC*. Dessutom kan modulen matar ut en felmatris som visar antalet positiva, false negativ, falska positiva identifieringar och SANT negativ samt *ROC*, *Precision/återkallande*, och  *Flytta* kurvor.

Precision är helt enkelt den korrekt klassificerade instanser. Det är vanligtvis den första mått som du tittar på när du utvärderar en klassificerare. Men när testdata är obalanserade (där de flesta av de hör till någon av klasserna) eller om du är intresserad av mer precision inte prestanda på någon av klasserna verkligen avbilda effektiviteten i en klassificerare. Anta att du testar på vissa data där 99% av instanserna motsvarar personer som du betalar, desto mindre än eller lika med 50K per år intäkter på klassificering för scenariot. Det är möjligt att uppnå en 0.99 noggrannhet genom att förutsäga klassen ”< = 50K” för alla instanser. Klassificeraren visas i det här fallet att utföra en övergripande bra, men i verkligheten kan det går inte att klassificera någon av high-income personer (1-%) korrekt.

Därför är det bra att beräkna ytterligare mått som samlar in mer specifika aspekter av utvärderingen. Innan du fortsätter i detaljerna för mått, är det viktigt att förstå felmatrisen för en binär klassificering utvärderingen. Klass-etiketter i träningsmängden kan ha endast 2 möjliga värden som vi vanligtvis refererar till som positivt eller negativt. Positiva och negativa instanserna som en klassificerare beräknar korrekt kallas korrekta positiva identifieringar (TP) och SANT negativ (TN). På samma sätt kallas felaktigt klassificerad instanser falska positiva identifieringar (FP) och FALSKT negativ (FN). Felmatrisen är helt enkelt en tabell som visar antalet instanser som hör till var och en av följande 4 kategorier. Azure Machine Learning beslutar automatiskt som två klasser i datauppsättningen är positivt klassen. Om klassen etiketter är booleskt värde eller heltal, har namngivna instanser 'true' eller '1' tilldelats klassen positivt. Om etiketterna är strängar, som i fallet med inkomst datauppsättningen etiketterna sorteras alfabetiskt och den första nivån väljs vara negativt klassen medan den andra nivån är positivt klassen.

![Binär klassificering Felmatris](./media/evaluate-model-performance/6a.png)

Figur 6. Binär klassificering Felmatris.

Går tillbaka till klassificeringsproblem intäkter, vill vi skulle ställa flera utvärderingsfrågor som hjälper oss att förstå prestanda för klassificerarna används. Det är en mycket naturlig fråga: ' Från personer som modellen förutse till att tjäna > 50 K (TP + FP), hur många har klassificerats korrekt (TP) ”? Den här frågan besvaras genom att titta på den **Precision** av modellen, vilket är den positiva identifieringar som klassificeras på korrekt sätt: TP/(TP+FP). En annan vanlig fråga är ”utanför alla hög tjäna anställda med inkomst > 50 k (TP + FN), hur många klassificeraren klassificera korrekt (TP)”. Hittar du faktiskt den **återkalla**, eller true positiva identifieringar: TP/(TP+FN) för klassificerarna. Du kanske märker att det finns en uppenbar säkerhetsaspekten precision och återkallande. Till exempel får en relativt belastningsutjämnade datauppsättning, skulle en klassificerare som beräknar huvudsakligen positivt instanser, finns en hög återkallande, men en i stället Låg precision som många av instanserna i negativt skulle klassificeras vilket resulterar i ett stort antal falska positiva identifieringar. Om du vill se ett diagram över hur de här två måtten varierar, kan du klicka på den **PRECISION/återkallande** kurvan i utvärderingssidan resultatet utdata (övre vänstra del av bild 7).

![Utvärderingsresultat av binär klassificering](./media/evaluate-model-performance/7.png)

Figur 7. Utvärderingsresultat av binär klassificering.

En annan relaterade mått som används ofta är det **F1 poäng**, som tar både precision och återkallande i beräkningen. Det är det harmoniska medelvärdet av de här 2 måtten och beräknas därför: F1 = 2 (precision x återkallande) / (precision + återkallande). F1-poängen är ett bra sätt att sammanfatta utvärdering i ett enda tal, men det är alltid en bra idé att titta på både precision och återkallande tillsammans för att bättre förstå hur en klassificerare fungerar.

Dessutom kan en kan inspektera SANT positiva identifieringar kontra andel falska positiva identifieringar i den **mottagare fungerar egenskap (ROC)** kurvan och motsvarande **området Under the kurvan (AUC)** värdet. Ju närmare kurvan är att det övre vänstra hörnet, desto bättre prestanda för den klassificerare är (som är att maximera SANT positiva identifieringar som minimeras andel falska positiva identifieringar). Kurvor som närmar sig diagonalt av området resultat från klassificerare som brukar göra förutsägelser som närmar sig slumpmässiga gissa.

### <a name="using-cross-validation"></a>Med hjälp av plattformsoberoende verifiering
Exemplet regression kan vi utföra flera valideringen för att träna, poäng och utvärdera olika delmängder av data automatiskt flera gånger. På samma sätt kan vi använda den [Kontrollera modellen] [ cross-validate-model] modulen, ett omdöme logistic regression-modellen och en datauppsättning. Etikettkolumnen måste anges till *inkomst* i den [Kontrollera modellen] [ cross-validate-model] modulens egenskaper. När du kör experimentet och klicka på höger utgående port på den [Kontrollera modellen] [ cross-validate-model] modulen, vi kan se måttvärden för binär klassificering för varje vikning dessutom att medelvärdet och standardavvikelsen för var och en. 

![Verifiera en binär Klassificeringsmodell mellan](./media/evaluate-model-performance/8.png)

Figur 8. Cross-validerar en binär Klassificeringsmodell.

![Korsvalidering resultatet av en binär klassificerare](./media/evaluate-model-performance/9.png)

Figur 9. Korsvalidering resultatet av en binär klassificerare.

## <a name="evaluating-a-multiclass-classification-model"></a>Utvärdering av en modell för multiklass-baserad klassificering
I det här experimentet använder vi den populära [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") datauppsättning som innehåller instanser av 3 olika typer (klasser) av iris anläggning. Det finns 4 funktionen värden (sepal längd och bredd och petal längd och bredd) för varje instans. I de föregående experiment vi tränas och testas modeller med samma datauppsättningar. Här kan vi använder den [dela Data] [ split] modul för att skapa 2 delmängder av data, träna på först och bedöma och utvärdera på andra. Iris-datauppsättningen är offentligt tillgängligt på den [UCI Machine Learning databasen](http://archive.ics.uci.edu/ml/index.html), och kan hämtas med hjälp av en [importera Data] [ import-data] modulen.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler i din arbetsyta i Azure Machine Learning Studio:

* [Importera Data][import-data]
* [Beslutsskog med multiclass][multiclass-decision-forest]
* [Dela Data][split]
* [Träna modell][train-model]
* [Poängsätta modell][score-model]
* [Utvärdera modellen][evaluate-model]

Anslut portarna som visas nedan i figur 10.

Ange etikett kolumnindex för de [Träningsmodell] [ train-model] modulen till 5. Datauppsättningen har ingen rubrikrad, men vi vet att klassen etiketter finns i den femte kolumnen.

Klicka på den [importera Data] [ import-data] modulen och ange den *datakälla* egenskap *Webbadress via HTTP*, och *URL* till http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Ange andelen av instanser som ska användas för utbildning i den [dela Data] [ split] modulen (0,7 till exempel).

![Utvärderar en inom klassificerare](./media/evaluate-model-performance/10.png)

Figur 10. Utvärderar en inom klassificerare

### <a name="inspecting-the-evaluation-results"></a>Kontrollera utvärderingsresultaten
Kör experimentet och klicka på utdataporten för [utvärdera modell][evaluate-model]. Utvärderingsresultatet visas i form av en felmatris i det här fallet. Matrisen visar de faktiska intäkter jämfört med förutsagda instanser för alla 3 klasser.

![Utvärderingsresultat av multiklass-baserad klassificering](./media/evaluate-model-performance/11.png)

Figur 11. Utvärderingsresultat av multiklass-baserad klassificering.

### <a name="using-cross-validation"></a>Med hjälp av plattformsoberoende verifiering
Som vi nämnde tidigare kan du utföra upprepade utbildning, bedömning och utvärderingar som automatiskt med den [Kontrollera modellen] [ cross-validate-model] modulen. Behöver du en datauppsättning, en omdöme modell och en [Kontrollera modellen] [ cross-validate-model] modulen (se bilden nedan). Igen måste du ange den etikettkolumnen i den [Kontrollera modellen] [ cross-validate-model] modulen (kolumnindex 5 i det här fallet). När du kör experimentet och klicka på höger utgående port på den [Kontrollera modellen][cross-validate-model], du kan inspektera måttvärden för varje vikning samt mean- och standard-avvikelse. Mått som visas här är den som liknar de som beskrivs i fallet binär klassificering. Observera dock att i multiklass-baserad klassificering, databehandling SANT positiva identifieringar/negativ och falska positiva identifieringar/negativ görs genom att räkna på basis av per klass, eftersom det inte finns någon övergripande positiv eller negativ klass. Till exempel när du beräknar precision återkallande av klassen Iris-setosa, antas det att det här är klassen positiva och alla andra som negativt.

![Cross-verifierande inom Klassificeringsmodellen](./media/evaluate-model-performance/12.png)

Figur 12. Cross-validerar en modell för multiklass-baserad klassificering.

![Korsvalidering resultatet av en modell för multiklass-baserad klassificering](./media/evaluate-model-performance/13.png)

Figur 13. Korsvalidering resultatet av en modell för multiklass-baserad klassificering.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

