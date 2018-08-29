---
title: Avtal för B2B - kommunikation i Azure Logic Apps | Microsoft Docs
description: Skapa avtal för B2B trading partner kommunikation med Azure Logic Apps och Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 06/29/2016
ms.openlocfilehash: 09bee10649e2bc0d745e42b8aa13ae9c21df35aa
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128835"
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Partner avtal för B2B-kommunikation med Azure Logic Apps och Enterprise-Integrationspaket

Avtal för att affärsentiteter kommunicera sömlöst med branschstandardprotokoll och är Hörnstenen i för business-to-business (B2B)-kommunikation. När du aktiverar B2B-scenarier för logic apps med Enterprise-Integrationspaketet är ett avtal en kommunikation mellan B2B-affärspartner. Detta avtal är baserat på den kommunikation som partner som vill upprätta och är protokoll eller transport-specifika.

Enterprise-integration stöder dessa protokoll eller transport standarder:

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Varför ska man använda avtal

Här följer några vanliga fördelarna när du använder avtal:

* Aktiverar olika organisationer och företag att utbyta information i wkt-format.
* Förbättrar effektiviteten vid B2B-transaktioner
* Enkelt att skapa, hantera och använda när du skapar enterprise integrationsappar

## <a name="how-to-create-agreements"></a>Så här skapar du avtal

* [Skapa ett AS2-avtal](logic-apps-enterprise-integration-as2.md)
* [Skapa en X12 avtal](logic-apps-enterprise-integration-x12.md)
* [Skapa ett EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>Hur du använder ett avtal

Du kan skapa [logikappar](logic-apps-overview.md "Lär dig mer om Logic apps") med B2B-funktioner med hjälp av ett avtal som du skapade.

## <a name="how-to-edit-an-agreement"></a>Så här redigerar du ett avtal

Du kan redigera alla avtalet genom att följa dessa steg:

1. Välj integrationskontot som har det avtal som du vill uppdatera.

2. Välj den **avtal** panelen.

3. På den **avtal** bladet välj avtalet.

4. Välj **redigera**. Gör dina ändringar.

5. För att spara ändringarna, Välj **OK**.

## <a name="how-to-delete-an-agreement"></a>Hur du tar bort ett avtal

Du kan ta bort alla avtalet genom att följa dessa steg:

1. Välj integrationskontot som har det avtal som du vill ta bort.
2. Välj den **avtal** panelen.
3. På den **avtal** bladet välj avtalet.
4. Välj **ta bort**.
5. Bekräfta att du vill ta bort det valda avtalet.

    Bladet avtal visas inte längre har tagits bort avtalet.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett AS2-avtal](logic-apps-enterprise-integration-as2.md)
