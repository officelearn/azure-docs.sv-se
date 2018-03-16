---
title: "Översikt över Azure Machine Learning modellen Management | Microsoft Docs"
description: "Det här dokumentet beskriver begrepp för hantering av modellen för Azure Machine Learning."
services: machine-learning
author: nk773
ms.author: padou
manager: mwinkle
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 89f95753248f74c7f6cb9ca1f680a01b07dd43d1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-machine-learning-model-management"></a>Azure Machine Learning-modellhantering

Azure Machine Learning modellen Management kan du hantera och distribuera machine learning arbetsflöden och modeller. 

Modellhantering av innehåller funktioner för:
- Modellen versionshantering
- Spårning av modeller i produktion
- Distribuera modeller för produktion via AzureML Compute-miljö med [Azure Container Service](https://azure.microsoft.com/services/container-service/) och [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Skapa Docker-behållare med modeller och testa dem lokalt
- Automatisk modellen via programmering
- Fånga modellen telemetri för tillämplig insikter. 

Azure Machine Learning modellen Management ger ett register över modellversioner. Det ger också automatiska arbetsflöden för förpackning och distribution av Machine Learning-behållare som REST API: er. Modeller och deras runtime-beroenden är paketerade i Linux-baserade dockerbehållare med förutsägelse API. 

Azure Machine Learning Compute miljöer bidra till att konfigurera och hantera skalbara kluster som värd för modellerna. Compute-miljön baseras på Azure-tjänster för behållaren. Azure Behållartjänster ger automatisk exponering av Machine Learning API: er som REST API-slutpunkter med följande funktioner:

- Autentisering
- Belastningsutjämning
- Automatisk skalbar
- Kryptering

Azure Machine Learning modellen Management ger dessa funktioner via CLI, API och Azure-portalen. 

Azure Machine Learning modellen management använder du följande information:

 - Modellfil eller katalog med modellfiler
 - Implementera en modell bedömningen funktionen Python-fil som skapats av användare
 - Conda beroende fil som innehåller runtime-beroenden
 - Runtime environment val och 
 - Schemafilen för API-parametrar 

Den här informationen används när du utför följande åtgärder:

- Registrera en modell
- Skapa ett manifest som används när du skapar en behållare
- Skapa en Docker behållare avbildning
- Distribuera en behållare till Azure Container Service
 
Följande bild visar en översikt över hur modeller är registrerat och distribuerat till klustret. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Skapa och hantera modeller 
Du kan registrera modeller med Azure Machine Learning modellen Management för att spåra modellversioner i produktion. För att underlätta reproducerbara och styrning hämtar tjänsten alla beroenden och tillhörande information. Du kan avbilda modellen telemetri med hjälp av den angivna SDK för djupare insikter om prestanda. Modellen telemetri arkiveras i användardefinierade lagring. Modellen telemetri kan användas senare för att analysera modellen prestanda, omtränings och få insikter för ditt företag.

## <a name="create-and-manage-manifests"></a>Skapa och hantera manifest 
Modeller kräver ytterligare artefakter att distribuera till produktionen. Systemet ger möjlighet att skapa ett manifest som omfattar modellen, beroenden, härledning skript (aka bedömningsprofil skript), exempeldata, schemat osv. Manifestet fungerar som ett recept att skapa en avbildning för Docker-behållare. Företag kan automatiskt skapa manifestet, skapa olika versioner och hantera sina manifest. 

## <a name="create-and-manage-docker-container-images"></a>Skapa och hantera avbildningar för Docker-behållare 
Du kan använda manifestet från föregående steg för att skapa Docker-baserade behållaren bilder i sina respektive miljöer. Bilder av, Docker-baserade ger företag möjlighet att köra dessa avbildningar på följande beräknings-miljöer:

- [Kubernetes baserad Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Lokala behållartjänster
- Utvecklingsmiljöer
- IoT-enheter

Dessa behållare Docker-avbildningar är självständigt med alla nödvändiga beroenden som krävs för att generera förutsägelser. 

## <a name="deploy-docker-container-images"></a>Distribuera avbildningar för Docker-behållare 
Du kan distribuera Docker-baserade behållaren bilder med ett enda kommando till Azure Container Service hanteras av ML Compute-miljö med Azure Machine Learning modellen Management. Dessa distributioner har skapats med en frontend-server som tillhandahåller följande funktioner:

- Låg latens förutsägelser i skala
- Belastningsutjämning
- Automatisk skalning av ML-slutpunkter
- Auktorisering av innehållsnyckel API
- API swagger-dokument

Du kan styra distributionen skala och telemetri via följande inställningar:

- Systemloggning och modellen telemetri för varje servicenivå för webbprogram. Om aktiverad, alla stdout loggar strömmas till [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Modellen telemetri arkiveras i lagring som du anger. 
- Autoskala och samtidighet gränser. Dessa inställningar öka automatiskt antalet distribuerade behållare utifrån belastningen i det befintliga klustret. De också styra genomflöde och konsekvenskontroll av svarstid för förutsägelse.

## <a name="consumption"></a>Förbrukning 
Azure Machine Learning modellen Management skapar REST API för distribuerade modellen tillsammans med swagger-dokument. Du kan använda distribuerade modeller genom att anropa REST-API: er med API nyckeln och modellerar indata för att hämta förutsägelser som en del av line-of-business-program. Exempelkod finns i GitHub för språk Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py), och C# för att anropa REST API: er. Azure Machine Learning modellen Management CLI tillhandahåller ett enkelt sätt att arbeta med dessa REST-API: er. Du kan använda API: er med hjälp av ett enda CLI-kommando, en swagger-aktiverade program eller curl. 

## <a name="retraining"></a>Omtränings 
Azure Machine Learning modellen Management innehåller API: er som du kan använda för att träna om modeller. Du kan också använda API: erna för att uppdatera befintliga distributioner med uppdaterade versioner av modellen. Som en del av arbetsflödet för datavetenskap kan återskapa du modellen i datormiljön experiment. Sedan kan du registrera modellen med hantering av modellen och uppdatera befintliga distributioner. Uppdateringar utförs med ett enda uppdatering CLI-kommando. Kommandot UPDATE uppdaterar befintliga distributioner utan att ändra API-URL eller nyckeln. Program som konsumerar modellen fortsätta att arbeta utan ändringar koden och börja få bättre förutsägelser med hjälp av en ny modell.

Fullständig arbetsflödet som beskriver de här koncepten avbildas i följande bild:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ) 
- **Vilka datatyper stöds? Kan jag skicka NumPy matriser direkt som indata till webbtjänsten?**

   Om du tillhandahåller schemafilen som skapades med generate_schema SDK sedan överföra du NumPy och/eller Pandas DF. Du kan också skicka alla serialiserbara JSON-indata. Du kan överföra avbildningen som samt binär kodad sträng.

- **Stöder webbtjänsten flera indata eller analysera olika indata?**

   Ja, du kan ta flera inmatningar paketeras i en JSON-begäran som en ordlista. Varje indata skulle motsvarar en enda unika ordlistans nyckel.

- **Anropet aktiveras av en begäran till webbservern som tjänsten en blockerande samtal eller ett asynkront anrop**

   Om tjänsten har skapats med alternativet i realtid som en del av CLI eller API, är det ett blockerar/synkrona anrop. Det förväntas vara snabb standarddatakällan i realtid. Även om blockering klienttråden på klientsidan kan du anropa den med async HTTP-biblioteket för att undvika.

- **Hur många förfrågningar kan webbtjänsten hantera samtidigt?**

   Det beror på klustret och web service skalan. Du kan skala ut tjänsten till 100 x av repliker och sedan den kan hantera många begäranden samtidigt. Du kan också konfigurera maximalt antal samtidiga begäranden per repliken att öka genomflödet för tjänsten.

- **Hur många förfrågningar kan webbtjänsten köa?**

   Den kan konfigureras. Som standard anges till 10 ~ per enskild replik, men du kan öka/minska det till kraven för application. Vanligtvis ökar den antalet begäranden i kö ökar genomströmningen för tjänsten men gör svarstiderna worse på högre percentiler. Om du vill behålla latens konsekvent, kan du ange queuing till ett lågt värde (1-5) och öka antalet repliker för att hantera genomflödet. Du kan också aktivera autoskalning kan göra antal repliker Justera automatiskt utifrån belastningen. 

- **Kan användas på samma dator eller kluster för flera slutpunkter för webbtjänster?**

   Absolut. Du kan köra 100 x över services-slutpunkter på samma kluster. 

## <a name="next-steps"></a>Nästa steg
Komma igång med hantering av modellen finns [konfigurera hantering av modellen](deployment-setup-configuration.md).
