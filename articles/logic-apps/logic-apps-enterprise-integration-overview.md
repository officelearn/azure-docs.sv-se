---
title: B2B-företagsintegration
description: Lär dig mer om hur du skapar automatiserade B2B-arbetsflöden för företagsintegrering med hjälp av Azure Logic Apps och Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191377"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B företagsintegrationslösningar med Azure Logic Apps och Enterprise Integration Pack

För B2B-lösningar (Business-to-Business) och sömlös kommunikation mellan organisationer kan du skapa automatiserade skalbara arbetsflöden för företagsintegrering med hjälp av EIP (Enterprise Integration Pack) med [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Även om organisationer använder olika protokoll och format kan de utbyta meddelanden elektroniskt. EIP omvandlar olika format till ett format som dina organisationers system kan bearbeta och stöder branschstandardprotokoll, inklusive [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)och [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Du kan också förbättra säkerheten för meddelanden med hjälp av både kryptering och digitala signaturer. Det europeiska innovationspartnerskapet stöder dessa [företagsintegrationsanslutningar](../connectors/apis-list.md#integration-account-connectors) och dessa branschstandarder:

* Elektroniskt datautbyte (EDI)
* Integrering av företagsapplikationer (EAI)

Om du är bekant med Microsoft BizTalk Server eller Azure BizTalk Services följer EIP liknande koncept, vilket gör funktionerna lätta att använda. En stor skillnad är dock att EIP är arkitektoniskt baserad på "integrationskonton" för att förenkla lagring och hantering av artefakter som används i B2B-kommunikation. Dessa konton är molnbaserade behållare som lagrar alla dina artefakter, till exempel partner, avtal, scheman, kartor och certifikat. 

## <a name="why-use-the-enterprise-integration-pack"></a>Varför använda Enterprise Integration Pack?

* Med EIP kan du lagra alla dina artefakter på ett ställe - ditt integrationskonto.

* Du kan skapa B2B-arbetsflöden och integrera med SaaS-appar från tredje part (Software-as-Service), lokala appar och anpassade appar med hjälp av Azure Logic Apps och-anslutningsappar.

* Du kan skapa anpassad kod för dina logikappar med Azure-funktioner.

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Innan du kan börja bygga B2B-logikapparbetsflöden med EIP behöver du följande objekt:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) med de artefakter som du vill använda

* Om du vill skapa kartor och scheman kan du använda [Microsoft Azure Logic Apps Enterprise Integration Tools för Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) och Visual Studio 2015.

När du har skapat ett integrationskonto och lagt till dina artefakter kan du börja bygga B2B-arbetsflöden med dessa artefakter genom att skapa en logikapp i Azure-portalen. Om du inte har tidigare i logikappar kan du prova att [skapa en grundläggande logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill arbeta med dessa artefakter kontrollerar du att du först länkar ditt integrationskonto till logikappen. Därefter kan logikappen komma åt ditt integrationskonto. Du kan också skapa, hantera och distribuera logikappar med hjälp av Visual Studio eller [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp).

Här är stegen på hög nivå för att komma igång med att skapa B2B-logikappar:

![Förutsättningar för att skapa B2B-logikappar](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Testa nu

[Distribuera en fullt fungerande exempellogikapp som skickar och tar emot AS2-meddelanden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Nästa steg

* [Skapa handelspartner](logic-apps-enterprise-integration-partners.md)
* [Skapa avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Lägga till scheman](logic-apps-enterprise-integration-schemas.md)
* [Lägg till kartor](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrera från BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
