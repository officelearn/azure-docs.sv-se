---
title: Hantera integration konto artefaktmetadata – Azure Logic Apps | Microsoft Docs
description: Lägg till eller hämta artefaktmetadata från integrationskonton i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128811"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Hantera artefaktmetadata från integrationskonton i Azure Logic Apps med Enterprise-Integrationspaket

Du kan definiera anpassade metadata för artefakter i integrationskonton och hämta dem under körning för din logikapp. Du kan till exempel ange metadata för artefakter, till exempel partners, avtal, scheman och maps - alla lagra metadata med hjälp av nyckel / värde-par. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Lägga till metadata till artefakter i integrationskonton

1. I Azure-portalen skapar en [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md).

2. Lägg till en artefakt i ditt integrationskonto, till exempel, ett [partner](logic-apps-enterprise-integration-partners.md), [avtal](logic-apps-enterprise-integration-agreements.md), eller [schemat](logic-apps-enterprise-integration-schemas.md).

3. Välj artefakten, Välj **redigera**, och ange metadata-information.

   ![Ange metadata](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Hämta metadata från artefakter för logic apps

1. I Azure-portalen skapar en [logikapp](quickstart-create-first-logic-app-workflow.md).

2. Skapa en [länk från din logikapp till ditt integrationskonto](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. I Logic App Designer, lägger du till en utlösare som **begära** eller **HTTP** i logikappen.

4. Under utlösaren väljer **nytt steg** > **Lägg till en åtgärd**. Sök efter ”integrationskontot” så att du kan hitta och välj sedan den här åtgärden: **Integrationskonto – integrering sökning efter Integrationskontoartefakt**

   ![Välj Integration sökning efter Integrationskontoartefakt](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Välj den **Artefakttypen** och ge den **Artefaktnamn**. Exempel:

   ![Välj typ av artefakt och ange sammansättningsartefaktens namn](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exempel: Hämta partner metadata

Anta att den här partnern har metadata med `routingUrl` information:

![Hitta partner ”routingURL” metadata](media/logic-apps-enterprise-integration-metadata/image6.png)

1. I din logikapp, lägger du till utlösaren och en **Integrationskonto – integrering sökning efter Integrationskontoartefakt** åtgärd för din partner och en **HTTP** åtgärden, till exempel:

   ![Lägga till utlösaren och sökning efter integrationskontoartefakt HTTP-åtgärd i din logikapp](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Om du vill hämta URI, i verktygsfältet Logic App Designer väljer **kodvy** för din logikapp. Din definition för logikappen bör se ut som i följande exempel:

   ![Sök sökning](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om avtalen](logic-apps-enterprise-integration-agreements.md)
