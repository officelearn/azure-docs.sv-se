---
title: "Vanliga frågor och svar om Azure Machine Learning | Microsoft Docs"
description: "Introduktion till Azure Machine Learning: Vanliga frågor och svar om fakturering, funktioner och begränsningar i en molntjänst för effektiv förutsägelsemodellering."
keywords: "introduktion till maskininlärning, förutsägelsemodellering, vad är maskininlärning, machine learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/02/2017
ms.author: garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2b4d04af7fe7a40a1d907a06ab8772f20956bc19
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Vanliga frågor och svar om Azure Machine Learning: Fakturering, funktioner, begränsningar och support
Här får du svar på vanliga frågor om molntjänsten Azure Machine Learning som tillhandahåller förutsägelsemodeller och operationaliseringslösningar genom webbtjänster. Avsnittet ger svar på frågor om hur du använder tjänsten, inklusive faktureringsmodellen, funktioner, begränsningar och support.

**Har en fråga som du inte kan hitta här?**

Azure Machine Learning har även ett forum på MSDN där datatekniker kan ställa frågor om Azure Machine Learning. Forumet övervakas av Azure Machine Learning-teamet. Gå till [Azure Machine Learning-forumet](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)för att söka efter svar eller ställa en ny fråga.

## <a name="general-questions"></a>Allmänna frågor
**Vad är Azure Machine Learning?**

Azure Machine Learning är en helt hanterad tjänst som du kan använda för att skapa, testa, tillämpa och hantera förutsägelseanalyslösningar i molnet. Du kan logga in, ladda upp data och genast starta maskininlärningsexperiment. Allt du behöver är en webbläsare. Förutsägelsemodellering med dra-och-släpp-funktioner, ett stort utbud av modeller och ett bibliotek med startmallar gör det enkelt att snabbt utföra vanliga maskininlärningsaktiviteter. Mer information finns i [Översikt över tjänsten Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). En introduktion till maskininlärning där viktiga termer och begrepp beskrivs finns i [Introduktion till Azure Machine Learning](what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**Vad är Machine Learning Studio?**

Machine Learning Studio är en arbetsstationsmiljö som du kommer åt via en webbläsare. I Machine Learning Studio får du tillgång till flera olika moduler i ett visuellt gränssnitt så att du enkelt kan skapa ett datavetenskapsflöde från slutpunkt till slutpunkt i form av ett experiment.

Mer information om Machine Learning Studio finns i [Vad är Machine Learning Studio?](what-is-ml-studio.md)

**Vad är Machine Learning API-tjänsten?**

Med Machine Learning API kan du distribuera förutsägelsemodeller, som de som skapas i Machine Learning Studio, som skalbara, feltoleranta webbtjänster. Webbtjänsterna som skapas med Machine Learning API-tjänsten är REST-API:er som tillhandahåller ett gränssnitt för kommunikation mellan externa program och dina förutsägelseanalysmodeller.

Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

**Var visas mina klassiska webbtjänster? Var visas mina nya Azure Resource Manager-baserade webbtjänster?**

Webbtjänster som har skapats med den klassiska distributionsmodellen och webbtjänster som har skapats med den nya Azure Resource Manager-distributionsmodellen visas i portalen [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).

Klassiska webbtjänster visas också på fliken **Webbtjänster** i [Machine Learning Studio](http://studio.azureml.net).

## <a name="azure-machine-learning-questions"></a>Frågor om Azure Machine Learning
**Vad är Azure Machine Learning-webbtjänster?**

Machine Learning-webbtjänster är ett gränssnitt mellan ett program och en bedömningsmodell för ett Machine Learning-arbetsflöde. Med Azure Machine Learning kan ett externt program kommunicera i realtid med en arbetsflödesbaserad poängmodell i Machine Learning. Ett anrop till en Machine Learning-webbtjänst returnerar förutsägelser till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. En Machine Learning-webbtjänst baseras på REST, ett populärt arkitekturval för programmeringsprojekt.

Azure Machine Learning har två typer av webbtjänster:

* RRS (Request-Response Service): En tjänst med korta svarstider och hög skalbarhet som tillhandahåller ett gränssnitt för de tillståndslösa modeller som skapas och distribueras med Machine Learning Studio.
* BES (Batch Execution Service): En asynkron tjänst som poängsätter en batch med dataposter.

Du kan använda REST-API:et och komma åt webbtjänsten på flera sätt. Du kan till exempel skriva ett program i C#, R eller Python med exempelkoden som genererades när du distribuerade webbtjänsten.

Exempelkoden finns på:
- Sidan Använda för webbtjänsten i portalen Azure Machine Learning-webbtjänster
- API-hjälpsidan i instrumentpanelen för webbtjänster i Machine Learning Studio

Du kan också använda Microsoft Excel-exempelarbetsboken som skapas åt dig. Den finns också på instrumentpanelen för webbtjänsten i Machine Learning Studio.

**Vilka är de viktigaste uppdateringarna i Azure Machine Learning?**

De senaste uppdateringarna finns i [Nyheter i Azure Machine Learning](whats-new.md).

## <a name="machine-learning-studio-questions"></a>Frågor om Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Importera och exportera data för Machine Learning
**Vilka datakällor stöder Machine Learning?**

Du kan ladda ned data till ett Machine Learning Studio-experiment på tre sätt:

- Överföra en lokal fil som en datauppsättning
- Använda en modul för att importera data från molndatatjänster
- Importera en datamängd som sparats i ett annat experiment

Mer information om vilka filformat som stöds finns i [Importera träningsdata till Machine Learning Studio](import-data.md).

#### <a id="ModuleLimit"></a>Hur stor kan datauppsättningen vara för mina moduler?
Modulerna i Machine Learning Studio stöder datauppsättningar på upp till 10 GB med kompakta numeriska data för vanliga användningsfall. Om en modul hämtar indata från mer än ett ställe är värdet 10 GB summan av alla indata. Du kan också ta prover av större datauppsättningar med frågor från Hive eller Azure SQL Database, eller så kan du bearbeta data i förväg med modulen Inlärning med antal före införandet.  

Följande typer av data kan expanderas till större datauppsättningar under funktionsnormalisering och är begränsade till mindre än 10 GB:

* Utspridda
* Kategoriska
* Strängar
* Binära data

Följande moduler är begränsade till datauppsättningar som är mindre än 10 GB:

* Moduler för rekommenderare
* Modulen SMOTE (Synthetic Minority Oversampling Technique)
* Skriptmoduler: R, Python, SQL
* Moduler där den utgående datastorleken kan vara större än den inkommande datastorleken, till exempel kopplings- eller funktions-hashning
* Korsvalidering, hyperparametrar för justeringsmodeller, ordningstalsregression och ”en eller alla”-multiklasser, om antalet iterationer är mycket stort

#### <a id="UploadLimit"></a>Vilka gränser gäller för datauppladdningar?
För datauppsättningar som är större än ett par GB laddar du upp data till Azure Storage eller Azure SQL Database. Du kan också använda Azure HDInsight i stället för att ladda upp direkt från en lokal fil.

**Kan jag läsa data från Amazon S3?**

Om du har en liten mängd data och vill exponera dem via en HTTP-URL kan du använda modulen [Importera data][import-data]. Om du har större mängder data laddar du upp dem till Azure Storage först och använder sedan modulen [Importera data][import-data] för att ta med dem i experimentet.
<!--

<SEE CLOUD DS PROCESS>
-->

**Finns det inbyggda bildinhämtningsfunktioner?**

Mer information om bildinmatningsfunktioner finns i referensen för [bildimport][image-reader].

### <a name="modules"></a>Moduler
**Algoritmen, datakällan, dataformatet eller datatransformeringsåtgärden som jag söker efter finns inte i Azure Machine Learning Studio. Vad har jag för alternativ?**

I vårt [forum för användarfeedback](http://go.microsoft.com/fwlink/?LinkId=404231) ser du vilka funktionsförfrågningar som vi följer upp. Lägg din röst på en begäran om en funktion som du letar efter redan har begärts. Om funktionen som du letar efter inte finns skapar du en ny begäran. Du kan även visa statusen för din begäran i det här forumet. Vi följer noga den här listan och uppdaterar statusen för funktionstillgänglighet med jämna mellanrum. Du kan också använda det inbyggda stödet för R och Python för att skapa anpassade transformeringar vid behov.

**Kan jag använda min befintliga kod i Machine Learning Studio?**

Ja, du kan hämta din befintliga R- eller Python-kod till Machine Learning Studio, köra den i experiment med Azure Machine Learning-inlärning och distribuera lösningen som en webbtjänst via Azure Machine Learning. Mer information finns i [Utöka ditt experiment med R](extend-your-experiment-with-r.md) och [Köra Python-maskininlärningsskript i Azure Machine Learning Studio](execute-python-scripts.md).

**Går det att använda något som [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) för att definiera en modell?**

Nej, PMML (Predictive Model Markup Language) stöds inte. Du kan använda anpassad R- och Python-kod för att definiera en modul.

**Hur många moduler kan jag köra parallellt i mitt experiment?**  

Du kan köra upp till fyra moduler parallellt i ett experiment.

### <a name="data-processing"></a>Databearbetning
**Går det att visualisera data (utöver R-visualiseringar) interaktivt i experimentet?**

Du kan visualisera data och få statistik genom att klicka på utdata från en modul.

**När jag förhandsgranskar resultatet eller data i webbläsaren är antalet rader och kolumner begränsat. Varför?**

Eftersom stora mängder data kan skickas till en webbläsare är datastorleken begränsad för att förhindra att Machine Learning Studio går långsamt. Om du vill visualisera alla data/resultat är det bättre att ladda ned data och använda Excel eller något annat verktyg.

### <a name="algorithms"></a>Algoritmer
**Vilka befintliga algoritmer stöds i Machine Learning Studio?**

Machine Learning Studio tillhandahåller avancerade algoritmer som skalbara förstärkta beslutsträd, Bayesian Recommendation-system, djupa neurala nätverk och beslutsdjungler utvecklade av Microsoft Research. Skalbara maskininlärningspaket med öppen källkod, som Vowpal Wabbit, ingår också. Machine Learning Studio stöder maskininlärningsalgoritmer för multiklass-baserad och binär klassificering, regression och kluster. Se den fullständiga listan med [Machine Learning-moduler][machine-learning-modules].

**Föreslås rätt Machine Learning-algoritm automatiskt för mina data?**

Nej, i Machine Learning Studio finns flera sätt att jämföra resultatet med varje algoritm och fastställa vilken som passar ditt problem.

**Finns det några riktlinjer för att välja en algoritm framför en annan för de tillgängliga algoritmerna?**

Se [Välja en algoritm ](algorithm-choice.md).

**Är de tillgängliga algoritmerna skrivna i R eller Python?**

Nej, dessa algoritmer är huvudsakligen skrivna i kompilerade språk för att tillhandahålla bättre prestanda.

**Finns det någon information om de tillgängliga algoritmerna?**

Dokumentationen innehåller en del information om algoritmerna, och parametrarna för justering beskrivs så att du kan optimera algoritmen och anpassa den efter din användning.  

**Finns det stöd för onlineinlärning?**

Nej, för närvarande stöds endast omträning via programmering.

**Kan jag visualisera lagren i en neuronnätverksmodell med hjälp av den inbyggda modulen?**

Nej.

**Kan jag skapa egna moduler i C# eller på ett annat språk?**

För närvarande kan du bara använda R för att skapa nya anpassade moduler.

### <a name="r-module"></a>R-modulen
**Vilka R-paket är tillgängliga i Machine Learning Studio?**

För närvarande stöder Machine Learning Studio mer än 400 R CRAN-paket. Här är den [uppdaterade listan](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) över alla paket som ingår. Se även [Utöka ditt experiment med R](extend-your-experiment-with-r.md) om du vill lära dig hur du kan hämta den här listan själv. Om det paket som du behöver inte finns med i listan lägger du in namnet på paketet i vårt [forum för användarfeedback](http://go.microsoft.com/fwlink/?LinkId=404231).

**Går det att skapa en anpassad R-modul?**

Ja. Mer information finns i [Redigera anpassade R-moduler i Azure Machine Learning](custom-r-modules.md).

**Finns det en REPL-miljö för R?**

Nej, det finns ingen REPL-miljö (Read-Eval-Print-Loop) för R i Studio.

### <a name="python-module"></a>Python-modulen
**Går det att skapa en anpassad Python-modul?**

Det går inte för närvarande, men du kan använda en eller flera moduler av typen [Kör Python-skript][python] för att få samma resultat.

**Finns det en REPL-miljö för Python?**

Du kan använda Jupyter Notebooks i Machine Learning Studio. Mer information finns i [Introduktion till Jupyter Notebooks i Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webbtjänst
### <a name="retrain"></a>Omträning
**Hur tränar jag om Azure Machine Learning-modeller via programmering?**

Använd omtränings-API:erna. Mer information finns i [Träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). Exempelkod är också tillgänglig i demonstrationen [Microsoft Azure Machine Learning Retraining](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Skapa
**Kan jag distribuera modellen lokalt eller i ett program som saknar Internetanslutning?**

Nej.

**Finns det en typisk svarstid som kan förväntas för alla webbtjänster?**

Mer information finns i [Azure-prenumerationsbegränsningar](../../azure-subscription-service-limits.md).

### <a name="use"></a>Användning
**När ska jag köra min förutsägelsemodell som en batchkörningstjänst (BES) eller som en svar på begäran-tjänst (RRS)?**

RR-tjänsten är en högskalig webbtjänst med korta svarstider som används för att tillhandahålla ett gränssnitt för tillståndslösa modeller som skapas och distribueras från experimentmiljön. BES (Batch Execution Service) är en tjänst som asynkront poängsätter en samling dataposter. Indata för BES är som de indata som används av RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive-fråga) och HTTP-källor. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

**Hur uppdaterar jag modellen för den distribuerade webbtjänsten?**

Du kan uppdatera en förutsägelsemodell för en redan distribuerad tjänst genom att ändra och köra om experimentet som du använde för att redigera och spara den tränade modellen. När du har en ny version av den tränade modellen frågar Machine Learning Studio om du vill uppdatera webbtjänsten. Mer information om hur du uppdaterar en distribuerad webbtjänst finns i [Distribuera en Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

Du kan också använda omtränings-API:erna.
Mer information finns i [Träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). Exempelkod är också tillgänglig i demonstrationen [Microsoft Azure Machine Learning Retraining](https://azuremlretrain.codeplex.com/).

**Hur övervakar jag min webbtjänst när den har distribuerats i produktionsmiljön?**

När du har distribuerat en förutsägbar modell kan du övervaka den från den klassiska Azure-portalen (endast klassiska webbtjänster) eller portalen för Azure Machine Learning-webbtjänster. Varje distribuerad tjänst har sin egen instrumentpanel där du kan se övervakningsinformation för tjänsten. Mer information om hur du hanterar distribuerade webbtjänster finns i [Hantera en webbtjänst med hjälp av portalen för Azure Machine Learning-webbtjänster](manage-new-webservice.md) och [Hantera en Azure Machine Learning-arbetsyta](manage-workspace.md).

**Finns det någon plats där jag kan se utdata för min RRS-/BES-tjänst?**

För RRS är webbtjänstsvaret vanligtvis där du ser resultatet. Du kan också skriva det till Azure Blob Storage. För BES skrivs utdata till en blobb som standard. Du kan också skriva utdata till en databas eller tabell med hjälp av modulen [Exportera Data][export-data].

**Kan jag bara skapa webbtjänster från modeller som skapats i Machine Learning Studio?**

Nej, du kan också skapa webbtjänster direkt med Jupyter Notebooks och RStudio.

**Var hittar jag information om felkoder?**

En lista med felkoder och beskrivningar finns i [Felkoder för Machine Learning-moduler](https://msdn.microsoft.com/library/azure/dn905910.aspx).

## <a name="scalability"></a>Skalbarhet
**Hur skalbar är webbtjänsten?**

För närvarande är standardslutpunkten etablerad med 20 samtidiga RRS-förfrågningar per slutpunkt. Du kan skala det här till 200 samtidiga begäranden per slutpunkt, och du kan skala varje webbtjänst till 10 000 slutpunkter per webbtjänst. Mer information finns i [Skala en webbtjänst](scaling-webservice.md). För BES kan varje slutpunkt bearbeta 40 begäranden i taget. Ytterligare begäranden utöver dessa placeras i kö. Dessa köade förfrågningar körs automatiskt när kön krymper.

**Fördelas R-jobb mellan noder?**

Nej.  

**Hur mycket data kan jag använda för träning?**

Modulerna i Machine Learning Studio stöder datauppsättningar på upp till 10 GB med kompakta numeriska data för vanliga användningsfall. Om en modul hämtar indata från mer än ett ställe är den totala storleken för alla indata 10 GB. Du kan också ta prov på större datauppsättningar via Hive-frågor, via Azure SQL Database-frågor eller genom att bearbeta data i förväg med modulen [Inlärning med antal][counts] före införandet.  

Följande typer av data kan expanderas till större datauppsättningar under funktionsnormalisering och är begränsade till mindre än 10 GB:

* Utspridda
* Kategoriska
* Strängar
* Binära data

Följande moduler är begränsade till datauppsättningar som är mindre än 10 GB:

* Moduler för rekommenderare
* Modulen SMOTE (Synthetic Minority Oversampling Technique)
* Skriptmoduler: R, Python, SQL
* Moduler där den utgående datastorleken kan vara större än den inkommande datastorleken, till exempel kopplings- eller funktions-hashning
* Korsvalidering, hyperparametrar för justeringsmodeller, ordningstalsregression och ”en eller alla”-multiklasser, om antalet iterationer är mycket stort

Om datauppsättningarna är större än några få GB laddar du upp data till Azure Storage eller Azure SQL Database eller använder HDInsight i stället för att ladda upp direkt från en lokal fil.

**Finns det några begränsningar vad gäller vektorstorlek?**

Rader och kolumner har samma begränsningar som .NET med ett högsta antal heltal på 2 147 483 647.

**Kan jag justera storleken på den virtuella dator som kör webbtjänsten?**

Nej.  

## <a name="security-and-availability"></a>Säkerhet och tillgänglighet
**Vem har tillgång till HTTP-slutpunkten för webbtjänsten som standard? Hur begränsar jag åtkomsten till slutpunkten?**

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Du kan lägga till fler slutpunkter med deras egna nycklar från den klassiska Azure-portalen eller via programmering med hjälp av Web Service Management-API:erna. Åtkomstnycklar behövs för att göra anrop till webbtjänsten. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

**Vad händer om det inte går att hitta mitt Azure-lagringskonto?**

Machine Learning Studio behöver ett Azure-lagringskonto för att kunna spara mellanliggande data när arbetsflödet körs. Det här lagringskontot uppges till Machine Learning Studio när en arbetsyta skapas. Om lagringskontot tas bort och inte längre hittas efter att arbetsytan har skapats slutar arbetsytan att fungera och alla experiment på arbetsytan misslyckas.

Om du har tagit bort lagringskontot av misstag ska du återskapa kontot med samma namn i samma region som det borttagna lagringskontot. Efter det synkroniserar du om åtkomstnyckeln.

**Vad händer om åtkomstnyckeln för mitt lagringskonto inte är synkroniserat?**

Machine Learning Studio behöver ett Azure-lagringskonto för att kunna spara mellanliggande data när arbetsflödet körs. Det här lagringskontot uppges för Machine Learning Studio när en arbetsyta skapas och åtkomstnycklarna associeras med den arbetsytan. Om åtkomstnycklarna ändras när arbetsytan har skapats kan arbetsytan inte längre komma åt lagringskontot. Det slutar fungera och alla experiment i arbetsytan misslyckas.

Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av den klassiska Azure-portalen.  

## <a name="support-and-training"></a>Support och utbildning
**Var kan jag lära mig mer om Azure Machine Learning?**

På [Azure Machine Learning Documentation Center](https://azure.microsoft.com/services/machine-learning/) hittar du videokurser och instruktionsguider. Dessa stegvisa guider ger en introduktion till tjänsterna och förklarar datalivscykeln för import, rensning, utveckling av förutsägelsemodeller och distribution av dem till produktionsmiljön med hjälp av Azure Machine Learning.

Vi lägger till nytt material på Machine Learning Center med jämna mellanrum. Du kan skicka dina önskemål om ytterligare utbildningsmaterial på Machine Learning Center via vårt [forum för användarfeedback](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Du kan också hitta kurser på [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Hur får jag support för Azure Machine Learning?**

Om du behöver teknisk support för Azure Machine Learning går du till [Azure-support](/support/options/) och väljer **Machine Learning**.

Azure Machine Learning har även ett community-forum på MSDN där du kan ställa frågor om Azure Machine Learning. Forumet övervakas av Azure Machine Learning-teamet. Gå till [Azure-forumet](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Frågor om fakturering
**Hur fungerar faktureringen av Machine Learning?**

Azure Machine Learning har två komponenter: Machine Learning Studio och Machine Learning-webbtjänster.

När du utvärderar Machine Learning Studio kan du använda den kostnadsfria faktureringsnivån. På den kostnadsfria nivån kan du distribuera en klassisk webbtjänst med begränsad kapacitet.

När du har bekräftat att Azure Machine Learning uppfyller dina behov kan du registrera dig för standardnivån. Du måste ha en Microsoft Azure-prenumeration för att registrera dig.

På standardnivån debiteras du varje månad för varje arbetsyta som du definierar i Machine Learning Studio. När du kör ett experiment i Studio debiteras du för beräkningsresurser när du kör ett experiment. När du distribuerar en klassisk webbtjänst debiteras du för transaktioner och beräkningstimmar enligt ”betala per användning”-modellen.

De nya Resource Manager-baserade webbtjänsterna har faktureringsplaner som gör det lättare att förutse kostnaderna. Nivåindelade priser ger rabatterade hastigheter till kunder som behöver stor kapacitet.

När du skapar en plan förbinder du dig att betala en fast kostnad som inkluderar ett antal API-beräkningstimmar och API-transaktioner. Om du behöver ytterligare kapacitet kan du lägga till instanser i din faktureringsplan. Om du behöver betydligt större kapacitet kan du välja en plan på en högre nivå med avsevärt mycket större kapacitet och rabatt.

När det inkluderade antalet beräkningstimmar och transaktioner i befintliga instanser har förbrukats debiteras du för ytterligare användning enligt överförbrukningstaxan för faktureringsplanen på relevant nivå.

> [!NOTE]
Det inkluderade antalet tilldelas på nytt var 30:e dag och eventuell outnyttjad användning förs inte över till nästa period.

Mer information om fakturering och priser finns i [Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/).

**Finns det någon kostnadsfri utvärderingsversion för Machine Learning?**

 Azure Machine Learning har ett kostnadsfritt prenumerationsalternativ som beskrivs i [Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/). Machine Learning Studio har en snabbutvärdering på åtta timmar som är tillgänglig när du loggar in på [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2).

 När du registrerar dig för en kostnadsfri utvärderingsversion av Azure kan du dessutom prova alla Azure-tjänster i en månad. Mer information om den kostnadsfria utvärderingsversionen av Azure finns i [vanliga frågor och svar om den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

**Vad är en transaktion?**

En transaktion representerar ett API-anrop som Azure Machine Learning svarar på. Transaktioner från RRS- (Request-Response Service) och BES-anrop (Batch Execution Service) aggregeras och debiteras mot din faktureringsplan.

**Kan jag använda det inkluderade transaktionsantalet i en plan för både RRS- och BES-transaktioner?**

Ja, dina transaktioner från RRS och BES summeras och debiteras mot din faktureringsplan.

**Vad är en API-beräkningstimme?**

En API-beräkningstimme är faktureringsenheten för den tid det tar att köra API-anrop med hjälp av Machine Learning-beräkningsresurserna. Alla dina anrop summeras för faktureringsändamål.

**Hur lång tid tar ett typiskt API-anrop i produktionsmiljön?**

Tiden för API-anrop i produktionsmiljö kan variera avsevärt, från några hundratals millisekunder till ett par sekunder. Vissa API-anrop kan ta minuter om databehandlingen och maskininlärningsmodellen är komplexa. Det bästa sättet att uppskatta tidsåtgången för API-anrop i produktionsmiljön är att mäta prestanda för en modell i Machine Learning-tjänsten.

**Vad är en Studio-beräkningstimme?**

En Studio-beräkningstimme är faktureringsenheten för den sammanlagda tid du experimenterar med beräkningsresurser i Studio.

**Vad är utvecklings- och testnivån till för i de nya Azure Resource Manager-baserade webbtjänsterna?**

De nya Resource Manager-webbtjänsterna tillhandahåller flera nivåer som du kan använda för att etablera en lämplig faktureringsplan. I utvecklings- och testnivån ingår ett begränsat antal så att du kan testa ditt experiment som en ny webbtjänst utan att dra på dig kostnader. Du har möjlighet att testa och se hur det fungerar.

**Finns det separata lagringskostnader?**

På den kostnadsfria nivån för Machine Learning varken krävs eller tillåts separat lagring. På standardnivån för Machine Learning måste användaren ha ett Azure-lagringskonto. Azure Storage [faktureras separat](https://azure.microsoft.com/pricing/details/storage/).

**Hur stöder Machine Learning arbete som kräver hög tillgänglighet?**

Ja. Mer information och en beskrivning av serviceavtalet finns i [Machine Learning-priser](https://azure.microsoft.com/en-us/pricing/details/machine-learning/).

**Vilka specifika beräkningsresurser körs mina API-anrop på i produktionsmiljön?**

Machine Learning-tjänsten är en multitenant-tjänst. De faktiska beräkningsresurser som används på serverdelen varierar och är optimerade för prestanda och förutsägbarhet.

### <a name="management-of-new-resource-manager-based-web-services"></a>Hantering av de nya Resource Manager-baserade webbtjänsterna
**Vad händer om jag bort tar bort min plan?**

Planen tas bort från din prenumeration och du debiteras för en proportionellt beräknad förbrukning.

> [!NOTE]
Du kan inte ta bort en plan som används av en webbtjänst. Om du vill ta bort planen måste du tilldela en ny plan till webbtjänsten eller ta bort webbtjänsten.

**Vad är en planinstans?**

En planinstans är en enhet med ett inkluderat antal som du kan lägga till i din faktureringsplan. När du väljer en faktureringsnivå för din faktureringsplan medföljer en instans. Om du behöver ett större inkluderat antal kan du lägga till instanser på den valda faktureringsnivån i din plan.

**Hur många planinstanser kan jag lägga till?**

Du kan ha en instans av utvecklings- och testnivån i en prenumeration.

För nivåerna Standard S1, Standard S2 och Standard S3 kan du lägga till så många du behöver.

> [!NOTE]
Beroende på din förväntade användning kan det vara mer kostnadseffektivt att uppgradera till en nivå med ett större inkluderat antal i stället för att lägga till instanser på den nuvarande nivån.

**Vad händer om jag byter till en betalningsplan på en annan nivå (uppgraderar/nedgraderar)?**

Den gamla planen tas bort och den aktuella förbrukningen faktureras baserat på en proportionell beräkning. En ny plan med hela det inkluderade antalet för nivån som du uppgraderar eller nedgraderar till skapas för resten av perioden.

> [!NOTE]
Det inkluderade antalet tilldelas per period och eventuell outnyttjad användning förs inte över till nästa period.

**Vad händer om jag öka antalet instanser i en plan?**

Antalet inkluderas baserat på en proportionell beräkning och det kan ta 24 timmar innan ändringen tillämpas.

**Vad händer om jag tar bort en instans i en plan?**

Instansen tas bort från din prenumeration och du debiteras för en proportionellt beräknad förbrukning.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Registrera dig för planerna i de nya Resource Manager-baserade webbtjänsterna
**Hur registrera jag mig för en plan?**

Du kan skapa faktureringsplaner på två sätt.

Första gången du distribuerar en ny Resource Manager-baserad webbtjänst kan du välja en befintlig plan eller skapa en ny plan.

De planer som du skapar på det här sättet finns i din standardregion, och din webbtjänst distribueras till den regionen.

Om du vill distribuera tjänster till andra regioner än din standardregion kanske du vill definiera dina faktureringsplaner innan du distribuerar tjänsten.

I så fall kan du logga in på portalen för Azure Machine Learning Web Services och gå till sidan för betalningsplaner. Där kan du lägga till och ta bort planer och ändra befintliga planer.

**Vilken plan ska jag börja med?**

Vi rekommenderar att du börjar med standardnivån S1 och övervakar hur tjänsten används. Om du märker att du snabbt förbrukar det inkluderade antalet kan du lägga till instanser eller byta till en högre nivå med bättre rabatt. Du kan justera din faktureringsplan efter behov genom faktureringscykeln.

**I vilka regioner är de nya planerna tillgängliga?**

De nya faktureringsplanerna är tillgängliga i de tre produktionsregioner där vi stöder de nya webbtjänsterna:

* Södra centrala USA
* Västra Europa
* Sydostasien

**Jag har webbtjänster i flera regioner. Behöver jag en plan för varje region?**

Ja. Priserna för planer varierar beroende på region. När du distribuerar en webbtjänst till en annan region måste du associera den med en plan som är specifik för den regionen. Mer information finns i [Produkttillgänglighet per region]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>De nya webbtjänsterna – överförbrukning
**Hur kontrollerar jag om jag överskrider webbtjänstanvändningen?**

Du kan visa förbrukningen för alla dina planer på sidan för faktureringsplaner på Azure Machine Learning Web Services-portalen. Logga in på portalen och klicka på menyalternativet för **planer**.

I kolumnerna **Transaktioner** och **Beräkning** i tabellen ser du det inkluderade antalet för planen och förbrukningen i procent.

**Vad händer om jag förbrukar det inkluderade antalet på utvecklings- och testnivån?**

Tjänster som är kopplade till en utvecklings- och testnivå stoppas fram till nästa period eller tills du flyttar dem till en betalnivå.

**Hur beräknas priser för RRS- och BES-arbetsbelastningar för de klassiska webbtjänsterna och överförbrukning för de nya Resource Manager-baserade webbtjänsterna?**

För en RRS-arbetsbelastning debiteras du för varje API-transaktionsanrop som du gör samt för beräkningstiden som associeras med dessa förfrågningar. Dina API-transaktionskostnader för RRS i produktionsnivån beräknas som det sammanlagda antalet API-anrop som du gör multiplicerat med priset per 1 000 transaktioner (proportionellt beräknat baserat på enskilda transaktioner). Dina kostnader för API-beräkningstimmar för RRS i produktionsmiljön beräknas som den mängd tid som krävs för att köra varje API-anrop multiplicerat med det totala antalet API-transaktioner multiplicerat med priset per API-beräkningstimme i produktionsmiljön.

För överförbrukning på standardnivån S1 resulterar exempelvis 1 000 000 API-transaktioner som tar 0,72 sekunder vardera att köra (1 000 000  0,50 USD/1K API-transaktioner) i 500 USD i API-transaktionskostnader i produktionsmiljön och (1 000 000  0,72 sek * 2 USD/h) 400 USD i API-beräkningstimmar i produktionsmiljön, vilket ger en summa på 900 USD.

För en BES-arbetsbelastning debiteras du på samma sätt. Men API-transaktionskostnaderna representerar antalet batch-jobb som du skickar och beräkningskostnaderna representerar beräkningstiden som associeras med batch-jobben. Dina API-transaktionskostnader för BES i produktionsmiljön beräknas därför som det sammanlagda antalet jobb som skickats multiplicerat med priset per 1 000 transaktioner (proportionellt beräknat baserat på enskilda transaktioner). Dina kostnader för API-beräkningstimmar för BES i produktionsmiljön beräknas som den mängd tid som krävs för att köra varje rad i jobbet multiplicerat med det sammanlagda antalet rader i jobbet multiplicerat med det sammanlagda antalet jobb multiplicerat med priset per API-beräkningstimme i produktionsmiljön. När du använder Machine Learning-kalkylatorn representerar transaktionsmätaren antalet jobb som du planerar att skicka och fältet för tid per transaktion representerar den kombinerade tid som krävs för att köra alla rader i varje jobb.

Anta att du har en överförbrukning på standardnivån S1. Du skickar 100 jobb per dag och varje jobb består av 500 rader som tar 0,72 sekunder vardera. Din månatliga överförbrukning skulle vara (100 jobb per dag = 3 100 jobb/månad  0,50 USD/1 000 API-transaktioner) 1,55 USD i API-transaktionskostnader i produktionsmiljön och (500 rader  0,72 sek  3 100 jobb  2 USD/tim) 620 USD i API-beräkningstimmar i produktionsmiljön, vilket ger en totalsumma på 621,55 USD.

### <a name="azure-machine-learning-classic-web-services"></a>Klassiska Azure Machine Learning-webbtjänster
**Kan jag fortfarande betala per användning?**

Ja, de klassiska webbtjänsterna finns kvar i Azure Machine Learning.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Den kostnadsfria nivån och standardnivån för Azure Machine Learning
**Vad ingår i den kostnadsfria Azure Machine Learning-nivån?**

Den kostnadsfria Azure Machine Learning-nivån är avsedd att ge en detaljerad introduktion till Azure Machine Learning Studio. Allt du behöver är ett Microsoft-konto för att registrera dig. Den kostnadsfria nivån tillhandahåller gratis åtkomst till en Azure Machine Learning Studio-arbetsyta per [Microsoft-konto](https://www.microsoft.com/account/default.aspx). I den här nivån kan du använda upp till 10 GB lagringsutrymme och operationalisera modeller som API:er för mellanlagring. Arbetsbelastningar på den kostnadsfria nivån omfattas inte av något SLA och är endast avsedda för utveckling och personligt bruk. 

Arbetsytor på den kostnadsfria nivån har följande begränsningar:

* Arbetsbelastningar kan inte få åtkomst till data genom att ansluta till en lokal server som kör SQL Server.
* Du kan inte distribuera nya grundläggande Resource Manager-webbtjänster.


**Vad ingår i standardnivån och standardplanerna för Azure Machine Learning?**

Standardnivån för Azure Machine Learning är en betald produktionsversion av Azure Machine Learning Studio. Avgiften för Azure Machine Learning Studio faktureras månadsvis per arbetsyta och beräknas proportionellt för ofullständiga månader. Experimenttimmar i Studio för Azure Machine Learning debiteras per beräkningstimme för aktiva experiment. Faktureringen beräknas proportionellt för ofullständiga timmar.  

Azure Machine Learning API-tjänsten debiteras beroende på om det rör sig om en klassisk webbtjänst eller en ny Resource Manager-webbtjänst.

Följande avgifter beräknas per arbetsyta för din prenumeration.

* Prenumeration på Machine Learning-arbetsyta: Prenumerationen på Machine Learning-arbetsyta är en månadsavgift som ger åtkomst till en Machine Learning Studio-arbetsyta. Prenumerationen krävs för att köra experiment i Studio och för att använda API:er i produktionsmiljön.
* Experimenttimmar i Studio: Den här mätaren räknar ihop alla beräkningsrelaterade avgifter som ackumuleras när du kör experiment i Machine Learning Studio och API-anrop för produktion i mellanlagringsmiljön.
* Kom åt data genom att ansluta till en lokal server som kör SQL Server i dina modeller för utbildning och bedömning.
* För de klassiska webbtjänsterna:
  * Beräkningstimmar för produktions-API: Den här mätaren visar beräkningsrelaterade avgifter som ackumuleras av webbtjänster som körs i produktionsmiljön.
  * Transaktioner i produktions-API (i tusental): Den här mätaren visar kostnader som ackumuleras per anrop till din webbtjänst i produktionsmiljön.

Förutom föregående kostnader räknas avgifter för den valda planen in för den nya Resource Manager-baserade webbtjänsten:

* Standard S1/S2/S3 – API-plan (enheter): Den här mätaren representerar den typ av instans som valts för Resource Manager-baserade webbtjänster.
* Standard S1/S2/S3 – överförbrukning av API-beräkningstimmar: Den här mätaren visar beräkningsrelaterade avgifter som ackumuleras av de nya Resource Manager-baserade webbtjänsterna som körs i produktionsmiljön när det inkluderade antalet i befintliga instanser har förbrukats. Den ytterligare användningen debiteras enligt överförbrukningstaxan för plannivå S1/S2/S3.
* Standard S1/S2/S3 – överförbrukning av API-transaktioner (i tusental): Den här mätaren visar avgifter som ackumuleras per anrop till din Resource Manager-baserade webbtjänst i produktionsmiljön när det inkluderade antalet i befintliga instanser har förbrukats. Den ytterligare användningen debiteras enligt överförbrukningstaxan för plannivå S3/S1/S2.
* Inkluderade API-beräkningstimmar: Med Resource Manager-baserade webbtjänster representerar den här mätaren det inkluderade antalet API-beräkningstimmar.
* Inkluderade API-transaktioner (i 1 000-tal): Med Resource Manager-baserade webbtjänster representerar den här mätaren det inkluderade antalet API-transaktioner.

**Hur jag registrerar jag mig för den kostnadsfria Azure Machine Learning-nivån?**

Allt du behöver är ett Microsoft-konto. Gå till [startsidan för Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) och klicka på **Starta nu**. Logga in med ditt Microsoft-konto så skapas en arbetsyta på den kostnadsfria nivån. Du kan börja utforska och skapa Machine Learning-experiment direkt.

**Hur jag registrerar jag mig för Azure Machine Learning-standardnivån?**

Du måste först ha åtkomst till en Azure-prenumeration för att kunna skapa en Machine Learning-arbetsyta på standardnivån. Du kan registrera dig för en 30-dagars kostnadsfri utvärderingsversion av en Azure-prenumeration och senare uppgradera till en Azure-betalprenumeration eller köpa en betald Azure-prenumeration direkt. När du har fått tillgång till prenumerationen kan du skapa en Machine Learning-arbetsyta från den klassiska Microsoft Azure-portalen. Se [de stegvisa anvisningarna](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Du kan också bli inbjuden till en Machine Learning-arbetsyta på standardnivån av arbetsytans ägare.

**Kan jag välja att använda mitt eget Azure Blob Storage-konto på den kostnadsfria nivån?**

Nej, standardnivån motsvarar den version av Machine Learning-tjänsten som var tillgänglig innan nivåerna introducerades.

**Kan jag distribuera mina maskininlärningsmodeller som API:er på den kostnadsfria nivån?**

Ja, du kan operationalisera maskininlärningsmodeller till API-tjänster i mellanlagringsmiljön som en del av den kostnadsfria nivån. Du måste använda standardnivån för att flytta API-mellanlagringstjänsten till produktionsmiljön och få en produktionsslutpunkt för operationaliseringstjänsten.

**Vad är skillnaden mellan den kostnadsfria Azure-utvärderingsversionen och den kostnadsfria Azure Machine Learning-nivån?**

Med en [kostnadsfri utvärderingsversion av Microsoft Azure](https://azure.microsoft.com/free/) får du krediter som du kan använda för valfri Azure-tjänst under en månad. Den kostnadsfria Azure Machine Learning-nivån ger tillgång till Azure Machine Learning för arbetsbelastningar som inte är för produktion.

**Hur flyttar jag ett experiment från den kostnadsfria nivån till standardnivån?**

Så här kopierar du experiment från den kostnadsfria nivån till standardnivån:

1. Logga in i Azure Machine Learning Studio och kontrollera att både den kostnadsfria arbetsytan och standardarbetsytan visas i arbetsyteväljaren i det övre navigeringsfältet.
2. Växla till den kostnadsfria arbetsytan om du befinner dig på standardarbetsytan.
3. Välj ett experiment som du vill kopiera i experimentlistvyn och klicka sedan på kommandoknappen **Kopiera**.
4. Välj arbetsytan Standard i dialogrutan som öppnas och klicka på knappen **Kopiera**.
   Alla associerade datauppsättningar, tränade modeller osv. kopieras tillsammans med experimentet till standardarbetsytan.
5. Du måste köra om experimentet och publicera om webbtjänsten på standardarbetsytan.

### <a name="studio-workspace"></a>Studio-arbetsytan
**Får jag olika fakturor för olika arbetsytor?**

Avgifterna för en arbetsyta visas separat för varje tillämplig mätare på samma faktura.

**Vilka specifika typer av beräkningsresurser körs mina experiment på?**

Machine Learning-tjänsten är en multitenant-tjänst. De faktiska beräkningsresurser som används på serverdelen varierar och är optimerade för prestanda och förutsägbarhet.

### <a name="guest-access"></a>Gäståtkomst
**Vad är gäståtkomst i Azure Machine Learning Studio?**

Gäståtkomst är en begränsad utvärderingsmiljö. Du kan skapa och köra experiment i Azure Machine Learning Studio utan kostnad och utan autentisering. Gästsessioner är icke-beständiga (går inte att spara) och begränsade till åtta timmar. Exempel på andra begränsningar är avsaknaden av stöd för R och Python, frånvaron av mellanlagrings-API:er samt storleksbegränsningar för datauppsättningar och lagringskapacitet. Jämförelsevis har användare som väljer att logga in med ett Microsoft-konto fullständig åtkomst till den kostnadsfria Machine Learning Studio-nivån som beskrivs ovan, som innehåller en beständig arbetsyta och mer omfattande funktioner. Välj din kostnadsfria Machine Learning-miljö genom att klicka på **Kom igång** på [https://studio.azureml.net](https://studio.azureml.net). Välj sedan **Gästbehörighet** eller logga in med ditt Microsoft-konto.

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

