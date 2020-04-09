---
title: Cognitive Services-behållare med vanliga frågor och svar (FAQ)
titleSuffix: Azure Cognitive Services
description: Vanliga frågor och svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8aae650065e8de11ccdc55a8a056c379a219fcb4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876514"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Cognitive Services-behållare (Vanliga frågor och svar)

## <a name="general-questions"></a>Allmänna frågor

**F: Vad är tillgängligt?**

**S:** [Behållarstöd i Azure Cognitive Services](../cognitive-services-container-support.md) gör det möjligt för utvecklare att använda samma intelligenta API:er som är tillgängliga i Azure, men med [fördelarna](../cognitive-services-container-support.md#features-and-benefits) med containerisering. Behållarsupport är för närvarande tillgängligt i förhandsversionen för en delmängd av Azure Cognitive Services, inklusive delar av:

> [!div class="checklist"]
> * [Avvikelseidentifiering][ad-containers]
> * [Visuellt innehåll][cv-containers]
> * [Ansikte][fa-containers]
> * [Formigenkänning][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Textanalys][ta-containers]

**F: Finns det någon skillnad mellan Cognitive Services-molnet och behållarna?**

**A.** Cognitive Services-behållare är ett alternativ till Cognitive Services-molnet. Behållare erbjuder samma funktioner som motsvarande molntjänster. Kunder kan distribuera behållarna lokalt eller i Azure. Kärn-AI-teknik, prisnivåer, API-nycklar och API-signatur är desamma mellan behållaren och motsvarande molntjänster. Här är [funktionerna och fördelarna](../cognitive-services-container-support.md#features-and-benefits) med att välja behållare framför deras molntjänst motsvarande.

**F: Kommer behållare att vara tillgängliga för alla Kognitiva tjänster och vilka är nästa uppsättning behållare vi bör förvänta oss?**

**A.** Vi vill göra fler Kognitiva tjänster tillgängliga som behållarerbjudanden. Kontakta din lokala Microsoft-kontohanterare för att få uppdateringar om nya behållarversioner och andra Cognitive Services-meddelanden.

**F: Vad blir servicenivåavtalet (SLA) för Cognitive Services-behållare?**

**A.** Cognitive Services-behållare har inget serviceavtal.

Cognitive Services-behållarkonfigurationer av resurser styrs av kunder, så Microsoft kommer inte att erbjuda ett serviceavtal för allmän tillgänglighet (GA). Kunder är fria att distribuera behållare lokalt, vilket innebär att de definierar värdmiljöerna.

> [!IMPORTANT]
> Mer information om Cognitive Services Service-Level Agreements [finns på vår SLA-sida.](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

**F: Finns dessa behållare i suveräna moln?**

**A.** Inte alla är bekant med termen "suveräna moln", så låt oss börja med definition:

> Det "suveräna molnet" består av [Azure Government,](../../azure-government/documentation-government-welcome.md) [Azure Germany](../../germany/germany-welcome.md)och Azure [China 21Vianet-molnen.](https://docs.microsoft.com/azure/china/overview-operations)

Tyvärr stöds *inte* Cognitive Services-behållarna internt i de suveräna molnen. Behållarna kan köras i dessa moln, men de kommer att hämtas från det offentliga molnet och måste skicka användningsdata till den offentliga slutpunkten.

### <a name="versioning"></a>Versionshantering

**F: Hur uppdateras behållarna till den senaste versionen?**

**A.** Kunder kan välja när de behållare som de har distribuerat ska uppdateras. Behållare markeras med [standarddockertaggar](https://docs.docker.com/engine/reference/commandline/tag/) som `latest` anger den senaste versionen. Vi uppmuntrar kunder att hämta den senaste versionen av behållare när de släpps, kassan [Azure Container Registry webhooks](../../container-registry/container-registry-webhook.md) för information om hur du får meddelanden när en avbildning uppdateras.
 
**F: Vilka versioner kommer att stödjas?**

**A.** Den aktuella och sista huvudversionen av behållaren kommer att stödjas. Vi uppmuntrar dock kunderna att hålla sig aktuella för att få den senaste tekniken.
 
**F: Hur versionshanteringsversioner?**

**A.** Huvudversionsändringar indikerar att det finns en brytande ändring av API-signaturen. Vi räknar med att detta i allmänhet kommer att sammanfalla med större versionsändringar i motsvarande Cognitive Service-molnerbjudande. Ändringar i mindre versioner indikerar buggfixar, modelluppdateringar eller nya funktioner som inte gör en brytande ändring av API-signaturen.

## <a name="technical-questions"></a>Tekniska frågor

**F: Hur ska jag köra Cognitive Services-behållarna på IoT-enheter?**

Oavsett om du inte har en tillförlitlig internetuppkoppling, eller vill spara på bandbredd kostnad. Eller om har krav på låg latens, eller hanterar känsliga data som måste analyseras på plats, azure [IoT Edge med Cognitive Services-behållarna](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) ger dig konsekvens med molnet.

**F: Hur ger jag produktfeedback och funktionsrekommendationer?**

**A.** Kunderna uppmanas att [uttrycka sin oro](https://cognitive.uservoice.com/) offentligt, och upp-rösta andra som har gjort samma sak där potentiella frågor överlappar varandra. Användarens röstverktyg kan användas för både produktfeedback och funktionsrekommendationer.

**F: Vem kontaktar jag för support?**

**A.** Kundsupportkanaler är desamma som Cognitive Services molnerbjudande. Alla Cognitive Services-behållare innehåller loggningsfunktioner som hjälper oss och communitysupportkunderna. Mer support finns i följande alternativ.

### <a name="customer-support-plan"></a>Kundtjänstplan

Kunder bör referera till sin [Azure-supportplan](https://azure.microsoft.com/support/plans/) för att se vem de ska kontakta för support.

### <a name="azure-knowledge-center"></a>Azure kunskapscenter

Kunden kan utforska [Azure-kunskapscentret](https://azure.microsoft.com/resources/knowledge-center/) för att svara på frågor och supportproblem.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) är en fråga och svarssajt för professionella och entusiastprogrammerare.

Utforska följande taggar för potentiella frågor och svar som stämmer överens med dina behov.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**F: Hur fungerar fakturering?**

**A.** Kunder debiteras baserat på förbrukning, liknande cognitive services-molnet. Behållarna måste konfigureras för att skicka data för mätning till Azure och transaktioner faktureras därefter. Resurser som används i de värdbaserade och lokala tjänsterna läggs till en kvot med nivåindelad prissättning, vilket räknar mot båda användningarna. Mer information finns på prissidan för motsvarande erbjudande.

* [Avvikelseidentifiering][ad-containers-billing]
* [Visuellt innehåll][cv-containers-billing]
* [Ansikte][fa-containers-billing]
* [Formigenkänning][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [Textanalys][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata till Microsoft.
 
**F: Vad är den aktuella supportgarantin för behållare?**

**A.** Det finns ingen garanti för förhandsvisningar. Microsofts standardgaranti för företagsprogramvara gäller när behållare formellt tillkännages som allmän tillgänglighet (GA).
 
**F: Vad händer med Cognitive Services-behållare när internetanslutningen går förlorad?**

**A.** Cognitive Services-behållare är *inte licensierade* för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera med mätartjänsten.

**F: Hur länge kan behållaren fungera utan att vara ansluten till Azure?**

**A.** Cognitive Services-behållare är *inte licensierade* för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera med mätartjänsten.
 
**F: Vilken maskinvara krävs för att köra dessa behållare?**

**A.** Cognitive Services-behållare är x64-baserade behållare som kan köra alla kompatibla Linux-nod-, VM- och kantenheter som stöder x64 Linux Docker Containers. De kräver alla CPU-processorer. De minsta och rekommenderade konfigurationerna för varje containererbjudande finns tillgängliga nedan:

* [Avvikelseidentifiering][ad-containers-recommendations]
* [Visuellt innehåll][cv-containers-recommendations]
* [Ansikte][fa-containers-recommendations]
* [Formigenkänning][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [Textanalys][ta-containers-recommendations]
 
**F: Stöds dessa behållare för närvarande i Windows?**

**A.** Cognitive Services-behållarna är Linux-behållare, men det finns visst stöd för Linux-behållare på Windows. Mer information om Linux-behållare i Windows finns i [Docker-dokumentationen](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**F: Hur upptäcker jag behållarna?**

**A.** Cognitive Services-behållare är tillgängliga på olika platser, till exempel Azure-portalen, Docker-hubben och Azure-behållarregister. De senaste behållarplatserna finns i [behållardatabaser och avbildningar](../cognitive-services-container-support.md#container-repositories-and-images).

**F: Hur jämför Cognitive Services-behållare med AWS- och Google-erbjudanden?**

**A.** Microsoft är den första molnleverantören som flyttar sina förtränade AI-modeller i behållare med enkel fakturering per transaktion som om kunderna använder en molntjänst. Microsoft anser att ett hybridmoln ger kunderna större valmöjligheter.

**F: Vilka efterlevnadscertifieringar har containrar?**

**A.** Cognitive services-behållare har inga efterlevnadscertifieringar

**F: Vilka regioner är Cognitive Services-behållare tillgängliga i?**

**A.** Behållare kan köras var som helst i alla regioner, men de behöver en nyckel och för att ringa tillbaka till Azure för mätning. Alla regioner som stöds för molntjänsten stöds för anropet för behållare som mäter mätning.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
