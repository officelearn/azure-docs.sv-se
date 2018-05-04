---
title: Hantera integration konto artefakt metadata - Azure Logic Apps | Microsoft Docs
description: Lägg till eller hämta artefakt metadata från integrationskonton för Logikappar i Azure
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 8ebca60c21366cddadccfd0456880696457d7777
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Hantera artefakt metadata i integrationskonton för logic apps

Du kan ange anpassade metadata för artefakter i integrationskonton och hämta den metadata under körningen för din logikapp. Du kan till exempel ange metadata för artefakter, till exempel partners, avtal, scheman och maps - alla store metadata med nyckel-värdepar. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Lägga till metadata artefakter i integrationskonton

1. I Azure portal, skapa en [integrering konto](logic-apps-enterprise-integration-create-integration-account.md).

2. Lägga till en artefakt till ditt konto integration, till exempel en [partner](logic-apps-enterprise-integration-partners.md), [avtal](logic-apps-enterprise-integration-agreements.md), eller [schemat](logic-apps-enterprise-integration-schemas.md).

3. Markera artefakten, Välj **redigera**, och ange information om metadata.

   ![Ange metadata](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Hämta metadata från artefakter för logikappar

1. I Azure portal, skapa en [logikapp](quickstart-create-first-logic-app-workflow.md).

2. Skapa en [länk från din logikapp till ditt konto integration](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. Lägga till en utlösare som logik App Designer **begära** eller **HTTP** till din logikapp.

4. Välj under utlösare, **nytt steg** > **lägga till en åtgärd**. Sök efter ”integration konto” du kan söka efter och välj sedan den här åtgärden: **integrering konto - Integration artefakt Kontouppslag**

   ![Välj Integration artefakt Kontouppslag](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Välj den **typ av artefakt** och ange den **artefakt namnet**. Exempel:

   ![Välj typ av artefakt och ange artefakt namn](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exempel: Hämta partner metadata

Anta att den här partnern har dessa metadata med `routingUrl` information:

![Hitta partner ”routingURL” metadata](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Lägg till din utlösare i din logikapp, en **integrering konto - Integration artefakt Kontouppslag** åtgärd för din partner och en **HTTP** åtgärden, till exempel:

   ![Lägg till utlösare, artefakt sökning och HTTP-åtgärd i din logikapp](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Om du vill hämta URI i verktygsfältet logik App Designer väljer **kodvy** för din logikapp. Din logik app definition bör se ut som i det här exemplet:

   ![Sök-sökning](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om avtalen](logic-apps-enterprise-integration-agreements.md)
