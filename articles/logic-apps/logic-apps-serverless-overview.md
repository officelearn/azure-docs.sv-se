---
title: Översikt - Azure Serverless för molnbaserade appar och lösningar
description: Lär dig hur du skapar molnbaserade appar och lösningar utan att behöva oroa dig för infrastruktur med hjälp av Azure Logic Apps och Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666558"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Översikt för att skapa molnbaserade appar och lösningar med Azure Logic Apps och Azure Functions

[Serverlösa](https://azure.microsoft.com/solutions/serverless/) appar erbjuder fördelar som ökad utvecklingshastighet, minskad kod, enkelhet och skala. Den här artikeln beskriver de olika attributen för serverlösa lösningar och Azure-serverlösa erbjudanden.

## <a name="what-is-serverless"></a>Vad är serverlöst?

Serverless betyder inte att det inte finns några servrar, utan utvecklare behöver inte oroa sig för servrar. En stor del av traditionell applikationsutveckling är att svara på frågor kring skalning, hosting och övervakningslösningar för att möta kraven i applikationen. Med serverless, dessa frågor tas om hand som en del av lösningen. Dessutom faktureras serverlösa appar på en förbrukningsbaserad plan. Om appen aldrig används debiteras ingen avgift. Dessa funktioner hjälper utvecklare att fokusera enbart på en lösnings affärslogik.

De grundläggande Azure-tjänsterna för serverlösa är [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) och Azure [Functions](https://azure.microsoft.com/services/functions/). Båda lösningarna följer de tidigare beskrivna principerna och hjälper utvecklare att skapa robusta molnappar med minimal kod.

## <a name="what-is-azure-logic-apps"></a>Vad är Azure Logic Apps?

[Azure Logic Apps](logic-apps-overview.md) är ett sätt att förenkla och implementera skalbara integreringar och arbetsflöden i molnet. Den här tjänsten tillhandahåller en visuell designer för att modellera och automatisera processen som en serie steg som kallas ett arbetsflöde. Det finns många [anslutningsappar](../connectors/apis-list.md) över molntjänster och lokala system som snabbt ansluter en serverlös app till andra API:er. Varje logikapp börjar med en utlösare, till exempel "När ett konto läggs till i Dynamics CRM". När utlösaren har avfyrats kan arbetsflödet köra kombinationer av åtgärder, konverteringar och villkorsstyrd logik. Logic Apps är ett utmärkt val när du dirigerar olika Azure-funktioner i en process, särskilt när processen kräver interaktion med ett externt system eller ETT externt API.

Börja med att [skapa din första logikapp](quickstart-create-first-logic-app-workflow.md)för att komma igång med Logic Apps . Mer teknisk information om Logic Apps finns i [utvecklarreferensen](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Vad är Azure Functions?

Azure Functions är en tjänst för att enkelt köra bitar av kod eller "funktioner" i molnet. Du kan bara skriva den kod som krävs för det aktuella problemet, utan att behöva oroa dig för en hel app eller den infrastruktur som krävs. Funktioner kan göra utvecklingen ännu mer produktiv, och du kan använda ditt utvecklingsspråk som du väljer, till exempel C#, F#, Node.js, Python eller PHP. Du betalar bara för den tid som koden körs och Azure skalar efter behov.

Börja med [Skapa din första Azure-funktion](../azure-functions/functions-create-first-azure-function.md)för att komma igång med Azure Functions. Mer teknisk information om Funktioner finns i [utvecklarreferensen](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hur kan jag skapa och distribuera serverlösa appar i Azure?

Azure tillhandahåller omfattande verktyg för att utveckla, distribuera och hantera serverlösa appar. Du kan skapa appar direkt i Azure-portalen, med [verktyg i Visual Studio](logic-apps-serverless-get-started-vs.md)eller Visual Studio [Code](quickstart-create-logic-apps-visual-studio-code.md). När du har byggt din app kan du [distribuera appen snabbt med Azure Resource Manager-mallar](logic-apps-deploy-azure-resource-manager-templates.md). Azure tillhandahåller också övervakning, som du kan komma åt via Azure-portalen, via API eller SDK: er, eller med integrerad verktyg för Azure Monitor-loggar och Application Insights.

## <a name="next-steps"></a>Nästa steg

* [Skapa en serverlös app i Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Skapa en instrumentpanel för kundinsikter med serverlös](logic-apps-scenario-social-serverless.md)
* [Automatisera distributionen av logikappar](logic-apps-azure-resource-manager-templates-overview.md)
