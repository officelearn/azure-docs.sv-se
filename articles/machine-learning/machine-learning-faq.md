<properties
    pageTitle="Vanliga frågor och svar om Azure Machine Learning | Microsoft Azure"
    description="Introduktion till Azure Machine Learning: Vanliga frågor och svar om fakturering, funktioner och begränsningar i en molntjänst för effektiv förutsägelsemodellering."
    keywords="machine learning introduction,predictive modeling,what is machine learning"
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
    ms.topic="get-started-article"
    ms.date="04/18/2016"
    ms.author="garye"/>

# Vanliga frågor och svar om Azure Machine Learning: Fakturering, funktioner, begränsningar och support

I det här avsnittet får du svar på frågor om Azure Machine Learning, en molntjänst för att utveckla förutsägelsemodeller och operationaliseringslösningar genom webbtjänster. Avsnittet ger svar på frågor om hur du använder tjänsten, inklusive faktureringsmodellen, funktioner, begränsningar och support.

## Allmänna frågor

**Vad är Azure Machine Learning?**

Azure Machine Learning är en helt hanterad tjänst som du kan använda för att skapa, testa, tillämpa och hantera förutsägelseanalyslösningar i molnet. Med bara en webbläsare kan du logga in, ladda upp data och genast starta maskininlärningsexperiment. Förutsägelsemodellering med dra-och-släpp-funktioner, ett stort utbud av modeller och ett bibliotek med startmallar gör det enkelt att snabbt utföra vanliga maskininlärningsaktiviteter.  Mer information finns i [Översikt över tjänsten Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). En introduktion till maskininlärning där viktiga termer och begrepp beskrivs finns i [Introduktion till Azure Machine Learning](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Vad är Machine Learning Studio?**

Machine Learning Studio är en arbetsstationsmiljö som du kommer åt via en webbläsare. Machine Learning Studio tillhandahåller en rad moduler med visuella gränssnitt som gör det enkelt att skapa ett datavetenskapsflöde från slutpunkt till slutpunkt i form av ett experiment.

Mer information om Machine Learning Studio finns i [Vad är Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

**Vad är Machine Learning API-tjänsten?**

Med Machine Learning API kan du distribuera förutsägelsemodeller, som de som skapas i Machine Learning Studio, som skalbara, feltoleranta webbtjänster. Webbtjänsterna som skapas med Machine Learning API-tjänsten är REST-API:er som tillhandahåller ett gränssnitt för kommunikation mellan externa program och dina förutsägelseanalysmodeller.

Mer information finns i [Ansluta till en Machine Learning-webbtjänst](machine-learning-connect-to-azure-machine-learning-web-service.md).


## Frågor om fakturering

**Hur fungerar faktureringen av Machine Learning?**

Information om fakturering och priser finns i avsnittet om [Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/).

**Finns det någon kostnadsfri utvärderingsversion för Machine Learning?**

 När du registrerar dig för en kostnadsfri utvärderingsversion av Azure kan du prova alla Azure-tjänster i en månad. Om du vill ha mer information om den kostnadsfria utvärderingsversionen av Azure läser du avsnittet med [vanliga frågor och svar om den kostnadsfria utvärderingsversionen av Azure](/pricing/free-trial-faq/).

## Frågor om Machine Learning Studio

### Skapa ett experiment

**Finns det någon versionskontroll eller Git-integrering för experimentdiagram?**

Nej, men Machine Learning Studio behåller varje iteration av ett experiment, som inte kan ändras av andra användare.
Mer information finns i [Hantera iterationer av experiment i Machine Learning Studio](machine-learning-manage-experiment-iterations.md).

### Importera och exportera data för Machine Learning

**Vilka datakällor stöder Machine Learning?**

Data kan läsas in i ett Machine Learning Studio-experiment på något av tre sätt: du kan ladda upp en lokal fil som en datauppsättning, du kan använda en modul för att importera data från datatjänster i molnet eller så kan du importera en datauppsättning som sparats från ett annat experiment. Mer information om vilka filformat som stöds finns i [Importera träningsdata till Machine Learning Studio](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Hur stor kan datauppsättningen vara för mina moduler?

Modulerna i Machine Learning Studio stöder datauppsättningar på upp till 10 GB med kompakta numeriska data för vanliga användningsfall. Om en modul hämtar indata från mer än ett ställe är 10 GB summan av alla indata. Du kan också ta prover av större datauppsättningar via Hive- eller Azure SQL Database-frågor eller genom att bearbeta data i förväg med modulen Inlärning med antal före införandet.  

Följande typer av data kan expanderas till större datauppsättningar under funktionsnormalisering och är begränsade till mindre än 10 GB:

- Utspridda
- Kategoriska
- Strängar
- Binära data

Följande moduler är begränsade till datauppsättningar som är mindre än 10 GB:

- Moduler för rekommenderare
- SMOTE-modul
- Skriptmoduler: R, Python, SQL
- Moduler där utgående datastorleken kan vara större än den inkommande datastorleken, till exempel kopplings- eller funktions-hashning.
- Korsvalidering, hyperparametrar för justeringsmodeller, ordningstalsregression och ”en eller alla”-multiklasser, om antalet iterationer är mycket stort.

För datauppsättningar som är större än några få GB bör du ladda upp data till Azure Storage eller Azure SQL Database eller använda HDInsight i stället för att ladda upp direkt från en lokal fil.


####<a id="UploadLimit"></a>Vilka gränser gäller för datauppladdningar?
För datauppsättningar som är större än ett par GB laddar du upp data till Azure Storage eller Azure SQL Database eller använder HDInsight i stället för att ladda upp direkt från en lokal fil.

**Kan jag läsa data från Amazon S3?**

Om du har en liten mängd data och vill exponera den via en HTTP-URL kan du använda modulen [Importera data][import-data]. Om du har större mängder data laddar du upp dem till Azure Storage först och använder sedan modulen [Importera data][import-data] för att ta med dem i experimentet.
<!--
<SEE CLOUD DS PROCESS>
-->

**Finns det inbyggda bildinhämtningsfunktioner?**

Mer information om bildinhämtningsfunktioner finns i referensen för [Importera bilder][image-reader].

### Moduler

**Algoritmen, datakällan, dataformatet eller datatransformeringsåtgärden som jag letar efter finns inte i Azure Machine Learning Studio. Vad har jag för alternativ?**

I vårt [forum för användarfeedback](http://go.microsoft.com/fwlink/?LinkId=404231) ser du vilka funktionsförfrågningar som vi följer upp. Lägg din röst på en begäran om en funktion som du letar efter redan har begärts. Om funktionen som du letar efter inte finns skapar du en ny begäran. Du kan även visa statusen för din begäran i det här forumet. Vi följer noga den här listan och uppdaterar statusen för funktionstillgänglighet med jämna mellanrum. Med inbyggt stöd för R och Python kan du dessutom skapa anpassade transformeringar vid behov.


**Kan jag använda min befintliga kod i Machine Learning Studio?**

Ja, du kan hämta din befintliga R- eller Python-kod till Machine Learning Studio, köra den i experiment med Azure Machine Learning-inlärning och distribuera lösningen som en webbtjänst via Azure Machine Learning. Mer information finns i [Utöka ditt experiment med R](machine-learning-extend-your-experiment-with-r.md) och [Köra Python-maskininlärningsskript i Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**Går det att använda något som [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) för att definiera en modell?**

Nej, det stöds inte, men anpassad R- och Python-kod kan användas för att definiera en modul.

**Hur många moduler kan jag köra parallellt i mitt experiment?**  

Du kan köra upp till fyra moduler parallellt i ett experiment.


### Databearbetning

**Går det att visualisera data (utöver R-visualiseringar) interaktivt i experimentet?**

Genom att klicka på utdata från en modul kan du visualisera data och få statistik.

**När jag förhandsgranskar resultatet eller data i webbläsaren är antalet rader och kolumner begränsat. Varför?**

Eftersom data överförs till webbläsaren och kan vara stora är storleken på data begränsad för att förhindra att Machine Learning Studio blir långsamt. Om du vill visualisera alla data/resultat är det bättre att ladda ned data och använda Excel eller ett annat verktyg.

### Algoritmer

**Vilka befintliga algoritmer stöds i Machine Learning Studio?**

Machine Learning Studio tillhandahåller avancerade algoritmer som skalbara förstärkta beslutsträd, Bayesian Recommendation-system, djupa neurala nätverk och beslutsdjungler utvecklade av Microsoft Research. Skalbara maskininlärningspaket med öppen källkod som Vowpal Wabbit ingår också. Machine Learning Studio stöder maskininlärningsalgoritmer för multiklass-baserad och binär klassificering, regression och kluster. Se den fullständiga listan med [maskininlärningsmoduler][machine-learning-modules].

**Föreslås rätt maskininlärningsalgoritm automatiskt för mina data?**

Nej, men du kan använda flera metoder i Machine Learning Studio för att jämföra resultatet med varje algoritm och fastställa vilken som passar ditt problem.

**Finns det några riktlinjer för att välja en algoritm framför en annan för de tillgängliga algoritmerna?**
Se [Välja en algoritm ](machine-learning-algorithm-choice.md).

**Är de tillgängliga algoritmerna skrivna i R eller Python?**

Nej, dessa algoritmer är huvudsakligen skrivna i kompilerade språk för att tillhandahålla högre prestanda.

**Finns det någon information om de tillgängliga algoritmerna?**

Dokumentationen innehåller en del information om algoritmerna och parametrarna som tillhandahålls för justering beskrivs så att du kan optimera algoritmen och anpassa den efter din användning.  

**Finns det stöd för onlineinlärning?**

Nej, för närvarande stöds endast omträning via programmering.

**Kan jag visualisera lagren i en neuronätverksmodell med hjälp av den inbyggda modulen?**

Nej.

**Kan jag skapa egna moduler i C# eller på ett annat språk?**

För närvarande kan nya anpassade moduler endast skapas i R.

### R-modulen

**Vilka R-paket är tillgängliga i Machine Learning Studio?**

För närvarande stöder Machine Learning Studio över 400 R CRAN-paket. Här är den [uppdaterade listan](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) över alla paket som ingår. Se även [Utöka ditt experiment med R ](machine-learning-extend-your-experiment-with-r.md) om du vill lära dig hur du kan hämta den här listan själv. Om det paket som du behöver inte finns med i listan lägger du in namnet på paketet i vårt [forum för användarfeedback](http://go.microsoft.com/fwlink/?LinkId=404231).

**Går det att skapa en anpassad R-modul?**

Ja. Mer information finns i [Redigera anpassade R-moduler i Azure Machine Learning](machine-learning-custom-r-modules.md).

**Finns det en REPL-miljö för R?**

Nej, det finns ingen REPL-miljö för R i Studio.

### Python-modulen

**Går det att skapa en anpassad Python-modul?**

Det går inte för närvarande, men du kan använda en eller flera moduler av typen [Kör Python-skript][python] för att få samma resultat.

**Finns det en REPL-miljö för Python?**

Du kan använda Jupyter Notebooks i Machine Learning Studio. Mer information finns i [Introduktion till Jupyter Notebooks i Azure Machine Learning Studio] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Webbtjänst

###Träna modeller via programmering

**Hur tränar jag om Azure Machine Learning-modeller via programmering?**

Använd omtränings-API:erna. Mer information finns i [Träna om Machine Learning-modeller via programmering](machine-learning-retrain-models-programmatically.md). Exempelkod är också tillgänglig i demonstrationen [Microsoft Azure Machine Learning Retraining](https://azuremlretrain.codeplex.com/).

### Skapa

**Kan jag distribuera modellen lokalt eller i ett program utan Internetanslutning?**

Nej.


**Finns det en typisk svarstid som kan förväntas för alla webbtjänster?**

Mer information finns i [Azure-prenumerationsbegränsningar](../azure-subscription-service-limits.md)

### Användning

**När ska jag köra min förutsägelsemodell som en BES-tjänst (Batch Execution Service) eller som en RR-tjänst (Request Response)?**

RR-tjänsten är en högskalig webbtjänst med korta svarstider som används för att tillhandahålla ett gränssnitt för tillståndslösa modeller som skapas och distribueras från experimentmiljön. BES-tjänsten är en tjänst för asynkron poängsättning av en samling dataposter. Indata för BES liknar de indata som används i RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel blob- och tabelltjänsterna i Azure, Azure SQL Database, HDInsight (fråga baserad på registreringsdatafil) och HTTP-källor. Mer information finns i [Använda Machine Learning-webbtjänster](machine-learning-consume-web-services.md).

**Hur uppdaterar jag modellen för den distribuerade webbtjänsten?**

Det är enkelt att uppdatera en förutsägelsemodell för en redan distribuerad tjänst. Allt du gör är att ändra och köra om experimentet som du använde för att redigera och spara den tränade modellen. När du har en ny version av den tränade modellen frågar Machine Learning Studio om du vill uppdatera webbtjänsten. Mer information om hur du uppdaterar en distribuerad webbtjänst finns i [Distribuera en Machine Learning-webbtjänst](machine-learning-publish-a-machine-learning-web-service.md).

Du kan också använda omtränings-API:erna.
Mer information finns i [Träna om Machine Learning-modeller via programmering](machine-learning-retrain-models-programmatically.md). Exempelkod är också tillgänglig i demonstrationen [Microsoft Azure Machine Learning Retraining](https://azuremlretrain.codeplex.com/).

**Hur övervakar jag min webbtjänst när den har distribuerats i produktionsmiljön?**

När en förutsägelsemodell har distribuerats kan du övervaka den från den klassiska Azure-portalen. Varje distribuerad tjänst har sin egen instrumentpanel där du kan se övervakningsinformation för tjänsten.

**Finns det någon plats där jag kan se utdata för min RRS-/BES-tjänst?**

För RRS är webbtjänstsvaret vanligtvis där du ser resultatet. Du kan också skriva det till Azure Blob Storage. För BES skrivs utdata till en blobb som standard. Du kan också skriva utdata till en databas eller tabell med hjälp av modulen [Exportera Data][export-data].

**Kan jag bara skapa webbtjänster från modeller som skapats i Machine Learning Studio?**

Nej, du kan också skapa webbtjänster direkt från Jupyter Notebooks och RStudio.

**Var hittar jag information om felkoder?**

En lista med felkoder och beskrivningar finns i [Felkoder för Machine Learning-moduler](https://msdn.microsoft.com/library/azure/dn905910.aspx).

## Skalbarhet

**Hur skalbar är webbtjänsten?**

För närvarande är standardslutpunkten etablerad med 20 samtidiga RRS-förfrågningar per slutpunkt. Du kan skala detta till 200 samtidiga förfrågningar per slutpunkt och du kan skala varje webbtjänst till 10 000 slutpunkter per webbtjänst. Mer information finns i [Skala API-slutpunkter](machine-learning-scaling-endpoints.md). För BES stöder varje slutpunkt bearbetning av 40 förfrågningar i taget. Ytterligare förfrågningar placeras i kö. Dessa köade förfrågningar körs automatiskt när kön krymper.


**Fördelas R-jobb mellan noder?**

Nej.  


**Hur mycket data kan jag använda för träning?**

Modulerna i Machine Learning Studio stöder datauppsättningar på upp till 10 GB med kompakta numeriska data för vanliga användningsfall. Om en modul hämtar indata från mer än ett ställe är 10 GB summan av alla indata. Du kan också ta prov på större datauppsättningar via Hive- eller Azure SQL Database-frågor eller genom att bearbeta data i förväg med modulen [Inlärning med antal][counts] före införandet.  

Följande typer av data kan expanderas till större datauppsättningar under funktionsnormalisering och är begränsade till mindre än 10 GB:

- utspridda
- kategoriska
- strängar
- binära data

Följande moduler är begränsade till datauppsättningar som är mindre än 10 GB:

- Moduler för rekommenderare
- SMOTE-modul
- Skriptmoduler: R, Python, SQL
- Moduler där utgående datastorleken kan vara större än den inkommande datastorleken, till exempel kopplings- eller funktions-hashning.
- Korsvalidering, hyperparametrar för justeringsmodeller, ordningstalsregression och ”en eller alla”-multiklasser, om antalet iterationer är mycket stort.

För datauppsättningar som är större än några få GB bör du ladda upp data till Azure Storage eller Azure SQL Database eller använda HDInsight i stället för att ladda upp direkt från en lokal fil.


**Finns det några begränsningar vad gäller vektorstorlek?**

Rader och kolumner har samma begränsningar som .NET med ett högsta antal heltal på 2 147 483 647.

**Kan jag justera storleken på den virtuella datorn som används för att köra webbtjänsten?**

Nej.  

## Säkerhet och tillgänglighet

**Vem har tillgång till HTTP-slutpunkten för webbtjänsten som standard? Hur begränsar jag åtkomsten till slutpunkten?**

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Ytterligare slutpunkter kan läggas till med deras egna nycklar från den klassiska Azure-portalen eller via programmering med hjälp av Web Service Management-API:erna. Åtkomstnycklar behövs för att göra anrop till webbtjänsten. Mer information finns i [Ansluta till en Machine Learning-webbtjänst](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Vad händer om det inte går att hitta mitt Azure-lagringskonto?**

Machine Learning Studio behöver ett Azure-lagringskonto för att kunna spara mellanliggande data när arbetsflödet körs. Det här lagringskontot uppges till Machine Learning Studio när en arbetsyta skapas. Om lagringskontot tas bort och inte längre hittas efter att arbetsytan har skapats slutar arbetsytan att fungera och alla experiment på arbetsytan misslyckas.

Om du har tagit bort lagringskontot av misstag är det enda sättet att återställa från kontot att återskapa det med samma namn i samma region som det borttagna lagringskontot. Efter det synkroniserar du om åtkomstnyckeln.


**Vad händer om åtkomstnyckeln för mitt lagringskonto inte är synkroniserat?**

Machine Learning Studio behöver ett Azure-lagringskonto för att kunna spara mellanliggande data när arbetsflödet körs. Det här lagringskontot uppges för Machine Learning Studio när en arbetsyta skapas och åtkomstnycklarna associeras med den arbetsytan. Om åtkomstnycklarna ändras efter att arbetsytan har skapats kan arbetsytan inte längre komma åt lagringskontot. Arbetsytan slutar att fungera och alla experiment på den misslyckas.

Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av den klassiska Azure-portalen.  


## Azure Marketplace

Mer information finns i [Vanliga frågor och svar för att publicera och använda appar på Machine Learning Marketplace](machine-learning-marketplace-faq.md).

## Support och utbildning

**Var kan jag lära mig mer om Azure Machine Learning?**

På [Azure Machine Learning Documentation Center](https://azure.microsoft.com/services/machine-learning/) hittar du videokurser och instruktionsguider. Dessa stegvisa guider ger en introduktion till tjänsterna och går igenom datalivscykeln för import, rensning, utveckling av förutsägelsemodeller och distribution av dem till produktionsmiljön med hjälp av Azure Machine Learning.

Vi lägger till nytt material på Machine Learning Center med jämna mellanrum. Du kan skicka dina önskemål om ytterligare utbildningsmaterial på Machine Learning Center via vårt [forum för användarfeedback](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Du kan också hitta kurser på [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Hur får jag support för Azure Machine Learning?**

Om du behöver teknisk support för Azure Machine Learning går du till [Azure-support](/support/options/) och väljer **Machine Learning**.

Azure Machine Learning har även ett community-forum på MSDN där du kan ställa frågor som rör Azure Machine Learning. Forumet övervakas av Azure Machine Learning-teamet. Besök [Azure-forumet](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx



<!--HONumber=Jun16_HO2-->


