---
title: UNH 2,5-segment i EDIFACT-meddelanden
description: Lösa EDIFACT-meddelanden med UNH 2.5-segment i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792536"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Hantera EDIFACT-dokument med UNH 2.5-segment i Azure Logic Apps

Om det finns ett UNH 2.5-segment i ett EDIFACT-dokument används segmentet för schema ökning. I det här exemplet för EDIFACT är fältet UNH `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Följ de här stegen som beskrivs nedan för att hantera det här meddelandet:

1. Uppdatera schemat.

1. Kontrol lera avtals inställningarna.

## <a name="update-the-schema"></a>Uppdatera schemat

För att kunna bearbeta meddelandet måste du distribuera ett schema som har rotnoden UNH 2,5. Till exempel är schema rot namnet för fältet Sample UNH `EFACT_D03B_ORDERS_EAN008`. För varje `D03B_ORDERS` som har ett annat UNH 2.5-segment måste du distribuera ett individuellt schema.

## <a name="add-schema-to-edifact-agreement"></a>Lägg till schema i EDIFACT-avtalet

### <a name="edifact-decode"></a>EDIFACT-avkodning

Om du vill avkoda det inkommande meddelandet ställer du in schemat i EDIFACT-avtalets mottagnings inställningar:

1. Öppna integrations kontot i [Azure Portal](https://portal.azure.com).

1. Lägg till schemat i integrations kontot.

1. Konfigurera schemat i EDIFACT-avtalets mottagnings inställningar.

1. Välj EDIFACT-avtalet och välj **Redigera som JSON**. Lägg till UNH 2.5-värdet i `schemaReferences`s avsnittet för mottagande avtal:

   ![Lägg till UNH 2.5 för att ta emot avtal](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT-kodning

Om du vill koda det inkommande meddelandet konfigurerar du schemat i EDIFACT-avtalet skicka inställningar

1. Öppna integrations kontot i [Azure Portal](https://portal.azure.com).

1. Lägg till schemat i integrations kontot.

1. Konfigurera schemat i EDIFACT-avtalets sändnings inställningar.

1. Välj EDIFACT-avtal och klicka på **Redigera som JSON**.  Lägg till UNH 2.5-värdet i **schemaReferences** för att skicka avtal

1. Välj EDIFACT-avtalet och välj **Redigera som JSON**. Lägg till UNH 2,5-värdet i avsnittet för att skicka avtal `schemaReferences`:

   ![Lägg till UNH 2.5 för att skicka avtal](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [integrerings konto avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)