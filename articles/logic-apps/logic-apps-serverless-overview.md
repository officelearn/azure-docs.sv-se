---
title: Översikt – Azure utan Server
description: Skapa kraftfulla lösningar i molnet utan att oroa dig över infrastrukturen
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: d658efd278425358a2a931fe976827c3bae4247d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792821"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Översikt: Azure server utan Azure Logic Apps och Azure Functions

[Server](https://azure.microsoft.com/solutions/serverless/) lös appar erbjuder fördelar som ökad utvecklings hastighet, minskad kod, enkelhet och skalning. Den här artikeln beskriver de olika attributen för lösningar utan server och Azure Server lösa erbjudanden.

## <a name="what-is-serverless"></a>Vad är Server lös?

Server lös innebär inte att det inte finns några servrar, utan i stället behöver utvecklare oroa sig för servrar. En stor del av traditionell program utveckling besvarar frågor kring skalnings-, värd-och övervaknings lösningar som uppfyller programmets krav. Med Server lös vidtas dessa frågor som en del av lösningen. Dessutom faktureras appar utan server utifrån en förbruknings plan. Om appen aldrig används kommer ingen kostnad att debiteras. Dessa funktioner hjälper utvecklare att fokusera enbart på en lösnings affärs logik.

De grundläggande Azure-tjänsterna för Server lös är [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) och [Azure Functions](https://azure.microsoft.com/services/functions/). Båda lösningarna följer de tidigare beskrivna principerna och hjälper utvecklare att bygga robusta molnappar med minimal kod.

## <a name="what-is-azure-logic-apps"></a>Vad är Azure Logic Apps?

[Azure Logic Apps](logic-apps-overview.md) är ett sätt att förenkla och implementera skalbara integreringar och arbets flöden i molnet. Den här tjänsten ger en visuell designer som kan modellera och automatisera processen som en serie steg som kallas för ett arbets flöde. Det finns många [anslutningar](../connectors/apis-list.md) mellan moln tjänster och lokala system som snabbt ansluter en app utan server till andra API: er. Varje Logic app börjar med en utlösare, till exempel "när ett konto läggs till i Dynamics CRM". När utlösaren utlöses kan arbets flödet köra kombinationer av åtgärder, konverteringar och villkorlig logik. Logic Apps är ett bra val när du dirigerar olika Azure Functions i en process, särskilt när processen kräver att du interagerar med ett externt system eller API.

Börja med att [skapa din första Logic-app](quickstart-create-first-logic-app-workflow.md)för att komma igång med Logic Apps. Mer teknisk information om Logic Apps finns i referens för [utvecklare](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Vad är Azure Functions?

Azure Functions är en tjänst för att enkelt köra delar av kod eller "Functions" i molnet. Du kan bara skriva den kod som krävs för det aktuella problemet, utan att oroa dig för en hel app eller den infrastruktur som krävs. Functions kan göra utvecklingen ännu mer produktiv och du kan använda det utvecklings språk som du väljer, till C#exempel F#Node. js, python eller php. Du betalar bara för den tid som koden körs och Azure skalas vid behov.

Börja med att [skapa din första Azure-funktion](../azure-functions/functions-create-first-azure-function.md)för att komma igång med Azure Functions. Mer teknisk information om funktioner finns i referens för [utvecklare](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hur kan jag bygga och distribuera appar utan server i Azure?

Azure innehåller omfattande verktyg för att utveckla, distribuera och hantera appar utan server. Du kan bygga appar direkt i Azure Portal, med [verktyg i Visual Studio](logic-apps-serverless-get-started-vs.md)eller [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). När du har skapat din app kan du [distribuera appen snabbt med Azure Resource Manager mallar](logic-apps-deploy-azure-resource-manager-templates.md). Azure tillhandahåller också övervakning, som du kan komma åt via Azure Portal, via API eller SDK eller med integrerade verktyg för Azure Monitor loggar och Application Insights.

## <a name="next-steps"></a>Nästa steg

* [Bygg en app utan server i Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Skapa en instrument panel för kund insikter med Server lös](logic-apps-scenario-social-serverless.md)
* [Automatisera Logic app-distribution](logic-apps-azure-resource-manager-templates-overview.md)
