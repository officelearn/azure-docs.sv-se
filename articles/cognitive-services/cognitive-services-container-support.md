---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur Docker-behållare kan få Cognitive Services närmare dina data.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876849"
---
# <a name="container-support-in-azure-cognitive-services"></a>Stöd för behållare i Azure Cognitive Services

Med stöd för behållare i Azure Cognitive Services kan utvecklare använda samma omfattande API: er som är tillgängliga i Azure och möjliggör flexibilitet i var de ska distribueras och vara värd för de tjänster som följer [Docker-behållare](https://www.docker.com/what-container). Container support är för närvarande tillgängligt för en delmängd av Azure Cognitive Services, inklusive delar av:

> [!div class="checklist"]
> * [Avvikelseidentifiering][ad-containers]
> * [Visuellt innehåll][cv-containers]
> * [Ansikte][fa-containers]
> * [Formigenkänning][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Textanalys][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Skapa behållare är en metod för program varu distribution där ett program eller en tjänst, inklusive dess beroenden & konfiguration, paketeras tillsammans som en behållar avbildning. Med lite eller ingen ändring kan en behållar avbildning distribueras på en behållar värd. Behållare är isolerade från varandra och det underliggande operativ systemet, med en mindre plats än en virtuell dator. Behållare kan instansieras från behållar avbildningar för kortsiktiga uppgifter och tas bort när de inte längre behövs.

Cognitive Services resurser är tillgängliga på [Microsoft Azure](https://azure.microsoft.com). Logga in på [Azure Portal](https://portal.azure.com/) för att skapa och utforska Azure-resurser för de här tjänsterna.

## <a name="features-and-benefits"></a>Funktioner och fördelar

- **Oåterkallelig infrastruktur**: Aktivera DevOps Teams för att utnyttja en konsekvent och tillförlitlig uppsättning kända system parametrar, samtidigt som du kan anpassa för att ändra. Behållare ger flexibiliteten att pivotera i ett förutsägbart eko system och undvika konfigurations avvikelser.
- **Kontroll över data**: låt kunderna välja var dessa Cognitive Services bearbeta sina data. Detta är viktigt för kunder som inte kan skicka data till molnet men som behöver åtkomst till Cognitive Services teknik. Stöd för konsekvens i hybrid miljöer – över data, hantering, identitet och säkerhet.
- **Kontroll över modell uppdateringar**: ge kunderna flexibilitet vid versions hantering och uppdatering av modeller som har distribuerats i deras lösningar.
- **Portabel arkitektur**: möjliggör skapande av en bärbar program arkitektur som kan distribueras på Azure, lokalt och på gränsen. Behållare kan distribueras direkt till [Azure Kubernetes-tjänsten](../aks/index.yml), [Azure Container instances](../container-instances/index.yml)eller till ett [Kubernetes](https://kubernetes.io/) -kluster som distribuerats till [Azure Stack](/azure-stack/operator). Mer information finns i [distribuera Kubernetes till Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Högt data flöde/låg latens**: ge kunderna möjlighet att skala för höga krav på data flöden och låg latens genom att aktivera Cognitive Services att köra fysiskt nära sin program logik och data. Behållare omfattar inte Cap-transaktioner per sekund (TPS) och kan göras för att skala upp och ut för att hantera efter frågan om du anger de nödvändiga maskin varu resurserna.
- **Skalbarhet**: med den ständigt växande populariteten av skapa behållare och behållar Dirigerings program, till exempel Kubernetes; skalbarhet är i det tekniska förskottet i Forefront. Genom att bygga vidare på en skalbar kluster bas kan program utveckling tillgodose hög tillgänglighet.

## <a name="containers-in-azure-cognitive-services"></a>Behållare i Azure Cognitive Services

Azure Cognitive Services-behållare innehåller följande uppsättning Docker-behållare, som var och en innehåller en delmängd funktioner från tjänster i Azure Cognitive Services:

| Tjänst | Pris nivå som stöds | Container | Beskrivning |
|---------|----------|----------|-------------|
|[Avvikelse detektor][ad-containers] |F0, S0|**Avvikelser-detektor** |Med API: t för avvikelse identifiering kan du övervaka och identifiera avvikelser i dina Time Series-data med Machine Learning.<br>[Begär åtkomst](https://aka.ms/adcontainer)|
|[Visuellt innehåll][cv-containers] |F0, S1|**Läsa** |Extraherar utskriven text från bilder av olika objekt med olika ytor och bakgrunder, till exempel inleveranser, affischer och visitkort. I Läs behållaren identifieras även *handskriven text* i bilder och innehåller stöd för PDF/TIFF/flera sidor.<br/><br/>**Viktigt:** Läs behållaren fungerar för närvarande endast med engelska.|
|[Ansikte][fa-containers] |F0, S0|**Ansikte** |Identifierar människo ansikten i bilder och identifierar attribut, inklusive ansikts landmärken (till exempel näsaer och ögon), kön, ålder och andra maskin förväntade ansikts funktioner. Förutom identifiering kan FACET kontrol lera om två ansikten i samma bild eller olika bilder är desamma genom att använda en säkerhets poäng, eller jämföra ansikten mot en databas för att se om det redan finns ett liknande eller identiskt ansikte. Det kan också organisera liknande ansikten i grupper med hjälp av delade visuella egenskaper.<br>[Begär åtkomst](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Formulär igenkänning][fr-containers] |F0, S0|**Formigenkänning** |Forms förståelse använder Machine Learning-teknik för att identifiera och extrahera nyckel/värde-par och tabeller från formulär.<br>[Begär åtkomst](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Luis** ([bild](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Läser in en utbildad eller publicerad Language Understanding modell, som även kallas LUIS-app, i en Docker-behållare och ger åtkomst till frågans förutsägelser från behållarens API-slutpunkter. Du kan samla in frågeuttryck från behållaren och överföra tillbaka dem till Luis- [portalen](https://www.luis.ai) för att förbättra appens förutsägelse noggrannhet.|
|[Speech Service API][sp-containers-stt] |F0, S0|**Tal till text** |Transkriberar kontinuerlig realtidsöversättning av tal till text.|
|[Speech Service API][sp-containers-cstt] |F0, S0|**Custom Speech till text** |Översätter kontinuerlig real tids tal till text med hjälp av en anpassad modell.|
|[Speech Service API][sp-containers-tts] |F0, S0|**Text till tal** |Konverterar text till naturligt tal.|
|[Speech Service API][sp-containers-ctts] |F0, S0|**Anpassad text till tal** |Konverterar text till naturligt ljuds tal med hjälp av en anpassad modell.|
|[Textanalys][ta-containers-keyphrase] |F0, S|**Extrahering av diskussionsämne** ([bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extraherar viktiga fraser för att identifiera huvud punkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. |
|[Textanalys][ta-containers-language]|F0, S|**Språkidentifiering** ([bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |För upp till 120 språk identifierar det språk som indatamängden är skrivet i och rapporterar en enda språkkod för varje dokument som skickas på begäran. Språkkoden paras med poäng som anger styrkan hos poängen. |
|[Textanalys][ta-containers-sentiment]|F0, S|**Attitydanalys** ([bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyserar rå text för LED trådar om positiv eller negativ sentiment. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva. Analys modellerna är förtränade med en omfattande text-och naturliga språk teknik från Microsoft. För [utvalda språk](./text-analytics/language-support.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Dessutom stöds vissa behållare i Cognitive Services [**alla-i-ett-erbjudande**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resurs nycklar. Du kan skapa en enda Cognitive Services alla-i-en-resurs och använda samma fakturerings nyckel mellan tjänster som stöds för följande tjänster:

* Visuellt innehåll
* Ansikte
* LUIS
* Textanalys

## <a name="container-availability-in-azure-cognitive-services"></a>Tillgänglighet för behållare i Azure Cognitive Services

Azure Cognitive Services-behållare är offentligt tillgängliga via din Azure-prenumeration och Docker-behållar avbildningar kan hämtas från antingen Microsoft Container Registry-eller Docker-hubben. Du kan använda kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från lämpligt register.

> [!IMPORTANT]
> För närvarande måste du slutföra en registrerings process för att få åtkomst till följande behållare, där du kan fylla i och skicka in en enkät med frågor om dig, ditt företag och det användnings fall som du vill implementera behållarna för. När du har beviljat åtkomst och angett autentiseringsuppgifter kan du hämta behållar avbildningarna från ett privat behållar register som Azure Container Registry.
> * [Avvikelse detektor](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Ansikte](Face/face-how-to-install-containers.md)
> * [Formigenkänning](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Läsa](computer-vision/computer-vision-how-to-install-containers.md)
> * [Tal till text och text till tal](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Krav

Du måste uppfylla följande krav innan du använder Azure Cognitive Services-behållare:

**Docker-motorn**: du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)och [Windows](https://docs.docker.com/docker-for-windows/). I Windows måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes-tjänsten](../aks/index.yml) eller [Azure Container instances](../container-instances/index.yml).

Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure.

**Bekant med Microsoft container Registry och Docker**: du bör ha grundläggande kunskaper om både Microsoft container Registry-och Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar samt kunskaper om `docker` grundläggande kommandon.

En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

Enskilda behållare kan också ha sina egna krav, inklusive krav på Server och minnesallokering.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [behållar recept](containers/container-reuse-recipe.md) som du kan använda med Cognitive Services.

Installera och utforska de funktioner som finns i behållare i Azure Cognitive Services:

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
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment