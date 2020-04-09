---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Läs om hur Docker-behållare kan få Cognitive Services närmare dina data.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876849"
---
# <a name="container-support-in-azure-cognitive-services"></a>Behållarstöd i Azure Cognitive Services

Behållarstöd i Azure Cognitive Services gör det möjligt för utvecklare att använda samma avancerade API:er som är tillgängliga i Azure och möjliggör flexibilitet i var de kan distribuera och vara värd för de tjänster som med [sig med Docker-behållare](https://www.docker.com/what-container). Behållarsupport är för närvarande tillgängligt för en delmängd av Azure Cognitive Services, inklusive delar av:

> [!div class="checklist"]
> * [Avvikelseidentifiering][ad-containers]
> * [Visuellt innehåll][cv-containers]
> * [Ansikte][fa-containers]
> * [Formigenkänning][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Textanalys][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Containerization är en metod för programvarudistribution där ett program eller en tjänst, inklusive dess beroenden & konfiguration, paketerars tillsammans som en behållaravbildning. Med liten eller ingen ändring kan en behållaravbildning distribueras på en behållarvärd. Behållare är isolerade från varandra och det underliggande operativsystemet, med ett mindre fotavtryck än en virtuell dator. Behållare kan instansieras från behållaravbildningar för kortsiktiga uppgifter och tas bort när de inte längre behövs.

Cognitive Services-resurser är tillgängliga på [Microsoft Azure](https://azure.microsoft.com). Logga in på [Azure-portalen](https://portal.azure.com/) för att skapa och utforska Azure-resurser för dessa tjänster.

## <a name="features-and-benefits"></a>Funktioner och fördelar

- **Oföränderlig infrastruktur**: Aktivera DevOps-teamens för att utnyttja en konsekvent och tillförlitlig uppsättning kända systemparametrar, samtidigt som de kan anpassa sig till förändringar. Behållare ger flexibiliteten att svänga inom ett förutsägbart ekosystem och undvika konfigurationsdrift.
- **Kontroll över data:** Tillåt kunder att välja var dessa Cognitive Services behandlar sina data. Detta är viktigt för kunder som inte kan skicka data till molnet men behöver åtkomst till Cognitive Services-teknik. Stöd konsekvens i hybridmiljöer – över data, hantering, identitet och säkerhet.
- **Kontroll över modelluppdateringar:** Ge kunderna flexibilitet i versionshantering och uppdatering av modeller som distribueras i deras lösningar.
- **Bärbar arkitektur**: Aktivera skapandet av en bärbar programarkitektur som kan distribueras på Azure, lokalt och på kanten. Behållare kan distribueras direkt till [Azure Kubernetes Service,](../aks/index.yml) [Azure Container Instances](../container-instances/index.yml)eller till ett [Kubernetes-kluster](https://kubernetes.io/) som distribueras till [Azure Stack](/azure-stack/operator). Mer information finns i [Distribuera Kubernetes till Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Hög dataflöde / låg latens:** Ge kunderna möjlighet att skala för hög dataflöde och låg latens krav genom att göra det möjligt för Cognitive Services att köra fysiskt nära deras program logik och data. Behållare tak inte transaktioner per sekund (TPS) och kan göras för att skala både upp och ut för att hantera efterfrågan om du tillhandahåller nödvändiga maskinvaruresurser.
- **Skalbarhet**: Med den ständigt växande populariteten för containerisering och containerorkestreringsprogram, såsom Kubernetes; skalbarhet är i framkant av tekniska framsteg. Programutveckling bygger på en skalbar klustergrund och tillgodoser hög tillgänglighet.

## <a name="containers-in-azure-cognitive-services"></a>Behållare i Azure Cognitive Services

Azure Cognitive Services-behållare innehåller följande uppsättning Docker-behållare, som var och en innehåller en delmängd av funktioner från tjänster i Azure Cognitive Services:

| Tjänst | Prisnivå som stöds | Container | Beskrivning |
|---------|----------|----------|-------------|
|[Avvikelsedetektor][ad-containers] |F0, S0|**Avvikelsedetektor** |Med API:et för avvikelsedetektorer kan du övervaka och identifiera avvikelser i dina tidsseriedata med maskininlärning.<br>[Begär åtkomst](https://aka.ms/adcontainer)|
|[Visuellt innehåll][cv-containers] |F0, S1|**Läsa** |Extraherar tryckt text från bilder av olika objekt med olika ytor och bakgrunder, till exempel kvitton, affischer och visitkort. Läs-behållaren identifierar också *handskriven text* i bilder och ger PDF/TIFF/multi-page-stöd.<br/><br/>**Viktigt:** Läs-behållaren fungerar för närvarande endast med engelska.|
|[Ansikte][fa-containers] |F0, S0|**Ansikte** |Upptäcker mänskliga ansikten i bilder och identifierar attribut, inklusive ansiktslandmärken (till exempel näsor och ögon), kön, ålder och andra maskinspåverkade ansiktsdrag. Förutom identifiering kan Face kontrollera om två ansikten i samma bild eller olika bilder är desamma genom att använda en konfidenspoäng, eller jämföra ansikten mot en databas för att se om det redan finns ett liknande eller identiskt ansikte. Det kan också organisera liknande ansikten i grupper, med hjälp av delade visuella egenskaper.<br>[Begär åtkomst](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Formulärigenkänning][fr-containers] |F0, S0|**Formigenkänning** |Formulärförståelse tillämpar maskininlärningsteknik för att identifiera och extrahera nyckelvärdespar och tabeller från formulär.<br>[Begär åtkomst](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([bild](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Läser in en tränad eller publicerad språkförståelsemodell, även känd som en LUIS-app, i en docker-behållare och ger åtkomst till frågeförutsägelserna från behållarens API-slutpunkter. Du kan samla in frågeloggar från behållaren och överföra dessa tillbaka till [LUIS-portalen](https://www.luis.ai) för att förbättra appens förutsägelsenoggrannhet.|
|[Speech Service API][sp-containers-stt] |F0, S0|**Tal till text** |Transkriberar kontinuerlig realtidsöversättning av tal till text.|
|[Speech Service API][sp-containers-cstt] |F0, S0|**Anpassad tal-till-text** |Transkriberar kontinuerligt tal i realtid till text med hjälp av en anpassad modell.|
|[Speech Service API][sp-containers-tts] |F0, S0|**Text till tal** |Konverterar text till naturligt tal.|
|[Speech Service API][sp-containers-ctts] |F0, S0|**Anpassad text till tal** |Konverterar text till naturligt klingande tal med en anpassad modell.|
|[Textanalys][ta-containers-keyphrase] |F0, S|**Extraktion av nyckelfraser** ([bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extraherar nyckelfraser för att identifiera de viktigaste punkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. |
|[Textanalys][ta-containers-language]|F0, S|**Språkidentifiering** ([bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |För upp till 120 språk upptäcker du vilket språk indatatexten är skriven i och rapporterar en enda språkkod för varje dokument som skickas in på begäran. Språkkoden paras med poäng som anger styrkan hos poängen. |
|[Textanalys][ta-containers-sentiment]|F0, S|**Sentimentanalys** ([bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyserar rå text för ledtrådar om positiva eller negativa känslor. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva. Analysmodellerna är förutbildade med hjälp av en omfattande mängd text- och naturligt språkteknik från Microsoft. För [utvalda språk](./text-analytics/language-support.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Dessutom stöds vissa behållare i Cognitive Services [**Allt-i-ett-erbjudande om**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resursnycklar. Du kan skapa en enda Allt-i-ett-resurs för Cognitive Services och använda samma faktureringsnyckel för tjänster som stöds för följande tjänster:

* Visuellt innehåll
* Ansikte
* LUIS
* Textanalys

## <a name="container-availability-in-azure-cognitive-services"></a>Behållarinstans i Azure Cognitive Services

Azure Cognitive Services-behållare är allmänt tillgängliga via din Azure-prenumeration och Docker-behållaravbildningar kan hämtas från antingen Microsoft Container Registry eller Docker Hub. Du kan använda [docker pull-kommandot](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta en behållaravbildning från rätt register.

> [!IMPORTANT]
> För närvarande måste du slutföra en registreringsprocess för att komma åt följande behållare, där du fyller i och skickar en enkät med frågor om dig, ditt företag och användningsfallet som du vill implementera behållarna för. När du har beviljats åtkomst och angett autentiseringsuppgifter kan du sedan hämta behållaravbildningarna från ett privat behållarregister som finns i Azure Container Registry.
> * [Avvikelsedetektor](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Ansikte](Face/face-how-to-install-containers.md)
> * [Formigenkänning](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Läsa](computer-vision/computer-vision-how-to-install-containers.md)
> * [Tal-till-text och text-till-tal](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Krav

Du måste uppfylla följande förutsättningar innan du använder Azure Cognitive Services-behållare:

**Docker Motor:** Du måste ha Docker Engine installerat lokalt. Docker tillhandahåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)och [Windows](https://docs.docker.com/docker-for-windows/). I Windows måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes Service](../aks/index.yml) eller [Azure Container Instances](../container-instances/index.yml).

Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure.

**Förtrogenhet med Microsoft Container Registry och Docker:** Du bör ha en grundläggande förståelse för både Microsoft Container Registry och Docker begrepp, `docker` som register, databaser, behållare och containeravbildningar, samt kunskap om grundläggande kommandon.

En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

Enskilda behållare kan ha sina egna krav, inklusive server- och minnesallokeringskrav.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [behållarrecept](containers/container-reuse-recipe.md) som du kan använda med Cognitive Services.

Installera och utforska funktionerna som tillhandahålls av behållare i Azure Cognitive Services:

* [Behållare för avvikelsedetektor][ad-containers]
* [Behållare för datorseende][cv-containers]
* [Ansiktsbehållare][fa-containers]
* [Formulärconkänningsbehållare][fr-containers]
* [LUIS-behållare (Language Understanding)][lu-containers]
* [API-behållare för taltjänst][sp-containers]
* [Behållare för textanalys][ta-containers]

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