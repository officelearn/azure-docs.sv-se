---
title: "Azure Machine Learning 2017 Preview vanliga frågor och svar | Microsoft Docs"
description: "Den här artikeln innehåller vanliga frågor och svar för Azure Machine Learning förhandsgranskningsfunktioner"
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 94c6576f92b5a004e0fd509c1dc6e091c294658a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Machine Learning

## <a name="general-product-questions"></a>Generella frågor

**Vad är Azure Machine Learning?**

Azure Machine Learning är en helt hanterad Azure-tjänst som gör att du kan skapa, testa, hantera och distribuera machine learning och AI-modeller. Vår nya tjänster hämtningsbart program erbjuder en kod första metod som utnyttjar moln, lokalt och kant för att tillhandahålla tåget, distribuera, hantera och övervaka modeller med ström, hastighet och flexibilitet. Azure Machine Learning Studio tillhandahåller också en webbläsarbaserad, visuell dra och släpp redigeringsmiljön där ingen kodning krävs. 

**Hur kommer jag igång med förhandsgranskningen?**

Det enklaste sättet att komma igång är att registrera dig för tjänsten experiment i Azure. Du kan också hämta appen ledigt arbetsstationen eller registrera dig för ett konto för hantering av modellen från portalen. Dessutom kan du besöka vårt dokumentationswebbplats för artiklar, videor, demo-kod och mycket mer. 

**Jag har inte en Azure-prenumeration. Kan jag prova de nya tjänsterna?**

Som en del av Microsoft Azure-portfölj kräver vår nya tjänster en Azure-prenumeration. Du måste dessutom ha rätt behörigheter för att skapa tillgångar resursgrupper, virtuella datorer, t.ex. 

**Regioner som är nya tjänster är tillgängliga?**

Hitta Azure-regioner som stöds för undersökningar och modellen management-tjänsten under Azure Machine Learning på den [Azure produkter efter region](https://azure.microsoft.com/regions/services/) sidan.

När vi utvecklar produkten läggs flera områden.  Du kan hjälpa oss att prioritera vilka regioner för distribution till på vår [Azure Machine Learning Feedback](https://feedback.azure.com/forums/257792-machine-learning) plats. 

**Vilka andra Azure-tjänster krävs?**

Om du vill utnyttja alla funktioner i Azure Machine Learning, använder Azure datorn Learnign Visual Studio VSTS konto, Azure Blob Storage, Azure Container registret, datavetenskap VM eller HDInsight beräkning och Azure Container Service.

**Hur Azure Machine Learning är relaterade till Microsoft Machine Learning-tjänster i SQL Server 2017?**   

Machine Learning tjänster i SQL Server 2017 är en utökningsbar, skalbar plattform för att integrera machine learning aktiviteter i arbetsflöden i databasen. Det är ett perfekt val för scenarier där en lokal lösning är krävs, till exempel där dataflyttning är dyr eller ohållbara. Däremot är moln eller hybrid arbetsbelastningar ett bra val för vår nya Azure-tjänster. 

**Har du stöd för både Python och R? Nyheter om andra programmeringsspråk som C++**

Vi stöder för närvarande Python endast. Vi arbetar på R-integrering och tror att den blir tillgänglig snart. 

**Hur Azure Machine Learning relaterar till Microsoft Machine Learning Spark?**

S: MMLSpark ger djup learning och datavetenskap tools för Apache Spark med betoning på produktiviteten, enkel undersökningar och den senaste algoritmer. MMLSpark möjliggör integrering av Spark Machine Learning rörledningar med Microsoft kognitiva Toolkit och OpenCV. Du kan skapa kraftfulla skalbara förutsägbara och analysdata modeller för bilden och texten data. MMLSpark är tillgänglig under en licens för öppen källkod och ingår i AML arbetsstationen som en uppsättning konsumeras modeller och algoritmer. Mer information om MMLSpark finns i vår produktdokumentation. 

**Vilka versioner av Spark stöds av de nya verktyg och tjänster? Överst**

Arbetsstationen innehåller för närvarande och stöder MMLSpark version 0,8, som är kompatibel med Apache Spark 2.1. Du har också ett alternativ för att använda GPU-aktiverade Docker-bild av MMLSpark 0,8 på Linux virtuella datorer.

## <a name="experimentation-service"></a>Experiment Service

**Vad är Azure Machine Learning experiment tjänsten?**

Tjänsten experiment är en hanterad Azure-tjänst som tar machine learning-experiment till nästa nivå. Experiment kan byggas lokalt eller i molnet. Snabbt skala prototyp på skrivbordet, sedan till virtuella datorer eller Spark-kluster. Virtuella Azure-datorer med den senaste GPU kan du delta i djup learning snabbt och effektivt sätt. Vi har också inkluderat djupgående integrering med Git så att du enkelt kan ansluta till befintliga arbetsflöden för koden spårning, konfiguration och samarbete. 

**Hur kommer jag att debiteras för tjänsten experiment?**

Två första användarna som är associerade med din Azure Machine Learning experiment tjänst är kostnadsfri. Ytterligare användare kommer att debiteras införda Public Preview $50 per månad. Mer information om priser och fakturering för att besöka sidan med våra priser.

**Jag debiteras baserat på hur många experiment körs?**

Nej, kan tjänsten experiment så många experiment som du behöver och kostnader endast baserat på antalet användare. Beräkningsresurser för Experimentering debiteras separat. Vi rekommenderar att du kan utföra flera experiment så att du kan hitta den bästa passning modell för din lösning.   

**Vilka specifika typer av beräknings-och lagringsresurser kan jag använda?**

Tjänsten experiment kan köra dina experiment på lokala datorer (direkt eller Docker-baserade), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), och [HDInsight](https://azure.microsoft.com/services/hdinsight/). Tjänsten har också åtkomst till en [Azure Storage](https://azure.microsoft.com/services/storage/) konto för att lagra körning av utdata och kan dra nytta av en [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) konto för versionskontroll och Git-lagring. Observera att du debiteras separat för varje förbrukade beräknings- och lagringsresurser, baserat på deras enskilda prisnivå.  


## <a name="model-management"></a>Modellhantering

**Vad är Azure Machine Learning modellen Management?**

Azure Machine Learning modellen Management är en hanterad Azure-tjänst som kan forskare och dev ops grupper att distribuera förutsägelsemodeller på ett tillförlitligt sätt i en mängd olika miljöer. Ange spårbarhet och repeterbara Git-databaser och Docker-behållare. Modeller kan distribueras på ett tillförlitligt sätt i molnet, lokalt eller kant. En gång i produktion, du kan hantera modellen prestanda och proaktivt träna om om prestanda försämras. Du kan distribuera modeller på lokala datorer till [virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/), Väck på [HDInsight](https://azure.microsoft.com/services/hdinsight/) eller Kubernetes för samordnade [Azure Container Service](https://azure.microsoft.com/services/container-service/) kluster.  

**Vad är en ”modell”?**

En modell är resultatet av ett experiment som körs som har uppgraderats för modellhantering av. En modell som har registrerats i kontot värd räknas mot din plan, inklusive modeller uppdateras genom omtränings eller version iteration.

**Vad är en ”hanterad modell”?**

En modell är utdata från en träningsprocess och resultatet när en algoritm för maskininlärning körts på träningsdata. Modellen Management kan du distribuera modeller som webbtjänster, hantera olika versioner av modeller och övervaka deras prestanda och mått. ”Hanterad” modeller har registrerats med ett konto i Azure Machine Learning modellen Management. Tänk dig till exempel en situation där du försöker skapa en prognos för framtida försäljning. Under fasen experiment generera många modeller med hjälp av olika datauppsättningar eller algoritmer. Du har genererat fyra modeller med olika noggrannhet men väljer att registrera endast modellen med den högsta noggrannheten. Modellen som har registrerats blir din första hanterade modell.
 
**Vad är en ”distribution”?**

Modellen Management kan du distribuera modeller som paketerade web-behållaren i Azure. Dessa webbtjänster kan anropas med hjälp av REST API: er. Varje webbtjänst räknas som en enda distribution och det totala antalet aktiva distributioner räknas mot din plan. Med försäljningsprognoser exempelvis när du distribuerar din bäst prestanda modellen ökas planen med en distribution. Om du träna om och distribuera en annan version, har du två distributioner. Om du anser att nyare modellen är bättre och ta bort ursprungligt distributionen beräkningen dras av någon.  

**Vilka specifika beräkningsresurser är tillgängliga för min distributioner?** 

Modellen Management kan köra distributionen som behållare i Docker registrerad [Azure Container Service](https://azure.microsoft.com/services/container-service/), som [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), eller på lokala datorer. Ytterligare mål läggs inom kort. Observera att du debiteras separat för varje förbrukade beräkningsresurser baserat på deras enskilda prisnivå.     

**Kan jag använda Azure Machine Learning modellen Management för att distribuera modeller som skapats med andra verktyg än experiment tjänsten?**

Du får bästa möjliga upplevelse när du distribuerar modeller som skapats med hjälp av tjänsten experiment. Du kan dock distribuera modeller som skapats med hjälp av andra ramverk och verktyg. Vi stöder en mängd olika modeller inklusive MMLSpark TensorFlow, Microsoft kognitiva Toolkit, scikit-Läs Keras osv. 

**Kan jag använda min egen Azure-resurser?**

Ja, experiment Service och hantering av modellen arbeta tillsammans med flera Azure datalager, beräkna arbetsbelastningar och andra tjänster. Referera till vår tekniska dokumentationen för mer information om nödvändiga Azure-tjänster.

**Du stöd för både lokala och molnet distributionsscenarier?**

Ja. Vi har stöd för lokala och molnet distributionsscenarier via Docker-behållare. Lokal körning mål omfattar: nod Docker-distributioner, [Microsoft SQL Server med ML Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop eller Spark. Vi stöder också molnet distributioner via Docker, inklusive: klustrade distributioner via Azure Container Service och Kubernetes, HDInsight eller Spark-kluster. Edge scenarier stöds via Docker-behållare och Azure IOT kant. 

**Kan jag köra en Docker-avbildning som har skapats med hjälp av Azure Machine Learning CLI på en annan värd?**

Ja. Du kan använda bilden som en webbtjänst på alla docker-värdar som värden har tillräcklig beräkningsresurser som värd för docker-bild.

**Du har stöd för omtränings av distribuerade modeller?**

Ja, du kan distribuera flera versioner av samma modell. Uppdateringar av tjänsten stöder modellhantering av för alla uppdaterade modeller och bilder.

## <a name="workbench"></a>Workbench

**Vad är Azure Machine Learning arbetsstationen?**

Azure Machine Learning-arbetsstationen är ett tillhörande program som skapats för professionella datavetare. Tillgänglig för Windows- och Mac, Machine Learning-arbetsstationen innehåller översikt, hantering och kontroll för machine learning-lösningar. Machine Learning-arbetsstationen inbegriper tillgång till banbrytande AI ramverk från både Microsoft och community för öppen källkod. Vi har lagt till de mest populära datavetenskap verktyg, inklusive TensorFlow, Microsoft kognitiva Toolkit, Spark ML, scikit-Läs och mycket mer. Vi har också aktiverat integrering med populära datavetenskap IDEs som Jupyter-anteckningsböcker, PyCharm och Visual Studio-koden. Machine Learning-arbetsstationen har inbyggda förberedelse möjligheter till exempel, förstå och förbereda data, om strukturerade eller Ostrukturerade snabbt. Vår nya förberedelseverktyget för data, kallas [PROSE](https://microsoft.github.io/prose/), bygger på den senaste tekniken från Microsoft Research.  

**Är arbetsstationen IDE-miljö?**

Nej. Machine Learning-arbetsstationen har utformats som en tillhörande till populära IDEs, till exempel Jupyter-anteckningsböcker och Visual Studio Code PyCharm men det är inte en fullt fungerande IDE. Machine Learning-arbetsstationen erbjuder vissa grundläggande text redigera funktioner, men felsökning, intellisense och andra ofta använda IDE-funktioner inte stöds. Vi rekommenderar att du använder din favorit IDE för koden utveckling, redigera och felsökning. 

**Finns det en avgift för att använda Azure Machine Learning arbetsstationen?**

Nej. Azure Machine Learning arbetsstationen är ett kostnadsfritt program. Du kan ladda ned det till så många datorer och användare som behövs. Du måste ha ett konto för Experimentering när du ska använda Azure Machine Learning Workbench. .  

**Stöder kommandoradsverktyget funktioner?**

S: Ja, Azure Machine Learning erbjuder en fullständig CLI-gränssnitt. Machine Learning CLI installeras som standard med Azure Machine Learning-arbetsstationen. Det finns även som en del av den virtuella datorn i Linux datavetenskap på Azure och integreras i [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)


**Kan jag använda Jupyter-anteckningsböcker med arbetsstationen?**

Visst! Du kan köra Jupyter notebooks i arbetsstationen med arbetsstationen som värd klientprogrammet, precis som du vill använda en webbläsare som en klient. 

**Vilka Jupyter-anteckningsbok kärnor stöds?**

S: den aktuella versionen av Jupyter som ingår i arbetsstationen startar en Python 3 kernel och en ytterligare kernel för varje ”runconfig”-fil i mappen aml_config. Konfigurationer som stöds är:
- Lokala Python
- Python i lokala eller fjärranslutna Docker

## <a name="data-formats-and-capabilities"></a>Dataformat och funktioner

**Vilka filformat som stöds för närvarande för datapåfyllning i arbetsstationen?**

Förberedelse av Dataverktyg i arbetsstationen för närvarande stöder införandet från följande format: 
- Avgränsade filer CSV, TVS, t.ex.  
- Filer med fast bredd
- Filer med oformaterad text
- Excel (xls/xlsx)
- JSON-filer
- Parkettgolv filer 
- Anpassade filer (skript) om din lösning kräver datapåfyllning från källor som ytterligare Python-kod kan användas för att... 

**Vilka data lagringsplatser stöds för närvarande?**

För public preview stöder arbetsstationen datapåfyllning från: 
- Hårddisken eller mappade nätverksenheter lagringsplats
- Azure BLOB- eller Azure Storage (kräver en Azure-prenumeration)
- Azure SQL Server
- Microsoft SQL Server


**Vilka typer av data wrangling förberedelse och omvandlingar är tillgängliga?**

För public preview stöder arbetsstationen ”härledd kolumn med hjälp av exempel”, ”delade kolumn med hjälp av exempel”, ”Text-kluster,” hantera värden saknas ”och många andra.  Arbetsstationen stöder också datatypkonvertering Datasammanställning (antal, medelvärde, varians, etc.) och komplexa data kopplingar. En fullständig lista över funktioner som stöds finns i vår produktdokumentation. 

**Finns det några storleksbegränsningar tvingas av Azure Machine Learning arbetsstationen, undersökningar eller hantering av modellen?**

S: inte kräver nya tjänster inte några begränsningar. Det finns emellertid begränsningar i miljön där du utför din förberedelse av data, modell utbildning, undersökningar eller distribution. Till exempel om du använder en lokal miljö för träning begränsas du av det tillgängliga utrymmet på hårddisken. Du kan också om du använder HDInsight du begränsas av alla associerade storlek eller compute begränsningar. 

## <a name="algorithms-and-libraries"></a>Algoritmer och bibliotek

**Vilka algoritmer stöds i Azure Machine Learning arbetsstationen?**

Vår förhandsgranskning produkter och tjänster är bäst av community för öppen källkod. Vi stöder en mängd olika algoritmer och bibliotek inklusive TensorFlow scikit – Lär dig Apache Spark och Microsoft kognitiva Toolkit. Azure Machine Learning arbetsstationen paket även den [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) paketet.

**Hur Azure Machine Learning är relaterade till Microsoft kognitiva Toolkit?**

Den [Microsoft kognitiva Toolkit](https://www.microsoft.com/cognitive-toolkit/) är en av många ramverk som stöds av våra nya verktyg och tjänster. Kognitiva Toolkit är en enhetlig djup learning Verktygslåda som gör att du kan använda och kombinera populära maskininlärning modeller inklusive Feed vanlig djupa Neurala nätverk, Convolutional nät Sequence-Sequence och återkommande nätverk. Mer information om Microsoft kognitiva Toolkit Besök vår [produktdokumentationen](https://docs.microsoft.com/cognitive-toolkit/). 

## <a name="pricing-and-billing"></a>Priser och fakturering

**Är du debitering för Azure Machine Learning under förhandsgranskningen?** 

S: i Azure Machine Learning-arbetsstationen programmet är tillgängligt utan kostnad till Azure-prenumeranter. Erbjuder kostnadsfria nivåer, förutom betalnivåer avialable med rabatt under Public Preview experiment tjänsten och hantering av modellen.

**Jag debiteras baserat på hur många experiment körs?**

Nej, i Experimentering kan du ha hur många experiment du vill, och du debiteras endast utifrån antalet användare. Beräkningsresurser för Experimentering debiteras separat.  Vi rekommenderar att du kan utföra flera försök att komma fram till den bästa modellen för din lösning. 

**Kommer det att debiteras baserat på hur många gånger min webbtjänster kallas?**

Nej. Webbtjänster kan anropas så ofta som krävs, utan någon modell Management fakturering effekter. Du avgör själv hur du vill skala dina distributioner sett till behoven i dina tillämpningar.

**Hur kan jag för att skala antalet enheter som jag har köpt i Azure Machine Learning modellen Management?**

Du kan ändra antalet enheter, uppåt eller nedåt med hjälp av Azure-portalen eller CLI. 

**Vad kan min faktura ut?**

Växlar produceras dagligen. I faktureringssyfte börjar en dag vid midnatt UTC-tid. Fakturor genereras per månad. Separata avgifter tas ut för Azure-tjänster som används tillsammans med Azure Machine Learning. Avgifter kan inkludera, men är inte begränsade till: 
- Beräkna avgifter
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Azure Event Hub
- Azure Stream Analytics för ytterligare information eller om du vill visa ett prov faktura besöker du sidan med våra priser. 

## <a name="support-and-training"></a>Support och utbildning

**Var kan jag lära mig mer om Azure Machine Learning?**

På [Azure Machine Learning Documentation Center](./overview-what-is-azure-ml.md) hittar du videokurser och instruktionsguider. Dessa stegvisa guider införa tjänsterna och förklarar datalivscykeln. Vi lägger till nytt material på Machine Learning Center med jämna mellanrum. Du kan skicka dina önskemål om ytterligare utbildningsmaterial på användaren Feedbackforum.

**Hur får jag support för Azure Machine Learning?**

För teknisk support, gå till stöd för Azure och välj Machine Learning. Azure Machine Learning har även en aktiv community [StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) märkta med ”Azure-Machine-Learning”, övervakas av teamet.  Förslag och funktionsförfrågningar kan skickas på den [Azure Machine Learning Feedback](https://feedback.azure.com/forums/257792-machine-learning) plats. 
