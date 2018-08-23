---
title: Översikt över Azure Machine Learning-modellhantering | Microsoft Docs
description: Det här dokumentet beskriver modellhantering begrepp för Azure Machine Learning.
services: machine-learning
author: hjerezmsft
ms.author: hjerez
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 041f7147f171514d941555ff2f6144bac2062b06
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061709"
---
# <a name="azure-machine-learning-model-management"></a>Azure Machine Learning-modellhantering

Azure Machine Learning-modellhantering kan du hantera och distribuera machine learning arbetsflöden och modeller. 

Modellhantering innehåller funktioner för:
- Modell-versionshantering
- Spåra modeller i produktion
- Distribuera modeller till produktion genom AzureML Compute-miljö med [Azure Container Service](https://azure.microsoft.com/services/container-service/) och [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Skapa Docker-behållare med modeller och testa dem lokalt
- Automatiserad timmodellen
- Samla in modellen telemetri för användbara insikter. 

Azure Machine Learning-modellhantering ger ett register över modellversioner. Det ger också automatiserade arbetsflöden för förpackning och distribution av Machine Learning-behållare som REST-API: er. Modellerna och deras körningsberoenden paketeras i Linux-baserade Docker-behållare med förutsägande API. 

Azure Machine Learning Compute miljöer hjälpa dig att konfigurera och hantera skalbara kluster som värd för modellerna. Compute-miljö är baserad på Azure Container Services. Azure Container Services innehåller automatisk exponering av API: erna för Machine Learning som REST API-slutpunkter med följande funktioner:

- Autentisering
- Belastningsutjämning
- Automatisk skala ut
- Kryptering

Azure Machine Learning-modellhantering erbjuder dessa funktioner via CLI, API och Azure-portalen. 

Azure Machine Learning-modellhantering använder du följande information:

 - Modellfil eller katalog med modellfiler
 - Skapa Python-fil som implementerar en modell för bedömning av funktionen för användaren
 - Conda beroende fil som innehåller körningsberoenden
 - Runtime-miljö val och 
 - Schemafilen för API-parametrar 

Den här informationen används när du utför följande åtgärder:

- Registrera en modell
- Skapa ett manifest som används när du skapar en behållare
- Att skapa en Docker-behållaravbildning
- Distribuera en behållare till Azure Container Service
 
Följande bild visar en översikt över hur modeller är registrerat och distribuerat till klustret. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Skapa och hantera modeller 
Du kan registrera modeller med Azure Machine Learning-modellhantering för att spåra modellversioner i produktion. För att underlätta reproducerbarhet och styrning fångar tjänsten alla beroenden och tillhörande information. Du kan avbilda modellen telemetri med hjälp av angivna SDK för djupare insikter om prestanda. Modellen telemetri arkiveras i användardefinierade storage. Telemetri för modell kan användas senare för att analysera modellprestanda, träna och få insikter för ditt företag.

## <a name="create-and-manage-manifests"></a>Skapa och hantera manifest 
Modeller kräver ytterligare artefakter att distribuera till produktionen. Systemet kan du skapa ett manifest som omfattar modellen, beroenden, inferens skript (även kallat bedömningsskriptet), exempeldata, schemat osv. Den här manifestet fungerar som ett recept att skapa en Docker-behållaravbildning. Företag kan Autogenerera manifestet, skapa olika versioner och hantera sina manifest. 

## <a name="create-and-manage-docker-container-images"></a>Skapa och hantera Docker-behållaravbildningar 
Du kan använda manifestet från föregående steg för att skapa Docker-baserade avbildningar i sina respektive miljöer. Behållare, Docker-baserade avbildningar och ger företag möjlighet att köra dessa avbildningar i följande beräkningsmiljöer:

- [Kubernetes baserade Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Lokala behållartjänster
- Utvecklingsmiljö
- IoT-enheter

Dessa behållare Docker-avbildningar är självständigt med alla nödvändiga beroenden som krävs för att generera förutsägelser. 

## <a name="deploy-docker-container-images"></a>Distribuera Docker-behållaravbildningar 
Du kan distribuera Docker-baserade behållaravbildningar med ett enda kommando till Azure Container Service som hanteras av ML Compute-miljö med Azure Machine Learning-modellhantering. Dessa distributioner skapas med en frontend-server som tillhandahåller följande funktioner:

- Låg latens förutsägelser i stor skala
- Belastningsutjämning
- Automatisk skalning av ML-slutpunkter
- API: et auktoriseringsprincipen
- API swagger-dokument

Du kan styra distribution skala och telemetri via följande konfigurationsinställningar:

- Systemloggning och modellen telemetri för varje webbtjänstnivå. Om aktiverad kan alla stdout loggar strömmas till [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Modellen telemetri arkiveras i lagring som du anger. 
- Begränsningar för automatisk skalning och samtidighet. De här inställningarna kan du automatiskt öka antalet distribuerade behållare baserat på belastning i det befintliga klustret. De också styra dataflöde och konsekvens i förutsägelse svarstiden.

## <a name="consumption"></a>Förbrukning 
Azure Machine Learning-modellhantering skapar REST API för distribuerad modell tillsammans med swagger-dokument. Du kan använda distribuerade modeller genom att anropa REST-API: er med API-nyckeln och modellera indata för att hämta förutsägelserna som en del av line-of-business-program. Exempelkoden finns i GitHub för språk som Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py), och C# för att anropa REST API: er. Azure Machine Learning Model Management CLI tillhandahåller ett enkelt sätt att arbeta med de här REST-API: er. Du kan använda API: er med hjälp av ett enda kommando i CLI, en swagger-aktiverade program eller curl. 

## <a name="retraining"></a>Träna 
Azure Machine Learning-modellhantering innehåller API: er som du kan använda för att träna modeller. Du kan också använda API: erna för att uppdatera befintliga distributioner med uppdaterade versioner av modellen. Som en del av arbetsflöde för datavetenskap kan återskapa du modellen i miljön experimentering. Sedan kan du registrera modellen med modellhantering och befintliga distributioner av uppdateringar. Uppdateringarna utförs med hjälp av ett enda uppdatera CLI-kommando. Kommandot UPDATE uppdaterar befintliga distributioner utan att ändra API-URL eller nyckeln. De program som använder modellen fortsätta att fungera utan några ändringar i koden och få bättre förutsägelser med nya modellen.

Fullständig arbetsflödet som beskriver de här koncepten avbildas i följande bild:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ) 
- **Vilka datatyper stöds? Kan jag skicka NumPy matriser direkt som indata till webbtjänsten?**

   Om du tillhandahåller schemafilen som skapades med generate_schema SDK, sedan överföra du NumPy och/eller Pandas DF. Du kan även skicka någon typ av serialiserbara JSON-indata. Du kan överföra avbildningen som samt binära kodad sträng.

- **Om webbtjänsten stöder flera inmatningar eller parsa olika indata?**

   Ja, du kan ta flera inmatningar paketeras i en JSON-begäran som en ordlista. Varje indata skulle motsvarar en enda unika ordlista nyckel.

- **Är anropet aktiveras av en begäran till webb-tjänsten ett blockerande anrop eller ett asynkront anrop?**

   Om tjänsten har skapats med hjälp av alternativet i realtid som en del av CLI eller API: et, är det ett blockerande/synkron anrop. Det förväntas vara i realtid snabbt. Även om blockering klienttråden på klientsidan kan du anropa med asynkrona HTTP-biblioteket för att undvika.

- **Hur många begäranden kan webbtjänsten samtidigt ska hantera?**

   Det beror på klustret och web service skalan. Du kan skala ut din tjänst till 100 gånger av repliker och sedan den kan hantera många begäranden samtidigt. Du kan också konfigurera den maximala samtidig begäran per replik att öka dataflödet för tjänsten.

- **Hur många begäranden kan webbtjänsten kö?**

   Den kan konfigureras. Som standard anges till ~ 10 per enskild replik, men du kan öka/minska det till dina programkrav. Vanligtvis ökar det antalet begäranden i kö ökar dataflödet service men gör svarstider sämre på högre percentiler. Om du vill bevara svarstiderna, kan du vill ange att köa till ett lågt värde (1-5) och öka antalet repliker för att hantera dataflödet. Du kan också aktivera automatisk skalning så att antalet repliker Justera automatiskt baserat på belastning. 

- **Kan användas på samma dator eller kluster för flera slutpunkter för webbtjänster?**

   Absolut. Du kan köra 100 gånger tjänster/slutpunkter i samma kluster. 

## <a name="next-steps"></a>Nästa steg
Komma igång med modellhantering, finns i [konfigurera modellhantering](deployment-setup-configuration.md).
