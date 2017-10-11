---
title: "Avtal för B2B - kommunikation i Azure Logic Apps | Microsoft Docs"
description: "Skapa avtal så att partners kan kommunicera i B2B-scenarier för Azure Logic Apps och Enterprise-Integrationspaket"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: LADocs
ms.openlocfilehash: 7ce0860272901f3b4e4cf3d63f7361d539f64741
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Partner avtal för B2B-kommunikation med Azure Logikappar och Enterprise-Integrationspaket

Avtal kan kommunicera sömlöst med standardprotokollen affärsobjekt och är hörnstenarna för business-to-business (B2B)-kommunikation. När du aktiverar B2B-scenarier för logic apps med Enterprise-Integrationspaket är ett avtal en kommunikation mellan B2B handelspartner. Det här avtalet är baserat på de meddelanden som partners vill upprätta och är protokoll eller transport-specifika.

Enterprise integration stöder dessa protokoll eller transport standarder:

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Varför använda avtal

Här är några vanliga fördelar när du använder avtal:

* Aktiverar olika organisationer och företag att utbyta information i ett välkänt format.
* Förbättrar effektiviteten vid B2B-transaktioner
* Enkelt att skapa, hantera och använda när du skapar enterprise integrationsappar

## <a name="how-to-create-agreements"></a>Så här skapar du avtal

* [Skapa ett AS2-avtal](logic-apps-enterprise-integration-as2.md)
* [Skapa en X12 avtal](logic-apps-enterprise-integration-x12.md)
* [Skapa ett EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>Hur du använder ett avtal

Du kan skapa [logikappar](logic-apps-what-are-logic-apps.md "Lär dig mer om Logic apps") med B2B-funktioner med hjälp av ett avtal som du skapade.

## <a name="how-to-edit-an-agreement"></a>Så här redigerar du ett avtal

Du kan redigera avtal genom att följa dessa steg:

1. Välj det integration-konto som har det avtal som du vill uppdatera.

2. Välj den **avtal** panelen.

3. På den **avtal** bladet välj avtalet.

4. Välj **redigera**. Gör önskade ändringar.

5. Spara dina ändringar genom att välja **OK**.

## <a name="how-to-delete-an-agreement"></a>Hur du tar bort ett avtal

Du kan ta bort ett avtal på följande sätt:

1. Välj det integration-konto som har det avtal som du vill ta bort.
2. Välj den **avtal** panelen.
3. På den **avtal** bladet välj avtalet.
4. Välj **ta bort**.
5. Bekräfta att du vill ta bort det valda avtalet.

    Bladet avtal visas inte längre borttagna avtalet.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett AS2-avtal](logic-apps-enterprise-integration-as2.md)
