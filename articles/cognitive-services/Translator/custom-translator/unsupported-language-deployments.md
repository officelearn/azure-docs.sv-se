---
title: Språkdistributioner som inte stöds – Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du distribuerar språkpar som inte stöds i Azure Cognitive Services Custom Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837457"
---
# <a name="unsupported-language-deployments"></a>Språkdistributioner som inte stöds

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

I och med den kommande pensioneringen av Microsoft Translator Hub kommer Microsoft att distribuera alla modeller som för närvarande distribueras via hubben. Många av er har modeller som distribueras i hubben vars språkpar inte stöds i Anpassad översättare.  Vi vill inte att användarna i denna situation inte ska ha någon möjlighet att översätta sitt innehåll.

Vi har nu en process som gör att du kan distribuera dina modeller som inte stöds via custom translator.  Med den här processen kan du fortsätta att översätta innehåll med det senaste V3-API:et.  Dessa modeller kommer att vara värd tills du väljer att ta bort dem eller språkparet blir tillgängligt i Custom Translator.  I den här artikeln beskrivs processen för att distribuera modeller med språkpar som inte stöds.

## <a name="prerequisites"></a>Krav

För att dina modeller ska kunna vara kandidater för distribution måste de uppfylla följande kriterier:
* Projektet som innehåller modellen måste ha migrerats från hubben till den anpassade översättaren med migreringsverktyget.  Processen för att migrera projekt och arbetsytor finns [här](how-to-migrate.md).
* Modellen måste vara i distribuerat tillstånd när migreringen sker.  
* Språkparet i modellen måste vara ett språkpar som inte stöds i Custom Translator.  Språkpar där ett språk stöds till eller från engelska, men paret i sig inte innehåller engelska, är kandidater för språkdistributioner som inte stöds.  Till exempel anses en Hub-modell för ett franskt till tyskt språkpar vara ett språkpar som inte stöds, även om språkpar från franska till engelska och engelska stöds.

## <a name="process"></a>Process
När du har migrerat modeller från hubben som är kandidater för distribution kan du hitta dem genom att gå till sidan **Inställningar** för arbetsytan och rulla till slutet av sidan där du ser avsnittet **Translator Hub Trainings** som inte stöds.  Det här avsnittet visas bara om du har projekt som uppfyller de förutsättningar som nämns ovan.

![Så här migrerar du från Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

På **urvalssidan för Translator Hub Trainings som inte stöds** innehåller fliken **Ej begärda utbildningsplatser** modeller som är berättigade till distribution.  Välj de modeller som du vill distribuera och skicka en begäran.   Före distributionsfristen för 30 april kan du välja så många modeller som du vill för distribution.
 
![Så här migrerar du från Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

När den har skickats in är modellen inte längre tillgänglig på fliken **Ej begärda utbildningar** och visas i stället på fliken **Begärda utbildningar.**  Du kan visa dina begärda träningar när som helst.

![Så här migrerar du från Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Nästa steg

De modeller som du har valt för distribution sparas när hubben har inaktiverats och alla modeller är odedelade.  Du har fram till den 24 maj på dig att skicka begäranden om distribution av modeller som inte stöds.  Vi kommer att distribuera dessa modeller den 15 juni då de kommer att vara tillgängliga via Translator V3 API.  Dessutom kommer de att vara tillgängliga via V2 API fram till 1 juli.  

För ytterligare information om viktiga datum i utfasningen av Hub check [här](https://www.microsoft.com/translator/business/hub/).
När de har distribuerats tillkommer normala värdavgifter.  Se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) för mer information.  

Till skillnad från vanliga Anpassade översättare modeller, Hub modeller kommer bara att vara tillgängliga i en enda region, så flera regioner hosting avgifter kommer inte att gälla.  När du har distribuerats kan du när som helst ta bort distribuera din Hub-modell via det migrerade Custom Translator-projektet.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda din distribuerade anpassade översättningsmodell via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
