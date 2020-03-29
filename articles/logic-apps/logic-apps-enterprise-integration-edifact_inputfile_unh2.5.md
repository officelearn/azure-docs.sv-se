---
title: UNH 2.5 segment i EDIFACT-meddelanden
description: Lösa EDIFACT-meddelanden med UNH2.5-segment i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792536"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Hantera EDIFACT-dokument med UNH2.5-segment i Azure Logic Apps

Om det finns ett UNH2.5-segment i ett EDIFACT-dokument används segmentet för schemasökning. I det här EDIFACT-meddelandet är `EAN008`till exempel UNH-fältet:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Så här hanterar du det här meddelandet nedan:

1. Uppdatera schemat.

1. Kontrollera avtalsinställningarna.

## <a name="update-the-schema"></a>Uppdatera schemat

Om du vill bearbeta meddelandet måste du distribuera ett schema som har rotnodnamnet UNH2.5. Schemarotnamnet för exempelfältet UNH är `EFACT_D03B_ORDERS_EAN008`till exempel . För `D03B_ORDERS` varje som har ett annat UNH2.5-segment måste du distribuera ett enskilt schema.

## <a name="add-schema-to-edifact-agreement"></a>Lägga till schema i EDIFACT-avtalet

### <a name="edifact-decode"></a>EDIFACT Avkoda

Om du vill avkoda det inkommande meddelandet ställer du in schemat i EDIFACT-avtalets mottagningsinställningar:

1. Öppna ditt integrationskonto i [Azure-portalen.](https://portal.azure.com)

1. Lägg till schemat i ditt integrationskonto.

1. Konfigurera schemat i EDIFACT-avtalets mottagningsinställningar.

1. Välj EDIFACT-avtalet och välj **Redigera som JSON**. Lägg till UNH2.5-värdet i `schemaReferences` avsnittet Ta emot avtal:

   ![Lägg till UNH2.5 för att få avtal](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT-koda

Om du vill koda det inkommande meddelandet konfigurerar du schemat i EDIFACT-avtalets sändningsinställningar

1. Öppna ditt integrationskonto i [Azure-portalen.](https://portal.azure.com)

1. Lägg till schemat i ditt integrationskonto.

1. Konfigurera schemat i EDIFACT-avtalets sändningsinställningar.

1. Välj EDIFACT-avtal och klicka på **Redigera som JSON**.  Lägg till UNH2.5-värde i **schemareningar** för skicka avtal

1. Välj EDIFACT-avtalet och välj **Redigera som JSON**. Lägg till UNH2.5-värdet i `schemaReferences` avsnittet Skicka avtalet:

   ![Lägg till UNH2.5 för att skicka avtal](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om avtal om [integrationskonto](../logic-apps/logic-apps-enterprise-integration-agreements.md)