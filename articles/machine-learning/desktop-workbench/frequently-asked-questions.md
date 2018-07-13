---
title: Azure Machine Learning-förhandsversionen 2017 vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor och svar för Azure Machine Learnings förhandsversionsfunktioner
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 94a1f3bbba83e8e71cf9440b5ded0784f4616c99
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38674163"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Machine Learning

Azure Machine Learning är en fullständigt hanterad Azure-tjänst som hjälper dig att skapa, testa, hantera och distribuera machine learning och AI-modeller. Våra tjänster och hämtningsbart program erbjuder en metod som prioriterar kod som utnyttjar molnet, lokalt och edge för att tillhandahålla tåget, distribuera, hantera och övervaka modeller med power, hastighet och flexibilitet. Azure Machine Learning Studio erbjuder också en webbläsarbaserad, visuella dra och släpp utvecklingsmiljön där ingen kodning krävs. 

## <a name="general-product-questions"></a>Generella frågor

**Vilka andra Azure-tjänster krävs?**

Azure Blob Storage och Azure Container Registry som används av Azure Machine Learning. Dessutom behöver du etablera beräkningsresurser som en virtuell dator för datavetenskap eller HDInsight-kluster. Beräkning och värdtjänster krävs även när distribuera webbtjänster, till exempel [Azure Container Service](https://docs.microsoft.com/azure/aks).

**Hur relaterar Azure Machine Learning till Microsoft Machine Learning Services i SQL Server 2017?**   

Machine Learning Services i SQL Server 2017 är en utökningsbar och skalbar plattform för att integrera maskininlärning i databas-arbetsflöden. Det är passar perfekt för scenarier där en lokal lösning krävs, till exempel där dataförflyttning är dyra eller ohållbara. Moln eller hybrid arbetsbelastningar är däremot passade bra för våra nya Azure-tjänster. 

**Har du stöd för både Python och R? Vad gäller andra programmeringsspråk som C++**

Vi stöder för närvarande Python endast. Vi arbetar med R-integrering och förväntar sig att den är tillgänglig snart. 

**Hur Azure Machine Learning är relaterade till Microsoft Machine Learning för Spark?**

MMLSpark ger djupinlärning och dataforskningsverktyg för Apache Spark, med fokus på produktivitet, enkel experimentering och den senaste algoritmer. MMLSpark möjliggör integrering med Spark Machine Learning pipelines med Microsoft Cognitive Toolkit och OpenCV. Du kan skapa kraftfulla, mycket skalbara förutsägande och analytiska modeller för bild- och data. MMLSpark är tillgängliga under licens för öppen källkod och som ingår i AML Workbench som en uppsättning använda modeller och algoritmer. Mer information om MMLSpark finns i produktdokumentationen för. 

**Vilka versioner av Spark stöds av de nya verktyg och tjänster?**

Workbench omfattar för närvarande och har stöd för MMLSpark version 0,8, som är kompatibel med Apache Spark 2.1. Du har också ett alternativ för att använda GPU-aktiverade Docker-avbildning av MMLSpark 0,8 på Linux-datorer.

## <a name="experimentation-service"></a>Experimentering

**Vad är Azure Machine Learning Experimentation Service?**

Experimenteringstjänsten är en hanterad Azure-tjänst som använder machine learning-experimentering till nästa nivå. Experiment kan byggas lokalt eller i molnet. Snabbt prototyper på en stationär dator, sedan skala på virtuella datorer eller Spark-kluster. Virtuella Azure-datorer med den senaste GPU-tekniken gör att du kan bedriva deep learning snabbt och effektivt. Vi har även inkluderat djupgående integrering med Git så att du enkelt kan ansluta till befintliga arbetsflöden för kod, spåra, konfiguration och samarbete. 

**Hur kommer jag att debiteras för tjänsten experimentering?**

De två första användarna som är associerade med din Azure Machine Learning Experimentation Service är kostnadsfria. Ytterligare användare debiteras enligt priserna för offentliga förhandsversionen av 50 USD/månad. Besök sidan med våra priser mer information om priser och fakturering.

**Kommer jag att debiteras baserat på hur många experiment jag kör?**

Nej, i experimentering kan många experiment som du behöver och debiteras endast utifrån antalet användare. Beräkningsresurser för Experimentering debiteras separat. Vi rekommenderar att du kan köra flera experiment så att du kan hitta det bästa passning modell för din lösning.   

**Vilken specifik typ av beräknings- och lagringsresurser kan jag använda?**

Tjänsten experimentering kan köra dina experiment på lokala datorer (direkt eller dockerbaserade), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), och [HDInsight](https://azure.microsoft.com/services/hdinsight/). Tjänsten har också åtkomst till en [Azure Storage](https://azure.microsoft.com/services/storage/) för lagring av utdata från körningen och kan utnyttja en [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) konto för versionskontroll och Git-lagring. Observera att du faktureras separat för alla förbrukade beräknings- och lagringsresurser, baserat på den individuella prissättningen.  


## <a name="model-management"></a>Modellhantering

**Vad är Azure Machine Learning-modellhantering?**

Azure Machine Learning-modellhantering är en hanterad Azure-tjänst som gör att forskare och devops-team distribuera prediktiva modeller på ett tillförlitligt sätt i en mängd olika miljöer. Git-lagringsplatser och Docker-behållare ger spårbarhet och repeterbarhet. Modeller kan distribueras på ett tillförlitligt sätt i molnet, lokalt eller edge. En gång i produktion, du kan hantera modellprestanda och proaktivt tränar om prestanda försämras. Du kan distribuera modeller på lokala datorer till [virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/), Spark i [HDInsight](https://azure.microsoft.com/services/hdinsight/) eller Kubernetes-dirigerad [Azure Container Service](https://azure.microsoft.com/services/container-service/) kluster.  

**Vad är en ”modell”?**

En modell är utdata från ett experiment som körs som har gjorts för modellhantering. En modell som är registrerad i kontot som är värd räknas av mot din plan, inklusive modeller som uppdateras via träna eller version iteration.

**Vad är en ”hanterad modell”?**

En modell är utdata från en träningsprocess och resultatet när en algoritm för maskininlärning körts på träningsdata. Modellhantering kan du distribuera modeller som webbtjänster, hantera olika versioner av dina modeller och övervaka prestanda och mått. ”Hanterade” modeller har registrerats med ett konto i Azure Machine Learning-modellhantering. Tänk dig till exempel en situation där du försöker skapa en prognos för framtida försäljning. Under experimenteringsfasen genererar du många modeller med hjälp av olika datauppsättningar och algoritmer. Du har genererat fyra modeller med varierande noggrannhet men väljer att bara registrera modellen med den bästa noggrannheten. Den modell som har registrerats blir din första hanterade modell.
 
**Vad är ”distribution”?**

Modellhantering kan du distribuera modeller som paketerade webbtjänstbehållare i Azure. Dessa webbtjänster kan anropas med hjälp av REST API: er. Varje webbtjänst räknas som en distribution och det totala antalet aktiva distributioner räknas av mot ditt abonnemang. Med hjälp av försäljningsprognoser till exempel när du distribuerar modellen med bäst resultat, ökas din plan med en distribution. Om du sedan tränar om och distribuera en annan version, har du två distributioner. Om du anser att den nya modellen är bättre och ta bort ursprungligt antalet minskas med ett.  

**Vilka specifika beräkningsresurser är tillgängliga för Mina distributioner?** 

Modellhantering kan köra dina distributioner som dockerbehållare registrerad [Azure Container Service](https://azure.microsoft.com/services/container-service/), som [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), eller på lokala datorer. Ytterligare distributionskanaler läggs snart. Observera att du faktureras separat för alla beräkningsresurser du förbrukar baserat på den individuella prissättningen.     

**Kan jag använda Azure Machine Learning-modellhantering till att distribuera modeller som byggts med andra verktyg än tjänsten experimentering?**

Du får bästa möjliga upplevelse när du distribuerar modeller som skapats med tjänsten experimentering. Du kan dock distribuera modeller som byggts med andra ramverk och verktyg. Vi stöder en mängd olika modeller, inklusive MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-Läs, Keras osv. 

**Kan jag använda min egen Azure-resurser?**

Ja, experimentering och modellhantering arbeta tillsammans med flera Azure datalager, compute-arbetsbelastningar och andra tjänster. I våra tekniska dokumentationen finns mer information om nödvändiga Azure-tjänster.

**Du stöd för både lokala och molnbaserade distributionsscenarier?**

Ja. Vi har stöd för lokala och molnbaserade distributionsscenarier via Docker-behållare. Lokal körningsmål omfattar: nod Docker-distributioner, [Microsoft SQL Server med ML-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop och Spark. Vi har också stöd för cloud-distributioner via Docker, inklusive: klustrade distributioner via Azure Container Service och Kubernetes, HDInsight eller Spark-kluster. Edge-scenarier stöds via Docker-behållare och Azure IOT Edge. 

**Kan jag köra en Docker-avbildning som har skapats med hjälp av Azure Machine Learning CLI på en annan värd?**

Ja. Du kan använda avbildningen som en webbtjänst på några docker-värd som värden har tillräckligt med beräkningsresurser som värd för docker-avbildningen.

**Du har stöd för omtrimning av distribuerade modeller?**

Ja, du kan distribuera flera versioner av samma modell. Modellhantering stöder tjänstuppdateringar för alla uppdaterade modeller och bilder.

## <a name="workbench"></a>Workbench

**Vad är Azure Machine Learning Workbench?**

Azure Machine Learning Workbench är ett tillhörande program som byggts för datatekniker. Är tillgängligt för Windows och Mac, Machine Learning Workbench tillhandahåller översikt, hantering och kontroll för machine learning-lösningar. Machine Learning Workbench ger åtkomst till banbrytande AI-ramverk från både Microsoft och communityn för öppen källkod. Vi har inkluderat mest populära data science verktyg, inklusive TensorFlow, Microsoft Cognitive Toolkit, Spark ML och scikit-Lär dig och mycket mer. Vi har också aktiverat integrering med populära datavetenskaps IDE: er, till exempel Jupyter notebooks, pycharm med Visual Studio Code. Machine Learning Workbench har inbyggda funktioner för förberedelse av snabbt exempel, förstå och förbereda data, strukturerade och Ostrukturerade. Vår nya förberedelseverktyget för data, kallas [PROSE](https://microsoft.github.io/prose/), bygger på den senaste tekniken från Microsoft Research.  

**Är Workbench en IDE-miljö?**

Nej. Machine Learning Workbench har utformats som avsett för populära IDE: er som Jupyter Notebooks, Visual Studio Code och PyCharm men det är inte en komplett IDE. Machine Learning Workbench erbjuder vissa grundläggande text redigeringsfunktioner, men felsökning, intellisense och andra ofta använda IDE-funktioner inte stöds. Vi rekommenderar att du använder din favorit-IDE för kodutveckling, redigering och felsökning. Du kanske också vill prova [Visual Studio Code Tools för AI](https://www.visualstudio.com/downloads/ai-tools-vscode).

**Finns det några avgifter för användning av Azure Machine Learning Workbench?**

Nej. Azure Machine Learning Workbench är ett kostnadsfritt program. Du kan ladda ned det till så många datorer och användare som behövs. Du måste ha ett konto för Experimentering när du ska använda Azure Machine Learning Workbench. .  

**Finns det stöd för funktioner?**

Ja, Azure Machine Learning erbjuder en fullständig CLI-gränssnittet. Machine Learning CLI installeras som standard med Azure Machine Learning Workbench. Det finns också som en del av den virtuella datorn för datavetenskap för Linux på Azure och kommer att integreras i den [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Kan jag använda Jupyter-anteckningsböcker med Workbench?**

Visst! Du kan köra Jupyter-anteckningsböcker i arbetsstationen Workbench som värd klientprogrammet, precis som du använder en webbläsare som en klient. 

**Vilka Jupyter Notebook-kärnor stöds?**

Den aktuella versionen av Jupyter som ingår i Workbench startar en Python 3-kernel och en ytterligare kernel för varje ”runconfig”-fil i mappen aml_config. Konfigurationer som stöds är:
- Lokal Python
- Python i lokala eller fjärranslutna Docker

## <a name="data-formats-and-capabilities"></a>Dataformat och funktioner

**Vilka filformat som stöds för närvarande för datainmatning i Workbench?**

Verktygen för förberedelse av data i Workbench stöder för närvarande inmatning från följande format: 
- Avgränsade filer CSV, TVS, t.ex.  
- Filer med fast bredd
- Filer med oformaterad text
- Excel (.xls/xlsx)
- JSON-filer
- Parquet-filer 
- Anpassade filer (skript) om din lösning kräver inmatade data från källor som ytterligare Python-kod kan användas för att... 

**Vilka data lagringsplatser stöds för närvarande?**

Offentliga förhandsversionen stöder Workbench datainmatning från: 
- Lokal hårddisk eller mappade nätverksenheter lagringsplats
- Azure BLOB eller Azure Storage (kräver en Azure-prenumeration)
- Azure SQL Server
- Microsoft SQL Server


**Vilka typer av Datatransformering, förberedelser och transformationer är tillgängliga?**

Offentliga förhandsversionen stöder Workbench ”Härled kolumn genom att exempel”, ”dela kolumn genom att exempel”, ”kluster Text”, ”hantera saknas Values” och mycket mer.  Workbench stöder också datatypskonvertering Datasammanställning (antal, medelvärde, varians osv.) och komplexa kopplingar. En fullständig lista över funktioner som stöds finns i produktdokumentationen för. 

**Finns det några begränsningar för meddelandestorlek som tillämpas av Azure Machine Learning Workbench, experimentering och modellhantering?**

Nej, är det inte möjligt att införa några begränsningar för data med de nya tjänsterna. Det finns emellertid begränsningar i miljön där du utför din förberedelse av data, modellträning, experimentering eller distribution. Till exempel om du arbetar med en lokal miljö för utbildning använder begränsas du av det tillgängliga utrymmet på din hårddisk. Du kan också om du arbetar med HDInsight använder du begränsas av alla associerade storlekar eller compute begränsningar. 

## <a name="algorithms-and-libraries"></a>Algoritmer och bibliotek

**Vilka algoritmer stöds i Azure Machine Learning Workbench?**

Våra förhandsversion produkter och tjänster är bäst från communityn för öppen källkod. Vi stöder en mängd olika algoritmer och bibliotek, inklusive TensorFlow, scikit-Läs, Apache Spark och Microsoft Cognitive Toolkit. Azure Machine Learning Workbench paket även den [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) paketet.

**Hur relaterar Azure Machine Learning till Microsoft Cognitive Toolkit?**

Den [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) är en av många ramverk som stöds av våra nya verktyg och tjänster. Cognitive Toolkit är ett enhetligt djupinlärningsverktyg som gör det möjligt att förbruka och kombinera populära maskininlärningsmodeller inklusive Feed-Forward djupa Neurala nätverk, Konvolutionella nät Sequence-Sequence och återkommande nätverk. Mer information om Microsoft Cognitive Toolkit, Besök vår [produktdokumentationen](https://docs.microsoft.com/cognitive-toolkit/). 