---
title: Hantera integration konto artefakt metadata - Azure Logic Apps | Microsoft Docs
description: "Lägg till eller hämta artefakt metadata från integrationskonton för Logikappar i Azure"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 28bb8296ddd820ec5aa9793dc0928b4b1e67bf6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Hantera artefakt metadata i integrationskonton för logic apps

Du kan ange anpassade metadata för artefakter i integrationskonton och hämta den metadata under körningen för din logikapp. Exempelvis kan du ange metadata för artefakter som partners, avtal, scheman och maps - alla lagrar metadata med nyckel-värdepar. För närvarande artefakter kan inte skapa metadata via Användargränssnittet, men du kan också använda REST API: er för att skapa metadata. Om du vill lägga till metadata när du skapar eller välj en partner, avtal eller schemat i Azure portal, Välj **redigera som JSON**. Du kan använda funktionen Integration artefakt Kontouppslag för att hämta metadata för artefakten i logikappar.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Lägga till metadata artefakter i integrationskonton

1. Skapa en [integrering konto](logic-apps-enterprise-integration-create-integration-account.md).

2. Lägga till en artefakt till ditt konto integration, till exempel en [partner](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), [avtal](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements), eller [schemat](logic-apps-enterprise-integration-schemas.md).

3.  Markera artefakten, Välj **redigera som JSON**, och ange information om metadata.

    ![Ange metadata](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Hämta metadata från artefakter för logikappar

1. Skapa en [logikapp](logic-apps-create-a-logic-app.md).

2. Skapa en [länk från din logikapp till ditt konto integration](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. Lägga till en utlösare som logik App Designer *begära* eller *HTTP* till din logikapp.

4.  Välj **nästa steg** > **lägga till en åtgärd**. Sök efter *integrering* så att du kan söka efter och välj sedan **integrering konto - Integration artefakt Kontouppslag**.

    ![Välj Integration artefakt Kontouppslag](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Välj den **typ av artefakt**, och ange den **artefakt namnet**.

    ![Välj typ av artefakt och ange artefakt namn](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exempel: Hämta partner metadata

Partner metadata har dessa `routingUrl` information:

![Hitta partner ”routingURL” metadata](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Lägg till din utlösare i din logikapp, en **integrering konto - Integration artefakt Kontouppslag** åtgärd för din partner och en **HTTP**.

    ![Lägg till utlösare och artefakt sökning ”HTTP” i din logikapp](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Hämta URI genom att gå till kodvy för din logikapp. Din logik app definition bör se ut som i det här exemplet:

    ![Sök-sökning](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Nästa steg
* [Mer information om avtalen](logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  
