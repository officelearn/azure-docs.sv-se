---
title: Process för Cognitive Services hantera
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du kan använda för tidig åtkomst till nya Cognitive Services behållare och API: er.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599517"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Hantera-process för Azure Cognitive Services

> [!NOTE]
> När ett tjänst erbjudande har slutfört en gated Preview, hamnar den i en "ej grind" offentlig för hands version som inte kräver något program för åtkomst. Efter förhands gransknings processen släpps den som allmänt tillgänglig (GA).

När nya Azure Cognitive Services-erbjudanden lanseras går de igenom en gated Preview där kunder måste begära åtkomst via ett program. Den här hantera-processen hjälper till att identifiera möjligheter för förbättringar av tjänst erbjudanden innan de är mycket tillgängliga. 

Den här artikeln vägleder dig genom program processen för gated Cognitive Services erbjudanden.

## <a name="eligibility-and-approval-process"></a>Kvalificerings-och godkännande process

Hantera-processen är på plats för att hjälpa dig att mäta intresse och bättre förstå kundernas behov. Microsoft-teamet accepterar [program](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) från Microsofts kommersiella kunder med en giltig Azure-prenumeration och ett giltigt affärs scenario. Kunder kan få sina program nekade när:

 - De är inte kopplade till någon organisation
 - De har ingen giltig Azure-prenumeration
 - Programmet skickades via personligt e-post ( @hotmail.com , @gmail.com , @yahoo.com )
 - Ingen korrekt motivering eller ett affärs scenario har angetts

Vi försöker påskynda godkännande processen på grund av efter frågan från olika kund segment. Vi kan dock inte genomföra en tids linje. När ett beslut fattas kommer Microsoft-teamet att kontakta dig och ditt konto hanterings team för nästa steg. Vi uppskattar din förståelse och ditt tålamod.

Om programmet godkänns kommer Microsoft-teamet att skicka ett e-postmeddelande med information, dokumentation och vägledning. Cognitive Services pris information finns [här](https://azure.microsoft.com/pricing/details/cognitive-services/).


För närvarande erbjuds tjänsterna nedan genom hantera-processen:

## <a name="gated-web-apis"></a>Webb-API: er för gated

|Tjänst  |
|---------|
|Avvikelse detektor v2     | 

## <a name="gated-online-containers"></a>Gated online-behållare

| Tjänst                             | Behållare                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Visuellt innehåll][cv-containers]    | Läsa                                                                          |
| [Ansikte][fa-containers]               | Ansikte                                                                          |
| [Formigenkänning][fr-containers]    | Formigenkänning                                                               |
| [Speech Service API][sp-containers] | Tal till text (anpassad och standard) och text till tal (anpassad och standard) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> Om en tjänst eller ett behållar erbjudande inte finns med i listan, är det antingen inte gated eller otillgängligt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Registrera dig för gated-tjänster](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
