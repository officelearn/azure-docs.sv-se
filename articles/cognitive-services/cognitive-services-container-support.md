---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur Docker-behållare kan få närmare kognitiva tjänster till dina data.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 88d140c4890492b6a53c2e73328ab814293550e6
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994936"
---
# <a name="container-support-in-azure-cognitive-services"></a>Stöd för behållare i Azure Cognitive Services

Stöd för behållare i Azure Cognitive Services kan utvecklare använda samma omfattande API: er som är tillgängliga i Azure och möjliggör flexibilitet att distribuera och tillhandahålla tjänster som medföljer [Docker-behållare](https://www.docker.com/what-container). Container support finns för närvarande i för hands version för en delmängd av Azure Cognitive Services, inklusive delar av:

* [Avvikelse detektor][ad-containers]
* [Visuellt innehåll][cv-containers]
* [Ansikte][fa-containers]
* [Formulär igenkänning][fr-containers]
* [Språkförståelse (LUIS)][lu-containers]
* [Speech Service API][sp-containers]
* [Textanalys][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Skapa behållare är en metod för distribution av programvara där ett program eller tjänst, inklusive dess beroenden och konfiguration, är packade tillsammans som en behållaravbildning. Med lite eller ingen ändring av, kan du distribuera en behållaravbildning på en behållarvärd. Behållare är isolerade från varandra och det underliggande operativsystemet, med mindre avtryck än en virtuell dator. Behållare kan instansieras behållaravbildningar för kortsiktig uppgifter och tas bort när den inte längre behövs.

Cognitive Services resurser är tillgängliga på [Microsoft Azure](https://azure.microsoft.com). Logga in på den [Azure-portalen](https://portal.azure.com/) att skapa och utforska Azure-resurser för dessa tjänster.

## <a name="features-and-benefits"></a>Funktioner och erbjudanden

- **Kontroll över data**: Låt kunderna välja var dessa Cognitive Services bearbeta sina data. Detta är nödvändigt för kunder som det går inte att skicka data till molnet men som behöver åtkomst till Cognitive Services-teknik. Stöd för konsekvens i hybrid miljöer – över data, hantering, identitet och säkerhet.
- **Kontroll över modell uppdateringar**: Ge kunderna flexibilitet vid versions hantering och uppdatering av modeller som har distribuerats i deras lösningar.
- **Portabel arkitektur**: Gör det möjligt att skapa en bärbar program arkitektur som kan distribueras på Azure, lokalt och på gränsen. Behållare kan distribueras direkt till [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), eller till en [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](/azure-stack/operator). Mer information finns i [distribuera Kubernetes i Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Högt data flöde/låg latens**: Ge kunderna möjlighet att skala för högt data flöde och krav på låg latens genom att aktivera Cognitive Services att köra fysiskt nära sin program logik och data. Behållare cap inte transaktioner per sekund (TPS) och kan göras för att skala både uppåt och utåt för att hantera begäran om du anger nödvändiga maskinvaruresurser. 

## <a name="containers-in-azure-cognitive-services"></a>Behållare i Azure Cognitive Services

Azure Cognitive Services-behållare ger följande uppsättning Docker-behållare som innehåller en delmängd av funktioner från tjänster i Azure Cognitive Services:

| Tjänsten | Pris nivå som stöds | Container | Beskrivning |
|---------|----------|----------|-------------|
|[Avvikelse detektor][ad-containers] |F0, S0|**Avvikelser-detektor** |Med API: t för avvikelse identifiering kan du övervaka och identifiera avvikelser i dina Time Series-data med Machine Learning.<br>[Begär åtkomst](https://aka.ms/adcontainer)|
|[Visuellt innehåll][cv-containers] |F0, S1|**Identifiera Text** |Extraherar ut text från bilder för olika objekt med olika ytor och bakgrunder, till exempel kvitton och affischer visitkort.<br/><br/>**Viktigt:** Behållaren identifiera Text fungerar för närvarande bara på engelska.<br>[Begär åtkomst](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Ansikte][fa-containers] |F0, S0|**Ansikte** |Identifierar ansikten i bilder och identifierar attribut, inklusive ansiktslandmärken (till exempel och rörliga och ögon), kön, ålder och andra dator-förväntad ansiktsdrag. Förutom identifiering Kontrollera ansikte om två ansikten i samma bild eller olika bilder är samma med hjälp av ett förtroenderesultat eller jämföra ansikten mot en databas för att se om en likartade eller identiska ansikte finns redan. Det kan även sortera liknande ansikten i grupper, med hjälp av delade visual egenskaper.<br>[Begär åtkomst](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Formulär igenkänning][fr-containers] |F0, S0|**Formulär igenkänning** |Forms förståelse använder Machine Learning-teknik för att identifiera och extrahera nyckel/värde-par och tabeller från formulär.<br>[Begär åtkomst](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([bild](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Läser in en tränad eller publicerade Language Understanding modell, även kallat en LUIS-app, i en docker-behållare och ger tillgång till fråga förutsägelser från behållarens API-slutpunkter. Du kan samla in fråga loggar från behållaren och ladda upp dessa tillbaka till den [LUIS portal](https://www.luis.ai) förbättra appens prognosens noggrannhet.|
|[Speech Service API][sp-containers] |F0, S0|**Tal till text** |Transkriberar kontinuerlig realtidsöversättning av tal till text.<br>[Begär åtkomst](https://aka.ms/speechcontainerspreview/)|
|[Speech Service API][sp-containers] |F0, S0|**Text till tal** |Konverterar text till naturligt tal.<br>[Begär åtkomst](https://aka.ms/speechcontainerspreview/)|
|[Textanalys][ta-containers] |F0, S|**Nyckeln diskussionsämne** ([bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extraherar viktiga fraser för att identifiera de viktigaste aspekterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. |
|[Textanalys][ta-containers]|F0, S|**Språkidentifiering** ([bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Identifierar vilka språk som indatatexten är skriven i och rapportera en enda språkkod för varje dokument som skickats på begäran för upp till 120 språk. Språkkoden paras med poäng som anger styrkan hos poängen. |
|[Textanalys][ta-containers]|F0, S|**Attitydanalys** ([bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyserar rå text efter ledtrådar om positiv eller negativ attityd. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva. Analysis-modeller tränas före med hjälp av en omfattande mängd text och naturligt språk tekniker från Microsoft. För [utvalda språk](./text-analytics/language-support.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Dessutom stöds vissa behållare i Cognitive Services [**alla-i-ett-erbjudande**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resurs nycklar. Du kan skapa en enda Cognitive Services alla-i-en-resurs och använda samma fakturerings nyckel mellan tjänster som stöds för följande tjänster:

* Visuellt innehåll
* Ansikte
* LUIS
* Textanalys

## <a name="container-availability-in-azure-cognitive-services"></a>Tillgänglighet för behållare i Azure Cognitive Services

Azure Cognitive Services-behållare är tillgängliga för allmänheten via din Azure-prenumeration och Docker-behållaravbildningar kan hämtas från Microsoft Container Registry eller Docker Hub. Du kan använda den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från lämplig registret.

> [!IMPORTANT]
> För närvarande måste du slutföra en registrerings process för att få åtkomst till följande behållare, där du kan fylla i och skicka in en enkät med frågor om dig, ditt företag och det användnings fall som du vill implementera behållarna för. När du har beviljat åtkomst och angett autentiseringsuppgifter kan du hämta behållar avbildningarna från ett privat behållar register som Azure Container Registry.
> * [Avvikelse detektor](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Ansikte](Face/face-how-to-install-containers.md)
> * [Formulär igenkänning](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Identifiera Text](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Tal till text och text till tal](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Azure Cognitive Services-behållare:

**Docker-motor**: Du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), och [Windows](https://docs.docker.com/docker-for-windows/). På Windows, måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes Service](../aks/index.yml) eller [Azure Container Instances](../container-instances/index.yml).

Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure.

**Bekant med Microsoft container Registry och Docker**: Du bör ha grundläggande kunskaper om både Microsoft container Registry och Docker-koncept som register, databaser, behållare och behållar avbildningar samt kunskaper om grundläggande `docker` kommandon.

Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).

Enskilda behållare kan ha sina egna krav, samt, inklusive server och minneskrav för allokering.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [behållar recept](containers/container-reuse-recipe.md) som du kan använda med Cognitive Services.

Installera och utforska funktionerna i behållare i Azure Cognitive Services:

* [Avvikelse detektor behållare][ad-containers]
* [Visuellt innehåll behållare][cv-containers]
* [Ansikts behållare][fa-containers]
* [Formulär igenkännings behållare][fr-containers]
* [Language Understanding (LUIS) behållare][lu-containers]
* [API-behållare för Speech service][sp-containers]
* [Textanalys behållare][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md