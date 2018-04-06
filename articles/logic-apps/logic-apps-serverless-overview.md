---
title: Översikt över Azure serverlösa | Microsoft Docs
description: Skapa kraftfulla lösningar i molnet utan att tänka på infrastrukturen.
keywords: ''
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 30c7dd36efca5ffcae770fe310a43a107d9136af
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Översikt över Azure serverlösa med funktioner och Logic Apps

Serverlösa program ger fördelar för ökad hastighet för utveckling, minskad kod obligatorisk och enkelhet med en skala.  Den här artikeln övergår i olika attribut i serverlösa lösningar och Azure serverlösa erbjudanden.

## <a name="what-is-serverless"></a>Vad är serverlösa?

Utan Server betyder det finns inga servrar – det betyder bara att utvecklaren inte behöver bekymra sig om servrar.  En stor del av traditionella programutveckling svarar frågor kring skalning, värd och övervakning lösningar för att uppfylla kraven för programmet.  Dessa frågor är tagit hand om med utan server, som en del av lösningen.  Dessutom debiteras serverlösa program på en plan för förbrukningsbaserad.  Om programmet används aldrig, påförs en avgift aldrig.  De här funktionerna kan utvecklare kan fokusera endast på affärslogiken i lösningen.

De viktigaste tjänsterna i Azure runt utan Server är [Azure Functions](https://azure.microsoft.com/services/functions/) och [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/).  Båda lösningarna följer principerna ovan och låta utvecklare att bygga robusta molnprogram med minimal kod.

## <a name="what-are-azure-functions"></a>Vad är Azure Functions?

Azure Functions är en lösning för att enkelt köra små delar av kod eller "funktioner," i molnet. Du kan skriva enbart koden som du behöver för det aktuella problemet, utan att bekymra dig över hela programmet eller infrastrukturen för att köra den. Funktioner kan göra utvecklingen ännu mer produktiv och du kan använda programmeringsspråk du föredrar, till exempel C#, F #, Node.js, Python eller PHP. Betala endast för tiden koden körs och Azure skalas efter behov.

Om du vill komma igång snabbt med Azure Functions, börja med att [Skapa din första Azure-funktion](../azure-functions/functions-create-first-azure-function.md). Mer teknisk information om Azure Functions finns i [Info för utvecklare](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Vad är Azure Logic Apps?

Med Azure Logikappar kan du förenkla och implementerar skalbara integreringar och arbetsflöden i molnet. Det ger en visuell designer för att modellera och automatisera processen som en serie steg kallas ett arbetsflöde.  Det finns [många kopplingar](../connectors/apis-list.md) för molnet och lokala tjänster att snabbt ansluta en serverlösa app till andra API: er.  En logikapp börjar med en utlösare (t.ex. ”när ett konto läggs till i Dynamics CRM”) och kan när den utlösts starta många kombinationer av åtgärder, konverteringar och villkorslogik.  Logic Apps är ett bra val när samordna olika Azure-funktionerna i en process, särskilt när processen kräver interaktion med ett externt system eller API.

Börja med att komma igång med Logic Apps [skapa din första logikapp](quickstart-create-first-logic-app-workflow.md).  Om du behöver mer teknisk information om Logic Apps finns i [för utvecklare](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Hur kan skapa och distribuera serverlösa program i Azure?

Azure tillhandahåller en omfattande uppsättning av verktyg för utveckling, distribution och hantering av serverlösa appar.  Appar kan skapas direkt i Azure-portalen eller med [tooling från Visual Studio](logic-apps-serverless-get-started-vs.md).  När ett program som har utvecklats kan det vara [distribueras direkt](logic-apps-create-deploy-template.md).  Azure tillhandahåller också övervakning för serverlösa appar.  Denna övervakning kan nås från Azure-portalen via API: et eller SDK eller med integrerad verktygsuppsättning logganalys och Application Insights.

## <a name="next-steps"></a>Nästa steg

* [Komma igång med att skapa en serverlösa app i Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Skapa en kund insikter instrumentpanel med utan Server](logic-apps-scenario-social-serverless.md)
* [Skapa en Distributionsmall för en logikapp](logic-apps-create-deploy-template.md)