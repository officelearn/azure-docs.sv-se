---
title: Vanliga frågor (FAQ) om Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio: Vanliga frågor och svar som täcker fakturering, funktioner och begränsningar i en molntjänst för effektiv förutsägelsemodellering.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462287"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Azure Machine Learning Studio vanliga frågor och svar: funktioner och begränsningar
Här får du svar på vanliga frågor om molntjänsten Azure Machine Learning som tillhandahåller förutsägelsemodeller och operationaliseringslösningar genom webbtjänster. 

## <a name="general-questions"></a>Allmänna frågor
**Vad är Machine Learning Studio?**

Machine Learning Studio är en miljö med dra och släpp arbetsytor som du har åtkomst till med hjälp av en webbläsare. I Machine Learning Studio får du tillgång till flera olika moduler i ett visuellt gränssnitt så att du enkelt kan skapa ett datavetenskapsflöde från slutpunkt till slutpunkt i form av ett experiment.

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

* Request-Response Service (RR): Med låg fördröjning, mycket skalbar tjänst som tillhandahåller ett gränssnitt för de tillståndslösa modeller som skapas och distribueras med hjälp av Machine Learning Studio.
* Batch Execution Service (BES): En asynkron tjänst som poängsätter en batch med dataposter.

Du kan använda REST-API:et och komma åt webbtjänsten på flera sätt. Du kan till exempel skriva ett program i C#, R eller Python med exempelkoden som genererades när du distribuerade webbtjänsten.

Exempelkoden finns på:
- Sidan Använda för webbtjänsten i portalen Azure Machine Learning-webbtjänster
- API-hjälpsidan i instrumentpanelen för webbtjänster i Machine Learning Studio

Du kan också använda Microsoft Excel-exempelarbetsboken som skapas åt dig. Den finns också på instrumentpanelen för webbtjänsten i Machine Learning Studio.

**Vilka är de viktigaste uppdateringarna i Azure Machine Learning?**

De senaste uppdateringarna finns i [Nyheter i Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="import-and-export-data-for-machine-learning"></a>Importera och exportera data för Machine Learning
**Vilka datakällor stöder Machine Learning?**

Du kan ladda ned data till ett Machine Learning Studio-experiment på tre sätt:

- Överföra en lokal fil som en datauppsättning
- Använda en modul för att importera data från molndatatjänster
- Importera en datamängd som sparats i ett annat experiment

Mer information om vilka filformat som stöds finns i [Importera träningsdata till Machine Learning Studio](import-data.md).

### <a id="ModuleLimit"></a>Hur stor kan datauppsättningen vara för mina moduler?
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

### <a id="UploadLimit"></a>Vilka gränser gäller för datauppladdningar?
För datauppsättningar som är större än ett par GB laddar du upp data till Azure Storage eller Azure SQL Database. Du kan också använda Azure HDInsight i stället för att ladda upp direkt från en lokal fil.

**Kan jag läsa data från Amazon S3?**

Om du har en liten mängd data och vill exponera dem via en HTTP-URL kan du använda modulen [Importera data][import-data]. Om du har större mängder data laddar du upp dem till Azure Storage först och använder sedan modulen [Importera data][import-data] för att ta med dem i experimentet.
<!--

<SEE CLOUD DS PROCESS>
-->

**Finns det inbyggda bildinhämtningsfunktioner?**

Mer information om bildinmatningsfunktioner finns i referensen för [bildimport][image-reader].

## <a name="modules"></a>Moduler
**Algoritmen, datakällan, dataformatet eller datatransformeringsåtgärden som jag söker efter finns inte i Azure Machine Learning Studio. Vad har jag för alternativ?**

I vårt [forum för användarfeedback](https://go.microsoft.com/fwlink/?LinkId=404231) ser du vilka funktionsförfrågningar som vi följer upp. Lägg din röst på en begäran om en funktion som du letar efter redan har begärts. Om funktionen som du letar efter inte finns skapar du en ny begäran. Du kan även visa statusen för din begäran i det här forumet. Vi följer noga den här listan och uppdaterar statusen för funktionstillgänglighet med jämna mellanrum. Du kan också använda det inbyggda stödet för R och Python för att skapa anpassade transformeringar vid behov.

**Kan jag använda min befintliga kod i Machine Learning Studio?**

Ja, du kan hämta din befintliga R- eller Python-kod till Machine Learning Studio, köra den i experiment med Azure Machine Learning-inlärning och distribuera lösningen som en webbtjänst via Azure Machine Learning. Mer information finns i [Utöka ditt experiment med R](extend-your-experiment-with-r.md) och [Köra Python-maskininlärningsskript i Azure Machine Learning Studio](execute-python-scripts.md).

**Går det att använda något som [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) för att definiera en modell?**

Nej, PMML (Predictive Model Markup Language) stöds inte. Du kan använda anpassad R- och Python-kod för att definiera en modul.

**Hur många moduler kan jag köra parallellt i mitt experiment?**  

Du kan köra upp till fyra moduler parallellt i ett experiment.

## <a name="data-processing"></a>Databearbetning
**Går det att visualisera data (utöver R-visualiseringar) interaktivt i experimentet?**

Du kan visualisera data och få statistik genom att klicka på utdata från en modul.

**När jag förhandsgranskar resultatet eller data i webbläsaren är antalet rader och kolumner begränsat. Varför?**

Eftersom stora mängder data kan skickas till en webbläsare är datastorleken begränsad för att förhindra att Machine Learning Studio går långsamt. Om du vill visualisera alla data/resultat är det bättre att ladda ned data och använda Excel eller något annat verktyg.

## <a name="algorithms"></a>Algoritmer
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

## <a name="r-module"></a>R-modulen
**Vilka R-paket är tillgängliga i Machine Learning Studio?**

För närvarande stöder Machine Learning Studio mer än 400 R CRAN-paket. Här är den [uppdaterade listan](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) över alla paket som ingår. Se även [Utöka ditt experiment med R](extend-your-experiment-with-r.md) om du vill lära dig hur du kan hämta den här listan själv. Om det paket som du behöver inte finns med i listan lägger du in namnet på paketet i vårt [forum för användarfeedback](https://go.microsoft.com/fwlink/?LinkId=404231).

**Går det att skapa en anpassad R-modul?**

Ja. Mer information finns i [Redigera anpassade R-moduler i Azure Machine Learning](custom-r-modules.md).

**Finns det en REPL-miljö för R?**

Nej, det finns ingen REPL-miljö (Read-Eval-Print-Loop) för R i Studio.

## <a name="python-module"></a>Python-modulen
**Går det att skapa en anpassad Python-modul?**

Det går inte för närvarande, men du kan använda en eller flera moduler av typen [Kör Python-skript][python] för att få samma resultat.

**Finns det en REPL-miljö för Python?**

Du kan använda Jupyter Notebooks i Machine Learning Studio. Mer information finns i [Introduktion till Jupyter Notebooks i Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webbtjänst

**Hur tränar jag om Azure Machine Learning-modeller via programmering?**

Använd omtränings-API:erna. Mer information finns i [Träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). Exempelkod är också tillgänglig i demonstrationen [Microsoft Azure Machine Learning Retraining](https://azuremlretrain.codeplex.com/).

**Kan jag distribuera modellen lokalt eller i ett program som saknar Internetanslutning?**

Nej.

**Finns det en typisk svarstid som kan förväntas för alla webbtjänster?**

Mer information finns i [Azure-prenumerationsbegränsningar](../../azure-subscription-service-limits.md).

**När ska jag köra min förutsägelsemodell som en batchkörningstjänst (BES) eller som en svar på begäran-tjänst (RRS)?**

RR-tjänsten är en högskalig webbtjänst med korta svarstider som används för att tillhandahålla ett gränssnitt för tillståndslösa modeller som skapas och distribueras från experimentmiljön. BES (Batch Execution Service) är en tjänst som asynkront poängsätter en samling dataposter. Indata för BES är som de indata som används av RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive-fråga) och HTTP-källor. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

**Hur uppdaterar jag modellen för den distribuerade webbtjänsten?**

Du kan uppdatera en förutsägelsemodell för en redan distribuerad tjänst genom att ändra och köra om experimentet som du använde för att redigera och spara den tränade modellen. När du har en ny version av den tränade modellen frågar Machine Learning Studio om du vill uppdatera webbtjänsten. Mer information om hur du uppdaterar en distribuerad webbtjänst finns i [Distribuera en Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

Du kan också använda omtränings-API:erna.
Mer information finns i [Träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md). Exempelkod är också tillgänglig i demonstrationen [Microsoft Azure Machine Learning Retraining](https://azuremlretrain.codeplex.com/).

**Hur övervakar jag min webbtjänst när den har distribuerats i produktionsmiljön?**

När du har distribuerat en förutsägbar modell kan du övervaka den från portalen för Azure Machine Learning-webbtjänster. Varje distribuerad tjänst har sin egen instrumentpanel där du kan se övervakningsinformation för tjänsten. Mer information om hur du hanterar distribuerade webbtjänster finns i [Hantera en webbtjänst med hjälp av portalen för Azure Machine Learning-webbtjänster](manage-new-webservice.md) och [Hantera en Azure Machine Learning-arbetsyta](manage-workspace.md).

**Finns det någon plats där jag kan se utdata för min RRS-/BES-tjänst?**

För RRS är webbtjänstsvaret vanligtvis där du ser resultatet. Du kan också skriva det till Azure Blob Storage. För BES skrivs utdata till en blobb som standard. Du kan också skriva utdata till en databas eller tabell med hjälp av modulen [Exportera Data][export-data].

**Kan jag bara skapa webbtjänster från modeller som skapats i Machine Learning Studio?**

Nej, du kan också skapa webbtjänster direkt med Jupyter Notebooks och RStudio.

**Var hittar jag information om felkoder?**

En lista med felkoder och beskrivningar finns i [Felkoder för Machine Learning-moduler](https://msdn.microsoft.com/library/azure/dn905910.aspx).

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

Rader och kolumner är var begränsade till .NET-begränsning av Max Int: 2,147,483,647.

**Kan jag justera storleken på den virtuella dator som kör webbtjänsten?**

Nej.  

## <a name="security-and-availability"></a>Säkerhet och tillgänglighet
**Vem har tillgång till HTTP-slutpunkten för webbtjänsten som standard? Hur begränsar jag åtkomsten till slutpunkten?**

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Du kan lägga till fler slutpunkter med deras egna nycklar från Web Services-portalen eller via programmering med hjälp av Web Service Management-API:erna. Åtkomstnycklar behövs för att göra anrop till webbtjänsten. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

**Vad händer om det inte går att hitta mitt Azure-lagringskonto?**

Machine Learning Studio behöver ett Azure-lagringskonto för att kunna spara mellanliggande data när arbetsflödet körs. Det här lagringskontot uppges till Machine Learning Studio när en arbetsyta skapas. Om lagringskontot tas bort och inte längre hittas efter att arbetsytan har skapats slutar arbetsytan att fungera och alla experiment på arbetsytan misslyckas.

Om du har tagit bort lagringskontot av misstag ska du återskapa kontot med samma namn i samma region som det borttagna lagringskontot. Efter det synkroniserar du om åtkomstnyckeln.

**Vad händer om åtkomstnyckeln för mitt lagringskonto inte är synkroniserat?**

Machine Learning Studio behöver ett Azure-lagringskonto för att kunna spara mellanliggande data när arbetsflödet körs. Det här lagringskontot uppges för Machine Learning Studio när en arbetsyta skapas och åtkomstnycklarna associeras med den arbetsytan. Om åtkomstnycklarna ändras när arbetsytan har skapats kan arbetsytan inte längre komma åt lagringskontot. Det slutar fungera och alla experiment i arbetsytan misslyckas.

Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av Azure Portal.  


## <a name="billing-questions"></a>Frågor om fakturering

Information om fakturering och priser finns i avsnittet om [Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/).


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
