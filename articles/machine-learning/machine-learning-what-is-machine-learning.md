<properties
    pageTitle="Vad är Machine Learning i Azure? | Microsoft Azure"
    description="Förklarar grundläggande begrepp om den fullständigt hanterade tjänsten Machine Learning, en molnteknik som du kan använda för att skapa, operationalisera och tjäna pengar på dina lösningar."
    keywords="what is machine learning,cloud technology,predictive,what is predictive analytics,operationalize"
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
    ms.date="04/28/2016"
    ms.author="cgronlun;tedway;olgali"/>


# Introduktion till maskininlärning i Microsoft Azure

## Vad är maskininlärning?

Maskininlärning använder datorer för att köra förutsägelsemodeller som lär sig från befintliga data för att kunna göra prognoser för framtida beteenden, resultat och trender.

Dessa prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort dras används maskininlärning för att jämföra transaktionen med en transaktionsdatabas, vilket kan hjälpa banken att upptäcka bedrägerier.

## Vad är Machine Learning i Microsoft Azure?

Azure Machine Learning är kraftfull molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar.

Azure Machine Learning ger inte bara tillgång till verktyg för att modellera förutsägelseanalyser, utan innehåller också en fullständigt hanterad tjänst som du kan använda för att distribuera förutsägelsemodeller som användningsklara webbtjänster. Azure Machine Learning innehåller de verktyg som du behöver för att skapa fullständiga förutsägelseanalyslösningar i molnet. Du kan snabbt skapa, testa, operationalisera och hantera förutsägelsemodeller. Du behöver inte köpa någon maskinvara eller hantera virtuella datorer manuellt.

![Vad är maskininlärning? Grundläggande arbetsflöde för att operationalisera förutsägelseanalyser i Azure Machine Learning.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Vad är förutsägelseanalyser?

Förutsägelseanalyser analyserar mönster eller trender i insamlade eller aktuella data med hjälp av olika statistiska metoder (i detta fall maskininlärning) för att göra prognoser för framtida händelser.

Azure Machine Learning är ett särskilt kraftfullt sätt att göra förutsägelseanalyser: du kan utgå från ett färdigt biblioteket med algoritmer, skapa modeller på en Internetansluten dator utan att köpa ytterligare utrustning eller infrastruktur och snabbt distribuera din förutsägelselösning. Du kan också hitta användningsklara exempel och lösningar på [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) och i  [Cortana Intelligence-galleriet](http://gallery.cortanaintelligence.com/).

## Skapa fullständiga maskininlärningslösningar i molnet

Azure Machine Learning har allt du behöver för att skapa förutsägelseanalyslösningar i molnet: ett stort algoritmbibliotek, verktyg för modellutveckling och ett enkelt sätt att distribuera din modell som en webbtjänst.

### Machine Learning Studio: Skapa förutsägelsemodeller

Skapa förutsägelsemodeller i det webbläsarbaserade verktyget [Machine Learning Studio](machine-learning-what-is-ml-studio.md) genom att dra, släppa och koppla moduler.

![Vad är förutsägelseanalyser: Exempel på ett förutsägelseanalysexperiment i Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* Kom snabbt igång med dina förutsägelsemodeller genom att dra nytta av det stora biblioteket med [algoritmer och moduler för maskininlärning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) i Machine Learning Studio. Välj ett bibliotek med exempelexperiment, R- och Python-paket och algoritmer av högsta klass från Xbox och Bing med flera. Utöka Studio-modulerna med dina egna anpassade [R](machine-learning-r-quickstart.md)- och [Python](machine-learning-execute-python-scripts.md)-skript.
* I [Cortana Intelligence-galleriet](machine-learning-gallery-how-to-use-contribute-publish.md) kan du prova analyslösningar som skapats av andra eller bidra med egna lösningar med hjälp av Azure-tjänster som Machine Learning, HDInsight (Hadoop), Stream Analytics och Data Lake Analytics, samt stordatalager och datahanteringstjänster i Azure.  Skicka frågor eller kommentarer om experiment till communityn eller dela länkar till experiment via sociala nätverk, till exempel LinkedIn och Twitter.  

    ![Prova förutsägelseexperiment eller bidra med egna i Azure Cortana Intelligence-galleriet](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

### Operationalisera förutsägelseanalyslösningar: Köp webbtjänster eller publicera dina egna

* Köp användningsklara webbtjänster från [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), till exempel Recommendations, Text Analytics och Anomaly Detection.

* Operationalisera förutsägelseanalysmodeller:
    * [Distribuera webbtjänster](machine-learning-publish-a-machine-learning-web-service.md)
    * [Träna och träna om modeller via API:er](machine-learning-retrain-models-programmatically.md)
    * [Hantera slutpunkter för webbtjänster](machine-learning-create-endpoint.md)
    * [Skala webbtjänster](machine-learning-scaling-endpoints.md)
    * [Konsumera webbtjänster](machine-learning-consume-web-services.md)

## Grundläggande termer och begrepp inom maskininlärning
### Datagranskning, beskrivande analys och förutsägelseanalys

**Datagranskning** är en process där du samlar in information om en stor och ofta ostrukturerad datauppsättning för att identifiera egenskaper för fokuserad analys. **Datautvinning** syftar på automatiserad datagranskning.

**Beskrivande analys** är en process där du analyserar en datauppsättning för att sammanfatta vad som hänt. Merparten av alla företagsanalyser – försäljningsrapporter, mätvärden från webben och analys av sociala nätverk – är beskrivande.

**Förutsägelseanalyser** är en process där du skapar modeller från historiska eller aktuella data för att förutsäga framtida resultat.


### Övervakad och oövervakad inlärning
 Algoritmer för **övervakad inlärning** tränas med märkta data, dvs. data bestående av exempel på önskade svar. En modell som identifierar användning av falska kreditkort kan exempelvis tränas från en datauppsättning med märkta datapunkter för kända bedrägliga och giltiga betalningar. Merparten av all maskininlärning är övervakad.

 **Oövervakad inlärning** används med omärkta data och målet är att identifiera relationer i dessa data. Du kanske exempelvis vill identifiera grupperingar av kundområden med liknande köpvanor.

### Träning och utvärdering av modeller
En maskininlärningsmodell är en abstraktion av den fråga som du försöker besvara eller det resultat som du vill förutsäga. Modeller tränas och utvärderas från befintliga data.

#### Träning från data
I Azure Machine Learning skapas en modell från en algoritmmodul som bearbetar träningsdata och funktionella moduler, t.ex. en poängsättningsmodul.

Om du tränar en bedrägeriidentifieringsmodell med övervakad inlärning använder du en uppsättning transaktioner som är märkta som bedrägliga eller giltiga. Du delar datauppsättningen slumpmässigt och använder en del för att träna modellen och en del för att testa eller utvärdera modellen.

#### Utvärdering av data
När du har en tränad modell utvärderar du modellen med hjälp av återstående testdata. Du använder data vars resultat du redan känner till, så att du kan se om din modell gör korrekta förutsägelser.

### Andra vanliga maskininlärningstermer

* **algoritmen**: En fristående uppsättning regler som används för att lösa problem genom databearbetning, beräkning eller automatiserade resonemang.
* **kategoriska data**: Data som är ordnade efter kategorier och som kan delas in i grupper. En uppsättning kategoriska data för bilar kan exempelvis definiera år, märke, modell och pris.
* **klassificering**: En modell för att organisera datapunkter i kategorier baserat på en datauppsättning vars kategorigrupperingar redan är kända.
* **egenskapsval**: En process för att extrahera eller välja egenskaper relaterade till en datauppsättning för att förbättra datauppsättningen och förbättra resultatet. Information om flygpriser kan till exempel förbättras med dagar i veckan och helgdagar. Mer information finns i avsnittet om [egenskapsval i Azure Machine Learning](machine-learning-feature-selection-and-engineering.md).
* **modul**: Ett funktionellt element i en Machine Learning Studio-modell, till exempel modulen Ange data som kan användas för att mata in och redigera små datauppsättningar. En algoritm är också en typ av modul i Machine Learning Studio.
* **modell**: För övervakad inlärning är en modell produkten av ett maskininlärningsexperiment bestående av en datauppsättning för träning, en algoritmmodul och funktionella moduler som modulen Poängsätta modell.
* **numeriska data**: Data som representerar mått (kontinuerliga data) eller antal (diskreta data). Kallas även *kvantitativa data*.
* **partition**: En metod för att dela in data i prover. Mer information finns i [Partitionera och ta prover](https://msdn.microsoft.com/library/azure/dn905960.aspx).
* **förutsägelse**: En förutsägelse är en prognos av värden från en maskininlärningsmodell. Du kan också se termen ”förutsägelsepoäng”, men detta är inte det slutgiltiga resultatet för en modell. Poängsättningen följs av en utvärdering av modellen.
* **regression**: En mall för att förutsäga ett kontinuerligt värde baserat på oberoende variabler, till exempel att förutsäga priset på en bil baserat på dess år och märke.
* **poäng**: Ett prognostiserat värde som genereras från en tränad klassificerings- eller regressionsmodell med hjälp av modulen [Poängsätta modell](https://msdn.microsoft.com/library/azure/dn905995.aspx) i Machine Learning Studio. Klassificeringsmodeller bedömer också sannolikheten för förutsägelsevärdet. När du har genererat poäng från en modell kan du utvärdera modellens precision med hjälp av modellen [Utvärdera modell](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **prov**: En del av en datauppsättning som är avsedd att representera helheten. Prover kan väljas slumpmässigt eller baserat på specifika funktioner i datauppsättningen.



## Nästa steg
Du kan lära dig grunderna i förutsägelseanalyser och maskininlärning genom att gå en [självstudiekurs med stegvisa anvisningar](machine-learning-create-experiment.md) och genom att [bygga vidare på prover](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/



<!--HONumber=Jun16_HO2-->


