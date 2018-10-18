---
title: Översikt över Azure utan Server | Microsoft Docs
description: Lär dig mer om att skapa kraftfulla lösningar i molnet utan att behöva bekymra dig om infrastrukturen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 068e5399073959d2c5aa6c4bbeb0d7bccf7d05e6
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393787"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Översikt: Azure utan server med Azure Logic Apps och Azure Functions

[Serverlös](https://azure.microsoft.com/solutions/serverless/) program erbjuder fördelar med sänkt hastigheten på utveckling, minskad krävs kod och enkelhet med skalning.  Den här artikeln hamnar i olika attribut för serverlösa lösningar och serverfria erbjudandena i Azure.

## <a name="what-is-serverless"></a>Vad är utan Server?

Serverlösa innebär inte att det finns inga servrar – det betyder bara att utvecklare inte behöver bekymra sig om servrar.  En stor del av traditionell programutveckling svarar på frågor om skalning, värdtjänsten och övervakningen lösningar för att uppfylla kraven för programmet.  Dessa frågor är tar hand om med utan server, som en del av lösningen.  Dessutom faktureras program utan server med en förbrukningsbaserad plan.  Om programmet används aldrig, påförs en avgift aldrig.  De här funktionerna kan utvecklare fokusera enbart på affärslogiken i lösningen.

De viktigaste tjänsterna i Azure runt utan Server är [Azure Functions](https://azure.microsoft.com/services/functions/) och [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Båda dessa lösningar följa principerna ovan och att utvecklare kan skapa robusta molnprogram med minimal kod.

## <a name="what-are-azure-functions"></a>Vad är Azure Functions?

Azure Functions är en lösning för att enkelt köra små delar av kod eller "funktioner," i molnet. Du kan skriva enbart koden som du behöver för det aktuella problemet, utan att bekymra dig över hela programmet eller infrastrukturen för att köra den. Functions kan göra utvecklingen ännu mer produktiv och du kan använda programmeringsspråk du föredrar, till exempel C#, F #, Node.js, Python eller PHP. Betala endast för den tid som koden körs och Azure kan skalas efter behov.

Om du vill komma igång snabbt med Azure Functions, börja med att [Skapa din första Azure-funktion](../azure-functions/functions-create-first-azure-function.md). Mer teknisk information om Azure Functions finns i [Info för utvecklare](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Vad är Azure Logic Apps?

Med Azure Logic Apps är ett sätt att förenkla och implementera skalbara integreringar och arbetsflöden i molnet. Det ger ett visuellt designverktyg för modellering och automatisering av processer som en serie steg kallade ett arbetsflöde.  Det finns [många anslutningsappar](../connectors/apis-list.md) i molnet och lokala tjänster enkelt kan ansluta en app utan server till andra API: er.  En logikapp börjar med en utlösare (t.ex. ”när ett konto läggs till i Dynamics CRM”) och kan när den utlösts starta många kombinationer av åtgärder, konverteringar och villkorslogik.  Logic Apps är ett bra val när samordna olika Azure-funktionerna i en process, särskilt när processen kräver att interagera med ett externt system eller API: et.

Om du vill komma igång med Logic Apps kan du börja med [skapa din första logikapp](quickstart-create-first-logic-app-workflow.md).  Om du behöver mer teknisk information om Logic Apps finns i den [utvecklarreferens](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Hur kan jag skapa och distribuera program utan server i Azure?

Azure tillhandahåller en omfattande uppsättning verktyg för utveckling, distribution och hantering av appar utan server.  Appar kan skapas direkt i Azure portal eller med [verktyg från Visual Studio](logic-apps-serverless-get-started-vs.md).  När ett program har utvecklats kan det vara [distribueras direkt](logic-apps-create-deploy-template.md).  Azure tillhandahåller även övervakning av appar utan server.  Den här övervakningen kan nås från Azure-portalen via API: et eller SDK: er eller med integrerade verktyg till Log Analytics och Application Insights.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med att skapa en app utan server i Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Skapa en instrumentpanel för kunden med utan Server](logic-apps-scenario-social-serverless.md)
* [Skapa en Distributionsmall för en logikapp](logic-apps-create-deploy-template.md)
