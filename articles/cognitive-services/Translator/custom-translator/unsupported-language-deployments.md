---
title: Nepodporovaný jazyk distributioner - anpassad Translator
titleSuffix: Azure Cognitive Services
description: Så här distribuerar par språket stöds inte i anpassade Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 09fbd771d945646fe385508779d38e4abb2ee293
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476512"
---
# <a name="unsupported-language-deployments"></a>Nepodporovaný jazyk distributioner

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Med inmatningsprotokollen för Microsoft Translator Hub, Microsoft kommer den kan avinstalleras alla modeller som för närvarande har distribuerats via Hub. Många har modeller som distribuerats i hubben språkpar vars inte stöds i anpassade Translator.  Vi vill inte användare i det här fallet har ingen möjlighet för översättning av deras innehåll.

Nu har vi en process där du kan distribuera dina modeller som stöds inte via anpassade Translator.  Den här processen kan du fortsätta att översätta innehåll med hjälp av senaste V3-API.  Dessa modeller kommer att finnas tills du väljer att ta bort dem eller språkparet blir tillgänglig i anpassade Translator.  Den här artikeln beskriver processen för att distribuera modeller med par språket stöds inte.

## <a name="prerequisites"></a>Nödvändiga komponenter

För dina modeller att vara kandidater för distribution, måste de uppfylla följande kriterier:
* Projektet som innehåller modellen måste har migrerats från hubben till den anpassade Translator med hjälp av migreringsverktyget.  Processen för att migrera projekt och arbetsytor finns [här](how-to-migrate.md).
* Modellen måste vara i distribuerat läge när migreringen sker.  
* Språk-par med modellen måste vara ett nyckelpar med språket stöds inte i anpassade Translator.  Språkpar där ett språk som stöds till eller från engelska men paret själva omfattar inte engelska, lämpar sig för distributioner av språket stöds inte.  Till exempel anses en Hub modell för en franska till tyska par en språket stöds inte par även dock till engelska och engelska till tyska är stöds franska-par.

## <a name="process"></a>Process
När du har migrerat modeller från hubben som är lämpliga kandidater för distribution kan du hitta dem genom att gå till den **inställningar** för din arbetsyta och rulla till slutet av sidan där du kan se en **stöds inte Translator Hub utbildningar** avsnittet.  Det här avsnittet visas bara om du har projekt som uppfyller de förutsättningar som nämns ovan.

![Hur du migrerar från hubben](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

I den **stöds inte Translator Hub utbildningar** sida för val av den **ej begärda utbildningar** fliken innehåller modeller som är kvalificerade för distribution.  Välj de modeller som du vill distribuera och skicka en begäran.   Du kan välja valfritt antal modeller som du vill att distributionen innan 30 April distributionstidsgräns använder.
 
![Hur du migrerar från hubben](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

När skickats kan modellen inte längre tillgänglig på den **ej begärda utbildningar** fliken och i stället visas på den **begärt utbildningar** fliken.  Du kan visa dina begärda utbildningar när som helst.

![Hur du migrerar från hubben](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Nästa steg

Modeller som du har valt för distribution sparas när hubben är inaktiverad och alla modeller har avinstallerats.  Nu har du fram till den 24 maj att skicka förfrågningar för distribution av modeller som inte stöds.  Vi distribuerar dessa modeller 15 juni då de kommer att vara tillgänglig via API: et för Translator V3.  Dessutom kan blir de tillgängliga via V2 API fram till den 1 juli.  

Mer information om viktiga datum i utfasningen av kontrollen Hub [här](https://www.microsoft.com/translator/business/hub/).
När du distribuerade, normal värdbaserade avgifter tillkommer.  Se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) mer information.  

Till skillnad från standard anpassad Translator modeller blir Hub modeller endast tillgängliga i en region, så inte gäller avgifterna för flera regioner som värd.  När distribuerats kommer du att kunna ta bort modellen Hub när som helst via migrerade anpassad Translator-projektet.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda dina distribuerade anpassade översättningsmodellen via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
