<properties
    pageTitle="Vad är Machine Learning i Azure? | Microsoft Azure"
    description="Förklarar grundläggande begrepp relaterade till maskininlärning i molnet och vad du kan använda det till, samt definierar viktiga termer inom maskininlärning."
    keywords="Vad är maskininlärning, termer inom maskininlärning, förutsäga, vad är förutsägelseanalys, operationalisera"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="cgronlun;tedway;olgali"/>


# Introduktion till maskininlärning i molnet

## Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som hjälper datorer att lära sig från befintliga data för att kunna göra prognoser om framtida beteenden, resultat och trender.  

Dessa prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket gör det lättare att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

För en kortfattad översikt rekommenderar vi videoserien [Data Science for Beginners](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) (datavetenskap för nybörjare). Utan teknisk jargong eller matematiskt språk förklarar videoserien vad maskininlärning är och vägleder dig genom en enkel förutsägelsemodell.

## Vad är Machine Learning i Microsoft Azure-molnet?

Azure Machine Learning är kraftfull molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar. När du använder Azure-molnet för att köra maskininlärningsexperiment eller skapa lösningar behöver du inte köpa dyr maskinvara eller infrastruktur.

![Vad är maskininlärning? Grundläggande arbetsflöde för att operationalisera förutsägelseanalyser i Azure Machine Learning.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Azure Machine Learning ger inte bara tillgång till verktyg för att modellera förutsägelseanalyser, utan tillhandahåller också en fullständigt hanterad tjänst som du kan använda för att distribuera förutsägelsemodeller som användningsklara webbtjänster. Azure Machine Learning innehåller de verktyg som du behöver för att skapa fullständiga förutsägelseanalyslösningar i molnet. Du kan snabbt skapa, testa, operationalisera och hantera förutsägelsemodeller.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Vad är förutsägelseanalyser?

Förutsägelseanalyser använder olika matematiska formler kallade algoritmer som analyserar historiska eller aktuella data för att identifiera mönster eller trender som sedan används som utgångspunkt för prognoser om framtida händelser.

Azure Machine Learning är ett särskilt kraftfullt sätt att göra förutsägelseanalyser: du kan arbeta från ett färdigt bibliotek med algoritmer, använda algoritmerna för att skapa modeller på en Internetansluten dator och snabbt distribuera din förutsägelselösning. För att snabbt få resultat kan du börja med färdiga exempel och lösningar i [Cortana Intelligence-galleriet](http://gallery.cortanaintelligence.com/).

## Skapa fullständiga maskininlärningslösningar i molnet

Azure Machine Learning har allt du behöver för att skapa förutsägelseanalyslösningar i molnet: ett stort algoritmbibliotek, verktyg för modellutveckling och ett enkelt sätt att distribuera din modell som en webbtjänst.

### Machine Learning Studio: Skapa förutsägelsemodeller

I [Machine Learning Studio](machine-learning-what-is-ml-studio.md) kan du snabbt skapa förutsägelsemodeller genom att dra, släppa och ansluta moduler. Det är enkelt att experimentera med olika kombinationer och du kan [prova verktyget utan kostnad](https://studio.azureml.net/?selectAccess=true&o=2).

![Vad är förutsägelseanalyser: Exempel på ett förutsägelseanalysexperiment i Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* I [Cortana Intelligence-galleriet](machine-learning-gallery-how-to-use-contribute-publish.md) kan du prova analyslösningar som skapats av andra eller bidra med dina egna. Skicka frågor eller kommentarer om experiment till communityn eller dela länkar till experiment via sociala nätverk, till exempel LinkedIn och Twitter.
* Kom snabbt igång med dina förutsägelsemodeller genom att dra nytta av det stora biblioteket med [algoritmer och moduler för maskininlärning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) i Machine Learning Studio. Välj bland exempelexperiment, R- och Python-paket och algoritmer av högsta klass från Xbox och Bing med flera. Utöka Studio-modulerna med dina egna anpassade [R](machine-learning-r-quickstart.md)- och [Python](machine-learning-execute-python-scripts.md)-skript.


    ![Prova förutsägelseexperiment eller bidra med egna i Azure Cortana Intelligence-galleriet](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

### Operationalisera förutsägelseanalyslösningar: Köp webbtjänster eller publicera dina egna

* Köp användningsklara webbtjänster från [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), till exempel Recommendations, Text Analytics och Anomaly Detection.

* Operationalisera förutsägelseanalysmodeller:
    * [Distribuera webbtjänster](machine-learning-publish-a-machine-learning-web-service.md)
    * [Träna och träna om modeller via API:er](machine-learning-retrain-models-programmatically.md)
    * [Hantera slutpunkter för webbtjänster](machine-learning-create-endpoint.md)
    * [Skala webbtjänster](machine-learning-scaling-endpoints.md)
    * [Konsumera webbtjänster](machine-learning-consume-web-services.md)

## Viktiga termer och begrepp inom maskininlärning

Maskininlärningstermerna kan vara förvirrande. Här följer definitioner av de viktigaste termerna. Skicka gärna kommentarer om det finns andra termer som du vill få förklarade.

### Datagranskning, beskrivande analys och förutsägelseanalys

**Datagranskning** är en process där du samlar in information om en stor och ofta ostrukturerad datauppsättning för att identifiera egenskaper för fokuserad analys. **Datautvinning** syftar på automatiserad datagranskning.

**Beskrivande analys** är en process där du analyserar en datauppsättning för att sammanfatta vad som hänt. Merparten av alla företagsanalyser – försäljningsrapporter, mätvärden från webben och analys av sociala nätverk – är beskrivande.

**Förutsägelseanalyser** är en process där du skapar modeller från historiska eller aktuella data för att förutsäga framtida resultat.


### Övervakad och oövervakad inlärning
 Algoritmer för **övervakad inlärning** tränas med märkta data, dvs. data bestående av exempel på önskade svar. En modell som identifierar falska kreditkort skulle exempelvis tränas från en datauppsättning med namngivna datapunkter för kända falska och giltiga debiteringar. Merparten av all maskininlärning är övervakad.

 **Oövervakad inlärning** används med omärkta data och målet är att identifiera relationer i dessa data. Du kanske exempelvis vill identifiera grupperingar av kundområden med liknande köpvanor.

### Träning och utvärdering av modeller
En maskininlärningsmodell är en abstraktion av den fråga som du försöker besvara eller det resultat som du vill förutsäga. Modeller tränas och utvärderas från befintliga data.

#### Träningsdata
När du tränar en modell från data använder du en känd datauppsättning och justerar modellen baserat på dataegenskaperna för att få det mest korrekta svaret. I Azure Machine Learning skapas en modell från en algoritmmodul som bearbetar träningsdata och funktionella moduler, t.ex. en poängsättningsmodul.

Om du tränar en bedrägeriidentifieringsmodell med övervakad inlärning använder du en uppsättning transaktioner som är märkta som bedrägliga eller giltiga. Du delar datauppsättningen slumpmässigt och använder en del för att träna modellen och en del för att testa eller utvärdera modellen.

#### Utvärdering av data
När du har en tränad modell utvärderar du modellen med hjälp av återstående testdata. Du använder data vars resultat du redan känner till, så att du kan se om din modell gör korrekta förutsägelser.

## Andra vanliga maskininlärningstermer

* **algoritmen**: En fristående uppsättning regler som används för att lösa problem genom databearbetning, beräkning eller automatiserade resonemang.
* **avvikelseidentifiering**: En modell som flaggar ovanliga händelser eller värden och hjälper dig att identifiera problem. Exempelvis letar en identifiering av falska kreditkort efter ovanliga köp.
* **kategoriska data**: Data som är ordnade efter kategorier och som kan delas in i grupper. En uppsättning kategoriska data för bilar kan exempelvis definiera år, märke, modell och pris.
* **klassificering**: En modell för att organisera datapunkter i kategorier baserat på en datauppsättning vars kategorigrupperingar redan är kända.
* **egenskapsval**: En process för att extrahera eller välja egenskaper relaterade till en datauppsättning för att förbättra datauppsättningen och förbättra resultatet. Information om flygpriser kan till exempel förbättras med dagar i veckan och helgdagar. Mer information finns i avsnittet om [egenskapsval i Azure Machine Learning](machine-learning-feature-selection-and-engineering.md).
* **modul**: Ett funktionellt element i en Machine Learning Studio-modell, till exempel modulen Enter Data (Ange data), som kan användas för att mata in och redigera små datauppsättningar. En algoritm är också en typ av modul i Machine Learning Studio.
* **modell**: En övervakad inlärningsmodell är produkten av ett maskininlärningsexperiment bestående av träningsdata, en algoritmmodul och funktionella moduler som modulen Score Model (Poängsätta modell).
* **numeriska data**: Data som representerar mått (kontinuerliga data) eller antal (diskreta data). Kallas även *kvantitativa data*.
* **partition**: En metod för att dela in data i prover. Mer information finns i [Partitionera och ta prover](https://msdn.microsoft.com/library/azure/dn905960.aspx).
* **förutsägelse**: En förutsägelse är en prognos av värden från en maskininlärningsmodell. Du kan också se termen ”förutsägelsepoäng”. Detta är emellertid inte en modells slutgiltiga data. Poängsättningen följs av en utvärdering av modellen.
* **regression**: En mall för att förutsäga ett värde baserat på oberoende variabler, till exempel att förutsäga priset på en bil baserat på dess år och märke.
* **poäng**: Ett prognostiserat värde som genereras från en tränad klassificerings- eller regressionsmodell med hjälp av modulen [Poängsätta modell](https://msdn.microsoft.com/library/azure/dn905995.aspx) i Machine Learning Studio. Klassificeringsmodeller bedömer också sannolikheten för förutsägelsevärdet. När du har genererat poäng från en modell kan du utvärdera modellens precision med hjälp av modellen [Utvärdera modell](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **prov**: En del av en datauppsättning som är avsedd att representera helheten. Prover kan väljas slumpmässigt eller baserat på specifika funktioner i datauppsättningen.



## Nästa steg
Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att gå en [självstudiekurs med stegvisa anvisningar](machine-learning-create-experiment.md) och genom att [bygga vidare på prover](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/



<!--HONumber=sep16_HO1-->


