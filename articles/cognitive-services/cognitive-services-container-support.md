---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur Docker-behållare kan få närmare kognitiva tjänster till dina data.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 241bda5c684197a43cc5564e950e924fed668b89
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147572"
---
# <a name="container-support-in-azure-cognitive-services"></a>Stöd för behållare i Azure Cognitive Services

Stöd för behållare i Azure Cognitive Services kan utvecklare använda samma omfattande API: er som är tillgängliga i Azure och möjliggör flexibilitet att distribuera och tillhandahålla tjänster som medföljer [Docker-behållare](https://www.docker.com/what-container). Stöd för behållare finns för närvarande i förhandsversion för en delmängd av Azure Cognitive Services, inklusive delar av:

* [Avvikelseidentifiering detektor](Anomaly-Detector/overview.md)
* [Visuellt innehåll](Computer-vision/Home.md)
* [Ansikte](Face/Overview.md)
* [Form Recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Språkförståelse](LUIS/luis-container-howto.md) (LUIS)
* [Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
* [Speech Service API](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Textanalys](text-analytics/overview.md)

Skapa behållare är en metod för distribution av programvara där ett program eller tjänst, inklusive dess beroenden och konfiguration, är packade tillsammans som en behållaravbildning. Med lite eller ingen ändring av, kan du distribuera en behållaravbildning på en behållarvärd. Behållare är isolerade från varandra och det underliggande operativsystemet, med mindre avtryck än en virtuell dator. Behållare kan instansieras behållaravbildningar för kortsiktig uppgifter och tas bort när den inte längre behövs.

Följande videoklipp visar hur du använder en Cognitive Services-behållare.

[![Demonstration av behållare för Cognitive Services](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

Cognitive Services-resurser är tillgängliga på [Microsoft Azure](https://azure.microsoft.com). Logga in på den [Azure-portalen](https://portal.azure.com/) att skapa och utforska Azure-resurser för dessa tjänster.

## <a name="features-and-benefits"></a>Funktioner och erbjudanden

- **Kontroll över data**: Att kunderna kan välja var de här Cognitive Services bearbetar data. Detta är nödvändigt för kunder som det går inte att skicka data till molnet men som behöver åtkomst till Cognitive Services-teknik. Stöd konsekvens i hybridmiljöer – över data, hantering, identitet och säkerhet.
- **Kontroll över modelluppdateringar**: Ger kunderna flexibilitet i versionshantering och uppdatering av modeller som distribuerats i sina lösningar.
- **Arkitektur för bärbara**: Aktivera skapandet av en bärbar programarkitektur som kan distribueras på Azure, lokalt och gränsen. Behållare kan distribueras direkt till [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), eller till en [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](/azure-stack/operator). Mer information finns i [distribuera Kubernetes i Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Högt dataflöde / låg latens**: Ger kunder möjlighet att skala dataflöden och låg latens krav genom att aktivera Cognitive Services körs fysiskt nära sina programmets logik och data. Behållare cap inte transaktioner per sekund (TPS) och kan göras för att skala både uppåt och utåt för att hantera begäran om du anger nödvändiga maskinvaruresurser. 


## <a name="containers-in-azure-cognitive-services"></a>Behållare i Azure Cognitive Services

Azure Cognitive Services-behållare ger följande uppsättning Docker-behållare som innehåller en delmängd av funktioner från tjänster i Azure Cognitive Services:

| Tjänst | Prisnivå som stöds | Container | Beskrivning |
|---------|----------|----------|-------------|
|[Avvikelseidentifiering detektor](https://go.microsoft.com/fwlink/?linkid=2083925&clcid=0x409) |F0, S0|**Avvikelseidentifiering detektor** |Avvikelseidentifiering detektor API kan du övervaka och identifiera avvikelser i tidsseriedata med machine learning.<br>[Begär åtkomst](https://aka.ms/adcontainer)|
|[Visuellt innehåll](Computer-vision/computer-vision-how-to-install-containers.md) |F0, S1|**Identifiera Text** |Extraherar ut text från bilder för olika objekt med olika ytor och bakgrunder, till exempel kvitton och affischer visitkort.<br/><br/>**Viktigt!** Behållaren identifiera Text fungerar för närvarande bara på engelska.<br>[Begär åtkomst](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Ansikte](Face/face-how-to-install-containers.md) |F0, S0|**Ansikte** |Identifierar ansikten i bilder och identifierar attribut, inklusive ansiktslandmärken (till exempel och rörliga och ögon), kön, ålder och andra dator-förväntad ansiktsdrag. Förutom identifiering Kontrollera ansikte om två ansikten i samma bild eller olika bilder är samma med hjälp av ett förtroenderesultat eller jämföra ansikten mot en databas för att se om en likartade eller identiska ansikte finns redan. Det kan även sortera liknande ansikten i grupper, med hjälp av delade visual egenskaper.<br>[Begär åtkomst](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Formuläret Igenkännande](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) |F0, S0|**Form Recognizer** |Förstå formulär gäller maskininlärningsteknik för att identifiera och extrahera nyckel / värde-par och tabeller från formulär.<br>[Begär åtkomst](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**LUIS** ([bild](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Läser in en tränad eller publicerade Language Understanding modell, även kallat en LUIS-app, i en docker-behållare och ger tillgång till fråga förutsägelser från behållarens API-slutpunkter. Du kan samla in fråga loggar från behållaren och ladda upp dessa tillbaka till den [LUIS portal](https://www.luis.ai) förbättra appens prognosens noggrannhet.|
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([bild](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Personanpassning i Azure är en molnbaserad API-tjänst som gör det möjligt att välja det bästa alternativet att visa användarna och drar lärdom av beteende i realtid.|
|[Speech Service API](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Tal till text** |Transkriberar kontinuerlig realtidsöversättning av tal till text.<br>[Begär åtkomst](https://aka.ms/speechcontainerspreview/)|
|[Speech Service API](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Text till tal** |Konverterar text till naturligt tal.<br>[Begär åtkomst](https://aka.ms/speechcontainerspreview/)|
|[Textanalys](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0, S|**Nyckeln diskussionsämne** ([bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extraherar viktiga fraser för att identifiera de viktigaste aspekterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. |
|[Textanalys](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Språkidentifiering** ([bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Identifierar vilka språk som indatatexten är skriven i och rapportera en enda språkkod för varje dokument som skickats på begäran för upp till 120 språk. Språkkoden paras med poäng som anger styrkan hos poängen. |
|[Textanalys](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Attitydanalys** ([bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyserar rå text efter ledtrådar om positiv eller negativ attityd. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva. Analysis-modeller tränas före med hjälp av en omfattande mängd text och naturligt språk tekniker från Microsoft. För [utvalda språk](./text-analytics/language-support.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. |

Dessutom kan vissa behållare stöds i Cognitive Services [ **allt-i-ett erbjudande** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resursnycklar. Du kan skapa en enda allt-i-ett-resurs för Cognitive Services och använder samma fakturering nyckel över tjänster som stöds för följande tjänster:

* Visuellt innehåll
* Ansikte
* LUIS
* Textanalys

## <a name="container-availability-in-azure-cognitive-services"></a>Tillgänglighet för behållare i Azure Cognitive Services

Azure Cognitive Services-behållare är tillgängliga för allmänheten via din Azure-prenumeration och Docker-behållaravbildningar kan hämtas från Microsoft Container Registry eller Docker Hub. Du kan använda den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från lämplig registret.

> [!IMPORTANT]
> För närvarande måste du slutföra registreringsprocessen att komma åt den [ansikte](Face/face-how-to-install-containers.md) och [identifiera Text](Computer-vision/computer-vision-how-to-install-containers.md) behållare, som du kan fylla i och skicka en enkät med frågor om dig, ditt företag och användningsfallet som du vill implementera behållarna. När du har beviljat åtkomst och angett autentiseringsuppgifter kan hämta du sedan behållaravbildningarna för behållarna ansikte och identifiera Text från ett privat behållarregister med Azure Container Registry.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Azure Cognitive Services-behållare:

**Docker-motorn**: Du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), och [Windows](https://docs.docker.com/docker-for-windows/). På Windows, måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes Service](../aks/index.yml) eller [Azure Container Instances](../container-instances/index.yml).

Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure.

**Liknar processen med Microsoft Container Registry och Docker**: Du bör ha grundläggande kunskaper om både Microsoft Container Registry och Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.

Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).

Enskilda behållare kan ha sina egna krav, samt, inklusive server och minneskrav för allokering.

## <a name="developer-samples"></a>Utvecklarexempel

Developer-exempel finns på vår [GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Nästa steg

Installera och utforska funktionerna i behållare i Azure Cognitive Services:

* [Avvikelseidentifiering detektor behållare](Anomaly-Detector/anomaly-detector-container-howto.md)
* [Datorn Vision behållare](Computer-vision/computer-vision-how-to-install-containers.md)
* [Ansikts-behållare](Face/face-how-to-install-containers.md)
* [Formuläret Igenkännande behållare](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Behållare för språk Språkförståelse (LUIS)](LUIS/luis-container-howto.md)
* [Personalizer behållare](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
* [Behållare för tal-API för tjänst](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Text Analytics behållare](text-analytics/how-tos/text-analytics-how-to-install-containers.md)