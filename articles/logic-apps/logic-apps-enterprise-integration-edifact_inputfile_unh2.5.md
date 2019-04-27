---
title: Hantera EDIFACT-meddelanden med UNH 2,5 segements – Azure Logic Apps | Microsoft Docs
description: Lös EDIFACT-dokument med UNH2.5-segment i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681686"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Hantera EDIFACT-dokument med UNH2.5-segment i Azure Logic Apps

När UNH2.5 finns i EDIFACT-dokument, används den för schema-sökning. 

Exempel: Fältet UNH är **EAN008** i EDIFACT-meddelande  
UNH + SSDD1 + ORDER: D: 03B: FN:**EAN008**”  

Steg att följa för att hantera meddelandet 
1. Uppdatera schemat
2. Kontrollera inställningarna för avtal  

## <a name="update-the-schema"></a>Uppdatera schemat
Du behöver distribuera ett schema med UNH2.5 rotnodens namn för att bearbeta meddelandet.  För den givna ett exempel rotnamn schemat skulle vara **EFACT_D03B_ORDERS_EAN008**  

För varje D03B_ORDERS med en annan UNH2.5-segment, skulle du behöva distribuera en enskild schema.  

## <a name="add-schema-to-the-edifact-agreement"></a>Lägga till schemat i EDIFACT-avtal
### <a name="edifact-decode"></a>EDIFACT-avkodning
Konfigurera schemat för att avkoda det inkommande meddelandet i EDIFACT avtal ta emot inställningarna
1. Lägga till schemat i integrationskontot    
2. Konfigurera schemat i EDIFACT avtal ta emot inställningarna. 
3. Välj EDIFACT-avtal och klicka på **redigera som JSON**.  UNH2.5 mervärde i avtalet får **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT-kodning
Konfigurera schemat för att koda det inkommande meddelandet i avsändarinställningarna för EDIFACT-avtal
1. Lägga till schemat i integrationskontot    
2. Konfigurera schemat i avsändarinställningarna för EDIFACT-avtal. 
3. Välj EDIFACT-avtal och klicka på **redigera som JSON**.  Lägg till UNH2.5-värde i avtalet skicka **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om integrering konto avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  